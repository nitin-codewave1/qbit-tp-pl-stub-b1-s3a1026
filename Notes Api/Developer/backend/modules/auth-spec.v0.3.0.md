---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: [notes_api:US-006-06]
commit_kind: fork
confidence: agent
run_id: cbf4f6bb-f33c-5c3c-8b4c-5b95b8794990
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

**Responsibility.** The `auth` module provides email one-time-code sign-in. It is an additional sign-in method; existing login methods are unchanged. [requirements.md NFR-016; backend.overview] It owns:

1. Issuing a 6-digit numeric code for an email address and handing it to the email transport (`API-AUTH-707260`). [requirements.md BR-029; stories.md US-006-06]
2. Limiting code requests to 3 within 10 minutes per normalised email address. Requests beyond the limit are refused with 415 and send nothing. [requirements.md BR-021, BR-028, BR-032]
3. Verifying a code (`API-AUTH-692907`). A code is valid for 10 minutes from issue and is single-use. [requirements.md BR-020, BR-023, BR-024]
4. Counting wrong codes and locking the address for 15 minutes after 5. Verification is refused during the lock. Requesting a code is not refused during the lock but stays subject to the resend limit. [requirements.md BR-022, BR-025, BR-027, BR-032]
5. Creating the User on first successful verification (`ASM-BE-025`).
6. Issuing a credential on successful verification. Its form is `Q-BE-012` and its lifetime is `GAP-BE-302`.
7. Owning the data of USER, OTP_CHALLENGE, SEND_ATTEMPT and LOGIN_LOCK. [backend.module-map]
8. Retention deletion of its own login records (`ASM-BE-303`).

**Not responsible for.**

- The existing authentication method (`GAP-BE-085`), or the authorisation checks that `notes` and `search` apply on protected routes.
- Note ownership and the note access rule (`CON-BE-003`).
- Email delivery. This module hands the message to the transport (`ASM-BE-027`). The transport region is `Q-BE-005` and `CON-BE-006`.
- Sign-in screens and their messages, which the web-app surface owns. [stories.md US-006-06]
- Scope-guardrail routing, the OpenAPI document and the global error envelope. Those belong to `scope-guardrail`, `api-docs` and `backend.error-model`.
- Retention deletion of notes. It belongs to `notes` and is `BLOCKED` on `CON-BE-300`.

**Operations owned.** `API-AUTH-707260` and `API-AUTH-692907`, referenced by id only.

**Identifier note.** This module mints from its reserved block: `ASM-BE-503` onward, `GAP-BE-514` onward, `INV-BE-012` onward. It mints no `CON-` and no `Q-`. All other ids are cited from the project spec. The project spec's `INV-BE-010` refers to a lock-refusal assumption; in this module that assumption is `ASM-BE-503`.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, nullability or constraints. The platform is PostgreSQL 16 [platform.md]. The types below are a proposal (`ASM-BE-505`). Entity and column names follow `backend.domain-model`.

**users**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `user_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | unique, normalised form (`ASM-BE-015`, `GAP-BE-031`) |
| `created_at` | timestamptz | NOT NULL | none | set at first successful verification only (`INV-BE-003`) |

PII: `email` (`ASM-BE-019`). Whether the existing authentication shares this table is `GAP-BE-521`.

**otp_challenges**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `challenge_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | normalised; no foreign key to `users` (the user may not exist yet) |
| `code_digest` | text | NOT NULL | none | storage form undecided (`GAP-BE-033`); the issued code is 6 numeric digits (BR-029) |
| `issued_at` | timestamptz | NOT NULL | none | |
| `expires_at` | timestamptz | NOT NULL | none | `issued_at` + 10 minutes (BR-024); `CHECK (expires_at > issued_at)` |
| `consumed_at` | timestamptz | NULL | none | set on success; `CHECK (consumed_at IS NULL OR consumed_at >= issued_at)` |

Index: btree `(email, issued_at DESC)`. It serves the lookup of the latest challenge at verification. A second index on `issued_at` serves the retention sweep.

**send_attempts**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `send_attempt_id` | bigint identity | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | normalised |
| `requested_at` | timestamptz | NOT NULL | none | |

