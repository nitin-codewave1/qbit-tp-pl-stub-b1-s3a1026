---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: []
commit_kind: append
confidence: agent
run_id: a9e25ecd-81f4-5040-b472-7fa3c16d60ab
okf_id: notes_api:backend/modules/auth-SPEC.md
---

<!-- SECTION: backend.auth.frontmatter | status: DERIVED -->
```yaml
id: auth
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-003, Q-BE-005, Q-BE-012, Q-BE-013, CON-BE-003, CON-BE-006, CON-BE-010, CON-BE-020]
scope: [API-AUTH-707260, API-AUTH-692907]
```
<!-- /SECTION: backend.auth.frontmatter -->
<!-- SECTION: backend.auth.purpose | status: DRAFT-UNVERIFIED -->
## 1. Purpose

**Responsibility.** The `auth` module provides email one-time-code sign-in, added by CR-125. It is an additional sign-in method. The existing authentication is unchanged. [feedback.md CR-125; requirements.md NFR-015, NFR-016] The module owns:

1. Issuing a one-time code for an email address and sending it through the email transport. Operation `API-AUTH-707260`. [stories.md US-004-01, US-006-06; requirements.md BR-029]
2. Limiting code requests to 3 per 10 minutes per email address. Requests beyond the limit are refused and send no code. [requirements.md BR-021, BR-028; feedback.md preflight answers]
3. Verifying a code. A code expires 10 minutes after issue and is single-use. Wrong codes are counted. Five wrong codes lock the email address for 15 minutes. [requirements.md BR-020, BR-022 to BR-027; feedback.md preflight answers] Operation `API-AUTH-692907`.
4. Creating the User on first successful verification. [INV-BE-003; ASM-BE-025]
5. Issuing a credential on successful verification, through the existing session or token mechanism. [ASM-BE-028] The form of the credential is `Q-BE-012`. Its lifetime is `Q-BE-013`.
6. Owning the User, OTP_CHALLENGE, SEND_ATTEMPT and LOGIN_LOCK data, as the project module map states. [backend.module-map]

**Not responsible for.**

- The existing authentication method and its checks on protected routes. No input names it. `GAP-BE-085`.
- Authorisation of note, tag, search or other protected operations. Those belong to `notes` and `search`. The note ownership predicate is `BLOCKED` on `CON-BE-003`.
- Delivery of the email. The module hands the message to the transport. The transport is `ASM-BE-027`. Its region is `Q-BE-005` and `CON-BE-006`. Delivery failure is `GAP-BE-066`.
- The scope guardrail, which runs before routing. [backend.module-map]
- The OpenAPI document (`api-docs`) and the global error envelope (`backend.error-model`).
- Sign-in screens and their messages. Those belong to the web-app surface. [stories.md US-006-06]
- Scheduled cleanup and retention of expired rows. `GAP-BE-101`.
- Logging and redaction of email addresses and codes. `GAP-BE-104`.

**Operations owned.** `API-AUTH-707260` (POST `/auth/otp/send`) and `API-AUTH-692907` (POST `/auth/otp/verify`). Both are referred to by ID only. Their contracts are rendered by DevAgent in `backend.auth.api-contract`.

**Identifier note.** The reserved ranges for `ASM-`, `CON-`, `GAP-`, `INV-` and `Q-` begin at 001. The project spec already uses those numbers. This module therefore mints from the next free number above the project spec's highest observed id, as core rule 5.2 requires. The collision is recorded as `GAP-BE-108`.

<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, nullability or constraints for this module. The platform is PostgreSQL 16. [platform.md] The types below are the module's proposal, recorded as `ASM-BE-047`. Column names come from the project domain model. [backend.domain-model]

**USER**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `user_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | unique; normalised form (`ASM-BE-015`); uniqueness is assumed (`GAP-BE-115`) |
| `created_at` | timestamptz | NOT NULL | none | set on first successful verification only (INV-BE-003) |

**OTP_CHALLENGE**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `challenge_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | normalised; no foreign key to USER; keyed by email because the user may not yet exist |
| `code_digest` | text | NOT NULL | none | stored form undecided (`GAP-BE-033`); the code is 6 numeric digits (BR-029) |
| `issued_at` | timestamptz | NOT NULL | none | set at issue |
| `expires_at` | timestamptz | NOT NULL | none | `issued_at` plus 10 minutes (BR-024); stored rather than derived so a later change of period does not rewrite history |
| `failed_attempts` | integer | NOT NULL | 0 | `CHECK (failed_attempts >= 0)`; placement depends on `CON-BE-010`: under a cumulative per-email reading this count moves to a per-email counter |
| `consumed_at` | timestamptz | NULL | none | set on success; `CHECK (consumed_at IS NULL OR consumed_at >= issued_at)` (INV-BE-002) |

