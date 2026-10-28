---
id: agent-developer/notes_api/backend/BACKEND-SPEC.md
type: api_contract
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.10.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/BACKEND-SPEC.md
depends_on: [US-004-03, US-006-06, US-005-01, US-005-04, US-005-06, US-003-03, US-005-02, US-002-01, US-002-06, US-002-03, US-001-06, US-003-01, US-003-06, US-002-05, US-004-01, US-001-03, US-001-04, US-001-02, US-005-03, US-002-04, US-004-04, US-001-01, US-001-05, US-002-02, US-003-02, US-003-04, US-003-05, US-004-02, US-004-05, US-004-06, US-005-05, US-006-01, US-006-02, US-006-03, US-006-04, US-006-05]
commit_kind: fork
confidence: agent
run_id: 74f30224-6cf6-52b7-b8e8-fac4d2eb580c
okf_id: backend/BACKEND-SPEC.md
---

<!-- SECTION: backend.overview | status: DRAFT-UNVERIFIED -->
## 1. Overview

**Responsibility.** `notes_api` is the single backend service for a team note-taking product. [stories.md EP-002, EP-003, EP-004; requirements.md BR-013] It is responsible for:

1. **Application API.** Create, read, edit, pin and unpin, tag, archive, unarchive and search team notes, published as an OpenAPI document. [stories.md EP-002, EP-003, EP-004; requirements.md BR-012]
2. **Email one-time-code login (CR-123).** A caller requests a one-time code for an email address. The service emails the code. The caller verifies it and receives a credential. The login is an **additional** sign-in method alongside the existing authentication, so the existing login endpoints are not changed. [feedback.md, preflight answer on login method; feedback.md CR-123] Code expiry, resend limits and lockout after repeated wrong codes apply. [feedback.md CR-123; requirements.md BR-020 to BR-028; stories.md US-004-03]
   - **All numeric limits are withdrawn.** CR-123 withdraws the previous numeric limits for code expiry, resend count and window, wrong-code threshold and lock duration. [feedback.md CR-123] The numeric values in BR-020, BR-021, BR-022, BR-024, BR-025, BR-026, BR-027 and BR-028, and in the corresponding passages of US-004-03 and US-006-06, are not used in this document. Recorded as `CON-BE-014`.
   - The re-decisions are `Q-BE-008` (resend count and window), `Q-BE-009` (code expiry period) and `Q-BE-010` (wrong-code threshold and lock duration). All three are class `blocking`. [feedback.md CR-123]
   - Login is an interface change: consumers must adapt. [feedback.md CR-123; requirements.md NFR-015]
3. **EU data-residency compliance.** Provisioning `notes_api`'s own database, compute and backups in approved EU regions. Gating deploys on a residency check. Generating a compliance report. Enforcing a launch gate. Alerting on post-launch region drift. [stories.md EP-001; requirements.md BR-001 to BR-004, NFR-001 to NFR-004]

**Not responsible for.**

- Rendering any user interface. The web-app surface (EP-006) is a separate run. This document defines only the API that surface consumes. [stories.md EP-006]
- Attachment and file-upload endpoints. [requirements.md BR-014]
- Any real-time channel (WebSocket, SSE, polling-push). [requirements.md BR-015]
- Mobile-specific endpoints. [stories.md US-005-01, US-005-04]
- Logging and classifying stakeholder feature requests. This is a product-management intake process. [requirements.md BR-017; stories.md US-005-06]
- Any login method other than email one-time code and the existing authentication. The existing method is not named in any supplied input. `GAP-BE-085`.

**Change requests in this revision.**

- **CR-123 (this change request).** Email one-time-code login, as in capability 2. Note, tag, search and residency behaviour is carried forward unchanged from the approved revision. [feedback.md CR-123]
- **CR-112 (retained).** Notes carry a boolean `pinned` flag, default `false`, settable on create and via PATCH, returned in note responses. Pinned notes sort first in list and search results. Interface change. [requirements.md BR-018, BR-019, NFR-014; feedback.md CR-112]
- **Label discrepancy.** The change request is headed `CR-123`. The approved revision labels the same login content `CR-119`, and other inputs use `CR-118`, `CR-113`, `CR-114`, `CR-115` and `CR-116`. The content is the same. This document uses `CR-123` as the heading in feedback.md. Recorded as `CON-BE-017` (`CR-123` against `CR-119`) and `CON-BE-013`, `CON-BE-015`, `CON-BE-005`, `CON-BE-008`, `CON-BE-009` for the older labels.

**Corrupted passages in the inputs.** Several inputs contain login-limit text pasted into unrelated sentences. Examples: AC-001-02-02 (backup config validation), US-003-03 (search performance), US-005-02 (multipart rejection), NFR-006 (search performance), and US-004-03 and US-006-06 (login). In each case the substituted text is not used as a requirement. Only the unaffected part of the sentence is used. Recorded as `CON-BE-016`.

**Stack.** Node.js 20, Express, PostgreSQL 16, hosted on AWS. [platform.md; feedback.md preflight answers] The approved revision specifies AWS SES for transactional email. CR-123 names no transport. `ASM-BE-027`. The approved revision specifies reuse of the application's existing session or token mechanism. The preflight answers do not restate this. `ASM-BE-028`. No ORM, migration tool or other library is named by any input. `GAP-BE-089`.

**Run mode.** `RUN-CONTEXT` declares `bootstrap`. feedback.md supplies the approved revision as the baseline and instructs the run to keep what CR-123 does not touch. `ASM-BE-026`.

