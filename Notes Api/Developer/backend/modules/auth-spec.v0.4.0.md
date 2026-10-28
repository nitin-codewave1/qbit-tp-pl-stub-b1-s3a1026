---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-004-03, notes_api:US-006-06]
commit_kind: fork
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: notes_api:backend/modules/auth-SPEC.md
---

<!-- SECTION: backend.auth.frontmatter | status: DERIVED -->
```yaml
id: auth
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-003, Q-BE-005, Q-BE-012, CON-BE-003, CON-BE-006]
scope: [API-AUTH-707260, API-AUTH-692907]
```
<!-- /SECTION: backend.auth.frontmatter -->
<!-- SECTION: backend.auth.purpose | status: DRAFT-UNVERIFIED -->
## 1. Purpose

**Responsibility.** The `auth` module provides email one-time-code sign-in. It is an additional sign-in method, and existing login methods are unchanged. [requirements.md NFR-016; feedback.md CR-129] It owns:

1. Issuing a 6-digit numeric code for an email address and handing it to the email transport (`API-AUTH-707260`). [requirements.md BR-029; stories.md US-006-06]
2. Limiting code requests to 3 within 10 minutes per normalised email address. Requests beyond the limit return 415 and create no code and send no email. [requirements.md BR-021, BR-028, BR-032; stories.md US-004-03]
3. Verifying a code (`API-AUTH-692907`). A code is valid for 10 minutes from issue and is single-use. [requirements.md BR-020, BR-023, BR-024]
4. Counting wrong codes and locking the address for 15 minutes after 5. Verification is refused during the lock. Requesting a code is not refused during the lock, but it stays subject to the resend limit. [requirements.md BR-022, BR-025, BR-027, BR-032]
5. Creating the User on first successful verification (`ASM-BE-025`).
6. Issuing a credential on successful verification. Its form is `Q-BE-012` and its lifetime is `GAP-BE-302`.
7. Owning the data of USER, OTP_CHALLENGE, SEND_ATTEMPT and LOGIN_LOCK. [backend.module-map]
8. Retention deletion of its own login records (`ASM-BE-303`).
9. Supplying the login-response content (limit and lock responses) that the published contract must document. [stories.md US-004-03]

**Not responsible for.**

- The existing authentication method (`GAP-BE-085`), or the credential checks that `notes` and `search` apply on protected routes.
- Note ownership and the note access rule (`CON-BE-003`).
- Email delivery. This module hands the message to the transport (`ASM-BE-027`). The transport region is `Q-BE-005` and `CON-BE-006`.
- Sign-in screens and their messages, which belong to the web-app surface. [stories.md US-006-06]
- The global error envelope (`backend.error-model`), the published OpenAPI document (`api-docs`) and scope-guardrail routing (`scope-guardrail`).
- Retention deletion of notes. It belongs to `notes` and is `BLOCKED` on `CON-BE-300`.

**Operations owned.** `API-AUTH-707260` and `API-AUTH-692907`, referenced by id only.

**Identifier note.** This module mints from its reserved block: `ASM-BE-306` onward, `GAP-BE-514` onward, `INV-BE-010` onward. It mints no `CON-` and no `Q-`. All other ids are cited from the project spec.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, nullability or constraints. The platform is PostgreSQL 16 [platform.md]. The types below are a proposal (`ASM-BE-306`). Entity and column names follow `backend.domain-model`.

**users**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `user_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | unique; normalised form (`ASM-BE-015`, `GAP-BE-031`) |
| `created_at` | timestamptz | NOT NULL | none | set at first successful verification only (`INV-BE-003`) |

PII: `email` (`ASM-BE-019`). Whether the existing authentication shares this table is `GAP-BE-518`.

**otp_challenges**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `challenge_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | normalised; no foreign key to `users`, because the user may not exist yet (`ASM-BE-025`) |
| `code_digest` | text | NOT NULL | none | storage form undecided (`GAP-BE-033`); the issued code is 6 numeric digits [BR-029] |
| `issued_at` | timestamptz | NOT NULL | none | |
| `expires_at` | timestamptz | NOT NULL | none | equals `issued_at` + 10 minutes [BR-024]; `CHECK (expires_at > issued_at)` |
| `consumed_at` | timestamptz | NULL | none | set on success; `CHECK (consumed_at IS NULL OR consumed_at >= issued_at)` |

