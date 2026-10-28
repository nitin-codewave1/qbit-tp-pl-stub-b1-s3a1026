---
id: agent-developer/notes_api/backend/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/auth-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 825ae1d3-abc3-5c68-8294-c18eb9926d6e
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

**Responsibility.** The `auth` module provides email one-time-code sign-in. It is an additional sign-in method, and existing login methods are unchanged. [requirements.md NFR-016; backend.overview] It owns:

1. Issuing a 6-digit numeric code for an email address and handing it to the email transport. Operation `API-AUTH-707260`. [requirements.md BR-029; stories.md US-006-06]
2. Limiting code requests to 3 within 10 minutes per email address. Requests beyond that are refused with 415 and send no code. [requirements.md BR-021, BR-028, BR-032]
3. Verifying a code. A code is valid for 10 minutes from issue and is single-use. [requirements.md BR-020, BR-023, BR-024] Operation `API-AUTH-692907`.
4. Counting wrong codes and locking the address for 15 minutes after 5. Verification is refused during the lock. Requesting a code is not refused during the lock but stays subject to the resend limit. [requirements.md BR-022, BR-025, BR-027, BR-032]
5. Creating the User on first successful verification. [backend.overview ASM-BE-025]
6. Issuing a credential on successful verification. The form is `Q-BE-012` and the lifetime is `GAP-BE-302`.
7. Owning USER, OTP_CHALLENGE, SEND_ATTEMPT and LOGIN_LOCK data. [backend.module-map]

**Not responsible for.**

- The existing authentication method (`GAP-BE-085`), or the authorisation checks that `notes` and `search` apply on protected routes.
- Note ownership and the note access rule (`CON-BE-003`).
- Email delivery. This module hands the message to the transport (`ASM-BE-027`). The transport region is `Q-BE-005` and `CON-BE-006`.
- Sign-in screens and their messages. The web-app surface owns them. [stories.md US-006-06]
- Scope guardrail routing, the OpenAPI document and the global error envelope. Those belong to `scope-guardrail`, `api-docs` and `backend.error-model`.
- Deletion of notes under NFR-019, which belongs to `notes` and is `BLOCKED` on `CON-BE-300`.

**Operations owned.** `API-AUTH-707260` and `API-AUTH-692907`, referenced by id only.

**Identifier note.** This module mints from its reserved block: `ASM-BE-306` onward, `GAP-BE-322` onward, `INV-BE-010` onward. It mints no `CON-` and no `Q-`. Other ids are cited from the project spec.
<!-- /SECTION: backend.auth.purpose -->
<!-- SECTION: backend.auth.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, nullability or constraints. The platform is PostgreSQL 16 [platform.md]. The types below are a proposal (`ASM-BE-311`). Entity and column names follow `backend.domain-model`, with one deviation recorded in `ASM-BE-308`.

