---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.7.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-006-06]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: notes_api:backend/modules/auth-SPEC.md
---

<!-- SECTION: backend.auth.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: auth
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-001, Q-BE-002, Q-BE-003]
scope: [API-AUTH-015365, API-AUTH-592570]
```
<!-- /SECTION: backend.auth.frontmatter -->
<!-- SECTION: backend.auth.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Responsible for** (module map, `backend.module-map`; US-006-06; BR-020 to BR-033; NFR-015 to NFR-019):

- Issuing a 6-digit numeric one-time login code and emailing it (BR-029; `API-AUTH-015365`).
- Limiting code requests to 3 within 10 minutes per email, with HTTP 429 beyond that (BR-021, BR-028, BR-032).
- Verifying a code (`API-AUTH-592570`): single use (BR-023), expiry 10 minutes after issue (BR-020, BR-024, BR-026), and a 15-minute lock of verification after 5 wrong codes (BR-022, BR-025, BR-027).
- The status mapping for verify refusals: locked 423, wrong 401, expired 410, already used 409 (BR-033). Precedence is lock, used, expired, wrong (ASM-BE-003).
- Retention of the three login record types (NFR-019).
- Masking of email local parts and exclusion of codes from logs (NFR-017, NFR-018).

**Not responsible for:**

- What a successful verify issues to the caller: credential type, lifetime, transport, refresh, logout. This is `Q-BE-003`, so this module defines no credential.
- Who is eligible to request or receive a code. This is `Q-BE-001` (BR-030 is garbled).
- Note ownership and note access checks (`Q-BE-002`, BR-031). These belong to `notes` and `search`.
- Existing login methods (`GAP-BE-007`).
- The web_app login and code-entry screens. They belong to the web_app surface.
- The error envelope and the final error-code registry. The proposal is in `backend.error-model` (`GAP-BE-011`).
- The OpenAPI document. It belongs to `contract-docs`.
- The transactional email service itself. It is external, in AWS eu-west-1 (NFR-017).
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Three tables, named by NFR-019 as "code challenges, send attempts and account locks". The inputs name no columns, so every column below is derived from BR-020 to BR-033 or assumed. Storage conventions follow `ASM-BE-001`, `ASM-BE-006` and `ASM-BE-122`. PostgreSQL 16 per `platform.md`.

### `login_send_attempts`

One row per accepted code request. It serves the resend limit (BR-021, BR-028).

| Column | Type | Null | Default | Constraint / note |
|---|---|---|---|---|
| `id` | bigint identity | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | PII. Trimmed and lower-cased (`ASM-BE-001`). At-rest treatment unstated (`GAP-BE-012`). |
| `requested_at` | timestamptz | NOT NULL | database `now()` | database clock (`ASM-BE-006`) |

Index `(email, requested_at)`. It serves the query "count attempts for this email with `requested_at > now - 10 minutes`".

### `login_code_challenges`

One row per issued code (BR-020, BR-023, BR-029).

| Column | Type | Null | Default | Constraint / note |
|---|---|---|---|---|
| `id` | bigint identity | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | PII, normalised as above |
| `code_hash` | text | NOT NULL | none | One-way hash of the 6-digit code. The plaintext code is never stored (`ASM-BE-114`, NFR-018). |
| `issued_at` | timestamptz | NOT NULL | database `now()` | expiry is derived from this column |
| `used_at` | timestamptz | NULL | NULL | NULL means unused. A non-NULL value means consumed (BR-023). |

Index `(email, code_hash, issued_at DESC)`. It serves the verify lookup for the most recent challenge matching the submitted code. There is no stored `expired` flag: expiry is derived (`ASM-BE-116`).

### `account_locks`

One row per email that has had a wrong code (BR-022, BR-025; `ASM-BE-004`).

| Column | Type | Null | Default | Constraint / note |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key. PII, normalised as above. |
| `wrong_code_count` | integer | NOT NULL | 0 | `CHECK (wrong_code_count BETWEEN 0 AND 4)`. It is reset to 0 at the moment a lock starts (`ASM-BE-004`). |
| `locked_until` | timestamptz | NULL | NULL | NULL means never locked. Lock active while `now < locked_until` (`ASM-BE-006`). |
| `updated_at` | timestamptz | NOT NULL | database `now()` | used for retention (`ASM-BE-123`) |

No foreign keys. The three tables are linked only by `email` (`ASM-BE-001`), because no account entity exists in any input. Retention periods are in `backend.auth.jobs`.
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

Two independent machines per email.

### Code challenge

| State | Meaning | Terminal |
|---|---|---|
| `ISSUED` | `used_at` is NULL and `now < issued_at + 10 minutes` | no |
| `USED` | `used_at` is set (BR-023) | yes |
| `EXPIRED` | `used_at` is NULL and `now >= issued_at + 10 minutes` (derived, not stored; `ASM-BE-116`) | yes |

After retention (1 day, `ASM-BE-005`) the row is deleted, which is outside the machine.

| From | Trigger | Guard | To |
|---|---|---|---|
| (none) | `API-AUTH-015365` accepted | send attempts in the last 10 minutes < 3; email valid; no lock check (BR-032) | `ISSUED` |
| `ISSUED` | `API-AUTH-592570` with the matching code | email not locked | `USED` |
| `ISSUED` | time passes | `now >= issued_at + 10 minutes` | `EXPIRED` |

Illegal transitions:

- Verify matching a `USED` challenge gives 409 (BR-033).
- Verify matching an `EXPIRED` challenge gives 410 (BR-033).
- Neither changes the challenge or the wrong-code counter.

### Account lock

| State | Meaning |
|---|---|
| `OPEN(n)` | `locked_until` is NULL or in the past, and `wrong_code_count = n`, with n in 0..4 |
| `LOCKED` | `now < locked_until` |

Neither state is terminal.

| From | Trigger | Guard | To |
|---|---|---|---|
| `OPEN(n)` | verify, wrong code | n < 4 | `OPEN(n+1)`; the response is 401 |
| `OPEN(4)` | verify, wrong code (the 5th) | none | `LOCKED`: `locked_until = now + 15 minutes`, counter reset to 0; this response is still 401 (`ASM-BE-004`) |
| `OPEN(n)` | verify, success | none | `OPEN(0)` |
| `LOCKED` | verify, any code | `now < locked_until` | `LOCKED`; the response is 423; the counter is not changed |
| `LOCKED` | time passes | `now >= locked_until` | `OPEN(0)` |
| any | `API-AUTH-015365` | none | unchanged; requesting a code is allowed during a lock (BR-032) |

A verify that is a malformed request (400) changes neither machine (`ASM-BE-120`).

### Verify evaluation order (ASM-BE-003)

1. Validate the request. Failure gives 400.
2. If the email is `LOCKED`, return 423.
3. Find the most recent challenge for the email whose hash matches the submitted code. If it is `USED`, return 409. If it is `EXPIRED`, return 410. If it is `ISSUED`, succeed.
4. If no challenge matches, it is a wrong code: return 401 and apply the counter transition.
<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Status codes and error-code names are those of `backend.error-model`. The `code` names are proposals (`ASM-BE-007`).

**BE-AUTH-FR-001** Issue a code
- Given a syntactically valid email with fewer than 3 send attempts in the last 10 minutes, when `API-AUTH-015365` is called, then the response is 202, a challenge is created and the code is emailed.
- Traces to. US-006-06; BR-032; BR-029.
- Verification. `test_request_code_issues_challenge` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-002** Code format
- Given a code is generated, when it is issued, then it is exactly 6 characters, all digits 0 to 9. A leading zero is allowed (`ASM-BE-115`).
- Traces to. BR-029.
- Verification. `test_code_is_six_numeric_digits` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-003** Resend limit
- Given an email has 3 send attempts with `requested_at` within the last 10 minutes, when `API-AUTH-015365` is called, then the response is 429, no code is sent, no challenge is created and no attempt is recorded (`ASM-BE-118`). Reading per `CON-BE-004`: 3 requests in total are accepted and the 4th is refused.
- Traces to. BR-021; BR-028; BR-032; US-006-06 (scenario 1).
- Verification. `test_fourth_request_in_window_is_429` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-004** Request allowed during lock
- Given an email is `LOCKED`, when `API-AUTH-015365` is called and the limit is not reached, then it behaves as in FR-001.
- Traces to. BR-032; `CON-BE-001`.
- Verification. `test_request_code_allowed_while_locked` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-005** Request validation
- Given the email is missing or syntactically invalid, when `API-AUTH-015365` is called, then the response is 400 and nothing is persisted or sent (`ASM-BE-121`).
- Traces to. US-004-03 (400 documented); NFR-012.
- Verification. `test_request_code_invalid_email_400` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-006** Delivery failure
- Given the email service fails, when `API-AUTH-015365` is called, then the response is 500 and no attempt or challenge row remains (`ASM-BE-125`).
- Traces to. NFR-017 (email service); `ASM-BE-013`.
- Verification. `test_email_failure_rolls_back` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-007** Successful verify
- Given an `ISSUED` challenge matches the submitted code and the email is not locked, when `API-AUTH-592570` is called, then the response is 200, the challenge becomes `USED` and the wrong-code counter is reset to 0. What credential is returned is blocked on `Q-BE-003`.
- Traces to. US-006-06; BR-023; BR-032.
- Verification. `test_verify_success_consumes_code` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-008** Wrong code
- Given no challenge matches and the email is not locked, when `API-AUTH-592570` is called, then the response is 401 and `wrong_code_count` increases by 1.
- Traces to. BR-033; BR-022.
- Verification. `test_wrong_code_401_increments` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-009** Lockout
- Given `wrong_code_count` is 4, when a 5th wrong code is submitted, then the response is 401, `locked_until = now + 15 minutes` and the counter is reset to 0. The next verify within the lock gets 423.
- Traces to. BR-022; BR-025; BR-027; BR-032; US-006-06 (scenario 3).
- Verification. `test_fifth_wrong_code_locks_15_minutes` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-010** Verify during lock
- Given `now < locked_until`, when `API-AUTH-592570` is called with any code, including a correct one, then the response is 423, the counter is unchanged and no challenge is consumed.
- Traces to. BR-022; BR-033.
- Verification. `test_verify_during_lock_423_even_if_correct` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-011** Used code
- Given the matching challenge is `USED`, when `API-AUTH-592570` is called, then the response is 409 and the counter is unchanged.
- Traces to. BR-023; BR-033.
- Verification. `test_used_code_409` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-012** Expired code
- Given the matching challenge has `now >= issued_at + 10 minutes` and is unused, when `API-AUTH-592570` is called, then the response is 410 and the counter is unchanged. The exact-10-minute boundary is `ASM-BE-116` (`GAP-BE-002`).
- Traces to. BR-020; BR-024; BR-026; BR-033; US-006-06 (scenario 2).
- Verification. `test_expired_code_410_and_boundary` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-013** Precedence
- Given more than one refusal applies, when `API-AUTH-592570` is called, then the order is lock, used, expired, wrong.
- Traces to. `ASM-BE-003`; BR-033.
- Verification. `test_refusal_precedence_matrix` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-014** Single use under concurrency
- Given two concurrent verifies carry the same valid code, when both are processed, then exactly one gets 200 and the other gets 409.
- Traces to. BR-023.
- Verification. `test_concurrent_verify_single_use` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-015** Verify validation
- Given the email is missing or invalid, or the code is not exactly 6 digits, when `API-AUTH-592570` is called, then the response is 400 and the counter is unchanged (`ASM-BE-120`, `GAP-BE-008`).
- Traces to. US-004-03; BR-029.
- Verification. `test_verify_malformed_400_not_counted` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-016** Log safety
- Given any request to either operation, when it is logged, then the code never appears and the email local part is masked.
- Traces to. NFR-017; NFR-018.
- Verification. `test_logs_contain_no_code_or_unmasked_email` (planned).
- API. `API-AUTH-015365`, `API-AUTH-592570`.

**BE-AUTH-FR-017** Retention
- Given login records are older than their retention period, when the sweep runs, then they are deleted. See `backend.auth.jobs`.
- Traces to. NFR-019.
- Verification. `test_retention_sweep_deletes_expired_records` (planned).
- API. none.

**BE-AUTH-FR-018** Email residency
- Given a code email is sent, when the service is called, then it is the transactional email service in AWS region eu-west-1. The conditional on transmission outside the EU is garbled (`GAP-BE-003`) and is not specified here.
- Traces to. NFR-017.
- Verification. `test_email_client_configured_for_eu_west_1` (planned).
- API. `API-AUTH-015365`.
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-015365` | POST | `/auth/request-code` | `auth` | 202 | 400, 429, 500 | Request a 6-digit one-time login code to be emailed to the given address. Limited to 3 requests within 10 minutes; allowed while verification is locked (BR-032). |
| `API-AUTH-592570` | POST | `/auth/verify-code` | `auth` | 200 | 400, 401, 409, 410, 423, 500 | Verify a one-time login code. Codes are single use, expire after 10 minutes, and 5 wrong codes lock verification for 15 minutes (BR-033). Refusal precedence: lock, used, expired, wrong (ASM-BE-003). |