Indexes:
- btree `(email, issued_at DESC)` serves the lookup of the latest challenge at verification.
- btree `(issued_at)` serves the retention sweep.

**send_attempts**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `send_attempt_id` | bigint identity | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | normalised |
| `requested_at` | timestamptz | NOT NULL | none | |

Index: btree `(email, requested_at)` serves the count of attempts in the trailing 10 minutes. Only accepted requests write a row (`ASM-BE-308`).

**login_locks**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key; normalised |
| `failed_count` | integer | NOT NULL | 0 | `CHECK (failed_count BETWEEN 0 AND 4)` (`ASM-BE-309`) |
| `locked_until` | timestamptz | NULL | none | set only at lock entry; null means never locked |
| `updated_at` | timestamptz | NOT NULL | none | last change; retention basis (`ASM-BE-312`) |

`failed_count` lives here, per email, because the counting default is per normalised email with no time window (`ASM-BE-305`).

**Referential integrity.** There are no foreign keys, so there is no `ON DELETE` behaviour. Tables are linked by normalised email value.

**Time.** Every instant is `timestamptz`.

**Encryption and PII.** Email addresses and `code_digest` are personal or sensitive data. No input states encryption at rest (`GAP-BE-033`, `GAP-BE-304`), so none is claimed.
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

All state is per normalised email address (`ASM-BE-015`, `ASM-BE-029`).

### 3.1 OTP challenge

States: `ISSUED`, `CONSUMED` (terminal), `EXPIRED` (terminal, derived by time: now is after `expires_at`; boundary `ASM-BE-307`, `GAP-BE-306`).

A `SUPERSEDED` state is not defined, because whether a newer code invalidates an older one is `GAP-BE-083`. The default is `ASM-BE-310`.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `ISSUED` | send accepted | fewer than 3 accepted send attempts in the trailing 10 minutes | challenge row created; send attempt row written; code handed to the transport |
| `ISSUED` | `CONSUMED` | correct code | email not locked; challenge is the latest unconsumed (`ASM-BE-310`); not expired | `consumed_at` set; user created if absent; `failed_count` set to 0; credential issued |
| `ISSUED` | `ISSUED` | wrong code | email not locked; not expired | `failed_count` incremented |
| `ISSUED` | `EXPIRED` | time passes | now after `expires_at` | none |
| `CONSUMED` | none | any verify | | refused (`INV-BE-002`) |
| `EXPIRED` | none | any verify | | refused as expired; does not count toward the lock (`ASM-BE-311`) |

### 3.2 Lock (per email)

States: `UNLOCKED` (`locked_until` null or past) and `LOCKED` (`locked_until` in the future). Neither is terminal.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| `UNLOCKED` | `LOCKED` | the 5th wrong code | `failed_count` reaches 5 (`ASM-BE-305`) | `locked_until` = now + 15 minutes; `failed_count` reset to 0 (`ASM-BE-309`) |
| `LOCKED` | `UNLOCKED` | time passes | now at or after `locked_until` | none (derived, no write) |
| `LOCKED` | `LOCKED` | verify, with any code | | refused; `failed_count` and `locked_until` unchanged (`ASM-BE-313`, `INV-BE-010`) |
| `LOCKED` | `LOCKED` | send | send limit not reached | accepted; new challenge issued [BR-032] |

### 3.3 Resend limiter

This is a count, not a state. A send is accepted when fewer than 3 accepted `send_attempts` fall in the trailing 10 minutes (sliding window, `ASM-BE-044`). The first request counts (`CON-BE-011`; reading used: every request counts).

### 3.4 Refusals

| Attempt | Result | Status |
|---|---|---|
| send beyond the limit | refused, no code, no email (`INV-BE-006`) | 415 [BR-028; US-004-03] |
| verify while locked | refused, locked-account response | `GAP-BE-038` |
| verify with a wrong, expired or consumed code | refused | `GAP-BE-053` |
| verify with no challenge for the email | refused; not counted toward the lock (`ASM-BE-314`) | `GAP-BE-053`, `GAP-BE-515` |

