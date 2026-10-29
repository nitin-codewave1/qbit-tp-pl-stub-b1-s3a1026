---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.9.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-006-06]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: notes_api:backend/modules/auth-SPEC.md
---

<!-- SECTION: backend.auth.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: auth
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-001, Q-BE-003]
scope: [API-AUTH-015365, API-AUTH-592570]
```

Derivation:
- `scope` lists the two operation ids minted by DevAgent for this module.
- `blocked_by` names the two blocking questions that touch this module (see `backend.auth.gaps`).
  - `Q-BE-001`: who may request and receive a code (BR-030).
  - `Q-BE-003`: what a successful verification yields (session model).
- `Q-BE-002` (note ownership) does not block this module.
<!-- /SECTION: backend.auth.frontmatter -->
<!-- SECTION: backend.auth.purpose | status: DERIVED -->
## Purpose

**Responsible for** (module map: `backend.module-map`; US-006-06; BR-020 to BR-033; NFR-015 to NFR-019):
- Issuing a 6-digit numeric one-time login code for an email address (BR-029) and emailing it (NFR-017).
- Expiring each code 10 minutes after issue (BR-020, BR-024, BR-026).
- Limiting code requests to 3 within 10 minutes per email, answering 429 beyond that (BR-021, BR-028, BR-032).
- Verifying a submitted code. A code is single use (BR-023). Wrong, expired and already-used codes are told apart by the status mapping in BR-033.
- Locking verification for 15 minutes after 5 wrong codes (BR-022, BR-025, BR-027). During a lock only verification is refused; requesting a code is not (BR-032).
- Keeping code challenges, send attempts and account locks, and purging them after their retention period (NFR-019, read per `ASM-BE-007`).
- Keeping codes and unmasked email local parts out of logs (NFR-018).

**Not responsible for:**
- **The credential or session issued after a successful verification.** No input defines it (`Q-BE-003`). This module records the verification outcome only.
- **Who is eligible to request or receive a code.** BR-030 is garbled and open (`Q-BE-001`). `ASM-BE-111` is the working default.
- **Note ownership and per-note access rules** (BR-031). These belong to `notes` and `search` and are pending `Q-BE-002`.
- **Existing login methods.** None is described by any input (`GAP-BE-008`).
- **The web_app login and code-entry screens.** They belong to a separate surface (US-006-06).
- **Email delivery infrastructure.** It is an external service in AWS eu-west-1 (NFR-017).
- **Publishing the OpenAPI document and the coverage check.** `contract-docs` owns these.
- **Refusing out-of-scope calls.** `scope-guard` owns this.
- **Residency scanning.** `residency` owns it.
- **Defining schemas, error envelopes or error-code names.** Those live in `backend.error-model` and the operation records, and are referenced here.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Table names, column names and key types are proposals (`ASM-BE-119`). The records themselves are the three named by NFR-019. The database is PostgreSQL 16 (`platform.md`). Every timestamp is `timestamptz` and stored in UTC. The email key is normalised per `ASM-BE-116`.

### `login_code_challenges`

| Column | Type | Null | Default | Notes |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | normalised address; PII |
| `code_secret` | text | NOT NULL | none | the code, 6 numeric digits (BR-029). Storage form unresolved (`GAP-BE-012`); the column is a placeholder. |
| `issued_at` | timestamptz | NOT NULL | `now()` | |
| `expires_at` | timestamptz | NOT NULL | none | `issued_at` + 10 minutes, set at issue (BR-020, BR-024) |
| `state` | text | NOT NULL | `'ACTIVE'` | one of `ACTIVE`, `USED`, `SUPERSEDED` |
| `used_at` | timestamptz | NULL | NULL | non-null if and only if `state = 'USED'` |

Constraints:
- `CHECK (expires_at > issued_at)`.
- `CHECK (state IN ('ACTIVE','USED','SUPERSEDED'))`.
- `CHECK ((state = 'USED') = (used_at IS NOT NULL))`.
- `CHECK (code_secret` is exactly 6 numeric digits`)`, enforced at the application layer if `code_secret` is stored in a non-readable form (`GAP-BE-012`).
- Partial unique index on `(email) WHERE state = 'ACTIVE'`. It enforces one active code per email (`INV-BE-108`).

Indexes:
- `(email, issued_at DESC)`. It serves the lookup of the latest challenges for an email on verify.
- `(issued_at)`. It serves the retention sweep.

### `login_send_attempts`

| Column | Type | Null | Default | Notes |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | normalised; PII |
| `requested_at` | timestamptz | NOT NULL | `now()` | |
| `challenge_id` | uuid | NULL | NULL | FK to `login_code_challenges(id)`, `ON DELETE SET NULL` |

- Only accepted requests are recorded (`ASM-BE-112`).
- Index `(email, requested_at)`. It serves the count of accepted requests in the last 10 minutes.
- Index `(requested_at)`. It serves the retention sweep.

### `account_locks`

| Column | Type | Null | Default | Notes |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key; normalised; PII |
| `wrong_code_count` | smallint | NOT NULL | 0 | `CHECK (wrong_code_count BETWEEN 0 AND 5)` |
| `locked_until` | timestamptz | NULL | NULL | non-null while a lock has been set and not yet reset |
| `created_at` | timestamptz | NOT NULL | `now()` | |
| `updated_at` | timestamptz | NOT NULL | `now()` | set on every change |

- `CHECK (wrong_code_count < 5 OR locked_until IS NOT NULL)` (`INV-BE-104`).
- A row is created on the first wrong code for an email. An absent row means count 0 and not locked.
- Index `(updated_at)`. It serves the retention sweep.
- The primary key serves the per-request lookup.

### Retention

Per `ASM-BE-007`: challenges 1 day from `issued_at`, send attempts 1 day from `requested_at`, locks 5 days from `updated_at`. The sweep is specified in `backend.auth.jobs`.

### PII and sensitivity

- `email` is PII in all three tables.
- `code_secret` is a credential-grade value.
- At-rest treatment of both is unstated (`GAP-BE-006`) and not asserted here.
- Every example uses a masked address such as `j***@example.com`.
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

### Challenge (`login_code_challenges.state`)

States: `ACTIVE`, `USED`, `SUPERSEDED`. Expiry is derived, not stored: an `ACTIVE` challenge with `now >= expires_at` is *expired* (`ASM-BE-005`).

| From | To | Trigger | Guard |
|---|---|---|---|
| (none) | `ACTIVE` | accepted `API-AUTH-015365` | valid email; fewer than 3 accepted requests for the email in the last 10 minutes; the email was handed to the delivery service (`ASM-BE-117`) |
| `ACTIVE` | `SUPERSEDED` | a later accepted `API-AUTH-015365` for the same email | same transaction as the new challenge's creation (`ASM-BE-113`) |
| `ACTIVE` | `USED` | successful `API-AUTH-592570` | email not locked; submitted code matches; `now < expires_at` |

**Terminal states:**
- `USED` and `SUPERSEDED` are terminal.
- An `ACTIVE` challenge past `expires_at` can never become `USED`, so it is effectively terminal (`INV-BE-107`).

**Illegal or non-transitioning submissions** (no challenge changes state):

| Submission | Outcome | Basis |
|---|---|---|
| matches an expired `ACTIVE` challenge | 410 | BR-033 |
| matches the most recent `USED` challenge and matches no `ACTIVE` one | 409 | BR-023, BR-033 |
| matches nothing, or matches only a `SUPERSEDED` one | 401, counted as wrong | BR-033, `ASM-BE-118` |

### Lock (per email, `account_locks`)

States: `OPEN` (count 0 to 4, `locked_until` unset or past) and `LOCKED` (`locked_until` in the future).

| From | To | Trigger | Guard |
|---|---|---|---|
| `OPEN` | `OPEN` | wrong code (401) | new count < 5 |
| `OPEN` | `LOCKED` | wrong code | new count reaches 5. `locked_until = now + 15 minutes`; this fifth response is still 401 (`ASM-BE-115`) |
| `OPEN` | `OPEN` (count 0) | successful verify | |
| `LOCKED` | `LOCKED` | any `API-AUTH-592570` | `now < locked_until`; answered 423, nothing changes |
| `LOCKED` | `OPEN` (count 0, `locked_until` cleared) | next verify or the sweep | `now >= locked_until` (`ASM-BE-114`) |

- Neither lock state is terminal.
- `API-AUTH-015365` neither reads nor changes this state (BR-032, `INV-BE-109`).
- Expired-code and used-code submissions do not move the count (`ASM-BE-008`).
<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Error bodies, envelope and machine-readable code names come from `backend.error-model`. They are not restated here. Retry hints and remaining lock time are open (`GAP-BE-004`).

**BE-AUTH-FR-001. Issue a code.**
- Given a valid email with fewer than 3 accepted requests in the last 10 minutes,
- When `API-AUTH-015365` is called,
- Then a 6-digit all-numeric code (leading zeros preserved) is generated,
  - a challenge is stored `ACTIVE` with `expires_at = issued_at + 10 minutes`,
  - any earlier `ACTIVE` challenge for that email becomes `SUPERSEDED`,
  - one send attempt is recorded,
  - the code is emailed,
  - and the success status is the one in the operation record.
- Traces to. US-006-06; BR-020, BR-024, BR-026, BR-029; NFR-015.
- Verification. `auth.request.issues_six_digit_code` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-002. Resend limit.**
- Given an email with 3 accepted requests in the last 10 minutes (rolling window, `ASM-BE-004`),
- When `API-AUTH-015365` is called again,
- Then the response is 429,
  - no challenge is created,
  - nothing is emailed,
  - and nothing is recorded (`ASM-BE-112`).
- Traces to. US-006-06 (first criterion); BR-021, BR-028, BR-032.
- Verification. `auth.request.fourth_request_in_window_returns_429` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-003. Request during a lock.**
- Given an email whose verification is locked and which is under the resend limit,
- When `API-AUTH-015365` is called,
- Then it is processed as in FR-001,
  - and the lock record is unchanged.
- Traces to. BR-032.
- Verification. `auth.request.allowed_while_locked` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-004. Request validation.**
- Given a missing, empty or invalid email (validity rules: `GAP-BE-124`),
- When `API-AUTH-015365` is called,
- Then the response is 400,
  - and no challenge or attempt is recorded.
- Traces to. US-004-03 (400 documented for validation errors); US-006-06.
- Verification. `auth.request.invalid_email_returns_400` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-005. Delivery.**
- Given an accepted request,
- When the code email is sent,
- Then it goes through the transactional email service in AWS eu-west-1 (NFR-017).
- Given the service fails to accept the email,
- When the request is processed,
- Then the response is 500,
  - no `ACTIVE` challenge from this call remains,
  - no attempt is recorded (`ASM-BE-117`),
  - and no automatic retry is made.
- The clause of NFR-017 on transfers outside the EU is garbled and is not asserted (`GAP-BE-005`).
- Traces to. NFR-017; US-006-06.
- Verification. `auth.request.delivery_failure_leaves_no_active_challenge` (planned).
- API. `API-AUTH-015365`.

**BE-AUTH-FR-006. Verify a correct code.**
- Given an email that is not locked and has an `ACTIVE` challenge with `now < expires_at`,
- When `API-AUTH-592570` is called with the matching code,
- Then the challenge becomes `USED` with `used_at` set,
  - the wrong-code count resets to 0,
  - and the success status is the one in the operation record.
- What credential accompanies success is undecided (`Q-BE-003`). It is not specified here.
- Traces to. US-006-06; BR-020, BR-023.
- Verification. `auth.verify.correct_code_consumes_challenge` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-007. Wrong code.**
- Given an email that is not locked,
- When `API-AUTH-592570` is called with a well-formed code that matches no `ACTIVE` challenge and no most-recent `USED` challenge for that email (`ASM-BE-118`),
- Then the response is 401,
  - and the wrong-code count increases by 1.
- When the count reaches 5, `locked_until` is set to now plus 15 minutes. That fifth response is still 401 (`ASM-BE-115`).
- An email with no records is counted the same way (`ASM-BE-118`).
- Traces to. US-006-06 (third criterion); BR-022, BR-025, BR-027, BR-033.
- Verification. `auth.verify.fifth_wrong_code_locks_for_15_minutes` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-008. Locked.**
- Given `locked_until > now` for the email,
- When `API-AUTH-592570` is called with any code, including a correct one,
- Then the response is 423,
  - the count is unchanged,
  - and no challenge is consumed.
- Traces to. US-006-06 (third criterion); BR-022, BR-032, BR-033.
- Verification. `auth.verify.locked_refuses_even_correct_code` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-009. Expired.**
- Given an email that is not locked and an `ACTIVE` challenge with `now >= expires_at` (a code at exactly 10 minutes is expired, `ASM-BE-005`),
- When `API-AUTH-592570` is called with the matching code,
- Then the response is 410,
  - the challenge is not consumed,
  - and the count is unchanged (`ASM-BE-008`).
- Traces to. US-006-06 (second criterion); BR-020, BR-024, BR-026, BR-033.
- Verification. `auth.verify.expired_code_returns_410_at_boundary` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-010. Already used.**
- Given an email that is not locked, a code that matches the most recent `USED` challenge, and no matching `ACTIVE` challenge (`ASM-BE-118`),
- When `API-AUTH-592570` is called,
- Then the response is 409,
  - and the count is unchanged (`ASM-BE-008`).
- Traces to. BR-023, BR-033.
- Verification. `auth.verify.reused_code_returns_409` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-011. Lock expiry.**
- Given `locked_until <= now` (the exact instant counts as unlocked),
- When `API-AUTH-592570` is called,
- Then the lock is cleared and the count reset to 0 before the code is evaluated (`ASM-BE-114`),
  - and the submission is then handled by FR-006 to FR-010.
- Traces to. BR-022, BR-025.
- Verification. `auth.verify.lock_clears_after_15_minutes` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-012. Verify validation.**
- Given a missing email, a missing code, or a code that is not exactly 6 numeric digits,
- When `API-AUTH-592570` is called,
- Then the response is 400,
  - and the count is unchanged (`ASM-BE-120`).
- Traces to. BR-029; US-004-03.
- Verification. `auth.verify.malformed_input_returns_400_uncounted` (planned).
- API. `API-AUTH-592570`.

**BE-AUTH-FR-013. Concurrency.**
- Given concurrent calls for the same email,
- When they are processed,
- Then each operation runs serialised per email in a transaction (`ASM-BE-121`),
  - of two simultaneous correct verifications exactly one succeeds (BR-023),
  - every concurrent wrong code is counted,
  - and the accepted-request count never exceeds 3 in a window (`INV-BE-103`).
- Traces to. BR-021, BR-022, BR-023.
- Verification. `auth.concurrency.single_use_and_limit_hold` (planned).
- API. `API-AUTH-015365`, `API-AUTH-592570`.

**BE-AUTH-FR-014. Logging.**
- Given any call to either operation,
- When the module logs,
- Then no code value appears in any log entry,
  - and any email in a log has its local part masked.
- Traces to. NFR-018.
- Verification. `auth.logging.no_codes_no_unmasked_email` (planned).
- API. `API-AUTH-015365`, `API-AUTH-592570`.

**BE-AUTH-FR-015. Retention.**
- Given a record older than its retention period (`ASM-BE-007`),
- When the sweep runs,
- Then the record is deleted.
- Traces to. NFR-019.
- Verification. `auth.jobs.sweep_deletes_expired_records` (planned).
- API. none.
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-015365` | POST | `/auth/request-code` | `auth` | 202 | 400, 429, 500 | Request an email one-time login code: 6 digits, expires 10 minutes after issue, limited to 3 requests per 10 minutes per email (429 beyond that). Allowed even while verification is locked (BR-032). |
| `API-AUTH-592570` | POST | `/auth/verify-code` | `auth` | 200 | 400, 401, 409, 410, 423, 500 | Verify an emailed one-time code. Errors: wrong code 401, already used 409, expired 410, locked after 5 wrong codes for 15 minutes 423 (BR-033). |