### `API-AUTH-015365` — POST `/auth/request-code`

Request a 6-digit one-time login code to be emailed to the given address. Limited to 3 requests within 10 minutes; allowed while verification is locked (BR-032).

**Request:** 
- `email` (string, required): Email address to send the one-time login code to (BR-020, ASM-BE-001; trimmed and compared case-insensitively).
**Response:** 
- `message` (string, required): Confirmation that a code has been sent, without revealing the code or an unmasked email address (NFR-018).
- `expires_in_seconds` (integer): Seconds until the issued code expires; 10 minutes per BR-020.

### `API-AUTH-592570` — POST `/auth/verify-code`

Verify a one-time login code. Codes are single use, expire after 10 minutes, and 5 wrong codes lock verification for 15 minutes (BR-033). Refusal precedence: lock, used, expired, wrong (ASM-BE-003).

**Request:** 
- `email` (string, required): Email address the code was issued to (ASM-BE-001).
- `code` (string, required): The 6-digit numeric one-time code (BR-029).
**Response:** 
- `verified` (boolean, required): True when the code was accepted. The credential issued on success is undecided (Q-BE-003), so no session or token field is defined.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

- **INV-BE-101.** For any email, at most 3 rows in `login_send_attempts` have `requested_at` within any rolling 10-minute window. This holds under concurrency (`ASM-BE-119`). Source: BR-021, BR-028.
- **INV-BE-102.** A challenge is consumed at most once: `used_at` goes from NULL to non-NULL once and never changes again. Source: BR-023.
- **INV-BE-103.** `wrong_code_count` is always between 0 and 4. Reaching 5 resets it to 0 and sets `locked_until`. Source: BR-022, `ASM-BE-004`.
- **INV-BE-104.** While `now < locked_until`, no verify returns 200 and no challenge is consumed. Source: BR-022, BR-033.
- **INV-BE-105.** A verify never returns 200 for a challenge with `now >= issued_at + 10 minutes`. Source: BR-020, BR-024.
- **INV-BE-106.** A request-code call that does not return 202 leaves no new attempt row, challenge row or sent email. Source: BR-028, `ASM-BE-013`, `ASM-BE-118`.
- **INV-BE-107.** No plaintext code is persisted or logged, and no log entry holds an unmasked email local part. Source: NFR-017, NFR-018.
- **INV-BE-108.** A verify that returns 409, 410, 423 or 400 does not change `wrong_code_count`. Source: BR-033, `ASM-BE-004`.
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: BLOCKED -->
## Authorization