**Identifier continuity.** New identifiers continue from the highest observed in the approved revision. No identifier is reused. New in this run: `GAP-BE-093` to `GAP-BE-095`, `ASM-BE-040` to `ASM-BE-041`, `CON-BE-016` to `CON-BE-018`, `Q-BE-011`. Carried forward unchanged: `GAP-BE-004`, `GAP-BE-005`, `GAP-BE-007`, `GAP-BE-041` to `GAP-BE-052`, `GAP-BE-071` to `GAP-BE-082`, `Q-BE-004`, `HANDOFF-BE-002`, `HANDOFF-BE-003`, `HANDOFF-BE-004`. Their text is not in the supplied material, and no section below depends on it.

**Preflight resolution.** The preflight question on whether email login is additive or a replacement is answered: additive. [feedback.md] This closes the replacement question. It does not close `GAP-BE-085`, because the existing login method is still unnamed.

**Open blocking questions.**

- `Q-BE-003`: who may verify and receive access.
- `Q-BE-005`: the AWS region from which email is sent, and the residency of email transmission.
- `Q-BE-008`: resend count and window. Re-decision required by CR-123.
- `Q-BE-009`: code expiry period. Re-decision required by CR-123.
- `Q-BE-010`: wrong-code threshold and lock duration. Re-decision required by CR-123.
- `Q-BE-007` (degradable in the approved revision, carried as blocking): default is that only verification is refused during a lock, and sending remains subject to the resend limit. `DRAFT-UNVERIFIED`. Its resend limit is undetermined by `Q-BE-008`.
- `Q-BE-011` (degradable): whether the 6-digit code length in BR-029 stands. Default: it stands, because CR-123 withdraws limits and does not name code length. `ASM-BE-040`.

Under the composition rules, the blocking questions map to `HALT-BLOCKING-QUESTION`.

**Open Critical contradictions.**

- `CON-BE-003`: registration scope against the shared-workspace default. Touches an authorisation rule.
- `CON-BE-006`: email transmission against BR-001. Touches a PII path, because the email address is personal data sent to a transport whose region is unknown.
- `CON-BE-010`: lock counting. Consecutive wrong codes (BR-027) against cumulative wrong codes (BR-022, BR-025). CR-123 says "repeated wrong codes", which fits either reading. Changes a lock state machine.

These map to `HALT-CRITICAL-CONTRADICTION`.

**Halting.** The orchestrator decides whether to halt. This document records the work completed. Sections that depend on an open blocking question or Critical contradiction are marked `BLOCKED` or `DRAFT-UNVERIFIED` in place.

<!-- /SECTION: backend.overview -->
<!-- SECTION: backend.domain-model | status: DRAFT-UNVERIFIED -->
## 2. Domain model

Three sub-domains are evidenced: the **note-taking domain** (Note, Tag), the **login domain** (User, OTP challenge, send attempt, login lock) and the **residency-compliance domain** (InfraResource, DriftAlert). The login domain is revised in this change request. [feedback.md CR-123] No input relates these sub-domains beyond "notes_api's infrastructure hosts the note-taking data." [requirements.md BR-013]

```mermaid
erDiagram
  NOTE ||--o{ NOTE_TAG : "tagged by"
  TAG  ||--o{ NOTE_TAG : "applied to"
  USER {
    string user_id
    string email "PII; normalisation per GAP-BE-031"
    datetime created_at "set on first successful verification (INV-BE-003, ASM-BE-025)"
  }
  OTP_CHALLENGE {
    string challenge_id
    string email "keyed by email, no FK to USER"
    string code_digest "storage form per GAP-BE-033"
    datetime issued_at
    datetime expires_at "issued_at + expiry period (Q-BE-009)"
    int failed_attempts "counting semantics per CON-BE-010"
    datetime consumed_at "nullable; single-use (INV-BE-002)"
  }
  SEND_ATTEMPT {
    string email "normalised (ASM-BE-015)"
    datetime requested_at "window and count per Q-BE-008; shape per ASM-BE-037"
  }
  LOGIN_LOCK {
    string email "normalised (ASM-BE-015); no FK to USER"
    datetime locked_until "entry + lock duration (Q-BE-010)"
  }
  NOTE {
    string id
    string title
    string body
    string status "active or archived"
    boolean pinned "default false (BR-018)"
    datetime created_at
    datetime updated_at
  }
  TAG {
    string name
  }
  NOTE_TAG {
    string note_id
    string tag_name
  }
  INFRA_RESOURCE {
    string resource_id
    string resource_type "db, compute or backup-storage"
    string region
    string compliance_status "pass, fail or unverified"
    datetime last_scanned_at
  }
  DRIFT_ALERT {
    string id
    string resource_id
    string detected_region
    datetime raised_at
  }
```

**Entities.**