### `API-AUTH-015365` — POST `/auth/request-code`

Request an email one-time login code: 6 digits, expires 10 minutes after issue, limited to 3 requests per 10 minutes per email (429 beyond that). Allowed even while verification is locked (BR-032).

**Request:** 
- `email` (string, required): Email address that will receive the 6-digit one-time login code. PII: masked in logs and examples.
**Response:** 
- `message` (string): Confirmation that the code request was accepted and a code email is being sent. Does not contain the code.

### `API-AUTH-592570` — POST `/auth/verify-code`

Verify an emailed one-time code. Errors: wrong code 401, already used 409, expired 410, locked after 5 wrong codes for 15 minutes 423 (BR-033).

**Request:** 
- `email` (string, required): Email address the code was issued to. PII: masked in logs and examples.
- `code` (string, required): The 6-digit numeric one-time code received by email. Single use; never logged.
**Response:** 
- `message` (string): Confirmation that the code was valid and login succeeded. The session credential (type, lifetime, transport) is undecided pending Q-BE-003, so no credential field is asserted.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

- **INV-BE-101.** A challenge reaches `USED` at most once, and a `USED` challenge is never consumed again (BR-023).
- **INV-BE-102.** No verification succeeds while the email's `locked_until` is in the future (BR-022).
- **INV-BE-103.** For any email, accepted send attempts in any rolling 10-minute window number at most 3 (BR-021, BR-028).
- **INV-BE-104.** `wrong_code_count` is between 0 and 5. A count of 5 always has a non-null `locked_until`, set to 15 minutes after the fifth wrong code (BR-022, BR-025).
- **INV-BE-105.** No log entry contains a code value or an unmasked email local part (NFR-018).
- **INV-BE-106.** Every issued code is exactly 6 numeric digits (BR-029).
- **INV-BE-107.** No challenge becomes `USED` at or after its `expires_at` (BR-020, `ASM-BE-005`).
- **INV-BE-108.** At most one `ACTIVE` challenge exists per email (`ASM-BE-113`).
- **INV-BE-109.** `API-AUTH-015365` never reads or modifies `account_locks` (BR-032).
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The roles, the permission matrix and the ownership predicate for these operations cannot be written.

