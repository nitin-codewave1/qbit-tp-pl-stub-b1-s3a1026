---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-006-06, notes_api:US-004-03]
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
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

**Responsibility.** `auth` provides email one-time-code sign-in. It is an additive sign-in method and existing login methods are unchanged. [requirements.md NFR-016; feedback.md CR-130] It owns:

1. Issuing a 6-digit numeric code for an email address and handing it to the email transport (`API-AUTH-707260`). [BR-029; US-006-06]
2. Limiting code requests to 3 within a trailing 10 minutes per normalised email. Every request counts, including the first (`CON-BE-011`, reading used). Further requests return 415 and create no code and send no email. [BR-021, BR-028, BR-032; US-004-03]
3. Verifying a code (`API-AUTH-692907`). A code is valid for 10 minutes from issue and is single-use. [BR-020, BR-023, BR-024]
4. Counting wrong codes and locking the address for 15 minutes after 5. Verification is refused during the lock. Requesting a code is not refused during the lock, but it stays subject to the resend limit. [BR-022, BR-025, BR-027, BR-032]
5. Refusing locked-account, wrong, expired and already-used codes with 415, distinguished only by the error code in `backend.error-model`. [BR-033]
6. Creating the User on first successful verification (`ASM-BE-025`).
7. Issuing a credential on successful verification. Its form is `Q-BE-012`; its lifetime is `GAP-BE-302`.
8. Owning the data of USER, OTP_CHALLENGE, SEND_ATTEMPT and LOGIN_LOCK, and the retention deletion of challenges, send attempts and locks. [backend.module-map; NFR-019]
9. Supplying the login-response content that the published contract must document. [US-004-03]

**Not responsible for.**

- The existing authentication method (`GAP-BE-085`), and the credential checks that `notes` and `search` apply on protected routes.
- Note ownership and the note access rule (`CON-BE-003`), and retention of notes (not deleted by retention, NFR-019).
- Email delivery. This module hands the message to the transport (`ASM-BE-027`). The transport region is `Q-BE-005` and `CON-BE-006`.
- Sign-in screens and their messages, which belong to the web-app surface. [US-006-06]
- The global error envelope and error-code registry (`backend.error-model`), the published OpenAPI document (`api-docs`), and out-of-scope routing (`scope-guardrail`).
- Retention of User rows (`GAP-BE-600`).

**Operations owned.** `API-AUTH-707260` and `API-AUTH-692907`, referenced by id only.

**Inbound handoffs.** Three web-app handoffs were listed for this run (HANDOFF-WEBAPP-163212, HANDOFF-WEBAPP-200601, HANDOFF-WEBAPP-743710). Their content was not supplied, so none could be read or answered here (`GAP-BE-602`). The distinct-error-code requirement (BE-AUTH-FR-009) and the open credential question (`Q-BE-012`) are stated for the web-app surface.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, nullability or constraints. The platform is PostgreSQL 16 [platform.md]. The types below are a proposal (`ASM-BE-401`). Entity and column names follow `backend.domain-model`. No ORM or migration tool is named (`GAP-BE-089`).

**users**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `user_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | unique; normalised form (`ASM-BE-015`, `GAP-BE-031`) |
| `created_at` | timestamptz | NOT NULL | none | set at first successful verification only (`INV-BE-003`) |

PII: `email` (`ASM-BE-019`). Whether this table is shared with the existing authentication is `GAP-BE-620`.

**otp_challenges**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `challenge_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | normalised; no foreign key to `users`, because the user may not exist yet (`ASM-BE-025`) |
| `code_digest` | text | NOT NULL | none | storage form undecided (`GAP-BE-033`); the issued code is 6 numeric digits [BR-029] |
| `issued_at` | timestamptz | NOT NULL | none | |
| `expires_at` | timestamptz | NOT NULL | none | equals `issued_at` + 10 minutes; `CHECK (expires_at = issued_at + interval '10 minutes')` |
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