Index: btree on `(email, issued_at DESC)`. Serves the lookup of the latest challenge at verification.

**SEND_ATTEMPT**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `send_attempt_id` | bigint identity | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | normalised (`ASM-BE-015`) |
| `requested_at` | timestamptz | NOT NULL | none | the request instant |

Index: btree on `(email, requested_at)`. Serves the count of attempts in the trailing 10 minutes. The column set is derived, not supplied (`ASM-BE-037`). Whether refused requests write a row is `GAP-BE-109`. Whether the first send counts is `CON-BE-011`.

**LOGIN_LOCK**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key; normalised (`ASM-BE-015`) |
| `locked_until` | timestamptz | NOT NULL | none | entry instant plus 15 minutes (BR-022, BR-025); written only at lock entry (INV-BE-008) |

No row means unlocked. A row whose `locked_until` has passed is unlocked. Rows are not deleted by this module. `GAP-BE-101`.

**Referential integrity.** This module declares no foreign keys. The link between OTP_CHALLENGE and USER is by email value. [backend.domain-model] No `ON DELETE` behaviour applies within the module.

**Time.** All instants are `timestamptz`. The time zone of display is not specified. Expiry and lock comparisons are made on the instant.

**Encryption and PII.** Email addresses and `code_digest` are personal or sensitive. No encryption at rest is stated. `GAP-BE-033`. No claim is made.

<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

Three state machines operate on each normalised email address. A fourth, the resend limit, is a count and not a state.

### 3.1 OTP_CHALLENGE

**States.** `ISSUED` (unconsumed, not yet expired). `CONSUMED` (terminal, success). `EXPIRED` (terminal, derived by time: the current instant is at or after `expires_at`). The row is retained and no sweep is specified. `GAP-BE-101`.

A `SUPERSEDED` state is not included. Whether a resend invalidates an earlier code is `GAP-BE-083`.

| From | To | Trigger | Guard | Effect | Status |
|---|---|---|---|---|---|
| none | `ISSUED` | send accepted | resend count below 3 (BR-028) | row created; `expires_at` set; SEND_ATTEMPT written | 200 assumed (`ASM-BE-046`; `GAP-BE-028`) |
| `ISSUED` | `ISSUED` | wrong code | not locked; not expired; not consumed | `failed_attempts` incremented | `GAP-BE-053` |
| `ISSUED` | `CONSUMED` | correct code | not locked; not expired; not consumed | `consumed_at` set; User created if absent; credential issued | 200 assumed (`ASM-BE-045`); credential `BLOCKED` (`Q-BE-012`) |
| `ISSUED` | `EXPIRED` | time passes | now at or after `expires_at` | none (derived) | `GAP-BE-053`; boundary `BLOCKED` (`CON-BE-020`) |
| `CONSUMED` | none | any verify | terminal | refused | `GAP-BE-053` |

**Terminal states.** `CONSUMED` and `EXPIRED`. A challenge in either state never returns to `ISSUED`.

### 3.2 LOGIN_LOCK (per normalised email)

**States.** `UNLOCKED` (no row, or `locked_until` has passed). `LOCKED` (`locked_until` is in the future). Neither is terminal. Lock expiry requires no write.

| From | To | Trigger | Guard | Effect | Status |
|---|---|---|---|---|---|
| `UNLOCKED` | `LOCKED` | the fifth counted wrong code | counting rule `CON-BE-010` | `locked_until` = entry plus 15 minutes | `BLOCKED` on `CON-BE-010` (Critical) |
| `LOCKED` | `UNLOCKED` | time passes | now at or after `locked_until` | none (derived) | none |
| `LOCKED` | `LOCKED` | any verify attempt, including a correct code | `locked_until` in the future | refused; `locked_until` unchanged (INV-BE-005, INV-BE-008) | `GAP-BE-038` |
| `LOCKED` | `LOCKED` | send request | resend count below 3 | new challenge issued (Q-BE-007 default) | `DRAFT-UNVERIFIED` |