What would unblock it:
1. `Q-BE-001`. BR-030 is garbled and open. Which email addresses may request, verify and receive a code? Until it is answered, `ASM-BE-111` is only a working default and no permission rule is asserted.
2. `Q-BE-003`. The session model is undecided. No input says whether either operation requires an existing session, nor what a successful verification grants.
3. The project-level `backend.auth-model` is itself `BLOCKED` on the same questions. This section follows it once it is unblocked.
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

### Retention sweep

- **Purpose.** Meets NFR-019 and supports `BE-AUTH-FR-015`.
- **Work.**
  - Delete `login_code_challenges` where `issued_at` is older than 1 day.
  - Delete `login_send_attempts` where `requested_at` is older than 1 day.
  - Delete `account_locks` where `updated_at` is older than 5 days.
  - Periods are per `ASM-BE-007`.
- **Guard.** A lock row with `locked_until > now` is never deleted. A challenge that is `ACTIVE` and unexpired is never deleted.
- **Idempotency.** It is delete-by-predicate, so a re-run changes nothing further.
- **Cadence and maximum deletion lag.** Unstated (`GAP-BE-123`). No value is invented.
- **Retries.** A failed sweep is re-run on the next scheduled run. No retry policy is stated by any input.

### Lock expiry

There is no job. A lock clears lazily on the next verify (`BE-AUTH-FR-011`) or when the sweep removes the row.

