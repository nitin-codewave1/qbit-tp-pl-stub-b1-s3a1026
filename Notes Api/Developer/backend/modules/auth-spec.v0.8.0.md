---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.8.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-006-06]
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
okf_id: notes_api:backend/modules/auth-SPEC.md
---

<!-- SECTION: backend.auth.frontmatter | status: VERIFIED -->
## Module frontmatter

```yaml
id: auth
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-001, Q-BE-002, Q-BE-003]
scope: [API-AUTH-015365, API-AUTH-592570]
```
<!-- /SECTION: backend.auth.frontmatter -->
<!-- SECTION: backend.auth.purpose | status: DERIVED -->
## Purpose

**Responsibility.** The `auth` module implements email one-time-code login for `notes_api`. It owns US-006-06. Sources: `backend.module-map`; BR-020 to BR-033; NFR-015 to NFR-019.

- Request-code (`API-AUTH-015365`): generate a 6-digit numeric code (BR-029) and email it. Enforce the limit of 3 requests per 10 minutes per email (BR-028, BR-032).
- Verify-code (`API-AUTH-592570`): check a submitted code. Codes are single use (BR-023) and expire 10 minutes after issue (BR-020). Five wrong codes lock verification for 15 minutes (BR-022, BR-025, BR-027).
- Keep the three records NFR-019 names: code challenges, send attempts and account locks. Purge them at the end of their retention period.
- Send the emails through the transactional email service in AWS eu-west-1 (NFR-017; garbled, `GAP-BE-003`).
- Keep codes and unmasked email addresses out of logs (NFR-018).

**Not responsible for**

- **Session or credential.** What a successful verification issues, its lifetime, refresh, logout, and whether notes or search require it. This is the blocking decision `Q-BE-003`.
- **Eligibility.** Which email addresses may request, verify or receive a code (BR-030, `Q-BE-001`).
- **Note ownership and access.** Handled by the `notes` and `search` modules and `Q-BE-002`.
- **Existing login methods.** NFR-016 says they are unchanged, but no input describes them (`GAP-BE-007`).
- **Web app screens.** Login and code entry belong to the web_app surface.
- **Publishing the OpenAPI document.** The `contract-docs` module owns it.
- **Residency provisioning, scanning and gating.** The `residency` module owns them.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Store: PostgreSQL 16 in an approved EU region (`platform.md`; BR-001). Table names follow the three records named in NFR-019.

**Cross-table decisions**

- No account entity exists in any input, so all three tables are keyed by email (`ASM-BE-001`).
- Email is stored trimmed and lower-cased, matching the case-insensitive comparison (`ASM-BE-001`).
- Ids are `uuid` and timestamps are `timestamptz` in UTC (`ASM-BE-101`).
- There are no foreign keys between the tables, so no `ON DELETE` rule applies. The tables have separate retention periods (NFR-019), and a foreign key would make purging order-dependent. The send-attempt-to-challenge link in `backend.domain-model` is conceptual only.

### `login_send_attempts`

One row per accepted request-code call (`ASM-BE-108`).

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PK |
| `email` | text | NOT NULL | none | normalized; PII |
| `requested_at` | timestamptz | NOT NULL | `now()` | none |

Indexes
- `(email, requested_at)`: counts accepted requests in the trailing 10 minutes (BR-028).
- `(requested_at)`: the retention purge (NFR-019).

### `login_code_challenges`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PK |
| `email` | text | NOT NULL | none | normalized; PII |
| `code_check_value` | text | NOT NULL | none | see note below |
| `issued_at` | timestamptz | NOT NULL | `now()` | none |
| `used_at` | timestamptz | NULL | NULL | non-null means used (BR-023) |
| `superseded_at` | timestamptz | NULL | NULL | non-null means replaced by a newer code (`ASM-BE-106`) |

Constraints
- `CHECK (used_at IS NULL OR superseded_at IS NULL)`.
- Partial unique index on `(email) WHERE used_at IS NULL AND superseded_at IS NULL`. This enforces `INV-BE-101`.

Indexes
- `(email, issued_at DESC)`: finds the latest challenge for an email.
- `(issued_at)`: the retention purge.

Notes
- The expiry instant is not stored. It is always `issued_at + 10 minutes` (BR-020), so there is one source of truth.
- `code_check_value` is whatever value lets the service decide whether a submitted code matches. Whether it is the plain code, a digest, or something else is unstated and open (`GAP-BE-012`, `ASM-BE-102`). The 6-numeric-digit format (BR-029) is enforced when a code is generated and when one is submitted, not as a column check.

