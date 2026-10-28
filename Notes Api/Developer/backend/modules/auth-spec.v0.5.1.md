---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-006-06]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
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
<!-- SECTION: backend.auth.purpose | status: DERIVED -->
## Purpose

**Responsible for** the email one-time-code login mechanics (BR-029, BR-032, BR-033; US-006-06; project `backend.module-map`):

- Issuing a 6-digit numeric code (BR-029) with a 10-minute lifetime (BR-020, BR-024, BR-026), delivered by email through AWS SES (`feedback.md`, preflight answer).
- Limiting code requests to 3 within 10 minutes (BR-028, BR-032).
- Verifying a code. A wrong, expired or already-used code is refused (BR-023, BR-033). A successful verify consumes the code (BR-023).
- Locking verification for 15 minutes after 5 wrong codes (BR-022, BR-025, BR-027, BR-032).
- Retention of challenge, send-attempt and lock records (NFR-019, as read in `ASM-BE-002`).
- Keeping email addresses and codes out of logs (NFR-018, as read in `ASM-BE-012`).

**Not responsible for:**

- What a successful verify issues to the caller (token type, lifetime, transport). It is undecided: `Q-BE-003`, `GAP-BE-004`.
- Who may request, verify or receive a code. It is undecided: `Q-BE-001`.
- Note ownership and the per-note access check. These belong to the `notes` module and are undecided: `Q-BE-002`.
- The "existing login methods" that NFR-016 says remain unchanged. They are not described anywhere: `GAP-BE-012`.
- Publishing the OpenAPI document. That belongs to `contract-docs`.
- Out-of-scope refusals. They belong to `scope-guard`.
- Provisioning SES or any other infrastructure, and the EU-region checks. Those belong to `residency`.
- The web_app sign-in screens (US-006-06 UI part).
- The contents of the four inbound handoff files, which were not supplied: `GAP-BE-001`.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Names, types and constraints are designed by this spec. Their only inputs are the entities named in NFR-019 and the rules BR-020 to BR-033. All timestamps are `timestamptz` and stored in UTC. All three tables are in PostgreSQL 16 in an approved EU region (BR-001, `platform.md`). Records are keyed by email address (`ASM-BE-001`, `ASM-BE-114`).

### `login_code_challenges`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PK |
| `email` | text | NOT NULL | — | normalised (`ASM-BE-114`); PII |
| `code_hash` | text | NOT NULL | — | never the plaintext code (`ASM-BE-113`) |
| `issued_at` | timestamptz | NOT NULL | now | — |
| `expires_at` | timestamptz | NOT NULL | — | `= issued_at + 10 minutes` (BR-020); CHECK `expires_at > issued_at` |
| `status` | text | NOT NULL | `'ISSUED'` | CHECK in (`ISSUED`, `USED`, `SUPERSEDED`) |
| `used_at` | timestamptz | NULL | NULL | CHECK `(status = 'USED') = (used_at IS NOT NULL)` |

The expired state is not stored. It is derived from `expires_at` (see state machine).

| Index | Serves |
|---|---|
| UNIQUE (`email`) WHERE `status = 'ISSUED'` | At most one live challenge per email. Enforces `ASM-BE-115`. |
| (`email`, `issued_at` DESC) | Verify lookup of the latest challenge for an email |
| (`issued_at`) | Retention sweep (age 1 day, `ASM-BE-121`) |

### `login_send_attempts`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | PK |
| `email` | text | NOT NULL | — | normalised; PII |
| `requested_at` | timestamptz | NOT NULL | now | — |
| `challenge_id` | uuid | NULL | NULL | FK → `login_code_challenges(id)` ON DELETE SET NULL |

| Index | Serves |
|---|---|
| (`email`, `requested_at`) | Count of accepted requests in the rolling 10-minute window |
| (`requested_at`) | Retention sweep |

### `account_locks`

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `email` | text | NOT NULL | — | PK; normalised; PII |
| `failed_count` | smallint | NOT NULL | 0 | CHECK 0 ≤ `failed_count` ≤ 5 |
| `locked_until` | timestamptz | NULL | NULL | CHECK `(failed_count = 5) = (locked_until IS NOT NULL)` is not enforced; see transitions |
| `updated_at` | timestamptz | NOT NULL | now | set on every change |

| Index | Serves |
|---|---|
| (`updated_at`) | Retention sweep (age 5 days, `ASM-BE-121`) |