**BLOCKED.** The role × operation matrix and the ownership predicate cannot be written without inventing an authorisation rule.

| Missing decision | Reference |
|---|---|
| Who may request, verify or receive a code (BR-030 is garbled) | `Q-BE-001` |
| Whether the operations require or issue a credential, and what that credential is | `Q-BE-003` |
| The access rule the login outcome feeds into on note operations | `Q-BE-002` |

What would unblock it: clean answers to `Q-BE-001`, `Q-BE-002` and `Q-BE-003`. The ownership predicate for these operations is the email address supplied in the request body (`ASM-BE-001`). It is not a user-scoped resource predicate, and the matrix cannot be completed without those answers. See also `backend.auth-model`, which is BLOCKED for the same reasons.
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

### Retention sweep (BE-AUTH-FR-017)

| Table | Delete rows older than | Measured from | Source |
|---|---|---|---|
| `login_code_challenges` | 1 day | `issued_at` | NFR-019; unit assumed (`ASM-BE-005`) |
| `login_send_attempts` | 1 day | `requested_at` | NFR-019; unit assumed (`ASM-BE-005`) |
| `account_locks` | 5 days | `updated_at` (`ASM-BE-123`) | NFR-019 |

- **Schedule.** Daily (`ASM-BE-124`; `GAP-BE-004` says the schedule is unstated).
- **Idempotency.** A delete by age is naturally idempotent. A retried or overlapping run has no extra effect. No idempotency key is needed.
- **Safety.** A challenge is deleted only after the 10-minute expiry and a send attempt only after the 10-minute window. Both retention periods are 1 day, so the sweep cannot affect the rules in FR-003 to FR-012. A lock row is deleted only after the 15-minute lock has ended.
- **Retries.** A failed sweep is simply re-run at the next schedule. No retry policy is stated by any input.