When several refusals apply at once, the order of checks is `GAP-BE-514`.
<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-AUTH-FR-001 Issue a code
**Given** an email address and fewer than 3 accepted send attempts for its normalised form in the trailing 10 minutes,
**When** `API-AUTH-707260` is called,
**Then** a 6-digit numeric code is generated, a challenge with `expires_at` = `issued_at` + 10 minutes is stored, a send attempt row is written, the code is handed to the email transport, and success is returned.
- **Traces to.** US-006-06; BR-020, BR-024, BR-029.
- **Verification.** `test-auth-send-issues-code` (planned).
- **API.** `API-AUTH-707260`.
- **Status.** Transport region is `BLOCKED` on `Q-BE-005`, `CON-BE-006`. Input validation is `GAP-BE-035`. Code generation source is `GAP-BE-516`. Transport failure behaviour is `GAP-BE-517`. Success status and body are `GAP-BE-519`.

### BE-AUTH-FR-002 Enforce the resend limit
**Given** 3 accepted send attempts for the email in the trailing 10 minutes,
**When** `API-AUTH-707260` is called,
**Then** the request is refused with 415, no challenge is created, no email is sent, and no send attempt row is written (`ASM-BE-308`).
- **Traces to.** BR-021, BR-028, BR-032; US-004-03; US-006-06.
- **Verification.** `test-auth-send-limit-415` (planned).
- **API.** `API-AUTH-707260`.

### BE-AUTH-FR-003 Send is allowed during a lock
**Given** the email is `LOCKED`,
**When** `API-AUTH-707260` is called and the resend limit is not reached,
**Then** a code is issued as in BE-AUTH-FR-001.
- **Traces to.** BR-032.
- **Verification.** `test-auth-send-while-locked` (planned).
- **API.** `API-AUTH-707260`.

### BE-AUTH-FR-004 Verify a correct code
**Given** the email is not locked, and the latest challenge for it is unconsumed, not expired, and matches the submitted code,
**When** `API-AUTH-692907` is called,
**Then** `consumed_at` is set, a user row is created if none exists, `failed_count` is set to 0, a credential is issued, and success is returned.
- **Traces to.** BR-023; US-006-06; `ASM-BE-025`; `ASM-BE-305`.
- **Verification.** `test-auth-verify-success` (planned).
- **API.** `API-AUTH-692907`.
- **Status.** The credential is `BLOCKED` on `Q-BE-012`. Who may verify is `BLOCKED` on `Q-BE-003`. Success status and body are `GAP-BE-519`.

### BE-AUTH-FR-005 Reject an expired code
**Given** a challenge issued at T,
**When** `API-AUTH-692907` is called at an instant after T + 10 minutes,
**Then** verification is refused as expired, no credential is issued, and `failed_count` is unchanged (`ASM-BE-311`).
- **Boundary.** The instant exactly T + 10 minutes follows `ASM-BE-307` and `GAP-BE-306`.
- **Traces to.** BR-020, BR-024, BR-026; US-006-06.
- **Verification.** `test-auth-verify-expired`, `test-auth-verify-expiry-boundary` (planned, injectable clock).
- **API.** `API-AUTH-692907`. Status `GAP-BE-053`.

### BE-AUTH-FR-006 Single use
**Given** a challenge already consumed,
**When** `API-AUTH-692907` is called with its code,
**Then** verification is refused and no credential is issued.
- **Concurrency.** Two simultaneous correct submissions of one code result in exactly one success (`INV-BE-011`).
- **Traces to.** BR-023.
- **Verification.** `test-auth-verify-single-use`, `test-auth-verify-concurrent-single-use` (planned).
- **API.** `API-AUTH-692907`. Status `GAP-BE-053`.

### BE-AUTH-FR-007 Count a wrong code
**Given** the email is not locked, the latest challenge is unexpired and unconsumed, and the submitted code is wrong,
**When** `API-AUTH-692907` is called,
**Then** verification is refused and `failed_count` is incremented by 1.
- **Traces to.** BR-022, BR-025, BR-027; `ASM-BE-305`, `ASM-BE-311`.
- **Verification.** `test-auth-verify-wrong-counts` (planned).
- **API.** `API-AUTH-692907`. Status `GAP-BE-053`.