**Counter behaviour under each reading of CON-BE-010.** Under the consecutive reading, a successful verification resets the wrong-code count. Under the cumulative reading, it does not. Neither reading is applied until `CON-BE-010` is answered. Whether a locked attempt changes the count is `GAP-BE-111`.

### 3.3 Resend limiter

Not a stored state. A send request is accepted when fewer than 3 SEND_ATTEMPT rows for the normalised email fall within the trailing 10 minutes. [BR-028; US-006-06] The window type is `ASM-BE-044`. The 415 status is `GAP-BE-100`.

**Interaction derived from the sources.** A lock is entered on a challenge that is already at most 10 minutes old, and the lock lasts 15 minutes. That challenge therefore expires before the lock lifts. A user who wants to sign in after the lock must request a new code, which is subject to the resend limit. A code requested during the lock, after `locked_until` less 10 minutes, remains usable after the lock lifts. This is derived from BR-022, BR-024 and the Q-BE-007 default. [requirements.md; feedback.md]

### 3.4 Illegal and refused transitions

| Attempt | Result | Status |
|---|---|---|
| verify with no challenge for the email | rejected | `GAP-BE-065`, `GAP-BE-053` |
| verify against a `CONSUMED` challenge | rejected (INV-BE-002) | `GAP-BE-053` |
| verify while `LOCKED` | refused (INV-BE-005) | `GAP-BE-038` |
| send beyond the resend limit | refused, no code sent (INV-BE-006) | `GAP-BE-100` |

**Order of refusal reasons.** When an attempt meets more than one refusal condition (for example, locked and expired), no input states which applies first. `GAP-BE-110`.

<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

Each entry carries its traces, its verification and the operation it implements. Entries whose trigger depends on an unresolved Critical contradiction are marked `BLOCKED`. The rest of the module is not held up by them.

### BE-AUTH-FR-001 Issue a one-time code

**Given** an email address that passes validation (`GAP-BE-035`), and whose normalised form has fewer than 3 SEND_ATTEMPT rows in the trailing 10 minutes,
**When** `POST /auth/otp/send` is called with that address,
**Then** a 6-digit numeric code is generated (BR-029); an OTP_CHALLENGE is created with `expires_at` 10 minutes after `issued_at` (BR-020, BR-024); a SEND_ATTEMPT row is written; the code is handed to the email transport (`ASM-BE-027`); and the success response is returned (`ASM-BE-046`).

- **Traces to.** US-006-06; US-004-01; BR-020; BR-024; BR-029; feedback.md preflight answers.
- **Verification.** `test-auth-send-issues-code` (planned).
- **API.** `API-AUTH-707260`.
- **Note.** The transport step is `BLOCKED` on `Q-BE-005` and `CON-BE-006` as to region. The rest of the requirement stands.

### BE-AUTH-FR-002 Enforce the resend limit

**Given** the normalised email has 3 SEND_ATTEMPT rows within the trailing 10 minutes,
**When** `POST /auth/otp/send` is called,
**Then** the request is refused with status 415 (`GAP-BE-100`), no OTP_CHALLENGE is created, and no email is sent (INV-BE-006).

- **Traces to.** BR-021; BR-028; US-004-03; US-006-06.
- **Verification.** `test-auth-send-limit-refused` (planned).
- **API.** `API-AUTH-707260`.
- **Note.** The rule assumes each accepted request counts. Whether the first send counts is `CON-BE-011`. Whether refused requests are recorded is `GAP-BE-109`.

### BE-AUTH-FR-003 Verify a correct code

**Given** an ISSUED challenge for the normalised email, which is not expired, not consumed, and whose code matches, and the email is not `LOCKED`,
**When** `POST /auth/otp/verify` is called,
**Then** `consumed_at` is set; a USER row is created if none exists (INV-BE-003); a credential is issued (`BE-AUTH-FR-012`); and the success response is returned (`ASM-BE-045`).