No other scheduled work exists. Lock expiry and code expiry are evaluated at request time from timestamps, so no job is required for them.
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

### Unit

- Code generation: 6 characters, digits only, leading zeros allowed (FR-002).
- Time boundaries: 9:59, exactly 10:00 and 10:01 after issue (FR-012); the send window at exactly 10 minutes.
- Verify decision function: lock, used, expired and wrong combinations (FR-013).
- Counter transitions: 0 to 4, the 5th wrong code, and reset on success (FR-008, FR-009).
- Email masking and log-redaction helper (FR-016).

### Integration

Run against PostgreSQL 16 with a fake email service.

- Request 3 codes, the 4th gets 429, and an attempt older than 10 minutes drops out of the window (FR-003).
- 5 wrong codes lock; a 6th verify with the correct code returns 423; the correct code succeeds after 15 minutes (FR-009, FR-010).
- A request is allowed during a lock (FR-004).
- Email failure leaves no rows (FR-006).
- Concurrent verifies and concurrent requests (FR-014, INV-BE-101, INV-BE-102).
- Retention sweep deletes only rows past their period (FR-017).
- Captured logs contain no code and no unmasked email (FR-016).

### Contract

- Both operations against the published OpenAPI document produced by `contract-docs`: methods, paths, status sets and the error envelope.
- `API-AUTH-015365` responses are 202, 400, 429 and 500 only. `API-AUTH-592570` responses are 200, 400, 401, 409, 410, 423 and 500 only.
- The consumer-side check that the web_app shows the lockout and expiry errors belongs to the web_app surface.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions and assumptions