**Notes**

- The wrong-code counter lives in `account_locks`. NFR-019 names only "code challenges", "send attempts" and "account locks", so counter storage is `ASM-BE-118`.
- At-rest treatment of the PII column `email` is unstated: `GAP-BE-010`.
- The storage scheme for `code_hash` is unspecified: `GAP-BE-134`.
- Ownership relations to notes are not modelled here (`Q-BE-002`).
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

### Challenge (`login_code_challenges.status` plus the clock)

| State | Meaning | Terminal |
|---|---|---|
| `ISSUED` and `now < expires_at` | Live. May be verified. | no |
| `ISSUED` and `now ≥ expires_at` | **Expired** (derived). A code at or after 10 minutes is expired (`ASM-BE-003`, `Q-BE-006`). | yes |
| `USED` | Verified successfully once (BR-023) | yes |
| `SUPERSEDED` | Replaced by a newer accepted request (`ASM-BE-115`) | yes |

A deleted record (retention) is not a state.

| From | Trigger | Guard | To |
|---|---|---|---|
| (none) | `API-AUTH-015365` accepted | Fewer than 3 accepted requests in the last 10 minutes | `ISSUED`. Any prior `ISSUED` row for the same email becomes `SUPERSEDED` in the same transaction. |
| `ISSUED` live | `API-AUTH-592570` with matching code | Account not locked | `USED` |
| `ISSUED` | clock reaches `expires_at` | — | Expired (derived) |
| `ISSUED` | newer request accepted | — | `SUPERSEDED` |

**Illegal transitions.** A `USED`, expired or `SUPERSEDED` challenge is never re-opened. A submission against one is refused with the codes in the project `backend.error-model` and changes no challenge state.

### Account verification state (per email, `account_locks`)

| State | Condition |
|---|---|
| `OPEN` | No row, or `failed_count < 5` and `locked_until` is NULL or in the past |
| `LOCKED` | `now < locked_until` |

| From | Trigger | Guard | To |
|---|---|---|---|
| `OPEN` | Wrong code on verify | `failed_count + 1 < 5` | `OPEN`, `failed_count + 1` |
| `OPEN` | Wrong code on verify | `failed_count + 1 = 5` | `LOCKED`, `locked_until = now + 15 minutes` (BR-022, BR-025) |
| `LOCKED` | Verify (any code) | `now < locked_until` | `LOCKED`, refused; counter and `locked_until` unchanged (`ASM-BE-118`) |
| `LOCKED` | clock reaches `locked_until` | — | `OPEN`, `failed_count = 0` (`ASM-BE-118`, `GAP-BE-013`) |
| `OPEN` | Successful verify | — | `OPEN`, `failed_count = 0` (`ASM-BE-118`) |

During `LOCKED`, requesting a code is **not** refused (BR-032). There is no terminal state. Rows are removed only by retention.
<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements follow the project error model (`backend.error-model`): 415 for the login failures (BR-033), with the registry `code` as the discriminator. Request and response shapes are defined in the operation records and are not restated here (core rule 6). Examples that need an email address use a masked one.

**BE-AUTH-FR-001 — Issue a code**
- Given a well-formed email and fewer than 3 accepted requests for it in the last 10 minutes
- When `API-AUTH-015365` is called
- Then a 6-digit, all-numeric code is generated (BR-029), a challenge is stored with `expires_at = issued_at + 10 minutes`, a send attempt is recorded, and the code is emailed through AWS SES.
- Traces to: BR-029, BR-020, BR-032, US-006-06. API: `API-AUTH-015365`.
- Verification: (planned) `auth.request_code.issues_six_digit_code_and_sends`.

**BE-AUTH-FR-002 — Resend limit**
- Given 3 accepted requests for an email within the last 10 minutes
- When `API-AUTH-015365` is called again for that email
- Then the call is refused with status 415 and code `LOGIN_CODE_RATE_LIMITED`. No code is sent, no challenge is created, and no send attempt is recorded (`ASM-BE-116`).
- The window is rolling: attempts with `requested_at > now - 10 minutes`. The limit counts requests including the first (`CON-BE-001` reading).
- Traces to: BR-021, BR-028, BR-032, US-004-03, US-006-06. API: `API-AUTH-015365`.
- Verification: (planned) `auth.request_code.fourth_request_in_window_returns_415`.