Index: btree `(email, requested_at)`. It serves the count of attempts in the trailing 10 minutes. Only accepted requests write a row (`ASM-BE-507`).

**login_locks**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key, normalised |
| `failed_count` | integer | NOT NULL | 0 | `CHECK (failed_count BETWEEN 0 AND 4)` (`ASM-BE-508`) |
| `locked_until` | timestamptz | NULL | none | set only at lock entry; null means never locked |
| `updated_at` | timestamptz | NOT NULL | none | last change; added to anchor retention (`ASM-BE-510`) |

`failed_count` lives here, per email, because the counting default is per normalised email with no time window (`ASM-BE-305`).

**Referential integrity.** No foreign keys, so no `ON DELETE` behaviour. Tables are linked by normalised email value.

**Time.** Every instant is `timestamptz`.

**Encryption and PII.** Email addresses and `code_digest` are personal or sensitive data. No input states encryption at rest (`GAP-BE-033`, `GAP-BE-304`), so none is claimed.
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

All state is per normalised email address (`ASM-BE-015`).

### 3.1 OTP challenge

States: `ISSUED`, `CONSUMED` (terminal), `EXPIRED` (terminal, derived by time: now is after `expires_at`; boundary `ASM-BE-504`, `GAP-BE-306`). A `SUPERSEDED` state is not defined, because whether a newer code invalidates an older one is `GAP-BE-083`; the default is `ASM-BE-506`.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `ISSUED` | send accepted | fewer than 3 accepted send attempts in the trailing 10 minutes | challenge row created; send attempt row written; code handed to the transport |
| `ISSUED` | `CONSUMED` | correct code | email not locked; challenge is the latest unconsumed (`ASM-BE-506`); not expired | `consumed_at` set; user created if absent; `failed_count` set to 0; credential issued |
| `ISSUED` | `ISSUED` | wrong code | email not locked; not expired | `failed_count` incremented |
| `ISSUED` | `EXPIRED` | time passes | now after `expires_at` | none |
| `CONSUMED` | none | any verify | | refused (`INV-BE-002`) |
| `EXPIRED` | none | any verify | | refused as expired; does not count toward the lock (`ASM-BE-509`) |

### 3.2 Lock (per email)

States: `UNLOCKED` (`locked_until` null or past), `LOCKED` (`locked_until` in the future). Neither is terminal.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| `UNLOCKED` | `LOCKED` | the 5th wrong code | `failed_count` reaches 5 (`ASM-BE-305`) | `locked_until` = now + 15 minutes; `failed_count` reset to 0 (`ASM-BE-508`) |
| `LOCKED` | `UNLOCKED` | time passes | now at or after `locked_until` | none (derived, no write) |
| `LOCKED` | `LOCKED` | verify, with any code | | refused; `failed_count` and `locked_until` unchanged (`ASM-BE-503`, `INV-BE-010`) |
| `LOCKED` | `LOCKED` | send | send limit not reached | accepted; new challenge issued (BR-032) |

### 3.3 Resend limiter

This is a count, not a state. A send is accepted when fewer than 3 accepted `send_attempts` fall in the trailing 10 minutes (sliding window, `ASM-BE-044`). The first request counts (`CON-BE-011`; reading used: every request counts).

### 3.4 Refusals

| Attempt | Result | Status |
|---|---|---|
| send beyond the limit | refused, no code, no email (`INV-BE-006`) | 415 [BR-028; US-004-03] |
| verify while locked | refused, locked-account response | `GAP-BE-038` |
| verify with a wrong, expired or consumed code | refused | `GAP-BE-053` |
| verify with no challenge for the email | refused | `GAP-BE-053`, counting `GAP-BE-515` |

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
- **Status.** The transport region is `BLOCKED` on `Q-BE-005`, `CON-BE-006`. Input validation is `GAP-BE-035`. Code generation source is `GAP-BE-516`. Behaviour when the transport fails is `GAP-BE-517`.

### BE-AUTH-FR-002 Enforce the resend limit
**Given** 3 accepted send attempts for the email in the trailing 10 minutes,
**When** `API-AUTH-707260` is called,
**Then** the request is refused with 415, no challenge is created, no email is sent, and no send attempt row is written (`ASM-BE-507`).
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
- **Status.** The credential is `BLOCKED` on `Q-BE-012`. Who may verify is `BLOCKED` on `Q-BE-003`.