- **Note.** Title, body, status (active or archived), `pinned`, created and updated timestamps. [stories.md US-002-01 to US-002-06] Title and body are mandatory and non-empty at creation. [requirements.md BR-005] Archiving never deletes data. [requirements.md BR-007, BR-008]
- **`pinned` (CR-112).** Boolean, default `false`. Settable on create and changeable via PATCH. Pinning does not alter title, body or tags. [requirements.md BR-018; stories.md AC-002-01-01, AC-002-01-02, AC-002-02-02] Nullability is not stated in any input. Proposed `NOT NULL` is `ASM-BE-034`.
- **Tag.** A name, many-to-many with Note through NOTE_TAG. Adding an existing tag is idempotent, with no duplicate entry. [stories.md US-002-03] Tag name format and length: `GAP-BE-009`. Cascade when a tag's last note is archived or deleted: `GAP-BE-016`.
- **User.** A person who has verified an email address. Created on first successful verification, not on code request. [INV-BE-003; ASM-BE-025] `email` is personal data. `ASM-BE-019`. Handling policy: `GAP-BE-033`.
- **OTP challenge.** One issued one-time code for one email address, keyed by email because the user may not yet exist. Expiry is the period in `Q-BE-009`, measured from issue. [feedback.md CR-123] Single-use. [requirements.md BR-023; INV-BE-002] Code length: BR-029 gives 6 digits, all numeric. [requirements.md BR-029] Character set and generation: `GAP-BE-026`, `GAP-BE-027`. Storage form: `GAP-BE-033`. Whether a resend invalidates an earlier unconsumed code: `GAP-BE-083`.
- **Send attempt.** One record per code request, kept so that a time-windowed resend limit can be evaluated. Its existence follows from "resend limits" in CR-123. Its columns are derived, not confirmed. `ASM-BE-037`. Count and window: `Q-BE-008`. Whether the initial send counts: `CON-BE-011`. Key: `Q-BE-006`, default per email (`ASM-BE-029`).
- **Login lock.** The lock state for an email address. Entered after wrong codes and held for the duration in `Q-BE-010`. [feedback.md CR-123] Keyed by normalised email: `ASM-BE-015`. Whether the wrong-code count is consecutive or cumulative, and whether it resets: `CON-BE-010` (Critical, blocked), `GAP-BE-024`.
- **InfraResource.** A provisioned notes_api component (DB, compute, backup storage) with its reported region and compliance status. Unrecognised resource types are flagged `unverified`, never silently omitted. [stories.md AC-001-04-01, AC-001-04-02]
- **DriftAlert.** A record of a post-launch detection of an InfraResource in a non-EU region. [stories.md US-001-06]

**Invariants.** These hold after every operation that touches the named entity.

- **INV-BE-001.** In `GET /notes` and `GET /notes/search`, every pinned note is returned before every unpinned note. Order within each group is unchanged. [requirements.md BR-019] Owned by `notes` for `GET /notes` and by `search` for `GET /notes/search`.
- **INV-BE-002.** A one-time code that has been verified successfully is refused on any later verification. [requirements.md BR-023]
- **INV-BE-003.** A User row is created only on successful verification, never on code request. [ASM-BE-025]
- **INV-BE-004.** A code submitted at or after issue plus the expiry period in `Q-BE-009` is rejected as expired. [feedback.md CR-123] No expiry value is asserted until `Q-BE-009` is answered.
- **INV-BE-005.** While an email address is locked, no verification succeeds, including with a correct code, until the lock expires. [requirements.md BR-022, BR-027; feedback.md CR-123 "lockout"]

**Cardinality and ownership.**

- User and Note have no relationship in the diagram. No input states whether a Note belongs to a user, is private, or is shared. `GAP-BE-003`. The default applied is `Q-BE-001` (degradable): notes form a shared team workspace, and any authenticated team member may read and write any note.
- **BLOCKED subpart.** `CON-BE-003` (Critical) calls that default into question. Under CR-123, any holder of an email address that verifies can obtain a credential, so "team member" no longer names a bounded group. Until `Q-BE-003` is answered, the shared-workspace default applies only to users created by a route the human has approved. Note ownership columns and the note-access rule are `BLOCKED`. See `backend.auth-model`.

**PII.** Personal data exists in this domain: `USER.email`, `OTP_CHALLENGE.email`, `SEND_ATTEMPT.email` and `LOGIN_LOCK.email`, which hold the same value. Note `title` and `body` are free text and may incidentally contain PII. No masking or handling policy is supplied for any of them: `GAP-BE-017` (general PII policy), `GAP-BE-033` (at-rest treatment), `GAP-BE-035` (validation scope for email and code fields). No encryption, redaction or retention claim is made in their absence.

<!-- /SECTION: backend.domain-model -->
<!-- SECTION: backend.module-map | status: DERIVED -->
## 3. Module map

| Module | Boundary | Depends on |
|---|---|---|
| `auth` | Email one-time-code login (CR-123): code issuance and delivery, verification, resend limits, lockout, code expiry, user creation on first verification, credential issuance through the existing session/token mechanism. Owns User, OTP_CHALLENGE, SEND_ATTEMPT, LOGIN_LOCK. Coexists with the existing authentication, which is unchanged. [feedback.md preflight] | external: email transport (`ASM-BE-027`; region blocked on `Q-BE-005`, `CON-BE-006`) |
| `notes` | Note CRUD, pin and unpin via PATCH, tag assignment and removal, archive and unarchive. Owns Note, Tag, NOTE_TAG. | `auth` (caller identity) |
| `search` | Read-only query over Note: keyword, tag filter, pagination, archived-exclusion default, pinned-first ordering. | `notes`, `auth` |
| `residency-compliance` | Provisioning region validation, backup-destination validation, CI/CD residency gate, compliance report, launch-gate status, post-launch drift scan and alerting. Owns InfraResource, DriftAlert. | none at runtime; operates on the infrastructure and CI/CD layer |
| `api-docs` | Publishes the OpenAPI document, including `pinned`, the ordering change, and both login endpoints. Computes the endpoint-coverage metric. Carries the data-residency note and the Scope section. | `notes`, `search`, `auth`, `residency-compliance` |
| `scope-guardrail` | Intercepts every inbound request ahead of routing. Rejects attachment and multipart, WebSocket and SSE-upgrade, and mobile-specific patterns with a structured out-of-scope response. Backs the contract test suite. | sits upstream of all modules; cross-cutting |