**BE-AUTH-FR-003 — Requesting during a lock**
- Given an email whose account is `LOCKED`
- When `API-AUTH-015365` is called and the resend limit is not reached
- Then the request is accepted as in FR-001. Only verification is refused during a lock.
- Traces to: BR-032. API: `API-AUTH-015365`.
- Verification: (planned) `auth.request_code.accepted_while_locked`.

**BE-AUTH-FR-004 — Newest code wins**
- Given an email with a live `ISSUED` challenge
- When a new request is accepted
- Then the earlier challenge becomes `SUPERSEDED` and only the new code verifies (`ASM-BE-115`, `Q-BE-109`).
- Traces to: BR-032, US-006-06. API: `API-AUTH-015365`.
- Verification: (planned) `auth.request_code.supersedes_prior_challenge`.

**BE-AUTH-FR-005 — Successful verify**
- Given a live `ISSUED` challenge, a matching code, and an account that is not `LOCKED`
- When `API-AUTH-592570` is called
- Then the challenge becomes `USED`, `failed_count` resets to 0, and the call succeeds. What the success response carries is **not specified** here (see FR-016).
- Traces to: BR-023, US-006-06. API: `API-AUTH-592570`.
- Verification: (planned) `auth.verify_code.success_consumes_code`.

**BE-AUTH-FR-006 — Single use**
- Given a challenge in state `USED`
- When its code is submitted again to `API-AUTH-592570`
- Then the call is refused with 415 and code `LOGIN_CODE_USED`. No state changes (`ASM-BE-117`).
- Traces to: BR-023, BR-033. API: `API-AUTH-592570`.
- Verification: (planned) `auth.verify_code.reuse_returns_415_used`.

**BE-AUTH-FR-007 — Expiry**
- Given a challenge with `now ≥ expires_at`
- When its code is submitted to `API-AUTH-592570`
- Then the call is refused with 415 and code `LOGIN_CODE_EXPIRED`. The boundary is inclusive: exactly 10 minutes counts as expired (`ASM-BE-003`).
- Traces to: BR-020, BR-024, BR-026, BR-033. API: `API-AUTH-592570`.
- Verification: (planned) `auth.verify_code.expired_at_exactly_10_minutes_returns_415`.

**BE-AUTH-FR-008 — Wrong code**
- Given an account that is not `LOCKED` and a well-formed code that matches no usable challenge for the email
- When `API-AUTH-592570` is called
- Then the call is refused with 415 and code `LOGIN_CODE_INVALID`, and `failed_count` increments (`ASM-BE-119`). An expired or used code does not increment it (`ASM-BE-117`, `Q-BE-108`).
- Traces to: BR-022, BR-033. API: `API-AUTH-592570`.
- Verification: (planned) `auth.verify_code.wrong_code_increments_counter`.

**BE-AUTH-FR-009 — Lock after 5 wrong codes**
- Given an account with `failed_count = 4`
- When a fifth wrong code is submitted
- Then `locked_until = now + 15 minutes` and the fifth call is itself refused as `LOGIN_CODE_INVALID`. The lock is visible from the next call.
- Traces to: BR-022, BR-025, BR-027, US-006-06. API: `API-AUTH-592570`.
- Verification: (planned) `auth.verify_code.fifth_wrong_code_locks_15_minutes`.

**BE-AUTH-FR-010 — Verification refused while locked**
- Given an account with `now < locked_until`
- When `API-AUTH-592570` is called with any code, including a correct one
- Then the call is refused with 415 and code `ACCOUNT_LOCKED`. No challenge is consumed, and the counter and `locked_until` do not change (`ASM-BE-118`, `ASM-BE-008`).
- Traces to: BR-022, BR-027, BR-032, BR-033, US-006-06. API: `API-AUTH-592570`.
- Verification: (planned) `auth.verify_code.locked_account_refuses_correct_code`.

**BE-AUTH-FR-011 — Input validation**
- Given a missing or malformed email, or a code that is not 6 numeric digits
- When either operation is called
- Then the call is refused with 400 and code `VALIDATION_ERROR`. Nothing is persisted and nothing is counted (`ASM-BE-114`, `ASM-BE-120`).
- Traces to: BR-029, project `backend.error-model`. API: `API-AUTH-015365`, `API-AUTH-592570`.
- Verification: (planned) `auth.validation.malformed_inputs_return_400`.