Index: btree `(email, requested_at)` serves the count of attempts in the trailing 10 minutes. Only accepted requests write a row (`ASM-BE-403`).

**login_locks**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key; normalised |
| `failed_count` | integer | NOT NULL | 0 | `CHECK (failed_count BETWEEN 0 AND 4)` (`ASM-BE-404`) |
| `locked_until` | timestamptz | NULL | none | set only at lock entry; null means never locked |
| `updated_at` | timestamptz | NOT NULL | none | last change; retention basis (`ASM-BE-407`) |

The row for an email is created on first use by either operation. It serves as the per-email serialisation point (BE-AUTH-FR-014).

**Referential integrity.** There are no foreign keys, so there is no `ON DELETE` behaviour. Tables are linked by normalised email value.

**Time.** Every instant is `timestamptz`; all comparisons use the database transaction clock.

**Encryption and PII.** Email addresses and `code_digest` are personal or sensitive data. No input states encryption at rest (`GAP-BE-033`, `GAP-BE-304`), so none is claimed.
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

All state is per normalised email address (`ASM-BE-015`, `ASM-BE-029`).

### 3.1 OTP challenge

States: `ISSUED`, `CONSUMED` (terminal), `EXPIRED` (terminal, derived by time: now is strictly after `expires_at`, `ASM-BE-402`, boundary `GAP-BE-306`).

A `SUPERSEDED` state is not defined, because whether a newer code invalidates an older one is `GAP-BE-083`. The working default is `ASM-BE-405`: only the latest challenge for the email is checked.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `ISSUED` | send accepted | fewer than 3 accepted send attempts in the trailing 10 minutes | challenge row created; send attempt row written; code handed to the transport |
| `ISSUED` | `CONSUMED` | correct code | email not locked; challenge is the latest; not expired | `consumed_at` set; user created if absent; `failed_count` set to 0; credential issued |
| `ISSUED` | `ISSUED` | wrong code | email not locked; challenge is the latest and not expired | `failed_count` incremented (`ASM-BE-406`) |
| `ISSUED` | `EXPIRED` | time passes | now after `expires_at` | none |
| `CONSUMED` | none | any verify | | refused as already used (`INV-BE-002`); no counter change |
| `EXPIRED` | none | any verify | | refused as expired; no counter change (`ASM-BE-406`) |

### 3.2 Lock (per email)

States: `UNLOCKED` (`locked_until` null or not in the future) and `LOCKED` (`locked_until` in the future). Neither is terminal.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| `UNLOCKED` | `LOCKED` | the 5th wrong code | `failed_count` would reach 5 (`ASM-BE-305`) | `locked_until` = now + 15 minutes; `failed_count` reset to 0 (`ASM-BE-404`) |
| `LOCKED` | `UNLOCKED` | time passes | now at or after `locked_until` | none (derived, no write) |
| `LOCKED` | `LOCKED` | verify, any code | | refused; `failed_count`, `locked_until` and the challenge unchanged (`ASM-BE-408`, `INV-BE-010`) |
| `LOCKED` | `LOCKED` | send | send limit not reached | accepted; new challenge issued [BR-032] |

### 3.3 Resend limiter

A count, not a state. A send is accepted when fewer than 3 accepted `send_attempts` fall in the trailing 10 minutes (sliding window, `ASM-BE-044`). A refused send writes no row (`ASM-BE-403`).

### 3.4 Verify decision order

Proposed order when several refusals could apply (`GAP-BE-601`, working default `ASM-BE-409`):

1. email `LOCKED` → locked-account refusal
2. no challenge for the email → invalid-code refusal, not counted
3. latest challenge consumed → already-used refusal
4. latest challenge expired → expired refusal
5. code differs → invalid-code refusal, counted
6. code matches → success