**Dependency direction.** `search → notes`, `search → auth`, `notes → auth`, `api-docs → notes, search, auth, residency-compliance`, `auth → email transport` (external). Requests flow through `scope-guardrail` first, then `auth` for protected routes, then `notes`, `search` or `api-docs`.

**Guardrail before authentication.** The guardrail runs before authentication, so an out-of-scope call returns its documented response without a credential. `ASM-BE-018`. The response form for excluded paths conflicts across inputs: `CON-BE-007`.

**CR-123 impact on boundaries.** `auth` is the module that changed. It is a new sign-in path alongside the existing one, not a replacement. It is the only module that calls the email transport. That dependency carries the residency question `CON-BE-006`. `notes` and `search` depend on `auth` for caller identity, as in the approved revision; CR-123 does not add a new dependency for them.

<!-- /SECTION: backend.module-map -->
<!-- SECTION: backend.api-surface | status: DRAFT-UNVERIFIED -->
## 4. API surface index

Full contracts (request and response schemas, every error, idempotency, rate limits) belong in the per-module `api-contract` sections, which this document does not produce. Schemas are referenced by `SCHEMA-` ID and are not restated here.

**Versioning.** An endpoint whose request or response shape changes is re-pinned from `@1` to `@2`. [ASM-BE-010] New endpoints start at `@1`. [ASM-BE-021] CR-123 identifies no shape change to the login endpoints, so they remain at `@1` (`ASM-BE-036`). If any consumer has integrated against `@1`, that assumption fails and both login endpoints re-pin to `@2`. Whether authenticated endpoints should bump because their caller obligation changes: `GAP-BE-037`.

| API ID | Method | Path | Auth | Module | Notes |
|---|---|---|---|---|---|
| `API-AUTH-001@1` | POST | `/auth/otp/send` | unauthenticated; resend-limited | `auth` | **Added (CR-123).** Issues a one-time code to an email address and emails it through the transport. Also the resend path. Requests beyond the resend limit are refused with 429 and send no code. [requirements.md BR-021, BR-028 (status retained, values withdrawn); stories.md US-004-03] Count and window: `Q-BE-008`. Initial send counts toward the limit: `CON-BE-011`. Limit key: `Q-BE-006`, default per email (`ASM-BE-029`). Response does not reveal whether an account exists (`ASM-BE-030`). Success status and body: `GAP-BE-028`. Whether a resend invalidates an earlier code: `GAP-BE-083`. Behaviour during an active lock: `GAP-BE-063`; `Q-BE-007` default applies. Transport failure: `GAP-BE-066`. Transport region: `Q-BE-005`, `CON-BE-006`. |
| `API-AUTH-002@1` | POST | `/auth/otp/verify` | unauthenticated; subject to lockout | `auth` | **Added (CR-123).** Verifies a code for an email address. Wrong, expired or already-used codes are rejected. [requirements.md BR-023, BR-024, BR-026 (expiry value withdrawn); INV-BE-002, INV-BE-004] Expiry period: `Q-BE-009`. After the wrong-code threshold, the address is locked and further verification is refused until the lock expires, including for a correct code (INV-BE-005). [requirements.md BR-022, BR-025, BR-027 (values withdrawn)] Threshold and duration: `Q-BE-010`. Lock counting: `CON-BE-010` (Critical, blocked). Locked-response status and body: `GAP-BE-038`. Wrong-code and expired-code statuses: `GAP-BE-053`. Unknown-email behaviour: `GAP-BE-065`. On success, creates the User if absent (INV-BE-003) and returns a credential from the existing session/token mechanism (`ASM-BE-028`, `GAP-BE-030`). |
| `API-NOTES-001@2` | POST | `/notes` | authenticated team member (`ASM-BE-031`) | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned` (boolean, default `false`). Returns `pinned` in the 201 body. Title and body rules unchanged. [stories.md AC-002-01-01, AC-002-01-02; requirements.md BR-005, BR-018] |
| `API-NOTES-002@2` | PATCH | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned`. Partial update: fields not sent are never overwritten. [requirements.md BR-006] Returns `pinned`. Pinning leaves title, body and tags unchanged. [stories.md AC-002-02-02] PATCH, not PUT: `ASM-BE-001`. Concurrent-write policy: `GAP-BE-040`. |
| `API-NOTES-003@2` | GET | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Returns full note detail including tags, archive status and `pinned`. [stories.md AC-002-06] A missing note returns 404 with no partial data. [stories.md US-002-06] |
| `API-SEARCH-???` | GET | `/notes/search` | authenticated team member | `search` | Keyword `q`, tag filter, pagination, archived exclusion by default with `include_archived`. [stories.md US-003-01 to US-003-06; requirements.md BR-009 to BR-011] Pinned-first ordering: INV-BE-001. The ID is not recoverable from the truncated approved revision: `GAP-BE-093`. |
| (not stated) | POST / DELETE | archive, unarchive, tag add and remove on a note | authenticated team member | `notes` | Operations exist in the stories and BR-007 and BR-008. [stories.md US-002-03 to US-002-05] No paths or IDs are stated. `GAP-BE-094`. |
| (not stated) | GET | OpenAPI document | unauthenticated (`ASM-BE-041`) | `api-docs` | Must list all endpoints, including login send and verify. [stories.md US-004-01; requirements.md BR-012] ID and path not stated. Access control: `GAP-BE-095`. |
| (not stated) | GET | compliance report | access not stated | `residency-compliance` | Access control: `GAP-BE-092`. |