### Idempotency of the operations

- `API-AUTH-015365` is deliberately not idempotent: each accepted call issues a new code and counts toward the limit of 3.
- `API-AUTH-592570` repeated with the same correct code succeeds once, then returns 409 (BR-023).
- No client-supplied idempotency key is defined by any input, and none is introduced.
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

The test framework and the email service identity are unnamed by the inputs (`GAP-BE-125`). Plans name behaviours, not tools. Time-dependent behaviour assumes an injectable clock.

### Unit

- Code generation: always 6 numeric digits, leading zeros preserved (`BE-AUTH-FR-001`, `INV-BE-106`).
- Expiry boundary: 9m59s accepted, exactly 10m expired (`BE-AUTH-FR-009`, `INV-BE-107`).
- Rolling-window counter: the 4th request inside 10 minutes is refused, and one made after the window slides is accepted (`BE-AUTH-FR-002`).
- Lock counter: increments on a wrong code only, locks at 5, the fifth response stays 401, resets on success and at `locked_until` (`BE-AUTH-FR-007`, `BE-AUTH-FR-011`).
- Match order: `ACTIVE` before `USED`, superseded and unknown codes are wrong (`ASM-BE-118`).
- Email normalisation and masking (`ASM-BE-116`, `BE-AUTH-FR-014`).