### BE-AUTH-FR-008 Lock after 5 wrong codes
**Given** `failed_count` is 4 and the email is not locked,
**When** a 5th wrong code is submitted,
**Then** `locked_until` is set to now + 15 minutes and `failed_count` is reset to 0.
- **Traces to.** BR-022, BR-025, BR-027; US-006-06.
- **Verification.** `test-auth-lock-on-fifth-wrong` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-009 Refuse verification during a lock
**Given** the email is `LOCKED`,
**When** `API-AUTH-692907` is called with a correct or a wrong code,
**Then** verification is refused with a locked-account response, `failed_count` and `locked_until` are unchanged, and the code is not consumed.
- **Traces to.** BR-022, BR-027, BR-032.
- **Verification.** `test-auth-verify-locked-correct-code` (planned).
- **API.** `API-AUTH-692907`. Status `GAP-BE-038`.

### BE-AUTH-FR-010 Lock expiry
**Given** `locked_until` is in the past,
**When** `API-AUTH-692907` is called,
**Then** the email is treated as `UNLOCKED`, and no write is needed to lift the lock.
- **Traces to.** BR-022, BR-025.
- **Verification.** `test-auth-lock-expires` (planned, injectable clock).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-011 No user disclosure
**Given** any email address,
**When** either operation is called,
**Then** no response distinguishes an address that has a User from one that does not.
- **Traces to.** Derived from the abuse controls in BR-021 and BR-022, as stated in `backend.auth-model`. Not cited in any input.
- **Verification.** `test-auth-no-user-disclosure` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-012 Log redaction
**Given** any login event,
**When** it is logged,
**Then** the entry contains no unredacted email address and no code.
- **Traces to.** NFR-018 (title and target metric; the rule text is corrupted, method `GAP-BE-104`).
- **Verification.** `test-auth-log-redaction` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-013 Retention deletion of login records
**Given** login records older than their retention period,
**When** the retention sweep runs,
**Then** challenges and send attempts older than 1 day and locks older than 5 days are deleted (`ASM-BE-303`, `ASM-BE-312`).
- **Traces to.** NFR-019 (positional reading). Mechanism `GAP-BE-101`.
- **Verification.** `test-auth-retention-sweep` (planned).