**Out-of-scope routes.** Attachment paths return 404 and a structured out-of-scope response. [stories.md US-005-02, US-005-04] The response form is contested: `CON-BE-007`.

<!-- /SECTION: backend.api-surface -->
<!-- SECTION: backend.auth-model | status: BLOCKED -->
## 5. Auth model

**Status reason.** Blocked. `Q-BE-003` (who may verify and receive access) is open. `CON-BE-003` (Critical) calls the note-access rule into question. The actor set and the note-access rows below are provisional until both are resolved.

**Actors.**

| Actor | Source | Notes |
|---|---|---|
| Unauthenticated caller | [feedback.md CR-123] | May call `/auth/otp/send` and `/auth/otp/verify`. Rate-limited and lock-limited. |
| Email-code credential holder | [feedback.md CR-123] | Holds a credential issued by the login path. Authority beyond login is undefined. `Q-BE-003`. |
| Authenticated team member | [stories.md EP-002, EP-003] | Holds a credential from an existing or new path. Identity mechanism: `ASM-BE-028`, `ASM-BE-031`. The existing sign-in method is not named: `GAP-BE-085`. |
| Platform/DevOps Engineer, Compliance/Security Auditor, Engineering Lead | [stories.md EP-001] | Human consumers of residency outputs. Not API callers unless stated. Access to the report: `GAP-BE-092`. |
| Release/CI pipeline owner | [stories.md US-001-03] | Runs the residency gate. Credential for CI: not stated. `GAP-BE-092`. |

**Permission matrix (resource × operation × actor).** `Y` allowed, `N` refused, `?` not determined by inputs, `BLOCKED` pending `Q-BE-003` or `CON-BE-003`.

| Resource | Operation | Unauthenticated | Email-code holder | Authenticated team member | Auditor / DevOps / CI |
|---|---|---|---|---|---|
| Login | send code | Y, resend-limited (`Q-BE-008`) | Y | Y | N/A |
| Login | verify code | Y, lock-limited (`Q-BE-010`) | Y | Y | N/A |
| Note | create | N | BLOCKED (`CON-BE-003`) | Y | N |
| Note | read, list, search | N | BLOCKED (`CON-BE-003`) | Y (any note, `Q-BE-001` default) | N |
| Note | edit, pin, tag, archive, unarchive | N | BLOCKED (`CON-BE-003`) | Y (any note, `Q-BE-001` default) | N |
| OpenAPI document | read | Y (`ASM-BE-041`) | Y | Y | Y |
| Compliance report | read | ? (`GAP-BE-092`) | N | ? | ? (`GAP-BE-092`) |
| Residency gate | evaluate | N/A | N/A | N/A | CI credential, `GAP-BE-092` |
| Drift alert | receive | N/A | N/A | N/A | DevOps team (`GAP-BE-090`) |
| Out-of-scope routes | any | rejected before auth (`ASM-BE-018`) | rejected | rejected | rejected |

**Ownership predicate.** No input supplies a per-user ownership predicate for notes. The shared-workspace default is `Q-BE-001`. It is `BLOCKED` by `CON-BE-003`. Until `Q-BE-003` is answered, the default applies only to users created by a route the human has approved.

**Credential issuance.** On successful verification, the login path returns a credential from the existing session or token mechanism. `ASM-BE-028`, `GAP-BE-030`. No token lifetime, format or revocation policy is stated: `GAP-BE-030`.

**Lockout scope.** Lockout is keyed by normalised email, not by account, because the user may not exist yet. `ASM-BE-015`. Lock state is not visible to unauthenticated callers beyond the locked response. `GAP-BE-038`.

<!-- /SECTION: backend.auth-model -->
<!-- SECTION: backend.data-lifecycle | status: DRAFT-UNVERIFIED -->
## 6. Data lifecycle

**Stored data.**

| Store | Contents | Source |
|---|---|---|
| Notes | title, body, status, `pinned`, timestamps, tag links | stories.md US-002-01 to US-002-06; requirements.md BR-018 |
| Tags | name, note links | stories.md US-002-03 |
| Users | verified email address, creation time | `INV-BE-003`, `ASM-BE-025`, `ASM-BE-019` |
| OTP challenges | email, code storage form, issue time, expiry, failed attempts, consumed time | feedback.md CR-123; `GAP-BE-033` for storage form |
| Send attempts | email, request time | `ASM-BE-037` |
| Login locks | email, lock expiry | `ASM-BE-015`, `Q-BE-010` |
| Infrastructure resources | id, type, region, compliance status, last scan | stories.md US-001-04 |
| Drift alerts | resource id, detected region, time | stories.md US-001-06 |

**Retention.**

- **Notes.** Archived notes are kept indefinitely. Archiving never deletes data. [requirements.md BR-007, BR-008] Any period after which archived notes are removed: not stated. No hard-delete requirement exists.
- **OTP challenges.** An expired or consumed code has no stated retention period. `GAP-BE-086`.
- **Login-attempt records.** Retention is not stated. `GAP-BE-086`.
- **Send attempts.** Retained at least for the resend window. The window length is `Q-BE-008`. Any longer retention: not stated.
- **Login locks.** Retained until the lock expires. `INV-BE-005`, `Q-BE-010`.