### `account_locks`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | PK; normalized; PII |
| `wrong_code_count` | smallint | NOT NULL | 0 | `CHECK (wrong_code_count BETWEEN 0 AND 5)` |
| `locked_until` | timestamptz | NULL | NULL | `CHECK (wrong_code_count < 5 OR locked_until IS NOT NULL)` |
| `updated_at` | timestamptz | NOT NULL | `now()` | none |

Indexes
- The primary key serves the lookup by email.
- `(updated_at)`: the retention purge. The reference point for the account-lock retention age is unstated, so `updated_at` is assumed (`GAP-BE-012`).

**PII and at-rest.** `email` is PII in all three tables. No input states an at-rest treatment, so none is asserted (see `backend.coverage` in the project spec).

**Residency.** All three tables sit in the EU database (BR-001, NFR-001).
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

### Code challenge

States: `ISSUED`, `USED`, `EXPIRED`, `SUPERSEDED`.

`EXPIRED` is derived from time (`now >= issued_at + 10 minutes`, `ASM-BE-103`) and is never written. `USED` and `SUPERSEDED` are stored.

| From | To | Trigger | Guard |
|---|---|---|---|
| (none) | `ISSUED` | accepted request-code | email valid; fewer than 3 accepted requests in the trailing 10 minutes; email send succeeded |
| `ISSUED` | `USED` | verify-code with the matching code | lock not active; not expired |
| `ISSUED` | `SUPERSEDED` | a newer challenge is issued for the same email | `ASM-BE-106` |
| `ISSUED` | `EXPIRED` | time passes | 10 minutes since `issued_at` |

**Terminal states:** `USED`, `EXPIRED`, `SUPERSEDED`.

**Illegal transitions** (`ASM-BE-113`)

| Verify submits the matching code of a challenge that is | Result |
|---|---|
| `USED` | 409 (BR-033) |
| `EXPIRED` | 410 (BR-033) |
| `SUPERSEDED` | treated as a wrong code, 401 (`ASM-BE-106`) |

### Account lock

States: `OPEN` (count 0 to 4), `LOCKED` (count 5, `locked_until` in the future).

| From | To | Trigger | Guard |
|---|---|---|---|
| `OPEN` | `OPEN` | wrong code | count after increment < 5 |
| `OPEN` | `LOCKED` | wrong code | count after increment = 5; `locked_until = now + 15 minutes` (BR-022) |
| `LOCKED` | `OPEN` | `locked_until <= now` | count is treated as 0 (`ASM-BE-109`) |
| `OPEN` | `OPEN` | successful verification | count reset to 0 (`ASM-BE-109`) |