**BE-AUTH-FR-012 — Atomic, serialised per email**
- Given concurrent calls for the same email
- When they are processed
- Then the resend count, the wrong-code counter and the single-use consumption behave as if the calls ran one at a time (`ASM-BE-122`). Two concurrent correct submissions of one code cannot both succeed.
- Traces to: BR-023, BR-021, BR-022. API: `API-AUTH-015365`, `API-AUTH-592570`.
- Verification: (planned) `auth.concurrency.single_use_under_parallel_verify`.

**BE-AUTH-FR-013 — Delivery through AWS SES**
- Given an accepted request
- When the code is sent
- Then delivery uses AWS SES (`feedback.md`).
- Whether the send happens in an EU region, and whether the email address leaves the EU, depends on NFR-017, whose text is corrupted: `GAP-BE-135`, `GAP-BE-003`.
- The response and challenge handling when SES fails are unspecified: `GAP-BE-129`.
- Traces to: NFR-017, `feedback.md`. API: `API-AUTH-015365`.
- Verification: (planned) `auth.request_code.sends_through_ses_port`.

**BE-AUTH-FR-014 — Log hygiene**
- Given any logging by this module
- When a log entry is written
- Then it contains no plaintext code and no unmasked email address (`ASM-BE-012`).
- Traces to: NFR-018. API: both.
- Verification: (planned) `auth.logging.no_code_or_raw_email_in_logs`.

**BE-AUTH-FR-015 — Retention**
- Given records older than their retention period
- When the sweep runs (see `backend.auth.jobs`)
- Then challenges and send attempts older than 1 day and lock rows older than 5 days are deleted (`ASM-BE-002`).
- Notes are never touched (`INV-BE-001`).
- Traces to: NFR-019. API: none.
- Verification: (planned) `auth.retention.deletes_expired_records_only`.

**BE-AUTH-FR-016 — Credential on success — BLOCKED**
- What a successful verify returns and what note and search endpoints require of the caller is undecided. See `Q-BE-003` and `GAP-BE-004`.
- This requirement cannot be written until they are answered.
- Traces to: US-006-06. API: `API-AUTH-592570`.