**Deletion.** No deletion path is stated for users, notes or login records. Tag cascade on last-note removal: `GAP-BE-016`.

**Encryption at rest.** Not stated in any input. No claim is made. `GAP-BE-017`, `GAP-BE-033`. Core rule 12 applies: the claim is `GAP-`, not a default.

**Email transmission.** The email address is sent to the transport. The region of transmission is unknown. `Q-BE-005`, `CON-BE-006`. BR-001 requires EU provisioning of notes_api's own infrastructure but does not state whether email transport is in scope. `BLOCKED`.

**Backups.** BR-001 and US-001-02 require backups in approved EU regions. [requirements.md BR-001; stories.md AC-001-02-01] Backup retention periods: not stated. `GAP-BE-086`.

<!-- /SECTION: backend.data-lifecycle -->
<!-- SECTION: backend.nfr | status: DRAFT-UNVERIFIED -->
## 7. Non-functional requirements

Every target below is cited. No figure is invented. Where a target is absent, a `GAP-` is recorded.

| Area | Target | Source | Status |
|---|---|---|---|
| Residency coverage | 100% of infrastructure components EU-verified | requirements.md NFR-001 | VERIFIED |
| Residency gate | 0 non-EU deploys reach production | requirements.md NFR-002 | VERIFIED |
| Drift alert latency | alert fired within 15 minutes of drift detection | requirements.md NFR-003 | VERIFIED; conflicts with "immediately" in AC-001-06-02, `CON-BE-018` |
| Compliance report | refreshed and 100% accurate at each launch gate check | requirements.md NFR-004 | VERIFIED |
| CRUD correctness | 100% functional-test pass rate, zero data loss | requirements.md NFR-005 | VERIFIED |
| Search latency | p95 < 300 ms | requirements.md NFR-006; stories.md US-003-03 | VERIFIED |
| Search at page size | p95 < 300 ms up to page size 100 | requirements.md NFR-007 | VERIFIED |
| Search dataset | latency SLA met on a dataset of 100k notes via indexed lookup | stories.md US-003-03 | VERIFIED as stated; the indexing approach is not specified |
| Availability (CRUD, search) | 99.9% uptime monthly | requirements.md NFR-013 (Should Have) | VERIFIED |
| Security | 0 critical injection vulnerabilities per release | requirements.md NFR-012 | VERIFIED |
| Spec coverage | 100% of implemented endpoints in published spec | requirements.md NFR-008 | VERIFIED |
| Login latency | not stated | `GAP-BE-088` | `GAP-` |
| Login throughput | not stated | `GAP-BE-088` | `GAP-` |
| General throughput | not stated | `GAP-BE-088` | `GAP-` |
| Concurrent writes | not stated; US-005-03 asks for a "standard last-write/conflict response" without naming it | stories.md US-005-03; `GAP-BE-040` | `GAP-` |

**Notes on the table.**

- The 15-minute drift window (NFR-003) and the "immediately" wording (AC-001-06-02) are both recorded. `CON-BE-018`. This document does not choose between them.
- The search targets (NFR-006, NFR-007) are cited to requirements. The NFR-006 `category` and title contain corrupted text (`CON-BE-016`). The `target_metric` is intact and is used.

<!-- /SECTION: backend.nfr -->
<!-- SECTION: backend.error-model | status: DRAFT-UNVERIFIED -->
## 8. Error model

**Global envelope.** Not stated. `GAP-BE-087`. Until it is supplied, no envelope shape is asserted here, and every endpoint's error body is `GAP-`.

**Status-code policy.** Each status below is cited to the input that requires it. The machine-readable code for each is `GAP-` unless stated.

| Status | Condition | Source | Code |
|---|---|---|---|
| 400 | validation failure (missing title, empty or invalid `q`, invalid `tags`, malformed filter, invalid page or limit, invalid `include_archived`, empty or null body) | stories.md AC-002-01-03, US-003-01 to US-003-06; requirements.md BR-005 | `GAP-` (validation code not named) |
| 401 / 403 | unauthenticated or unauthorised request on protected routes | stories.md US-004-03 | `GAP-` |
| 404 | note not found; deleted or never-existing note; attachment path | stories.md AC-002-02-03, US-002-05, US-002-06, US-005-02 | `GAP-` |
| 406 or default format | unsupported Accept on the OpenAPI document | stories.md US-004-01 | `GAP-` |
| 409 | note already archived | stories.md US-002-04 | `GAP-` |
| 429 | login resend beyond the resend limit; no code is sent | requirements.md BR-028; stories.md US-004-03 | `GAP-` (limit values withdrawn: `Q-BE-008`) |
| locked-account response | verification during a lock | requirements.md BR-027; stories.md US-004-03 | status and body `GAP-BE-038`; limit values withdrawn: `Q-BE-010` |
| expired or wrong-code response | verification of an expired or wrong code | requirements.md BR-024, BR-026 | `GAP-BE-053` |
| 503 with `Retry-After` | search index degraded or unavailable | stories.md US-003-03 | `GAP-` (header behaviour stated; code not) |
| out-of-scope response | calls to mobile, attachment or real-time endpoints | requirements.md BR-016; stories.md US-005-04 | structured code e.g. `OUT_OF_SCOPE` (the example is stated as "e.g.") |
| residency violation | backup destination outside EU, validation before run | stories.md AC-001-02-02 | `GAP-` (name not stated; the residency-violation error is named in the story) |