- No lock state is terminal.
- The 5th wrong code itself gets the wrong-code response. The lock refuses the verification attempts after it (BR-022: "after 5 wrong codes").
- While `LOCKED`, verification is refused and the counter does not change (`ASM-BE-111`). Requesting a code is not refused (BR-032).
<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** the run did not produce this section for module `auth`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-015365` | POST | `/auth/request-code` | `auth` | 202 | 400, 429, 500 | Request a 6-digit one-time login code to be emailed to an address. Limited to 3 requests per 10 minutes per email; still allowed while verification is locked (BR-028, BR-032). |
| `API-AUTH-592570` | POST | `/auth/verify-code` | `auth` | 200 | 400, 401, 409, 410, 423 | Verify a one-time login code for an email address. Codes are single use, expire 10 minutes after issue, and 5 wrong codes lock verification for 15 minutes (BR-022, BR-023, BR-033). The credential issued on success is undecided (Q-BE-003). |

### `API-AUTH-015365` — POST `/auth/request-code`

Request a 6-digit one-time login code to be emailed to an address. Limited to 3 requests per 10 minutes per email; still allowed while verification is locked (BR-028, BR-032).

**Request:** 
- `email` (string, required): Email address that will receive the one-time code. Masked in logs and examples (NFR-018).
**Response:** none

### `API-AUTH-592570` — POST `/auth/verify-code`

Verify a one-time login code for an email address. Codes are single use, expire 10 minutes after issue, and 5 wrong codes lock verification for 15 minutes (BR-022, BR-023, BR-033). The credential issued on success is undecided (Q-BE-003).

**Request:** 
- `email` (string, required): Email address the code was sent to.
- `code` (string, required): The 6-digit numeric one-time code (BR-029). Never logged (NFR-018).
**Response:** none
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

- **INV-BE-101.** After every request-code call, an email has at most one challenge that is neither used nor superseded (`ASM-BE-106`; enforced by the partial unique index).
- **INV-BE-102.** A challenge is accepted by verify-code at most once (BR-023).
- **INV-BE-103.** For any email, at most 3 send attempts are stored in any 10-minute window (BR-028).
- **INV-BE-104.** `wrong_code_count` is between 0 and 5. It equals 5 only while `locked_until` is set.
- **INV-BE-105.** While `locked_until` is in the future, no verification succeeds and no counter changes (BR-022).
- **INV-BE-106.** A request-code call that returns 400, 429 or 500 leaves no new challenge and no new send-attempt row (BE-AUTH-FR-002, -005, -006).
- **INV-BE-107.** No log line, error body or example contains a code or an unmasked email address (NFR-018).
- **INV-BE-108.** All login data is stored in an approved EU region (BR-001).
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The permission matrix cannot be written until three decisions are made.

| Open decision | Unblocked by |
|---|---|
| `Q-BE-001`: who may request, verify or receive a code (BR-030, garbled) | A clean statement of BR-030 |
| `Q-BE-002`: note ownership and the access rule (BR-031, placeholder) | A decision on ownership and the access rule |
| `Q-BE-003`: what a successful verification issues | A session-model decision |

- **Known by derivation.** The two operations produce the credential, so they cannot require it. No input says whether they are open to every caller or restricted.
- **Ownership predicate.** None is defined. Login records are keyed by email (`ASM-BE-001`), and no resource is user-scoped within this module.
- **Not decided here.** No role names are invented.
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**Retention purge** (NFR-019)
- It deletes rows from `login_code_challenges`, `login_send_attempts` and `account_locks` that are past their retention period. The ages are measured from `issued_at`, `requested_at` and `updated_at` respectively.
- The retention figures are garbled in the source (`GAP-BE-012`), and the schedule is unstated (`GAP-BE-104`).
- The job is idempotent: it deletes by age, so a re-run or an overlapping run is harmless. It does not retry within a run; the next run covers anything missed.
- It never touches notes.

**No expiry sweep.** Challenge expiry and lock expiry are decided at read time, so no job is needed.

**Idempotency**
- No idempotency key is defined by any input.
- Each accepted request-code call issues a new code and counts toward the 3-in-10-minutes limit (`ASM-BE-110`).
- Verify-code is safe to repeat: after success a repeat gets 409.

**Retries**
- The service performs no automatic email retry (`ASM-BE-013`). A failed send returns 500 and the client may call again.
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are planned. Time-dependent tests use an injectable clock, because the rules involve 10-minute and 15-minute boundaries.

**Unit**
- Code generation yields exactly 6 numeric characters (BR-029).
- Window count: 3 accepted in 10 minutes pass and the 4th is refused. Rejected calls are not counted (`ASM-BE-108`).
- Expiry boundary at 10 minutes (`ASM-BE-103`).
- Refusal precedence across combinations of locked, used, expired and wrong (`ASM-BE-003`, `ASM-BE-113`).
- Counter and lock transitions: the 5th wrong code gives 401 and sets the lock, the next call gives 423, and the lock lapses at 15 minutes.
- Email normalization (`ASM-BE-001`) and redaction of the local part (NFR-018).

**Integration** (PostgreSQL 16 and a stubbed email service)
- `auth_request_code_issues_six_digit_code`, `auth_request_code_fourth_request_in_window_is_429`, `auth_request_code_allowed_while_locked`, `auth_request_code_email_failure_persists_nothing`.
- `auth_verify_correct_code_marks_used`, `auth_verify_used_code_is_409`, `auth_verify_expired_code_is_410`, `auth_verify_wrong_code_is_401_and_counts`, `auth_fifth_wrong_code_sets_15_minute_lock`, `auth_verify_while_locked_is_423_even_for_correct_code`.
- `auth_new_code_supersedes_previous` and the partial-unique-index behaviour.
- `auth_concurrent_verify_single_winner`: parallel verifies of one correct code, and parallel wrong codes that must not push the count above 5.
- `auth_purge_deletes_only_records_past_retention`.
- `auth_no_code_or_raw_email_in_logs_or_errors`: capture logs across all outcomes.
- `auth_login_storage_and_email_region_check`.

**Contract**
- For `API-AUTH-015365`: the response statuses are exactly the documented set (success, 400, 429, 500).
- For `API-AUTH-592570`: the response statuses are exactly the documented set (success, 400, 401, 409, 410, 423, 500).
- The responses and the OpenAPI document match field for field. The `contract-docs` coverage check must report both operations present (BR-012).
- Error bodies follow the envelope once `GAP-BE-011` is closed.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: VERIFIED -->
## Gaps, contradictions and assumptions

**Carried from the project spec, not redefined:** `Q-BE-001`, `Q-BE-002`, `Q-BE-003`, `GAP-BE-003`, `GAP-BE-007`, `GAP-BE-008`, `GAP-BE-011`, `GAP-BE-012`, `ASM-BE-001`, `ASM-BE-003`, `ASM-BE-013`.

**Inbound handoffs.** `RUN-CONTEXT.upstream.handoffs` lists `HANDOFF-WEBAPP-526592`, `HANDOFF-WEBAPP-701819`, `HANDOFF-WEBAPP-733353` and `HANDOFF-WEBAPP-752470`. Their bodies were not supplied (`GAP-BE-001`), so none can be satisfied or declined on its content. Closing artefact: the four handoff files. This module answers only the points the requirements settle, which are the lock status 423 (BR-033) and that requesting a code is allowed during a lock (BR-032).

**Questions**
- **Q-BE-101** (degradable). Should a used-code or expired-code submission count toward the 5 wrong codes? Default: no, only a mismatching code counts (`ASM-BE-105`).

**Gaps**
- **GAP-BE-102.** No input says what verify-code does for an email that has no challenge. It is assumed to be 401 and counted (`ASM-BE-107`). Closing artefact: a decision on that case.
- **GAP-BE-103.** Email format and maximum length rules are unstated, and no account entity exists (`ASM-BE-001`). They degrade `backend.auth.data-model` and BE-AUTH-FR-006. Closing artefact: email validation rules and an account-model decision.
- **GAP-BE-104.** The purge schedule is unstated, and the retention figures are garbled (`GAP-BE-012`). They degrade `backend.auth.jobs`. Closing artefact: a schedule and corrected NFR-019 figures.
- **GAP-BE-105.** The transactional email service is not named, and bounce and delivery-failure handling is unstated. They degrade BE-AUTH-FR-005. Closing artefact: the service choice and a delivery-failure requirement.
- **GAP-BE-106.** The code generation method (the randomness source) is unstated. It degrades BE-AUTH-FR-001. Closing artefact: a security requirement for code generation.

**Contradictions.** None raised in this module. BR-021 and BR-028 say "resend" while BR-032 says "requests". BR-032 is read as governing, as the project spec already records.

**Assumptions**
- **ASM-BE-101.** Ids are `uuid` and timestamps are `timestamptz` in UTC. The inputs name neither.
- **ASM-BE-102.** `code_check_value` holds a value that lets the service compare a submitted code. Plain or hashed is open (`GAP-BE-012`).
- **ASM-BE-103.** A code is expired when `now >= issued_at + 10 minutes`. The source sentence on the exact boundary is garbled. Confirmation: a corrected BR-033.
- **ASM-BE-104.** Input validation runs before the lock check, and a malformed code is not counted as wrong.
- **ASM-BE-105.** Only a well-formed, mismatching code counts as wrong (`Q-BE-101`, whose default this is).
- **ASM-BE-106.** A new challenge supersedes the earlier unused one, and a superseded code is treated as wrong. This is the default for an open point in `GAP-BE-008`.
- **ASM-BE-107.** Verify for an email with no challenge returns 401 and counts (`GAP-BE-102`).
- **ASM-BE-108.** The send window is a trailing 10 minutes, and only accepted requests are recorded and counted.
- **ASM-BE-109.** The wrong-code count resets to 0 on a successful verification, and a lapsed lock resets it to 0.
- **ASM-BE-110.** There is no idempotency key. Each accepted request-code call issues a new code and counts toward the limit.
- **ASM-BE-111.** While locked, a verify call is refused before the code is compared, so the count does not change.
- **ASM-BE-112.** Codes are generated from a cryptographically secure random source (`GAP-BE-106`).
- **ASM-BE-113.** "Used" and "expired" outcomes apply only when the submitted code matches the latest challenge. Any other submission is a wrong code.
<!-- /SECTION: backend.auth.gaps -->