- **Traces to.** BR-023; INV-BE-003; US-006-06; feedback.md preflight answers.
- **Verification.** `test-auth-verify-success` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-004 Reject an expired code

**Given** a challenge issued at instant T,
**When** `POST /auth/otp/verify` is called with its correct code at instant t, where t is after T plus 10 minutes,
**Then** the request is rejected as expired and the code cannot be used (BR-024, BR-026; US-006-06).

- **Boundary.** The instant t equal to T plus 10 minutes is `BLOCKED` on `CON-BE-020` (Critical). BR-024 and US-006-06 read the boundary as strictly after. INV-BE-004 reads it as at or after. The module does not choose.
- **Traces to.** BR-020; BR-024; BR-026; US-006-06; feedback.md preflight answers.
- **Verification.** `test-auth-verify-expired` (planned, with clock injection). `test-auth-verify-expiry-boundary` (planned, after `CON-BE-020` is decided).
- **API.** `API-AUTH-692907`.
- **Status.** `BLOCKED` at the boundary. `DRAFT-UNVERIFIED` elsewhere.

### BE-AUTH-FR-005 Refuse a reused code

**Given** a challenge that was verified successfully,
**When** `POST /auth/otp/verify` is called again with the same code,
**Then** the request is rejected and no credential is issued (INV-BE-002).

- **Traces to.** BR-023.
- **Verification.** `test-auth-verify-single-use` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-006 Count and reject a wrong code

**Given** an ISSUED challenge that is not expired, and an incorrect code,
**When** `POST /auth/otp/verify` is called,
**Then** the request is rejected as wrong, and the wrong-code count is incremented under the reading applied by `CON-BE-010`.

- **Traces to.** BR-022; BR-025; BR-027; feedback.md preflight answers.
- **Verification.** `test-auth-verify-wrong-counts` (planned).
- **API.** `API-AUTH-692907`.
- **Note.** The response status and body are `GAP-BE-053`. The count's placement depends on `CON-BE-010`.

### BE-AUTH-FR-007 Lock after the wrong-code threshold

**Given** the wrong-code count reaches the threshold of 5 under the rule in `CON-BE-010`,
**When** the fifth counted wrong code is submitted,
**Then** the email is locked and `locked_until` is set to the entry instant plus 15 minutes (BR-022, BR-025).

- **Status.** `BLOCKED` on `CON-BE-010` (Critical). Also `BLOCKED` on the response shape (`GAP-BE-038`).
- **Traces to.** BR-022; BR-025; BR-027; feedback.md preflight answers.
- **Verification.** `test-auth-lock-entry` (planned, after `CON-BE-010` is decided).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-008 Refuse every verification while locked

**Given** the email's `locked_until` is in the future,
**When** `POST /auth/otp/verify` is called with any code, including a correct one,
**Then** the request is refused with the locked-account response. No USER row is created, no credential is issued, and `locked_until` is not changed (INV-BE-005, INV-BE-007, INV-BE-008).

- **Traces to.** BR-022; BR-027; US-004-03; US-006-06.
- **Verification.** `test-auth-verify-locked-correct-refused` (planned).
- **API.** `API-AUTH-692907`.
- **Note.** The status and body are `GAP-BE-038`.

### BE-AUTH-FR-009 Lift the lock on time

**Given** `locked_until` is at or before the current instant,
**When** `POST /auth/otp/verify` is called,
**Then** the lock no longer applies and the request is evaluated under BE-AUTH-FR-003 to BE-AUTH-FR-006.

- **Traces to.** BR-022 ("until the lock expires").
- **Verification.** `test-auth-lock-expiry` (planned, with clock injection).
- **API.** `API-AUTH-692907`.
- **Derived consequence.** The challenge that triggered the lock has expired by the time the lock lifts (`backend.auth.state-machine`, 3.3). A new code is needed.

### BE-AUTH-FR-010 Send a code during a lock

**Given** the email is `LOCKED`, and the resend count is below 3,
**When** `POST /auth/otp/send` is called,
**Then** a code is issued and sent, and the resend limit still applies.

- **Traces to.** Q-BE-007 (degradable; default applied); GAP-BE-063.
- **Verification.** `test-auth-send-while-locked` (planned).
- **API.** `API-AUTH-707260`.
- **Status.** `DRAFT-UNVERIFIED`. This is the default in Q-BE-007, not a sourced rule.