**BE-AUTH-FR-017 — Eligibility to request or verify — BLOCKED**
- Which addresses may request, verify or receive a code is undecided. BR-030 is garbled and the rule is open until decided. See `Q-BE-001`.
- No eligibility check is specified. As a placeholder only, all well-formed addresses are treated as eligible, which BR-030's own rationale flags as possibly conflicting with the shared-team-workspace default.
- Traces to: BR-030, US-006-06. API: both.
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-015365` | POST | `/auth/request-code` | `auth` | 202 | 400, 415 | Request a one-time login code, delivered by email through AWS SES. Limited to 3 requests per 10 minutes; not refused while the account is locked. The code is valid for 10 minutes and single-use. |
| `API-AUTH-592570` | POST | `/auth/verify-code` | `auth` | 200 | 400, 415 | Verify a one-time login code for an email address. Rejects wrong, expired, already-used codes, and refuses verification while the account is locked (15 minutes after 5 wrong codes). A successful verify consumes the code. |

### `API-AUTH-015365` — POST `/auth/request-code`

Request a one-time login code, delivered by email through AWS SES. Limited to 3 requests per 10 minutes; not refused while the account is locked. The code is valid for 10 minutes and single-use.

**Request:** 
- `email` (string, required): Email address the one-time login code is sent to. Must be a valid email address.
**Response:** 
- `message` (string, required): Human-readable confirmation that a code was sent. Never contains the code.
- `expires_in_seconds` (integer): Validity window of the issued code in seconds (10 minutes per BR-024).

### `API-AUTH-592570` — POST `/auth/verify-code`

Verify a one-time login code for an email address. Rejects wrong, expired, already-used codes, and refuses verification while the account is locked (15 minutes after 5 wrong codes). A successful verify consumes the code.

**Request:** 
- `email` (string, required): Email address the code was issued for.
- `code` (string, required): Six-digit one-time code received by email.
**Response:** 
- `verified` (boolean, required): True when the code was accepted. The credential type, lifetime and transport issued on success are undecided (Q-BE-003) and are not specified here.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DERIVED -->
## Invariants

- `INV-BE-104`: A challenge is accepted by verify at most once. After any verify, a `USED` challenge never returns to another state (BR-023).
- `INV-BE-105`: No verify succeeds when `now ≥ expires_at` of the matched challenge (BR-020, `ASM-BE-003`).
- `INV-BE-106`: No verify succeeds while `now < locked_until` for the email, whatever the code (BR-022, BR-032).
- `INV-BE-107`: After any operation, at most one `ISSUED` challenge exists per email, and the number of accepted send attempts for an email in any rolling 10-minute window is at most 3 (BR-032, `ASM-BE-115`, `ASM-BE-116`).
- `INV-BE-108`: No persisted or logged value contains a plaintext code. No log entry contains an unmasked email address (`ASM-BE-113`, `ASM-BE-012`).

Cited from the project spec: `INV-BE-001` (retention never deletes a note) and `INV-BE-003` (stored data resides in approved EU regions).
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The permission matrix (role × resource × operation) and the ownership predicate cannot be written. No input defines roles, who may obtain a code (BR-030, `Q-BE-001`), what credential a verify issues (`Q-BE-003`), or how note ownership follows from the caller (BR-031, `Q-BE-002`).

What is stated: both operations are the means by which a caller obtains access, so a caller has no credential when calling them. The calls are protected only by the resend limit, the lock and the code rules in `backend.auth.requirements`. The inputs state nothing further about their authentication (US-004-03 mentions 401/403 only conditionally).

**What would unblock it:** answers to `Q-BE-001`, `Q-BE-002` and `Q-BE-003`; or the contents of the four inbound handoff files if they answer them (`GAP-BE-001`).
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**Retention sweep** (NFR-019, `ASM-BE-002`, `ASM-BE-121`)

| Target | Deleted when |
|---|---|
| `login_code_challenges` | `issued_at` older than 1 day |
| `login_send_attempts` | `requested_at` older than 1 day |
| `account_locks` | `updated_at` older than 5 days |

- Schedule: unspecified (`GAP-BE-016`).
- The sweep is idempotent: running it twice deletes nothing further.
- It deletes only rows older than their retention period.
- The 1-day attempt retention exceeds the 10-minute window, so the sweep never changes a resend-limit decision. A lock lasts 15 minutes against 5 days of retention, so an active lock is never swept.
- It never touches notes (`INV-BE-001`).
- It logs only counts, never emails or codes (`INV-BE-108`).

**Retries and idempotency**

- `API-AUTH-015365` is not idempotent. Each accepted call issues a new code and counts toward the limit. No idempotency key is supplied by any input.
- `API-AUTH-592570` repeated with the same correct code fails the second time as `LOGIN_CODE_USED` (FR-006).
- Retry of a failed SES send is not specified (`GAP-BE-129`).

**No other scheduled work.** Expiry and lock end are evaluated against the clock at request time, so no sweep job is needed for them.
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit** (fake clock, in-memory port for SES)

- Code generator: always 6 numeric digits; leading zeros preserved (BR-029).
- Expiry boundary: 9 min 59 s accepted, exactly 10 min refused as expired, after 10 min refused as expired (FR-007).
- Resend window: three accepted, fourth refused; the oldest attempt falling out of the window frees a slot (FR-002).
- Lock: counter 0→5, the fifth wrong code locks for 15 minutes, a correct code during the lock is refused (FR-008 to FR-010).
- Counter resets: successful verify and lock expiry both reset to 0 (`ASM-BE-118`).
- Log redaction: no plaintext code or raw email in any emitted line (FR-014).

**Integration** (PostgreSQL 16)

- Full flow: request → verify success → reuse refused (FR-001, FR-005, FR-006).
- Supersede: a second request makes the first code unusable (FR-004); the partial unique index rejects two live challenges for one email.
- Parallel verify of one correct code: exactly one success (FR-012). Parallel requests at the limit: at most 3 accepted.
- Request during lock accepted; verify during lock refused (FR-003, FR-010).
- Retention: seeded old and new rows; only old rows are deleted; notes untouched (FR-015).
- SES failure path, once `GAP-BE-129` is answered.

**Contract**

- The published OpenAPI document lists both operations (US-004-01). Each documents 400, the 415 cases with their registry codes, and a locked-account response (US-004-03). Examples use masked emails (NFR-009, project core rule).
- Contract tests assert that the module exposes no endpoint beyond the two operations (`scope-guard`, NFR-010).
- Because 415 is overloaded (project `backend.error-model`), contract tests assert the `code` value for each case, not the status alone.
- Tests for FR-016 and FR-017 are not written until `Q-BE-001` and `Q-BE-003` are answered.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, assumptions and questions

### Contradictions

No new `CON-` is minted by this module. `CON-BE-001` (BR-021 "resend" versus BR-028/BR-032 "requests") is in force and read as in FR-002. The reading of BR-027's "login attempts" is `ASM-BE-008`.

### Questions (all degradable; defaults taken because the run is unattended)

- `Q-BE-108` (degradable): Do expired-code and used-code submissions count toward the 5 wrong codes? Default: no (`ASM-BE-117`).
- `Q-BE-109` (degradable): Does a new accepted request invalidate the earlier live code? Default: yes (`ASM-BE-115`).
- `Q-BE-110` (degradable): Does the wrong-code counter reset on successful verify and on lock expiry, and does it have a time window? Default: resets on both; no window (`ASM-BE-118`).

Blocking questions already open in the project spec and relied on here: `Q-BE-001`, `Q-BE-002`, `Q-BE-003`. Degradable `Q-BE-006` (expiry boundary) is applied via `ASM-BE-003`.

### Assumptions

- `ASM-BE-113`: Codes are generated with a cryptographically secure random source and stored only as a hash (`code_hash`), never in plaintext. Confirm with a platform security input (`GAP-BE-134`).
- `ASM-BE-114`: Email is trimmed and lowercased before use as a key. A value that is not a syntactically valid email address is a 400 `VALIDATION_ERROR`. Confirm with `GAP-BE-128`.
- `ASM-BE-115`: An accepted request supersedes any earlier `ISSUED` challenge for that email. Confirm with `Q-BE-109`.
- `ASM-BE-116`: A request refused by the resend limit is not recorded as a send attempt and does not extend the window. The window is rolling.
- `ASM-BE-117`: Submissions of an expired or used code do not increment `failed_count`. Confirm with `Q-BE-108`.
- `ASM-BE-118`: `failed_count` lives in `account_locks`. It resets to 0 on successful verify and on lock expiry. Refused verifications during a lock change nothing, and the lock is not extended. Confirm with `Q-BE-110` and `GAP-BE-013`.
- `ASM-BE-119`: A well-formed code that matches no usable challenge, including an email with no challenge at all, is `LOGIN_CODE_INVALID` and counts as a wrong code. Confirm with `GAP-BE-133`.
- `ASM-BE-120`: A code that is not exactly 6 numeric digits is a 400 `VALIDATION_ERROR` and is not counted as a wrong code.
- `ASM-BE-121`: Retention age is measured from `issued_at` (challenges), `requested_at` (attempts) and `updated_at` (locks).
- `ASM-BE-122`: Operations for the same email are serialised (row locking or equivalent). The mechanism is an implementation choice.

### Gaps

Each names the artefact that would close it.

- `GAP-BE-128`: Email normalisation, maximum length and format rules are unstated. Closing artefact: field constraints for the login endpoints.
- `GAP-BE-129`: The response and the challenge's fate when SES fails to send are unspecified, as is retry. Default taken: the challenge is voided and the status is unspecified. Closing artefact: an acceptance criterion for delivery failure.
- `GAP-BE-130`: The success status codes of the two operations are not supplied by any input. Closing artefact: acceptance criteria stating them.
- `GAP-BE-131`: Rate-limit class beyond the stated 3 per 10 minutes is unspecified, including whether limits also apply per client address. Closing artefact: an NFR.
- `GAP-BE-132`: Whether the request-code response may reveal that an address is unknown or ineligible is unspecified. Closing artefact: the answer to `Q-BE-001`.
- `GAP-BE-133`: Verify behaviour for an email that never requested a code (`ASM-BE-119`) is unstated. Closing artefact: an acceptance criterion.
- `GAP-BE-134`: The hashing scheme and any pepper or key management for `code_hash` are unspecified. Closing artefact: a platform security input.
- `GAP-BE-135`: The SES region, sender identity and whether the email address leaves the EU are undetermined. Closing artefact: the region list (`GAP-BE-002`) and clean NFR-017 text (`GAP-BE-003`).

Already open in the project spec and relied on: `GAP-BE-001` (inbound handoffs unread), `GAP-BE-003` (garbled source text), `GAP-BE-004` (credential after verify), `GAP-BE-010` (encryption at rest), `GAP-BE-012` (existing login methods), `GAP-BE-013` (counter reset), `GAP-BE-016` (sweep schedule).
<!-- /SECTION: backend.auth.gaps -->