**users**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `user_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | unique, normalised form (`ASM-BE-015`, `GAP-BE-031`) |
| `created_at` | timestamptz | NOT NULL | none | set at first successful verification only (INV-BE-003) |

PII: `email` (`ASM-BE-019`). Whether the existing authentication shares this table is `GAP-BE-327`.

**otp_challenges**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `challenge_id` | uuid | NOT NULL | none | primary key |
| `email` | text | NOT NULL | none | normalised; no foreign key to `users` (the user may not exist yet) |
| `code_digest` | text | NOT NULL | none | storage form undecided (`GAP-BE-033`); the issued code is 6 numeric digits (BR-029) |
| `issued_at` | timestamptz | NOT NULL | none | |
| `expires_at` | timestamptz | NOT NULL | none | `issued_at` + 10 minutes (BR-024); `CHECK (expires_at > issued_at)` |
| `consumed_at` | timestamptz | NULL | none | set on success; `CHECK (consumed_at IS NULL OR consumed_at >= issued_at)` |

Index: btree `(email, issued_at DESC)`. It serves the lookup of the latest challenge at verification.

**send_attempts**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `send_attempt_id` | bigint identity | NOT NULL | generated | primary key |
| `email` | text | NOT NULL | none | normalised |
| `requested_at` | timestamptz | NOT NULL | none | |

Index: btree `(email, requested_at)`. It serves the count of attempts in the trailing 10 minutes. Only accepted requests write a row (`ASM-BE-312`).

**login_locks**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `email` | text | NOT NULL | none | primary key, normalised |
| `failed_count` | integer | NOT NULL | 0 | `CHECK (failed_count BETWEEN 0 AND 5)` |
| `locked_until` | timestamptz | NULL | none | set only at lock entry; `locked_until` null means no lock |

`failed_count` lives here, per email, because the counting default is per normalised email with no time window (`ASM-BE-305`). This replaces the per-challenge `failed_attempts` column in the project domain model (`ASM-BE-308`).

**Referential integrity.** No foreign keys, so no `ON DELETE` behaviour. Tables are linked by normalised email value.

**Time.** Every instant is `timestamptz`.

**Encryption and PII.** Email addresses and `code_digest` are personal or sensitive data. No input states encryption at rest (`GAP-BE-033`, `GAP-BE-304`), so none is claimed.
<!-- /SECTION: backend.auth.data-model -->
<!-- SECTION: backend.auth.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

All state is per normalised email address (`ASM-BE-015`).

### 3.1 OTP challenge

States: `ISSUED`, `CONSUMED` (terminal), `EXPIRED` (terminal, derived by time: now is after `expires_at`, boundary `ASM-BE-307`, `GAP-BE-306`).

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `ISSUED` | send accepted | fewer than 3 accepted send attempts in the trailing 10 minutes | challenge row created; send attempt row written; code handed to transport |
| `ISSUED` | `CONSUMED` | correct code | email not locked; challenge is the latest unconsumed (`ASM-BE-309`); not expired | `consumed_at` set; user created if absent; `failed_count` set to 0; credential issued |
| `ISSUED` | `ISSUED` | wrong code | email not locked; not expired | `failed_count` incremented |
| `ISSUED` | `EXPIRED` | time passes | now after `expires_at` | none |
| `CONSUMED` | none | any verify | | refused (INV-BE-002) |
| `EXPIRED` | none | any verify | | refused as expired |

### 3.2 Lock (per email)

States: `UNLOCKED` (`locked_until` null or past), `LOCKED` (`locked_until` in the future). Neither is terminal.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| `UNLOCKED` | `LOCKED` | the 5th wrong code | `failed_count` reaches 5 (`ASM-BE-305`) | `locked_until` = now + 15 minutes; `failed_count` reset to 0 (`ASM-BE-313`) |
| `LOCKED` | `UNLOCKED` | time passes | now at or after `locked_until` | none (derived, no write) |
| `LOCKED` | `LOCKED` | verify, with any code | | refused; `failed_count` and `locked_until` unchanged (`ASM-BE-306`, INV-BE-010) |
| `LOCKED` | `LOCKED` | send | send limit not reached | accepted; new challenge issued (BR-032) |

### 3.3 Resend limiter

This is a count, not a state. A send is accepted when fewer than 3 accepted `send_attempts` fall in the trailing 10 minutes (sliding window `ASM-BE-044`). The first request counts (`CON-BE-011`, reading used: every request counts).

### 3.4 Refusals

| Attempt | Result | Status |
|---|---|---|
| send beyond the limit | refused, no code, no email (INV-BE-006) | 415 [BR-028; US-004-03] |
| verify while locked | refused, locked-account response | `GAP-BE-038` |
| verify with wrong, expired or consumed code | refused | `GAP-BE-053` |
| verify with no challenge for the email | refused | `GAP-BE-053`, counting `GAP-BE-328` |

Order of checks when several apply: `GAP-BE-323`.
<!-- /SECTION: backend.auth.state-machine -->
<!-- SECTION: backend.auth.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-AUTH-FR-001 Issue a code
**Given** an email address and fewer than 3 accepted send attempts for its normalised form in the trailing 10 minutes,
**When** `API-AUTH-707260` is called,
**Then** a 6-digit numeric code is generated, a challenge with `expires_at` = `issued_at` + 10 minutes is stored, a send attempt row is written, the code is handed to the email transport, and success is returned (200, `ASM-BE-310`).
- **Traces to.** US-006-06; BR-020, BR-024, BR-029.
- **Verification.** `test-auth-send-issues-code` (planned).
- **API.** `API-AUTH-707260`.
- **Status.** The transport region is `BLOCKED` on `Q-BE-005`, `CON-BE-006`. Input validation is `GAP-BE-035`. Code generation source is `GAP-BE-325`.

### BE-AUTH-FR-002 Enforce the resend limit
**Given** 3 accepted send attempts for the email in the trailing 10 minutes,
**When** `API-AUTH-707260` is called,
**Then** the request is refused with 415, no challenge is created, no email is sent, and no send attempt row is written (`ASM-BE-312`).
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
**Then** `consumed_at` is set, a user row is created if none exists, `failed_count` is set to 0, a credential is issued, and success is returned (200, `ASM-BE-310`).
- **Traces to.** BR-023; US-006-06; ASM-BE-025; ASM-BE-305.
- **Verification.** `test-auth-verify-success` (planned).
- **API.** `API-AUTH-692907`.
- **Status.** The credential is `BLOCKED` on `Q-BE-012`. Who may verify is `BLOCKED` on `Q-BE-003`.

### BE-AUTH-FR-005 Reject an expired code
**Given** a challenge issued at T,
**When** `API-AUTH-692907` is called at an instant after T + 10 minutes,
**Then** verification is refused as expired and no credential is issued.
- **Boundary.** The instant exactly T + 10 minutes follows `ASM-BE-307` and `GAP-BE-306`.
- **Traces to.** BR-020, BR-024, BR-026; US-006-06.
- **Verification.** `test-auth-verify-expired`, `test-auth-verify-expiry-boundary` (planned, injectable clock).
- **API.** `API-AUTH-692907`. Status `GAP-BE-053`.

### BE-AUTH-FR-006 Single use
**Given** a challenge already consumed,
**When** `API-AUTH-692907` is called with its code,
**Then** verification is refused and no credential is issued.
- **Concurrency.** Two simultaneous correct submissions of one code result in exactly one success (INV-BE-011).
- **Traces to.** BR-023.
- **Verification.** `test-auth-verify-single-use`, `test-auth-verify-concurrent-single-use` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-007 Count a wrong code
**Given** the email is not locked and the latest challenge is unexpired and unconsumed, and the submitted code is wrong,
**When** `API-AUTH-692907` is called,
**Then** verification is refused and `failed_count` is incremented by 1.
- **Traces to.** BR-022, BR-025, BR-027; ASM-BE-305.
- **Verification.** `test-auth-verify-wrong-counts` (planned).
- **API.** `API-AUTH-692907`. Status `GAP-BE-053`.

### BE-AUTH-FR-008 Lock after 5 wrong codes
**Given** `failed_count` is 4 and the email is not locked,
**When** a 5th wrong code is submitted,
**Then** `locked_until` is set to now + 15 minutes and `failed_count` is reset to 0.
- **Traces to.** BR-022, BR-025, BR-027; stories.md US-006-06.
- **Verification.** `test-auth-lock-on-fifth-wrong` (planned).
- **API.** `API-AUTH-692907`.

### BE-AUTH-FR-009 Refuse verification during a lock
**Given** the email is `LOCKED`,
**When** `API-AUTH-692907` is called, with a correct or a wrong code,
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
<!-- /SECTION: backend.auth.requirements -->
<!-- SECTION: backend.auth.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-707260` | POST | `/auth/otp/send` | `auth` | 204 | 400, 415, 500 | Request a 6-digit email one-time sign-in code. Issues and emails a code valid for 10 minutes. At most 3 code requests per email address within 10 minutes; requests beyond the limit are refused with 415. Still permitted (but subject to the limit) while the address is locked. |
| `API-AUTH-692907` | POST | `/auth/otp/verify` | `auth` | 200 | 400, 401, 403, 423, 429, 500 | Verify a one-time code for an email address. On first successful verification a User is created; on success the code is consumed (single use) and a credential is issued. Refused while the address is locked (15 minutes after 5 wrong codes), and for wrong, expired (after 10 minutes) or already-used codes. |