Refusals use the error codes in `backend.error-model` (OTP_ACCOUNT_LOCKED, OTP_CODE_USED, OTP_CODE_EXPIRED, OTP_CODE_INVALID), all with status 415 [BR-033].
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
- **Open.** Transport region `Q-BE-005` / `CON-BE-006`. Input validation `GAP-BE-035`. Code source `GAP-BE-617`. Transport failure `GAP-BE-618`. Success status and body `GAP-BE-604`.

### BE-AUTH-FR-002 Enforce the resend limit
**Given** 3 accepted send attempts for the email in the trailing 10 minutes,
**When** `API-AUTH-707260` is called,
**Then** the request is refused with status 415 and error code OTP_RATE_LIMITED, no challenge is created, no email is sent, and no send attempt row is written.
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
- **Open.** The credential is `Q-BE-012` (blocking); its lifetime `GAP-BE-302`. Who may verify is `Q-BE-003` (blocking). Success status and body `GAP-BE-604`.

### BE-AUTH-FR-005 Reject an expired code
**Given** a challenge issued at T,
**When** `API-AUTH-692907` is called at an instant after T + 10 minutes,
**Then** verification is refused with status 415 and error code OTP_CODE_EXPIRED, no credential is issued, and `failed_count` is unchanged.
- **Boundary.** The instant exactly T + 10 minutes follows `ASM-BE-402` and `GAP-BE-306`.
- **Traces to.** BR-020, BR-024, BR-026, BR-033; US-006-06.
- **Verification.** `test-auth-verify-expired`, `test-auth-verify-expiry-boundary` (planned, injectable clock).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-006 Single use
**Given** a challenge already consumed,
**When** `API-AUTH-692907` is called with its code,
**Then** verification is refused with status 415 and error code OTP_CODE_USED, and no credential is issued.
- **Concurrency.** Two simultaneous correct submissions of one code result in exactly one success (`INV-BE-011`).
- **Traces to.** BR-023, BR-033.
- **Verification.** `test-auth-verify-single-use`, `test-auth-verify-concurrent-single-use` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-007 Count a wrong code
**Given** the email is not locked, the latest challenge is unexpired and unconsumed, and the submitted code is wrong,
**When** `API-AUTH-692907` is called,
**Then** verification is refused with status 415 and error code OTP_CODE_INVALID, and `failed_count` is incremented by 1.
- **Traces to.** BR-022, BR-025, BR-027, BR-033; `ASM-BE-305`, `ASM-BE-406`.
- **Verification.** `test-auth-verify-wrong-counts` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-008 Lock after 5 wrong codes
**Given** `failed_count` is 4 and the email is not locked,
**When** a 5th wrong code is submitted,
**Then** the submission is refused as wrong, `locked_until` is set to now + 15 minutes, and `failed_count` is reset to 0.
- **Traces to.** BR-022, BR-025, BR-027; US-006-06.
- **Verification.** `test-auth-lock-on-fifth-wrong` (planned).
- **API.** `API-AUTH-692907`.
- **Open.** Whether the 5th refusal itself reports OTP_CODE_INVALID or OTP_ACCOUNT_LOCKED is `GAP-BE-623`. Default: OTP_CODE_INVALID, because the lock begins after the 5th wrong code [BR-022].