### Integration (against PostgreSQL 16)

- Full flow: request, verify, then verify again returns 409 (`BE-AUTH-FR-006`, `BE-AUTH-FR-010`).
- The 5-wrong-code lock, a correct code during the lock returning 423, request-code still accepted during the lock, and recovery after 15 minutes (`BE-AUTH-FR-003`, `BE-AUTH-FR-008`, `BE-AUTH-FR-011`).
- A second accepted request supersedes the first challenge, with a single `ACTIVE` row enforced by the partial unique index (`INV-BE-108`).
- Delivery failure leaves no `ACTIVE` challenge and no attempt row (`BE-AUTH-FR-005`).
- Concurrency: simultaneous correct verifications give exactly one success, and simultaneous requests never exceed 3 (`BE-AUTH-FR-013`).
- Retention sweep deletes only aged rows and never an active lock (`BE-AUTH-FR-015`).
- Log capture shows no code and no unmasked email (`BE-AUTH-FR-014`, `INV-BE-105`).

### Contract

- `contracts/openapi.yaml` documents, for `API-AUTH-015365`: 429 and 400, plus the error responses in its record.
- It documents, for `API-AUTH-592570`: 401, 409, 410, 423 and 400 (BR-033, US-004-03).
- The status each FR returns matches the documented status for that operation.
- Neither operation appears in the out-of-scope categories checked by `scope-guard`.
- Spec-versus-route coverage for these two routes is owned by `contract-docs` (NFR-008).
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions and assumptions