### BE-AUTH-FR-005 Reject an expired code
**Given** a challenge issued at T,
**When** `API-AUTH-692907` is called at an instant after T + 10 minutes,
**Then** verification is refused as expired, no credential is issued, and `failed_count` is unchanged (`ASM-BE-509`).
- **Boundary.** The instant exactly T + 10 minutes follows `ASM-BE-504` and `GAP-BE-306`.
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
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-007 Count a wrong code
**Given** the email is not locked, the latest challenge is unexpired and unconsumed, and the submitted code is wrong,
**When** `API-AUTH-692907` is called,
**Then** verification is refused and `failed_count` is incremented by 1.
- **Traces to.** BR-022, BR-025, BR-027; `ASM-BE-305`, `ASM-BE-509`.
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
**Then** the email is treated as `UNLOCKED` and no write is needed to lift the lock.
- **Traces to.** BR-022.
- **Verification.** `test-auth-lock-expires` (planned, injectable clock).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-011 No user disclosure
**Given** any email address,
**When** either operation is called,
**Then** no response distinguishes an address that has a User from one that does not.
- **Traces to.** Derived from the abuse controls in BR-021 and BR-022, as stated in `backend.error-model`. Not cited in any input.
- **Verification.** `test-auth-no-user-disclosure` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### BE-AUTH-FR-012 Log redaction
**Given** any login event,
**When** it is logged,
**Then** the entry contains no unredacted email address and no code.
- **Traces to.** NFR-018. The redaction method is `GAP-BE-104`.
- **Verification.** `test-auth-log-redaction` (planned).