### BE-AUTH-FR-009 Refuse verification during a lock
**Given** the email is `LOCKED`,
**When** `API-AUTH-692907` is called with a correct or a wrong code,
**Then** verification is refused with status 415 and error code OTP_ACCOUNT_LOCKED, `failed_count` and `locked_until` are unchanged, and the code is not consumed.
- **Traces to.** BR-022, BR-027, BR-032, BR-033; US-004-03.
- **Verification.** `test-auth-verify-locked-correct-code` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-010 Distinct error codes on a shared status
**Given** that the five login refusals share status 415,
**When** any refusal is returned,
**Then** the error code identifies the cause, one code per cause, as registered in `backend.error-model`. Consumers must switch on the error code, not the status.
- **Traces to.** BR-028, BR-033; US-004-03; US-006-06.
- **Verification.** `test-auth-contract-error-codes-distinct` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-011 Lock expiry
**Given** `locked_until` is not in the future,
**When** `API-AUTH-692907` is called,
**Then** the email is treated as `UNLOCKED`, and no write is needed to lift the lock.
- **Traces to.** BR-022, BR-025.
- **Verification.** `test-auth-lock-expires` (planned, injectable clock).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-012 No user disclosure
**Given** any email address,
**When** either operation is called,
**Then** no response distinguishes an address that has a User from one that has none (`ASM-BE-400`).
- **Traces to.** Derived: the abuse controls of BR-021 and BR-022 would otherwise leak account existence. Not stated in any input.
- **Verification.** `test-auth-no-user-disclosure` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-013 Log redaction
**Given** any login event,
**When** it is logged,
**Then** the entry contains no unredacted email address and no code.
- **Traces to.** NFR-018 (title and target metric; the rule text is corrupted, method `GAP-BE-104`).
- **Verification.** `test-auth-log-redaction` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-014 Per-email serialisation
**Given** concurrent requests for the same normalised email,
**When** they are processed,
**Then** the send count, the wrong-code counter, the lock transition and the consumption of a code are each decided under a row-level lock on that email's `login_locks` row, so that no more than 3 sends are accepted per window, the lock is entered exactly once at the 5th wrong code, and a code is consumed at most once.
- **Traces to.** BR-021, BR-022, BR-023, BR-028 (limits must hold under concurrency).
- **Verification.** `test-auth-concurrent-send-limit`, `test-auth-concurrent-wrong-codes` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-015 Retention deletion
**Given** challenges, send attempts and locks older than their retention period,
**When** the retention sweep runs (`backend.auth.jobs`),
**Then** challenges and send attempts older than 1 day and locks older than 5 days are deleted.
- **Traces to.** NFR-019; `ASM-BE-303`.
- **Verification.** `test-auth-retention-sweep` (planned).
- **API.** none.
- **Open.** Clock basis `ASM-BE-407` / `GAP-BE-603`. Mechanism `GAP-BE-101`. Users `GAP-BE-600`.
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-707260` | POST | `/auth/otp/send` | `auth` | 200 | 400, 415, 500, 503 | Request a 6-digit email one-time code for sign-in. Accepts at most 3 requests per email within a trailing 10 minutes (every request counts); further requests return 415 and create no code and send no email. Not refused during an account lock, but still subject to the resend limit. Response must not reveal whether the email has a User. |
| `API-AUTH-692907` | POST | `/auth/otp/verify` | `auth` | 200 | 400, 415, 500 | Verify a 6-digit email one-time code. A correct, unexpired, unused code on an unlocked account succeeds, consumes the code (single use, safe under concurrent submission) and creates the User on first successful verification. Locked account (15 minutes after 5 wrong codes), wrong code, expired code (older than 10 minutes) and already-used code are each refused with 415, distinguished only by the error code. Success status and credential returned are not fixed by inputs (GAP-BE-604, Q-BE-012). |

### `API-AUTH-707260` — POST `/auth/otp/send`

Request a 6-digit email one-time code for sign-in. Accepts at most 3 requests per email within a trailing 10 minutes (every request counts); further requests return 415 and create no code and send no email. Not refused during an account lock, but still subject to the resend limit. Response must not reveal whether the email has a User.

**Request:** 
- `email` (string, required): Email address to which the code is sent. Compared in normalised form (ASM-BE-015). Personal data; never logged unredacted. Format validation rule is unstated (GAP-BE-035).
**Response:** none

### `API-AUTH-692907` — POST `/auth/otp/verify`

Verify a 6-digit email one-time code. A correct, unexpired, unused code on an unlocked account succeeds, consumes the code (single use, safe under concurrent submission) and creates the User on first successful verification. Locked account (15 minutes after 5 wrong codes), wrong code, expired code (older than 10 minutes) and already-used code are each refused with 415, distinguished only by the error code. Success status and credential returned are not fixed by inputs (GAP-BE-604, Q-BE-012).

**Request:** 
- `email` (string, required): Email address the code was sent to. Compared in normalised form (ASM-BE-015). Personal data.
- `code` (string, required): The 6-digit numeric one-time code received by email (BR-029). Sent as a string so leading zeros survive.
**Response:** 
- `credential` (string): Credential granting authenticated access after successful verification. Form (body token, cookie or other) and lifetime are undecided (Q-BE-012, GAP-BE-302); may instead be delivered outside the body.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

Project-level invariants that bind this module: `INV-BE-002` (single use), `INV-BE-003` (User created only on successful verification), `INV-BE-004` (expiry), `INV-BE-005` (no success while locked), `INV-BE-006` (no code beyond the limit). They are defined in `backend.domain-model` and not restated.

Module invariants, holding after every operation:

- `INV-BE-010`. A verify refused because of a lock changes neither `failed_count`, `locked_until`, nor any challenge. [BR-022; `ASM-BE-408`]
- `INV-BE-011`. Under any number of concurrent submissions of one correct code, exactly one succeeds. [BR-023; BE-AUTH-FR-014]
- `INV-BE-012`. `failed_count` is between 0 and 4. `locked_until` is set only by the transition on the 5th wrong code, and is exactly that instant + 15 minutes. [BR-022, BR-025; `ASM-BE-404`]
- `INV-BE-013`. For any email, accepted `send_attempts` rows within any trailing 10 minutes number at most 3. [BR-028; BR-032]
- `INV-BE-014`. For every challenge, `expires_at` = `issued_at` + 10 minutes. [BR-024]
- `INV-BE-015`. No response, log entry or error from either operation discloses whether the email has a User, and no log entry holds an unredacted email or a code. [`ASM-BE-400`; NFR-018, rule text corrupted, `GAP-BE-104`]
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: DRAFT-UNVERIFIED -->
## 7. Authorization

**Callers.** Both operations are used before a credential exists, so the caller is unauthenticated. No input defines API roles (`GAP-BE-085`), and by default a role has no permission an input does not grant (`ASM-BE-302`). Whether the two operations accept any caller is therefore a proposal, not a fact: the permission matrix is limited to the table below.

| Role | `API-AUTH-707260` | `API-AUTH-692907` |
|---|---|---|
| unauthenticated caller | permitted, subject to eligibility | permitted, subject to eligibility |
| authenticated caller | not stated | not stated |

**Eligibility.** Who may request, verify or receive a code is `Q-BE-003` (blocking), because BR-030 is corrupted. `CON-BE-003` (Critical) keeps it open. Until answered, no eligibility check is specified and the operations above are the only access defined.

**Ownership predicate.** No resource owned by this module is user-scoped. State is keyed by normalised email and is reachable only through the two operations. There is no read endpoint for challenges, attempts, locks or users.

**Email transport.** Whether the address may leave the EU is `Q-BE-005` and `CON-BE-006`.

**Unblocked by.** Answers to `Q-BE-003` and `Q-BE-005` and the resolution of `CON-BE-003` and `CON-BE-006`.
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

| Job | Work | Period | Source |
|---|---|---|---|
| `auth-retention-sweep` | delete `otp_challenges` with `issued_at` older than 1 day, `send_attempts` with `requested_at` older than 1 day, and `login_locks` with `updated_at` older than 5 days | frequency not stated | NFR-019; `ASM-BE-303`, `ASM-BE-407`; `GAP-BE-101` |

- **Idempotency.** The sweep is idempotent: re-running deletes nothing further. A sweep does not remove a challenge that is still within its 10 minutes or a lock still in force, since the retention periods are longer.
- **Retries.** A failed sweep is retried on the next run. No retry policy is stated for the mechanism (`GAP-BE-101`).
- **Operation idempotency.** `API-AUTH-707260` is not idempotent: each accepted request creates a challenge and counts toward the limit. No idempotency key is supplied by any input, and a client retry therefore consumes quota (`GAP-BE-619`). `API-AUTH-692907` is safe to replay for a consumed code: it is refused as used (BE-AUTH-FR-006).
- **No other scheduled work.** The lock lifts by time with no job (BE-AUTH-FR-011).
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

**Unit** (injectable clock; no network)
- `test-auth-code-format`: generated code is 6 numeric digits. [BR-029]
- `test-auth-expiry-arithmetic`: `expires_at` = `issued_at` + 10 minutes (`INV-BE-014`).
- `test-auth-verify-decision-order`: the order of 3.4 over every combination of lock, consumed, expired and wrong.
- `test-auth-email-normalisation`: equal addresses in differing form share one counter (`ASM-BE-015`).
- `test-auth-log-redaction` (BE-AUTH-FR-013).

**Integration** (PostgreSQL 16; email transport faked)
- Send: `test-auth-send-issues-code`, `test-auth-send-limit-415`, `test-auth-send-while-locked`, `test-auth-send-window-slides`, `test-auth-refused-send-writes-no-row`.
- Verify: `test-auth-verify-success`, `test-auth-verify-expired`, `test-auth-verify-expiry-boundary`, `test-auth-verify-single-use`, `test-auth-verify-wrong-counts`, `test-auth-lock-on-fifth-wrong`, `test-auth-verify-locked-correct-code`, `test-auth-lock-expires`, `test-auth-verify-no-challenge-not-counted`.
- Concurrency: `test-auth-verify-concurrent-single-use`, `test-auth-concurrent-send-limit`, `test-auth-concurrent-wrong-codes`.
- Data: `test-auth-user-created-on-first-verify-only`, `test-auth-retention-sweep`, `test-auth-constraints` (the CHECK constraints of the data model).

**Contract** (against the published OpenAPI document)
- `test-auth-contract-send-documented`, `test-auth-contract-verify-documented`: both operations appear with their 400 and 415 responses. [BR-012; NFR-008; US-004-03]
- `test-auth-contract-error-codes-distinct`: the four verify refusals and the send refusal carry distinct error codes under status 415 (BE-AUTH-FR-010).
- `test-auth-no-user-disclosure` (BE-AUTH-FR-012): responses for an existing and a non-existing address are indistinguishable in status, body shape and error code.
- Success status, success body and credential assertions are not written until `GAP-BE-604` and `Q-BE-012` are closed.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps, contradictions and assumptions

### Open items from the project spec that bind this module

| ID | Why it matters here |
|---|---|
| `Q-BE-003` (blocking) | who may request, verify or receive a code |
| `Q-BE-005` (blocking) | email transport region; whether the address may leave the EU |
| `Q-BE-012` (blocking) | the credential a successful verification returns |
| `CON-BE-003` (Critical) | login eligibility and note access left open |
| `CON-BE-006` (Critical) | email address possibly leaving the EU against BR-001 |
| `CON-BE-011` | "resend" versus "request" counting; reading used: every request counts |
| `GAP-BE-024`, `ASM-BE-305` | wrong-code counting: per email, no window, cleared on success |
| `GAP-BE-031`, `GAP-BE-033`, `GAP-BE-035` | normalisation, at-rest treatment, input validation |
| `GAP-BE-083` | effect of a new code on earlier codes |
| `GAP-BE-085`, `GAP-BE-089` | existing authentication; libraries |
| `GAP-BE-101`, `GAP-BE-603`, `GAP-BE-600` | retention mechanism, clock basis, Users |
| `GAP-BE-104`, `GAP-BE-304` | log treatment; encryption at rest |
| `GAP-BE-301`, `GAP-BE-302` | no latency target; credential lifetime |
| `GAP-BE-306` | the instant exactly 10 minutes after issue |
| `GAP-BE-601`, `GAP-BE-604` | order of refusals; success status and body |
| `GAP-BE-602` | the content of three inbound handoffs was not supplied |

### New gaps raised by this module

- `GAP-BE-617`. The source and quality of randomness for the 6-digit code is not stated. Closing artefact: a security requirement for code generation.
- `GAP-BE-618`. Behaviour when the email transport fails after the challenge and send attempt are stored is not stated: whether the attempt still counts, whether the caller is told. Closing artefact: a decision from the product owner and a transport failure policy.
- `GAP-BE-619`. No idempotency key or de-duplication for `API-AUTH-707260`; a client retry counts against the limit of 3. Closing artefact: a decision on client retry behaviour.
- `GAP-BE-620`. Whether the `users` table is shared with, or separate from, the existing authentication's accounts is not stated. Closing artefact: the existing authentication description (`GAP-BE-085`).
- `GAP-BE-621`. No abuse control beyond the per-email limits (per source address, global) is stated, and the per-email limit alone does not stop one caller trying many addresses. Closing artefact: a security requirement.
- `GAP-BE-622`. The rate-limit class for the two operations is not stated beyond BR-021, BR-028 and BR-032. Closing artefact: a rate-limit policy.
- `GAP-BE-623`. Whether the 5th wrong code returns the invalid-code or the locked-account refusal is not stated. Default in BE-AUTH-FR-008: invalid-code.

### Assumptions introduced by this module

- `ASM-BE-401`. Column types: uuid keys, text email, timestamptz instants, integer counter, bigint identity. Reason: PostgreSQL 16 is the platform and no types are supplied. Confirmed by: a platform decision.
- `ASM-BE-402`. A code is expired only when the instant is strictly after `expires_at`. Reason: BR-024 and US-006-06 say "after"; BR-020 and BR-026 say "expires" and BR-033's boundary clause is corrupted (`GAP-BE-306`). Confirmed by: a decision on `GAP-BE-306`.
- `ASM-BE-403`. A refused send writes no `send_attempts` row. Reason: otherwise a caller who keeps retrying is never released from the window. Confirmed by: a rule from the product owner.
- `ASM-BE-404`. `failed_count` is reset to 0 on entering a lock, so it ranges 0 to 4. Reason: otherwise the first wrong code after the lock would re-lock. Confirmed by: a rule from the product owner.
- `ASM-BE-405`. Only the latest challenge for an email is checked at verification. Reason: `GAP-BE-083` is open and one live code is the narrower reading. Consequence: submitting an older code after a newer one was issued is judged against the newer challenge. Confirmed by: a decision on `GAP-BE-083`.
- `ASM-BE-406`. Only a wrong code against the latest unexpired, unconsumed challenge counts toward the lock; expired, used and no-challenge refusals do not. Reason: BR-022 says "wrong codes"; an expired or used code is refused, not wrong. Confirmed by: a rule from the product owner.
- `ASM-BE-407`. Retention clocks: `issued_at` for challenges, `requested_at` for send attempts, `updated_at` for locks. Reason: NFR-019 gives periods but no basis (`GAP-BE-603`). Confirmed by: a decision on `GAP-BE-603`.
- `ASM-BE-408`. A verify refused because of a lock changes no state. Reason: BR-022 refuses verification during the lock and states no extension. Confirmed by: a rule from the product owner.
- `ASM-BE-409`. The verify decision order of 3.4, and that a verify with no challenge for the email returns OTP_CODE_INVALID and is not counted. Reason: `GAP-BE-601`; this does not reveal whether the address exists (`ASM-BE-400`). Confirmed by: a decision on `GAP-BE-601`.

No new `CON-` or `Q-` is raised by this module. Contradictions and questions that touch it are the ones listed above, defined in the project spec.
<!-- /SECTION: backend.auth.gaps -->