### BE-AUTH-FR-011 Handle a verification for an unknown email

**Given** no OTP_CHALLENGE exists for the normalised email,
**When** `POST /auth/otp/verify` is called,
**Then** the request is rejected. No USER row is created and no credential is issued (INV-BE-007).

- **Traces to.** INV-BE-003; GAP-BE-065.
- **Verification.** `test-auth-verify-unknown-email` (planned).
- **API.** `API-AUTH-692907`.
- **Status.** The response status and body are `BLOCKED` on `GAP-BE-065` and `GAP-BE-053`. Whether this attempt counts toward the lock is `GAP-BE-112`.

### BE-AUTH-FR-012 Issue a credential

**Given** a successful verification under BE-AUTH-FR-003,
**When** the credential is issued,
**Then** it is issued through the existing session or token mechanism (`ASM-BE-028`). Its form and its lifetime are not specified.

- **Status.** `BLOCKED` on `Q-BE-012` (form) and `Q-BE-013` (lifetime). The human answer "follow existing conventions" cannot be applied because no convention is supplied.
- **Traces to.** feedback.md CR-125, second preflight question.
- **Verification.** `test-auth-credential-shape` (planned, after `Q-BE-012` and `Q-BE-013` are answered).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-013 Do not reveal whether an account exists

**Given** any email address, registered or not,
**When** `POST /auth/otp/send` is called,
**Then** the response is the same in each case (`ASM-BE-030`).

- **Traces to.** ASM-BE-030; US-004-01.
- **Verification.** `test-auth-send-non-enumeration` (planned, contract).
- **API.** `API-AUTH-707260`.

### BE-AUTH-FR-014 Generate a 6-digit numeric code

**Given** a code is generated,
**When** it is stored and sent,
**Then** it is exactly 6 decimal digits (BR-029). The generation method and any stricter character rule are not specified (`GAP-BE-026`, `GAP-BE-027`).

- **Traces to.** BR-029; feedback.md preflight answers.
- **Verification.** `test-auth-code-shape` (planned, unit).
- **API.** `API-AUTH-707260`.

### BE-AUTH-FR-015 Key the lock and the resend limit by normalised email

**Given** two addresses that normalise to the same value,
**When** a code is requested or a lock is evaluated for either,
**Then** both share one resend count and one lock.

- **Traces to.** ASM-BE-015; ASM-BE-029; GAP-BE-031.
- **Verification.** `test-auth-normalisation-shared-key` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.
- **Note.** BR-022 and BR-025 say "the account is locked". The module keys the lock by email because a User may not exist (domain model). The wording is recorded as a reading, not a requirement change.