### `API-AUTH-707260` — POST `/auth/otp/send`

Request a 6-digit email one-time sign-in code. Issues and emails a code valid for 10 minutes. At most 3 code requests per email address within 10 minutes; requests beyond the limit are refused with 415. Still permitted (but subject to the limit) while the address is locked.

**Request:** 
- `email` (string, required): Email address to send the one-time code to. Personal data (PII); normalisation and format validation rules are not stated in the inputs.
**Response:** none

### `API-AUTH-692907` — POST `/auth/otp/verify`

Verify a one-time code for an email address. On first successful verification a User is created; on success the code is consumed (single use) and a credential is issued. Refused while the address is locked (15 minutes after 5 wrong codes), and for wrong, expired (after 10 minutes) or already-used codes.

**Request:** 
- `email` (string, required): Email address the code was sent to. Personal data (PII).
- `code` (string, required): The 6-digit numeric one-time code received by email.
**Response:** 
- `credential` (object, required): Credential issued on successful verification. Its form (body token, cookie or other) and lifetime are not stated in the inputs and are an open blocking question; shape to be confirmed.
<!-- /SECTION: backend.auth.api-contract -->
<!-- SECTION: backend.auth.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

Project invariants that hold here: INV-BE-002 (consumed code refused), INV-BE-003 (user created only on verification), INV-BE-004 (expiry), INV-BE-005 (no verification while locked), INV-BE-006 (limit breach sends nothing). They are cited, not redefined.