**Checks run.** Story to requirement, requirement to requirement, story to design (the design brief repeats acceptance criteria and adds nothing for this module) and design to design. The garbled text of BR-027, BR-030, BR-031, BR-033 and NFR-017 to NFR-019 is a gap (`GAP-BE-005`), not a contradiction. No new `CON-` is raised. `CON-BE-001` (CR record versus BR-021, BR-028, BR-032 on resend) applies, and the requirements values are used.

**No new `Q-` is minted.** The open questions are `Q-BE-001`, `Q-BE-003` (both blocking this module), `Q-BE-004` and `Q-BE-009`. Defaults for the degradable ones are in `ASM-BE-115` and `ASM-BE-008`.

### New gaps

- **GAP-BE-123.** The retention sweep has no cadence or maximum deletion lag. NFR-019's target is the share of aged records deleted, and the "age from" basis for each table is `ASM-BE-007` plus `ASM-BE-122`. Degrades `backend.auth.jobs`. Closing artefact: an NFR statement giving the sweep cadence and permitted lag.
- **GAP-BE-124.** The validity rules for an email address are not stated: format, maximum length, case sensitivity. Degrades `BE-AUTH-FR-004`. Closing artefact: a requirement stating the accepted email form.
- **GAP-BE-125.** The transactional email service is unnamed, and so are its failure modes, its send retries and the test framework. Degrades `BE-AUTH-FR-005` and the test plan. Closing artefact: a platform statement naming the email service and test tooling.