**Error-code registry.**

| Code | Meaning | Source | Status |
|---|---|---|---|
| `OUT_OF_SCOPE` | out-of-scope endpoint or payload | stories.md US-005-04 (example) | `DRAFT-UNVERIFIED`; name is an example in the input |
| all others | not stated | `GAP-BE-087` | `GAP-` |

**Partial data.** A failed read returns no partial note data. [stories.md US-002-06]

**Login failure disclosure.** Responses must not reveal whether an email address has an account. `ASM-BE-030`. Whether that constraint covers the locked response: `GAP-BE-065`.

<!-- /SECTION: backend.error-model -->
<!-- SECTION: backend.observability | status: DRAFT-UNVERIFIED -->
## 9. Observability

**Logged.** Residency scan results, including "pass" entries. [stories.md US-001-06] Compliance report generation. [requirements.md NFR-004] Drift alerts with resource details. [stories.md AC-001-06-02] Login events: not stated. `GAP-BE-091`.

**Redacted.**

- One-time codes, code digests and any credential value are not written to any log. This follows core rule 12, which forbids writing secrets into artefacts. `ASM-BE-040`. The approved revision names no logging policy: `GAP-BE-091`.
- Email addresses are personal data. No masking policy is supplied: `GAP-BE-017`, `GAP-BE-035`.

**Metered.**

- Endpoint coverage: percentage of implemented endpoints present in the published spec. [requirements.md BR-012, NFR-008; stories.md US-004-04]
- Compliance: percentage of infrastructure EU-verified. [requirements.md NFR-001]
- Rejected out-of-scope requests. [requirements.md BR-017; stories.md US-005-06] This count is a product-management metric, produced by a process. It is not produced by this service unless `GAP-BE-091` names it.
- Login metrics (send count, lock count, verify failures): not stated. `GAP-BE-091`.

**Alerts.**

- Post-launch non-EU resource drift: alert to the DevOps team. [stories.md AC-001-06-02] Timing: `CON-BE-018` (immediately vs. 15 minutes). Delivery mechanism: `GAP-BE-090`.
- Failing residency check blocks the pipeline. [stories.md AC-001-03-02, AC-001-03-03] This is a build failure, not an alert.
- Launch gate status changes. [stories.md AC-001-05-01, AC-001-05-02]

**Health.** Search index degradation returns 503. [stories.md US-003-03] Health-check endpoint: not stated.

<!-- /SECTION: backend.observability -->
<!-- SECTION: backend.migration-plan | status: BLOCKED -->
## 10. Migration plan

**Status reason.** Partly blocked. The migration tool is not named (`GAP-BE-089`). The lock state machine is blocked by `CON-BE-010` (Critical). The `pinned` nullability is `ASM-BE-034`. Migrations are listed in the order required by their dependencies. Each is stated as reversible or not.

| Order | Migration | Contents | Reversible | Status |
|---|---|---|---|---|
| M1 | Add `pinned` to the notes table | boolean, default `false`, `NOT NULL` proposed | yes: drop column. Existing notes need a backfill to `false` (`ASM-BE-034`) | DRAFT-UNVERIFIED |
| M2 | Index for pinned-first ordering | composite on `pinned` and the existing sort key; query served: `GET /notes` and `GET /notes/search` (INV-BE-001) | yes: drop index | DERIVED |
| M3 | Create `users` table | verified email, creation time; unique on normalised email (`ASM-BE-015`) | yes, if no dependent data exists; must not be run until `Q-BE-003` is answered (`CON-BE-003`) | BLOCKED |
| M4 | Create `otp_challenges` table | email, storage form, issue, expiry, failed attempts, consumed time | yes: drop table; codes are short-lived, so loss of rows is acceptable but not stated by input. Storage form: `GAP-BE-033` | BLOCKED on `GAP-BE-033`, `Q-BE-009` for expiry semantics (expiry is computed at runtime, not stored as a constant) |
| M5 | Create `send_attempts` table | email, request time | yes: drop table | DRAFT-UNVERIFIED (`ASM-BE-037`, `Q-BE-008` for window) |
| M6 | Create `login_locks` table | email, lock expiry | yes: drop table | BLOCKED (`CON-BE-010`, `Q-BE-010`) |
| M7 | Add note-ownership columns | none specified | not applicable | BLOCKED (`CON-BE-003`, `Q-BE-001`) |

**Ordering rules.**

- M1 and M2 are additive and can be applied before any login migration.
- M3 must precede M4 and M6, because OTP and lock rows key by email with no FK to User (`INV-BE-003`), so M3 is not strictly required by M4 or M6. The ordering is a choice, not a dependency. `ASM-BE-025`.
- M7 is not an intended migration until `CON-BE-003` is resolved.

**Data migration.** No existing data for the login domain is supplied. `GAP-BE-089`.

<!-- /SECTION: backend.migration-plan -->
<!-- SECTION: backend.coverage | status: DRAFT-UNVERIFIED -->
## 11. Story coverage

Every story supplied in requirements.md and stories.md is listed. Stories for EP-006 are web-app scope and are not answered for by this surface. The backend dependencies of those stories are recorded in the table.