No new contradiction was found in this module. The four directions were checked.

- Story ↔ requirement: US-006-06 matches BR-021, BR-022 and BR-020 on 3 per 10 minutes, 5 wrong codes and 15 minutes, and 10 minutes.
- Requirement ↔ requirement: the duplicates agree. The existing `CON-BE-001` (lock versus request) and `CON-BE-004` (counting of the resend limit) apply here and are not re-minted.
- Story ↔ design and design ↔ design: the design brief has no auth content, so there is nothing to check.

### Open items inherited from the project spec

`Q-BE-001`, `Q-BE-002`, `Q-BE-003` (blocking), `GAP-BE-001`, `GAP-BE-002`, `GAP-BE-003`, `GAP-BE-004`, `GAP-BE-007`, `GAP-BE-008`, `GAP-BE-011`, `GAP-BE-012`, `CON-BE-001`, `CON-BE-004`, and `ASM-BE-001`, `ASM-BE-002`, `ASM-BE-003`, `ASM-BE-004`, `ASM-BE-005`, `ASM-BE-006`, `ASM-BE-007`, `ASM-BE-013`.

### New questions (degradable, unattended, so the default is taken)

| ID | Class | Question | Default taken |
|---|---|---|---|
| `Q-BE-104` | degradable | Does a newly issued code invalidate earlier unexpired, unused codes for the same email? | No. All remain valid until used or expired (`ASM-BE-117`). |
| `Q-BE-105` | degradable | Is `API-AUTH-592570` also rate-limited, independently of the lock? US-004-03 reads as if it is. | No separate limit; only the lock applies (`ASM-BE-126`). |