### BE-AUTH-FR-014 Documented login responses
**Given** the published contract,
**When** it describes `API-AUTH-707260` and `API-AUTH-692907`,
**Then** it documents the 415 response for more than 3 requests within 10 minutes and the locked-account response after 5 wrong codes locked for 15 minutes.
- **Traces to.** US-004-03; NFR-016.
- **Verification.** `test-auth-contract-documents-limits` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-707260` | POST | `/auth/otp/send` | `auth` | 200 | 400, 415 | Request a 6-digit email one-time code for sign-in. Limited to 3 requests within 10 minutes per normalised email; requests beyond the limit return 415 and create no code and send no email. Not refused during an account lock. Does not reveal whether a User exists. |
| `API-AUTH-692907` | POST | `/auth/otp/verify` | `auth` | 200 | 400, 401, 403, 423 | Verify a 6-digit email one-time code. Success consumes the code (single use), creates the User on first successful verification, clears the wrong-code count, and returns a credential (form unresolved, Q-BE-012). Refused during a 15-minute lock after 5 wrong codes, even with a correct code. Wrong, expired and already-used code statuses are not stated in any input (GAP-BE-038, GAP-BE-053). |

### `API-AUTH-707260` — POST `/auth/otp/send`

Request a 6-digit email one-time code for sign-in. Limited to 3 requests within 10 minutes per normalised email; requests beyond the limit return 415 and create no code and send no email. Not refused during an account lock. Does not reveal whether a User exists.

**Request:** 
- `email` (string, required): Email address to which the one-time code is sent. Personal data; compared in normalised form (ASM-BE-015). Format validation rule is not stated (GAP-BE-035).
**Response:** 
- `message` (string): Human-readable acknowledgement that a code was requested. Identical whether or not a User exists for the address. Response body shape is not supplied in any input; this is a proposal.
- `expires_at` (string): Timestamp at which the issued code expires, 10 minutes after issue (BR-020, BR-024). Proposed field; not stated in any input.

### `API-AUTH-692907` — POST `/auth/otp/verify`

Verify a 6-digit email one-time code. Success consumes the code (single use), creates the User on first successful verification, clears the wrong-code count, and returns a credential (form unresolved, Q-BE-012). Refused during a 15-minute lock after 5 wrong codes, even with a correct code. Wrong, expired and already-used code statuses are not stated in any input (GAP-BE-038, GAP-BE-053).

**Request:** 
- `email` (string, required): Email address the code was issued for; compared in normalised form (ASM-BE-015).
- `code` (string, required): The 6-digit numeric one-time code (BR-029).
**Response:** 
- `credential` (string): Credential returned on successful verification. Its form (body token, cookie or other) and lifetime are unanswered (Q-BE-012, GAP-BE-302); presence in the body is a placeholder pending that answer.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

Project-level invariants `INV-BE-002` to `INV-BE-006` also bind this module and are cited, not redefined.

- `INV-BE-010`. A verify refused because of a lock changes neither `failed_count` nor `locked_until`, and consumes no code (`ASM-BE-313`). [BR-022, BR-027]
- `INV-BE-011`. A challenge is consumed by at most one successful verification, including under concurrent submissions. [BR-023]
- `INV-BE-012`. For any normalised email, at most 3 accepted `send_attempts` rows fall in any trailing 10-minute window. [BR-028]
- `INV-BE-013`. After every operation, `failed_count` is between 0 and 4 (`ASM-BE-309`).
- `INV-BE-014`. For every challenge, `expires_at` equals `issued_at` plus 10 minutes. [BR-024]
- `INV-BE-015`. No response body or log line from either operation contains an email address, a code or a credential. [NFR-018 title; core 12; method `GAP-BE-104`]
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: DRAFT-UNVERIFIED -->
## 7. Authorization

| Operation | Anonymous | Authenticated team member |
|---|---|---|
| `API-AUTH-707260` | allow, limited per `INV-BE-012` | allow, same limit |
| `API-AUTH-692907` | allow, lock per `INV-BE-010` | allow, same lock |

- **Basis.** Both operations are called before a credential exists. [stories.md US-006-06] No other role is granted (`ASM-BE-302`).
- **Eligibility.** Who may request, verify or receive a code is `BLOCKED` on `Q-BE-003`. BR-030 is corrupted and `CON-BE-003` is open. Until it is decided, no eligibility check is specified.
- **Ownership predicate.** None. Neither operation acts on a user-scoped resource. A User is created by the first successful verification (`ASM-BE-025`). Any link between a User and notes depends on `Q-BE-003` and `CON-BE-003`.
- **Existing authentication.** Its interaction with this module is `GAP-BE-085`.
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

**Retention sweep.** Deletes `otp_challenges` and `send_attempts` older than 1 day (by `issued_at` and `requested_at`) and `login_locks` older than 5 days (by `updated_at`). Positional reading per `ASM-BE-303`, basis for locks per `ASM-BE-312`. Scheduler and frequency are `GAP-BE-101`. Deletion is irreversible.

**Lock expiry.** There is no job. A lock lifts by comparison of `locked_until` with the current instant (BE-AUTH-FR-010).

**Idempotency.**
- No idempotency key is supplied by any input for either operation. A repeated send is a new request and counts toward the limit. A repeated verify of a consumed code is refused (`INV-BE-002`).
- Concurrent verifications of one code resolve to exactly one success (`INV-BE-011`).
- The idempotency and rate-limit class of the operations are otherwise `GAP-BE-520`.

**Retries.** No retry policy is stated. Email transport retry is `GAP-BE-517`.
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

All time-dependent tests use an injectable clock.

**Unit.**
- Code is 6 numeric digits (`test-auth-code-format`; BR-029).
- Email normalisation yields one key for equivalent addresses (rule `GAP-BE-031`).
- Resend window count: 3 accepted, 4th refused, window slides (`test-auth-send-window`).
- Lock transitions: counts 1 to 4 do not lock, the 5th locks, `failed_count` resets (`test-auth-lock-transitions`).
- Expiry before, after and at the boundary (`ASM-BE-307`).

**Integration** (against PostgreSQL 16 [platform.md]).
- `test-auth-send-issues-code`, `test-auth-send-limit-415`, `test-auth-send-while-locked`.
- `test-auth-verify-success`, `test-auth-verify-expired`, `test-auth-verify-single-use`, `test-auth-verify-concurrent-single-use`.
- `test-auth-verify-wrong-counts`, `test-auth-lock-on-fifth-wrong`, `test-auth-verify-locked-correct-code`, `test-auth-lock-expires`.
- `test-auth-no-user-disclosure`, `test-auth-log-redaction`, `test-auth-retention-sweep`.

**Contract.**
- `test-auth-contract-documents-limits`: the published document describes both operations, the 415 response and the locked-account response (US-004-03; BR-012).
- Response statuses of wrong, expired, used and locked verifications are asserted only after `GAP-BE-038` and `GAP-BE-053` are closed.
- The `error.code` values are checked against `backend.error-model`, because 415 is shared with the file-upload rejection.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps, contradictions and assumptions

**Open items cited from the project spec** (defined there, not restated): `Q-BE-003`, `Q-BE-005`, `Q-BE-012`; `CON-BE-003`, `CON-BE-006`, `CON-BE-011`, `CON-BE-300`; `GAP-BE-024`, `GAP-BE-031`, `GAP-BE-033`, `GAP-BE-035`, `GAP-BE-038`, `GAP-BE-053`, `GAP-BE-083`, `GAP-BE-085`, `GAP-BE-089`, `GAP-BE-101`, `GAP-BE-104`, `GAP-BE-301`, `GAP-BE-302`, `GAP-BE-304`, `GAP-BE-306`; `ASM-BE-015`, `ASM-BE-019`, `ASM-BE-025`, `ASM-BE-027`, `ASM-BE-029`, `ASM-BE-044`, `ASM-BE-302`, `ASM-BE-303`, `ASM-BE-305`.

`GAP-BE-089`: no library or ORM is named, so none is specified here. `GAP-BE-301`: no latency or availability target for the login operations.

**Gaps raised by this module.**

| ID | Gap | Closing artefact |
|---|---|---|
| `GAP-BE-514` | The order of checks when several refusals apply (locked, expired, used, wrong, limit) is not stated. | A rule from the product owner |
| `GAP-BE-515` | Status and behaviour of a verify when no challenge exists for the email. | A status decision |
| `GAP-BE-516` | The code generation source (randomness requirement) is not stated. | A security policy |
| `GAP-BE-517` | Behaviour when the email transport fails or is slow, including whether a challenge and send attempt are kept. | An operations decision |
| `GAP-BE-518` | Whether the `users` table is shared with the existing authentication. | A description of the existing authentication (`GAP-BE-085`) |
| `GAP-BE-519` | Success statuses and response bodies of both operations. | A status and body decision |
| `GAP-BE-520` | Rate-limit class and idempotency behaviour for both operations beyond the stated limits. | A platform decision |

**Assumptions made by this module.**

| ID | Assumption | Reason | Confirmed by |
|---|---|---|---|
| `ASM-BE-306` | Column types: uuid keys, text email, timestamptz instants, integer counter, bigint identity. | PostgreSQL 16 is the platform and no types are supplied. | A platform decision |
| `ASM-BE-307` | A code is expired only when the instant is strictly after issue + 10 minutes. | BR-024 and US-006-06 say "after". BR-020 and BR-026 say "expires", so `GAP-BE-306` stays open. | A decision on `GAP-BE-306` |
| `ASM-BE-308` | A refused send writes no `send_attempts` row. | Otherwise a caller that keeps retrying is never released from the window. | A rule from the product owner |
| `ASM-BE-309` | `failed_count` is reset to 0 when a lock is entered, so it ranges 0 to 4. | Otherwise the first wrong code after the lock would re-lock immediately. | A rule from the product owner |
| `ASM-BE-310` | Only the latest unconsumed challenge for an email is checked at verification. | `GAP-BE-083` is open. One live code is the narrower reading. | A rule from the product owner |
| `ASM-BE-311` | Only a wrong code against the latest unexpired, unconsumed challenge counts toward the lock. Expired and consumed codes do not. | BR-022 says "wrong codes". An expired or used code is refused, not wrong. | A rule from the product owner |
| `ASM-BE-312` | `login_locks.updated_at` is the retention basis for locks. | NFR-019 gives a period but no basis for locks. | A rule from the product owner |
| `ASM-BE-313` | A verify refused because of a lock changes neither `failed_count` nor `locked_until`. | BR-022 refuses verification during the lock and states no extension. | A rule from the product owner |
| `ASM-BE-314` | A verify with no challenge for the email does not count toward the lock. | There is no code to be wrong against. | A rule from the product owner |

**Halting.** This module is blocked by `Q-BE-003`, `Q-BE-005`, `Q-BE-012`, `CON-BE-003` and `CON-BE-006`, as listed in the frontmatter. Everything else in this document is written.
<!-- /SECTION: backend.auth.gaps -->