- **INV-BE-010.** `locked_until` is written only at lock entry. No later attempt, correct or wrong, shortens or extends it. [BR-022, BR-027]
- **INV-BE-011.** A challenge is consumed at most once, and a credential is issued only by the operation that sets its `consumed_at`. [BR-023]
- **INV-BE-012.** No response from either operation reveals whether a User exists for the address. (Derived, BE-AUTH-FR-011.)
- **INV-BE-013.** For any email, accepted send attempts in any trailing 10-minute window never exceed 3. [BR-028]
- **INV-BE-014.** Neither a response nor a log entry contains a code, and no log entry contains an unredacted email address. [NFR-018]
<!-- /SECTION: backend.auth.invariants -->
<!-- SECTION: backend.auth.authorization | status: BLOCKED -->
## 7. Authorization

**BLOCKED.** The eligibility rule is open: who may request, verify or receive a code (`Q-BE-003`, `CON-BE-003`). BR-030 is corrupted as supplied. What would unblock it: a corrected BR-030 or an answer to `Q-BE-003`.

What can be stated now, from `backend.auth-model`:

| Operation | Unauthenticated caller | Team member | Other roles |
|---|---|---|---|
| `API-AUTH-707260` | allowed (derived: the operation exists to authenticate a caller) | `Q-BE-003` | no grant stated, refused (`ASM-BE-302`) |
| `API-AUTH-692907` | allowed (same derivation) | `Q-BE-003` | no grant stated, refused (`ASM-BE-302`) |

**Ownership predicate.** None. Both operations act on the email address in the request and on no user-scoped resource. Any eligibility check on the address is `BLOCKED` on `Q-BE-003`.

**Credential check on protected routes.** Out of this module's scope until the credential form is decided (`Q-BE-012`).
<!-- /SECTION: backend.auth.authorization -->
<!-- SECTION: backend.auth.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

**Retention sweep.** NFR-019 states retention periods as numbers in a corrupted sentence. Read positionally (`ASM-BE-303`): code challenges 1 day, send attempts 1 day, account locks 5 days. Rows older than the period are deleted. The scheduler, frequency and batching are `GAP-BE-101`. Deleting a challenge or send attempt after one day does not weaken the 10-minute limit and expiry, which look back only 10 minutes.

**Lock expiry.** Needs no job. It is derived at read time.

**Retries.** The email transport has no retry policy in any input (`GAP-BE-326`).

**Idempotency.** No idempotency key is stated for either operation (`GAP-BE-324`). Each accepted send counts toward the limit, so a client retry consumes one of the 3 allowed.
<!-- /SECTION: backend.auth.jobs -->
<!-- SECTION: backend.auth.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

**Unit** (injectable clock, no database):
- Window arithmetic: the 3rd request accepted, the 4th refused, a request after the oldest leaves the window accepted.
- Expiry just before, at and just after T + 10 minutes (boundary per `ASM-BE-307`).
- Counter: 4 wrong codes do not lock, the 5th does, success resets the count.
- Lock: verification refused for 15 minutes, accepted after, correct code refused during.
- Email normalisation (once `GAP-BE-031` is closed).