### BE-AUTH-FR-013 Retention deletion of login records
**Given** login records older than their retention period,
**When** the retention sweep runs,
**Then** challenges and send attempts older than 1 day and locks older than 5 days are deleted (`ASM-BE-303`).
- **Traces to.** NFR-019 (positional reading). Mechanism `GAP-BE-101`.
- **Verification.** `test-auth-retention-sweep` (planned).
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-707260` | POST | `/auth/otp/send` | `auth` | 204 | 400, 415, 500 | Request an email one-time sign-in code: 6 numeric digits, valid 10 minutes, at most 3 requests per 10 minutes per normalised email. Not refused during a lock. |
| `API-AUTH-692907` | POST | `/auth/otp/verify` | `auth` | 200 | 400, 401, 403, 423, 429, 500 | Verify an emailed one-time code. On success it creates the User if absent, marks the code consumed (single use) and issues a credential. Refused while the account is locked (5 wrong codes lock for 15 minutes). |

### `API-AUTH-707260` — POST `/auth/otp/send`

Request an email one-time sign-in code: 6 numeric digits, valid 10 minutes, at most 3 requests per 10 minutes per normalised email. Not refused during a lock.

**Request:** 
- `email` (string, required): Email address that receives the 6-digit code. Normalisation and format validation rules are not yet supplied (GAP-BE-031, GAP-BE-035). Personal data, never logged unredacted.
**Response:** none

### `API-AUTH-692907` — POST `/auth/otp/verify`

Verify an emailed one-time code. On success it creates the User if absent, marks the code consumed (single use) and issues a credential. Refused while the account is locked (5 wrong codes lock for 15 minutes).

**Request:** 
- `email` (string, required): Email address the code was sent to. Personal data. Normalisation per the unresolved GAP-BE-031.
- `code` (string, required): The 6-digit numeric one-time code received by email (BR-029). Never logged or echoed.
**Response:** 
- `credential` (string, required): Credential issued on successful verification. Its form (body token, cookie or other) and lifetime are unresolved (Q-BE-012, GAP-BE-302); this entry is provisional.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

Defined in the project spec and relied on here: `INV-BE-002` (a consumed code is refused), `INV-BE-003` (a User is created only on successful verification), `INV-BE-004` (a code after issue + 10 minutes is rejected), `INV-BE-005` (no verification succeeds while locked), `INV-BE-006` (a send beyond the limit creates nothing), `INV-BE-010` (a lock-refused verification changes no counter), `INV-BE-011` (exactly one success per code under concurrency).

Defined by this module:

- `INV-BE-012`. After every operation, `login_locks.failed_count` is between 0 and 4 (`ASM-BE-508`).
- `INV-BE-013`. `locked_until` changes only at lock entry. No other operation moves it. [requirements.md BR-022]
- `INV-BE-014`. For every challenge, `expires_at` equals `issued_at` plus 10 minutes. [requirements.md BR-024]
- `INV-BE-015`. No response body, log entry or error message contains a one-time code or an unredacted email address. [requirements.md NFR-018; `GAP-BE-104`]
- `INV-BE-016`. For one normalised email, at most one challenge is both unconsumed and unexpired and eligible for verification: the latest (`ASM-BE-506`).
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: BLOCKED -->
## 7. Authorization

**BLOCKED on `Q-BE-003` (who may request, verify or receive a code; `CON-BE-003`).** BR-030 as supplied is corrupted. No input grants more than the following.

| Operation | Unauthenticated caller | Authenticated team member | Other actors |
|---|---|---|---|
| `API-AUTH-707260` | Allow; no sign-in is needed to ask for a code [BR-032; US-006-06]. Eligibility is `Q-BE-003` | Allow (same) | Not stated, denied (`ASM-BE-302`) |
| `API-AUTH-692907` | Allow [BR-032; US-006-06]. Eligibility is `Q-BE-003` | Allow (same) | Not stated, denied (`ASM-BE-302`) |

**Ownership predicate.** None. A challenge, send attempt and lock are bound to a normalised email address, not to a user or session (`ASM-BE-029`). The caller proves control of the address by the code.

**Abuse controls.** The resend limit and lockout are per normalised email, not per client or IP, because no input names a client key (`ASM-BE-029`).

**Role model.** The roles model and existing authentication are `GAP-BE-085`.
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

**Retention sweep.** Deletes `otp_challenges` and `send_attempts` older than 1 day (by `issued_at` and `requested_at`) and `login_locks` older than 5 days (by `updated_at`, `ASM-BE-510`). [requirements.md NFR-019; `ASM-BE-303`] The scheduler and frequency are `GAP-BE-101`. Retention periods for the login records exceed the 10-minute and 15-minute functional windows, so the sweep never changes an outcome. A sweep that deletes a lock row also clears its `failed_count`; the interaction with retention basis is `GAP-BE-518`.

**Retries.** None defined by an input. A repeated send request is a new request, produces a new code and counts toward the limit (BR-028). A repeated verify with a consumed code is refused.

**Idempotency keys.** None. No input defines one, and send is deliberately not idempotent.

**Serialisation.** Check-and-write on the send limit and on the wrong-code counter is serialised per normalised email, so concurrent requests cannot exceed 3 sends or skip the 5th wrong code. This is derived from BR-028 and BR-022, and the mechanism is not specified (`GAP-BE-089`).
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

All time-dependent tests use an injectable clock.

**Unit.**
- Code generation yields exactly 6 numeric digits (`test-auth-code-format`; BR-029).
- Window arithmetic: 3 sends accepted, the 4th refused; the sliding window releases after 10 minutes (`test-auth-send-window-slides`).
- Expiry before, after and at the boundary (`test-auth-verify-expiry-boundary`; `ASM-BE-504`).
- Counter: 4 wrong codes leave `UNLOCKED`, the 5th locks, the counter resets (`test-auth-lock-on-fifth-wrong`).
- Email normalisation maps equivalent forms to one key, once `GAP-BE-031` closes (`test-auth-email-normalised`).

**Integration** (PostgreSQL 16).
- All of BE-AUTH-FR-001 to BE-AUTH-FR-013, named in each requirement.
- Concurrent correct submissions, exactly one success (`test-auth-verify-concurrent-single-use`).
- Concurrent sends at the limit, at most 3 accepted (`test-auth-send-concurrent-limit`).
- A refused send writes no row, and a lock-refused verify changes no column (`ASM-BE-507`, `ASM-BE-503`).
- No response or log contains a code or an unredacted address (`test-auth-log-redaction`).
- Retention sweep deletes only rows past their period (`test-auth-retention-sweep`).

**Contract.**
- `API-AUTH-707260` and `API-AUTH-692907` appear in the generated OpenAPI document with the 415 response on send and a locked-account response on verify. [stories.md US-004-03; BR-012]
- Responses conform to the global error envelope in `backend.error-model`.
- The scope-guardrail suite finds no auth path in an excluded category.

Fixtures use masked addresses only.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps, contradictions and assumptions

**Cited from the project spec (defined there, not here).**
- Questions: `Q-BE-003`, `Q-BE-005`, `Q-BE-012`.
- Contradictions: `CON-BE-003`, `CON-BE-006`, `CON-BE-011`, `CON-BE-300`.
- Gaps: `GAP-BE-024`, `GAP-BE-031`, `GAP-BE-033`, `GAP-BE-035`, `GAP-BE-038`, `GAP-BE-053`, `GAP-BE-083`, `GAP-BE-085`, `GAP-BE-089`, `GAP-BE-101`, `GAP-BE-104`, `GAP-BE-301`, `GAP-BE-302`, `GAP-BE-304`, `GAP-BE-306`.
- Assumptions: `ASM-BE-015`, `ASM-BE-019`, `ASM-BE-025`, `ASM-BE-027`, `ASM-BE-029`, `ASM-BE-044`, `ASM-BE-302`, `ASM-BE-303`, `ASM-BE-305`.

**Gaps raised by this module.**

| ID | Gap | Closing artefact |
|---|---|---|
| `GAP-BE-514` | The order of checks when several refusals apply (locked, expired, consumed, wrong, no challenge) is not stated. | A precedence decision from the product owner |
| `GAP-BE-515` | Whether a verify for an email with no challenge counts as a wrong code is not stated. | A counting rule from the product owner |
| `GAP-BE-516` | The code generation source (randomness requirement) is not stated. | A security policy |
| `GAP-BE-517` | What happens when the email transport fails after the challenge and send attempt are stored is not stated, including whether the attempt still counts. | A failure-handling decision |
| `GAP-BE-518` | Retention for locks (5 days, `ASM-BE-303`) could delete a non-zero `failed_count` that the no-window rule (`ASM-BE-305`) would otherwise keep. | A rule reconciling retention with the counting window |
| `GAP-BE-519` | The success statuses and response bodies of both operations are not stated in any input. | A contract decision, rendered by DevAgent |
| `GAP-BE-520` | No latency or availability target exists for either operation (see also `GAP-BE-301`). | A non-functional requirement |
| `GAP-BE-521` | Whether the existing authentication shares the `users` table is not stated. | A description of the existing authentication (`GAP-BE-085`) |

**Assumptions raised by this module.**

| ID | Assumption | Reason | Confirmed by |
|---|---|---|---|
| `ASM-BE-503` | A verify refused because of a lock changes neither `failed_count` nor `locked_until`. | BR-022 refuses verification during the lock and states no extension. | A rule from the product owner |
| `ASM-BE-504` | A code is expired only when the instant is strictly after issue + 10 minutes. | BR-024 and US-006-06 say "after". BR-020 and BR-026 say "expires", so `GAP-BE-306` stays open. | A decision on `GAP-BE-306` |
| `ASM-BE-505` | Column types: uuid keys, text email, timestamptz instants, integer counter, bigint identity. | PostgreSQL 16 is the platform and no types are supplied. | A platform decision |
| `ASM-BE-506` | Only the latest unconsumed challenge for an email is checked at verification. | No input says whether earlier codes stay valid (`GAP-BE-083`). One live code is the narrower reading. | A rule from the product owner |
| `ASM-BE-507` | A refused send writes no `send_attempts` row. | Otherwise a caller that keeps retrying is never released from the window. | A rule from the product owner |
| `ASM-BE-508` | `failed_count` is reset to 0 when a lock is entered, so it ranges 0 to 4. | Otherwise the first wrong code after the lock would re-lock immediately. | A rule from the product owner |
| `ASM-BE-509` | Only a wrong code against the latest unexpired, unconsumed challenge counts toward the lock. Expired and consumed codes do not. | BR-022 says "wrong codes". An expired or used code is not wrong, only refused. | A rule from the product owner |
| `ASM-BE-510` | `login_locks` has an `updated_at` column used as the retention basis. | NFR-019 gives a period but no basis for locks. | A rule from the product owner |
<!-- /SECTION: backend.auth.gaps -->