### Open items cited from the project spec

- `GAP-BE-001`: the four web-app handoff files were not supplied.
  - `HANDOFF-WEBAPP-752470` (session model) cannot be answered, because it depends on `Q-BE-003`.
  - `HANDOFF-WEBAPP-733353` (retry-after values, remaining lock time, re-enable rule, 400 detail) cannot be answered, because it depends on `GAP-BE-004`.
  - Two points are answered from requirements alone: requesting a code is allowed during a lock (BR-032), and the lock status is 423 (BR-033).
- `GAP-BE-004`: retry values, remaining lock time and the envelope.
- `GAP-BE-005`: garbled requirement text.
- `GAP-BE-006`: encryption at rest and in transit.
- `GAP-BE-007`: no performance targets for the login operations.
- `GAP-BE-008`: existing login methods unspecified.
- `GAP-BE-012`: how a code is stored.

### Assumptions raised by this module

- **ASM-BE-111.** Any syntactically valid email may request, verify and receive a code. Confirms with `Q-BE-001`.
- **ASM-BE-112.** A request refused with 429 is not recorded. Only accepted requests count toward the 3-per-10-minutes limit.
- **ASM-BE-113.** An accepted request supersedes any earlier `ACTIVE` challenge for the same email. This is not stated by BR-021 or BR-028, and the alternative is that every unexpired code stays valid.
- **ASM-BE-114.** The wrong-code count resets on a successful verification and when the lock has expired, with no time decay. A lock is expired at exactly `locked_until`. While locked the count stays at 5. Refines `ASM-BE-002`.
- **ASM-BE-115.** The fifth wrong code returns 401 and locks. The next attempt returns 423. Default for the degradable boundary question.
- **ASM-BE-116.** Email is trimmed and lower-cased for keying and comparison. Refines `ASM-BE-001`.
- **ASM-BE-117.** A delivery failure returns 500, leaves no `ACTIVE` challenge from that call and no recorded attempt, and is not retried.
- **ASM-BE-118.** A submitted code is matched against the `ACTIVE` challenge first, then the most recent `USED` challenge. A superseded or unknown code is wrong (401), and an email with no records still counts toward the lock. Because the match runs against the latest `ACTIVE` challenge, a previous expired code replaced by a new request reads as wrong rather than expired.
- **ASM-BE-119.** Table names, column names and key types are proposals, because no input names them.
- **ASM-BE-120.** A code that is not exactly 6 numeric digits is a 400 validation failure and is not counted as a wrong code.
- **ASM-BE-121.** Operations are serialised per email inside a transaction. The mechanism is not specified.
- **ASM-BE-122.** Retention age is measured from `issued_at` (challenges), `requested_at` (attempts) and `updated_at` (locks). Refines `ASM-BE-007`.

### Assumptions cited from the project spec

`ASM-BE-001`, `ASM-BE-002`, `ASM-BE-004`, `ASM-BE-005`, `ASM-BE-006`, `ASM-BE-007`, `ASM-BE-008`.
<!-- /SECTION: backend.auth.gaps -->