**Integration** (PostgreSQL 16):
- Send then verify end to end, creating the user exactly once across repeated sign-ins.
- Concurrent verification of one code yields one success (INV-BE-011).
- Concurrent sends at the limit accept at most 3 (INV-BE-013).
- Send during a lock is accepted and verify is refused.
- A challenge issued during a lock is usable after the lock lifts, if it is still unexpired.
- Retention sweep deletes only rows older than their period.

**Contract:**
- Both operations match their rendered contract, including 415 on a limit breach.
- Responses do not distinguish known from unknown addresses.
- Statuses for the locked, wrong, expired and used cases are asserted only after `GAP-BE-038` and `GAP-BE-053` close.
<!-- /SECTION: backend.auth.test-plan -->
<!-- SECTION: backend.auth.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps

Items are defined once in the project spec's open-item register and cited here.

**Blocking this module**

| ID | Effect |
|---|---|
| `Q-BE-003` | Eligibility to request, verify or receive a code. Blocks `backend.auth.authorization`. |
| `Q-BE-005`, `CON-BE-006` (Critical) | Email transport region and PII path. Blocks the transport step of BE-AUTH-FR-001. |
| `Q-BE-012` | Credential form. Blocks the credential step of BE-AUTH-FR-004. |
| `CON-BE-003` (Critical) | Shared-workspace default against open eligibility. |

**Cited gaps:** `GAP-BE-024` (default `ASM-BE-305`), `GAP-BE-031`, `GAP-BE-033`, `GAP-BE-035`, `GAP-BE-038`, `GAP-BE-053`, `GAP-BE-083`, `GAP-BE-085`, `GAP-BE-101`, `GAP-BE-104`, `GAP-BE-302`, `GAP-BE-304`, `GAP-BE-306`, `CON-BE-011`.

**New gaps**

| ID | Gap | Closing artefact |
|---|---|---|
| `GAP-BE-322` | The default in `ASM-BE-312` (refused sends write no row) is unconfirmed. Writing a row on refusal would keep a persistent caller blocked indefinitely. | rule from the product owner |
| `GAP-BE-323` | Order of refusal reasons when several apply (locked, expired, consumed, wrong). | rule from the product owner |
| `GAP-BE-324` | No idempotency or retry-safety rule for send. | decision from the product owner |
| `GAP-BE-325` | Source of code randomness is not stated. | security requirement |
| `GAP-BE-326` | Behaviour when the transport fails: error returned, and whether the attempt counts toward the limit. | decision from the product owner |
| `GAP-BE-327` | Whether the existing authentication shares the users table. | description of the existing authentication (`GAP-BE-085`) |
| `GAP-BE-328` | Whether a wrong code with no active challenge counts toward the 5. | rule from the product owner |

**New assumptions**

| ID | Assumption | Reason | Confirmed by |
|---|---|---|---|
| `ASM-BE-306` | A verify attempt refused because of a lock changes neither `failed_count` nor `locked_until`. | BR-022 refuses verification during the lock and states no extension. | rule from the product owner |
| `ASM-BE-307` | A code is expired only when the instant is strictly after issue + 10 minutes. | BR-024 and US-006-06 say "after". BR-020 and BR-026 say "expires", so `GAP-BE-306` stays open. | decision on `GAP-BE-306` |
| `ASM-BE-308` | The wrong-code counter is a column of `login_locks`, not of `otp_challenges`. | `ASM-BE-305` keys the count per email, not per challenge. | `GAP-BE-024` closing artefact |
| `ASM-BE-309` | Only the latest unconsumed challenge for an email is checked at verification. | No input says whether earlier codes stay valid (`GAP-BE-083`). One live code is the narrower reading. | rule from the product owner |
| `ASM-BE-310` | Both operations return 200 on success; send returns an empty body. | No success status is stated for either. | contract decision |
| `ASM-BE-311` | Column types: uuid keys, text email, timestamptz instants, integer counter. | PostgreSQL 16 is the platform and no types are supplied. | platform decision |
| `ASM-BE-312` | A refused send writes no `send_attempts` row. | Otherwise a caller that keeps retrying is never released from the window. | `GAP-BE-322` |
| `ASM-BE-313` | `failed_count` is reset to 0 when a lock is entered. | Otherwise the first wrong code after the lock would re-lock immediately. | rule from the product owner |
<!-- /SECTION: backend.auth.gaps -->