<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-707260` | POST | `/auth/otp/send` | `auth` | 200 | 415 | Request a one-time sign-in code by email. Subject to a limit of 3 requests per 10 minutes per email address; requests beyond the limit are refused and send no code (415, status conflict flagged as GAP-BE-100). Success status and response body are not stated by the inputs (200 and empty body are assumed). Whether the first send counts toward the limit is CON-BE-011. |
| `API-AUTH-692907` | POST | `/auth/otp/verify` | `auth` | 200 | — | Verify a one-time code and, on success, issue a credential. Creates the User on first successful verification. Rejects wrong codes, expired codes (10 minutes from issue), codes already consumed, and any attempt while the email is locked (5 wrong codes lock for 15 minutes; counting rule is CON-BE-010, Critical, open). Error statuses are not stated by the inputs for any of these cases (GAP-BE-038, GAP-BE-053), so none are listed. Success status 200 is assumed. Whether verification is restricted to certain users is Q-BE-003. |

### `API-AUTH-707260` — POST `/auth/otp/send`

Request a one-time sign-in code by email. Subject to a limit of 3 requests per 10 minutes per email address; requests beyond the limit are refused and send no code (415, status conflict flagged as GAP-BE-100). Success status and response body are not stated by the inputs (200 and empty body are assumed). Whether the first send counts toward the limit is CON-BE-011.

**Request:** 
- `email` (string, required): Email address to receive the one-time code. Field name is assumed (ASM-BE-042); the spec gives no field names. Validation is not specified (GAP-BE-035).
**Response:** none

### `API-AUTH-692907` — POST `/auth/otp/verify`

Verify a one-time code and, on success, issue a credential. Creates the User on first successful verification. Rejects wrong codes, expired codes (10 minutes from issue), codes already consumed, and any attempt while the email is locked (5 wrong codes lock for 15 minutes; counting rule is CON-BE-010, Critical, open). Error statuses are not stated by the inputs for any of these cases (GAP-BE-038, GAP-BE-053), so none are listed. Success status 200 is assumed. Whether verification is restricted to certain users is Q-BE-003.

**Request:** 
- `email` (string, required): Email address the code was sent to. Field name is assumed (ASM-BE-042).
- `code` (string, required): Six-digit numeric one-time code. Field name is assumed (ASM-BE-042).
**Response:** 
- `credential` (string, required): Credential issued on successful verification. Form (body token, httpOnly cookie or JWT) is unresolved (Q-BE-012); the field name is assumed.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

These hold after every operation that touches the named data. Inherited invariants are cited from the project spec. Module invariants are minted here.

**Inherited from the project spec, applied to this module.**

- **INV-BE-002.** A code verified successfully is refused on any later verification. [requirements.md BR-023]
- **INV-BE-003.** A User row is created only on successful verification, never on code request. [ASM-BE-025]
- **INV-BE-004.** A code submitted at or after issue plus 10 minutes is rejected as expired. **Disputed.** BR-024 and US-006-06 read "after". `CON-BE-020` (Critical).
- **INV-BE-005.** While an email is locked, no verification succeeds, including with a correct code, until the lock expires. [requirements.md BR-022, BR-027]

**Minted in this module.**

- **INV-BE-006.** A send request refused by the resend limit creates no OTP_CHALLENGE and does not call the email transport.
- **INV-BE-007.** No refused verification (wrong, expired, consumed, locked, or unknown email) creates a USER row or issues a credential.
- **INV-BE-008.** `locked_until` is written only at lock entry. No later operation changes it while the lock stands.

**Consequence.** INV-BE-002 requires that a successful single-use consumption is not repeatable under concurrent requests. No input specifies how. `GAP-BE-114`.

<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: DRAFT-UNVERIFIED -->
## 7. Authorisation

**Actors.**

| Operation | Anonymous | Authenticated | Rule |
|---|---|---|---|
| `API-AUTH-707260` (send) | allowed, subject to the resend limit | allowed, same limit | the same limit applies to both (`ASM-BE-029`) |
| `API-AUTH-692907` (verify) | allowed, subject to expiry and lockout | not stated | the only path to a credential for a new user |

Neither operation requires a prior credential. The scope guardrail runs before authentication. [backend.module-map]

**Ownership predicate.** None. These operations have no owning resource beyond the email address in the request.

**Who may receive access.** `Q-BE-003`. Until answered, verification accepts any address that passes it. `CON-BE-003` (Critical) records that under CR-125 "team member" no longer names a bounded group. The permission rule on the verify operation is `BLOCKED` on both.

**Credential scope.** Not specified. `BLOCKED` on `Q-BE-012` and `Q-BE-013`.

<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs, retries and idempotency

**Scheduled work.** None is specified. No input requires a sweep. Expiry and lock lapse are read at comparison time, not written by a job. Deletion of expired challenges, send attempts and lock rows is `GAP-BE-101`.

**Idempotency.**

- **Send is not idempotent.** Each accepted request issues a new code and writes a SEND_ATTEMPT. A client retry after a timeout therefore spends one unit of the resend limit and issues a second code. `GAP-BE-109` and `GAP-BE-028`. No idempotency key is specified; none is mandated.
- **Verify is not idempotent.** A repeat of a correct code after success is refused (INV-BE-002). A client that retries after a lost response will be refused. No idempotency key is specified.

**Retry on transport failure.** If the email transport fails after the challenge and SEND_ATTEMPT are written, the state is not specified. `GAP-BE-066`. Retry policy: not specified.

**Concurrency.** Single-use consumption and wrong-code counting both need serialised updates. No input specifies the mechanism. `GAP-BE-114`.

<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

Clock injection is required for every expiry and lock test. Test names are planned.

**Unit.**

- `test-auth-code-shape`: a generated code is exactly 6 numeric digits (BE-AUTH-FR-014).
- `test-auth-expiry-check`: a challenge is rejected at `expires_at` plus one second, and accepted one second before (BE-AUTH-FR-004; the exact boundary waits on `CON-BE-020`).
- `test-auth-send-window-count`: the count of SEND_ATTEMPT rows in the trailing 10 minutes, at the window edge (BE-AUTH-FR-002; window type `ASM-BE-044`).
- `test-auth-lock-until-arithmetic`: `locked_until` equals entry plus 15 minutes (BE-AUTH-FR-007, after `CON-BE-010`).

**Integration.**

- `test-auth-send-issues-code`, `test-auth-verify-success`: the happy path writes the correct rows (BE-AUTH-FR-001, FR-003).
- `test-auth-verify-single-use`.
- `test-auth-verify-wrong-counts`.
- `test-auth-verify-locked-correct-refused`: a correct code during a lock is refused and changes nothing (INV-BE-005, INV-BE-008).
- `test-auth-lock-expiry`: after `locked_until`, verification proceeds normally (BE-AUTH-FR-009).
- `test-auth-send-while-locked` (Q-BE-007 default).
- `test-auth-verify-unknown-email`.
- `test-auth-normalisation-shared-key`.
- `test-auth-concurrent-verify` (planned, pending `GAP-BE-114`): two simultaneous correct verifications; exactly one succeeds.
- `test-auth-transport-failure` (pending `GAP-BE-066`).

**Contract.**

- `test-auth-send-limit-refused`: 415 on the fourth request (`GAP-BE-100`).
- `test-auth-send-non-enumeration`: identical responses for registered and unregistered addresses (BE-AUTH-FR-013).
- `test-auth-verify-locked-response-shape` and `test-auth-verify-error-shapes` (pending `GAP-BE-038`, `GAP-BE-053`).
- `test-auth-credential-shape` (pending `Q-BE-012`, `Q-BE-013`).

<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: VERIFIED -->
## 10. Gaps, contradictions, assumptions and questions

Every item cites its source. Items already in the project spec are cited, not restated. Each open item names what would close it.

### Project-spec items cited by this module

| ID | Kind | Affects | Closing artefact |
|---|---|---|---|
| CON-BE-010 | CON, Critical | lock entry (BE-AUTH-FR-007); counter placement (data model) | human decision on consecutive or cumulative counting |
| CON-BE-003 | CON, Critical | authorisation (section 7) | human decision on registration scope |
| CON-BE-006 | CON, Critical | transport step (BE-AUTH-FR-001) | human decision on transport region |
| CON-BE-011 | CON | send counting (FR-002; SEND_ATTEMPT) | human decision on whether the first send counts |
| Q-BE-003 | Q, blocking | verify permission | human answer |
| Q-BE-005 | Q, blocking | transport region | human answer |
| Q-BE-007 | Q, degradable (default applied) | BE-AUTH-FR-010 | human confirmation of the default |
| Q-BE-012 | Q, blocking | credential form (FR-012) | human answer |
| GAP-BE-024 | GAP | counting semantics | answer to CON-BE-010 |
| GAP-BE-026, GAP-BE-027 | GAP | code generation method and character rules | written generation rule |
| GAP-BE-028 | GAP | send success status and body | written contract |
| GAP-BE-030 | GAP | credential contract | answer to Q-BE-012 |
| GAP-BE-031 | GAP | email normalisation | written rule |
| GAP-BE-033 | GAP | code digest and at-rest treatment | written storage rule |
| GAP-BE-035 | GAP | validation of email and code fields | written validation rule |
| GAP-BE-038 | GAP | locked-response status and body | written contract |
| GAP-BE-053 | GAP | wrong, expired and consumed statuses | written contract |
| GAP-BE-063 | GAP | sending during a lock | answer to Q-BE-007 |
| GAP-BE-065 | GAP | unknown-email verification | written contract |
| GAP-BE-066 | GAP | transport failure | written failure policy |
| GAP-BE-083 | GAP | whether a resend invalidates an earlier code | human decision |
| GAP-BE-085 | GAP | the existing authentication method (out of module) | naming of the existing method |
| GAP-BE-100 | GAP | the 415 status for the resend limit | human confirmation |
| GAP-BE-101 | GAP | retention and deletion | written retention policy |
| GAP-BE-104 | GAP | logging of email and code | written logging policy |
| GAP-BE-089 | GAP | no ORM or migration tool named | written choice of tooling |
| ASM-BE-015 | ASM | normalised email as key | confirmation with GAP-BE-031 |
| ASM-BE-019 | ASM | email as personal data | confirmation with GAP-BE-033 |
| ASM-BE-025 | ASM | User created only on verify | confirmation with INV-BE-003 |
| ASM-BE-027 | ASM | transport is AWS SES | confirmation with Q-BE-005 |
| ASM-BE-028 | ASM | credential via existing mechanism | answer to Q-BE-012 |
| ASM-BE-029 | ASM | resend key per email | confirmation |
| ASM-BE-030 | ASM | non-enumeration | confirmation |
| ASM-BE-037 | ASM | SEND_ATTEMPT columns derived | confirmation |
| ASM-BE-042 | ASM | operation paths | DevAgent rendering |
| ASM-BE-044 | ASM | sliding or fixed window | human answer |

### Items minted by this module

**GAP-BE-108.** Reserved-block collision. The reserved ranges for `ASM-`, `CON-`, `GAP-`, `INV-` and `Q-` begin at 001 and are already used by the project spec. This module mints from the next free number. **Closing artefact:** orchestrator confirmation of the numbering rule, or a reissued reserved block above the project high-water marks.

**GAP-BE-109.** Refused send requests. Not stated whether a refused request writes a SEND_ATTEMPT row. If it does, the window slides on refusals. **Closing artefact:** written rule.

**GAP-BE-110.** Refusal precedence. Not stated which refusal applies when an attempt is both locked and expired, or both consumed and wrong. **Closing artefact:** written precedence order.

**GAP-BE-111.** Locked attempts. Not stated whether a verify attempt during a lock increments the wrong-code count. **Closing artefact:** answer alongside CON-BE-010.

**GAP-BE-112.** Expired-code attempts. Not stated whether a wrong or expired-code verification counts toward the lock, or whether an unknown-email verification does. **Closing artefact:** answer alongside CON-BE-010.

**GAP-BE-113.** Source-level limits. Lockout is per email. No input states a limit on verify requests per source address. An attacker can therefore spread guesses across addresses, or across the lock cycle. Under the lock rule, one email allows 5 guesses per lock period. **Closing artefact:** written decision on source-level limits.

**GAP-BE-114.** Concurrency. Single-use and counting need serialised updates. No input specifies the mechanism. **Closing artefact:** written concurrency rule.

**GAP-BE-115.** Uniqueness. No input states that one User exists per normalised email. Uniqueness is assumed (`ASM-BE-047`). **Closing artefact:** confirmation.

**GAP-BE-116.** Index identifiers. The project spec's api-surface index lists the two login operations under identifiers that are not the ones DevAgent minted for this module. The index must be reconciled by DevAgent. The superseded identifiers are not used here. **Closing artefact:** DevAgent re-render of the project api-surface section.

**ASM-BE-045.** Verify success returns 200. The caller assumed this. No input states it. **Closing artefact:** `GAP-BE-053` and `GAP-BE-028` closed.

**ASM-BE-046.** Send success returns 200 with an empty body. The caller assumed this. No input states it. **Closing artefact:** `GAP-BE-028` closed.

**ASM-BE-047.** Column types and constraints are PostgreSQL 16 choices made by this module, and `email` is unique. No input states them. **Closing artefact:** confirmation of the data model.

**CON-BE-020.** Expiry boundary. BR-024 and US-006-06 say a code submitted after 10 minutes is rejected. INV-BE-004 says at or after 10 minutes. The two readings differ at one instant, but that instant is the guard of a state transition. **Critical**, by the state-machine test in core rule 8. Blocks BE-AUTH-FR-004 at the boundary. **Closing artefact:** human decision on the boundary.

**Q-BE-013.** Credential lifetime. feedback.md answers the credential form ("follow existing conventions") and the lockout values. It does not answer how long the session lasts. Session lifetime changes the credential's contract. **Blocking.** **Closing artefact:** human answer.

<!-- /SECTION: backend.auth.gaps -->