### New gaps

| ID | What is missing | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-117` | Email subject, body, sender address and template. NFR-017 gives the region only. | FR-001, FR-018 | A message-content decision. |
| `GAP-BE-118` | Email delivery timeout and retry behaviour. | FR-006 | An email-delivery requirement. |
| `GAP-BE-119` | Whether the 202 or the verify refusals may reveal that an email is ineligible or unknown. This depends on `Q-BE-001` and could allow email enumeration. | FR-001, FR-008 | The BR-030 decision. |
| `GAP-BE-120` | How clients should retry request-code safely. It is not idempotent: every accepted call consumes a send. No idempotency key is defined by any input. | FR-001, FR-003 | A client-retry decision. |

### New assumptions

| ID | Assumption |
|---|---|
| `ASM-BE-114` | The code is stored only as a one-way hash (`code_hash`). No input states how codes are stored (`GAP-BE-012`). |
| `ASM-BE-115` | Codes are generated by a cryptographically secure random source and zero-padded to 6 digits. BR-029 states only length and character set. |
| `ASM-BE-116` | A code is expired when `now >= issued_at + 10 minutes`, so a code at exactly 10 minutes is rejected. BR-033's boundary sentence is garbled (`GAP-BE-002`). |
| `ASM-BE-117` | Earlier unexpired, unused codes stay valid when a new one is issued (`Q-BE-104`). The verify lookup picks the most recent challenge matching the submitted code. |
| `ASM-BE-118` | A request refused with 429 does not record a send attempt, so refused requests do not extend the window. |
| `ASM-BE-119` | Both operations are serialised per email using a transaction-scoped PostgreSQL advisory lock keyed on the email, so INV-BE-101 and INV-BE-102 hold under concurrency. |
| `ASM-BE-120` | A verify whose email is missing or invalid, or whose code is not exactly 6 digits, returns 400 and is not counted as a wrong code (`GAP-BE-008`). |
| `ASM-BE-121` | The request-code email check is syntactic only; no input states the syntax rule. |
| `ASM-BE-122` | Storage conventions: surrogate keys are bigint identity, timestamps are timestamptz in UTC, and defaults use the database clock. |
| `ASM-BE-123` | Lock-row retention is measured from `updated_at`. |
| `ASM-BE-124` | The retention sweep runs daily. No schedule is stated (`GAP-BE-004`). |
| `ASM-BE-125` | The email is sent inside the same database transaction, which is committed only after the send succeeds. A failed send rolls back the attempt and the challenge (extends `ASM-BE-013`). |
| `ASM-BE-126` | Verify has no rate limit other than the lock (`Q-BE-105`). |
<!-- /SECTION: backend.auth.gaps -->