| Story | Title (short) | Covering requirement or module | Verification | Status |
|---|---|---|---|---|
| US-001-01 | provision DB and compute in EU | BR-001; `residency-compliance`; NFR-001 | planned: IaC region-constraint test | DRAFT-UNVERIFIED |
| US-001-02 | backups to EU storage | BR-001; `residency-compliance`; AC-001-02-01 | planned | DRAFT-UNVERIFIED. AC-001-02-02 corrupted, `CON-BE-016` |
| US-001-03 | pipeline residency gate | BR-002; NFR-002; AC-001-03-01 to 03 | planned: CI test | VERIFIED |
| US-001-04 | compliance report | NFR-004, NFR-001; AC-001-04-01, 02; access `GAP-BE-092` | planned | DRAFT-UNVERIFIED |
| US-001-05 | launch gate at 100% | BR-003; AC-001-05-01, 02 | planned | VERIFIED |
| US-001-06 | post-launch drift alert | BR-004; NFR-003; AC-001-06-01, 02 | planned | DRAFT-UNVERIFIED. Timing `CON-BE-018`; mechanism `GAP-BE-090` |
| US-002-01 | create note | `notes` API-NOTES-001@2; BR-005, BR-018; AC-002-01-01 to 03 | planned | VERIFIED |
| US-002-02 | edit note (PATCH only pinned) | API-NOTES-002@2; BR-006, BR-018; AC-002-02-01 to 03 | planned | VERIFIED |
| US-002-03 | add or remove tags | `notes`; NOTE_TAG; idempotent add | planned | DRAFT-UNVERIFIED. Tag name rules `GAP-BE-009`; path `GAP-BE-094` |
| US-002-04 | archive note | `notes`; BR-007; 409 on re-archive | planned | DRAFT-UNVERIFIED. Path `GAP-BE-094` |
| US-002-05 | unarchive note | `notes`; BR-008 | planned | DRAFT-UNVERIFIED. Path `GAP-BE-094` |
| US-002-06 | get note by ID | API-NOTES-003@2 | planned | VERIFIED |
| US-003-01 | keyword search | `search`; INV-BE-001; US-003-01 criteria | planned | DRAFT-UNVERIFIED. ID `GAP-BE-093` |
| US-003-02 | tag filter | `search`; BR-010 | planned | DRAFT-UNVERIFIED |
| US-003-03 | search SLA and 503 | NFR-006, NFR-007; 503 policy | planned: load test | DRAFT-UNVERIFIED. Corrupted AC, `CON-BE-016` |
| US-003-04 | combined filters | `search`; BR-010 | planned | VERIFIED |
| US-003-05 | pagination | `search`; BR-011 | planned | VERIFIED |
| US-003-06 | archived exclusion | `search`; BR-009 | planned | VERIFIED |
| US-004-01 | published OpenAPI document | `api-docs`; BR-012; NFR-008 | planned | DRAFT-UNVERIFIED. Access `ASM-BE-041`; ID `GAP-BE-095` |
| US-004-02 | request and response schemas | `api-docs`; BR-012 | planned: spec-validation CI | DRAFT-UNVERIFIED |
| US-004-03 | documented error responses | error model §8; 401, 403, 429, locked response | planned | DRAFT-UNVERIFIED. Corrupted login text, `CON-BE-016`; values `CON-BE-014` |
| US-004-04 | coverage metric | `api-docs`; BR-012; NFR-008 | planned | DRAFT-UNVERIFIED |
| US-004-05 | search and tagging examples | `api-docs`; NFR-009 | planned | DRAFT-UNVERIFIED |
| US-004-06 | residency note in docs | `api-docs`; BR-013 | planned | VERIFIED |
| US-005-01 | Scope section in docs | `api-docs`; BR-014, BR-015, BR-016 | planned: docs build lint | DRAFT-UNVERIFIED |
| US-005-02 | no attachment endpoints | `scope-guardrail`; BR-014; 404 on attachment paths. Multipart rejection criterion corrupted, `CON-BE-016` | planned: contract test | DRAFT-UNVERIFIED |
| US-005-03 | no real-time channel | `scope-guardrail`; BR-015; concurrency `GAP-BE-040` | planned: contract test | DRAFT-UNVERIFIED |
| US-005-04 | documented out-of-scope error | `scope-guardrail`; BR-016; NFR-011 | planned | DRAFT-UNVERIFIED |
| US-005-05 | scope contract suite | NFR-010; `scope-guardrail` | planned: CI | VERIFIED |
| US-005-06 | log stakeholder requests | BR-017 | not applicable to this surface | Uncovered: process, owned by product management (BR-017) |
| US-006-01 | web notes list | web-app scope | not answered here | Uncovered by design: web-app run. Backend dependency: API-NOTES-003 and the list operation (`GAP-BE-094`) |
| US-006-02 | web editor | web-app scope | not answered here | Uncovered by design: web-app run |
| US-006-03 | web search | web-app scope | not answered here | Uncovered by design: web-app run. Backend dependency: `search` |
| US-006-04 | web tags | web-app scope | not answered here | Uncovered by design: web-app run |
| US-006-05 | web archive | web-app scope | not answered here | Uncovered by design: web-app run |
| US-006-06 | web email sign-in | web-app scope; backend dependency `auth` (API-AUTH-001@1, API-AUTH-002@1) | not answered here | Uncovered by design: web-app run. Corrupted text `CON-BE-016`. Login behaviour is communicated to that run through `HANDOFF-BE-004` (existing; content not in supplied material here) |

**Coverage counts.** 36 stories supplied. 30 in backend scope (EP-001 to EP-005), of which 24 are covered and 1 (US-005-06) is uncovered for a stated reason. 6 are EP-006 stories, uncovered by design.

**Uncovered without reason.** None.

<!-- /SECTION: backend.coverage -->
