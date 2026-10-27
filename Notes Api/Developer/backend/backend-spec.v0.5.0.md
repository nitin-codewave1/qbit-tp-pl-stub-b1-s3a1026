---
id: agent-developer/notes_api/backend/BACKEND-SPEC.md
type: api_contract
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/BACKEND-SPEC.md
depends_on: [US-004-03, US-006-06, US-005-01, US-005-04, US-005-06, US-002-02, US-002-04, US-002-05, US-002-06, US-002-03, US-001-06, US-003-01, US-003-03, US-004-01, US-002-01, US-005-03, US-004-02, US-003-02, US-003-05, US-003-04, US-003-06, US-005-02, US-004-04, US-001-01, US-001-02, US-001-03, US-001-04, US-001-05, US-004-05, US-004-06, US-005-05, US-006-01, US-006-02, US-006-03, US-006-04, US-006-05]
commit_kind: fork
confidence: agent
run_id: a44b55f1-8889-5fd3-95a9-d4bf138c1691
okf_id: backend/BACKEND-SPEC.md
---

<!-- SECTION: backend.overview | status: DERIVED -->
## 1. Overview

**Responsibility.** `notes_api` is the single backend service for a team note-taking product. It is responsible for:

1. **Application API.** Create, read, update, pin/unpin, tag, archive/unarchive and search team notes, published as a versioned OpenAPI document. [stories.md EP-002, EP-003, EP-004]
2. **Email one-time-code login (CR-115).** A caller requests a one-time code for an email address and verifies it to receive a credential. The service enforces a resend limit, expires each code 10 minutes after issue, and locks an email address for 15 minutes after five wrong codes. [feedback.md CR-115; stories.md US-004-03, US-006-06] This is an interface change: consumers must adapt. [feedback.md CR-115]
3. **EU data-residency compliance.** Provisioning `notes_api`'s own database, compute and backups in approved EU regions, gating deploys on a residency check, generating a compliance report, enforcing a launch gate, and alerting on post-launch region drift. [stories.md EP-001; requirements.md BR-001 to BR-004, NFR-001 to NFR-004]

**Not responsible for.**

- Rendering any user interface. The web-app surface (stories.md EP-006) is a separate run. This document defines only the API it consumes. [stories.md EP-006]
- Attachment and file-upload endpoints of any kind. [requirements.md BR-014]
- Any real-time channel (WebSocket, SSE, polling-push) for collaborative editing. [requirements.md BR-015]
- Mobile-specific endpoints. [stories.md US-005-01, US-005-04]
- Any login method other than email one-time code. No supplied input names a password, social or SSO login. [feedback.md CR-115]
- Logging and classifying stakeholder feature requests. This is a Product-Manager intake process. [requirements.md BR-017; stories.md US-005-06]

**Change requests in this revision.**

- **CR-112 (retained from the approved revision).** Notes carry a boolean `pinned` flag, default `false`, settable on create and via PATCH, returned in note responses. Pinned notes sort before unpinned notes in list and search results. This is an interface change. [requirements.md BR-018, BR-019, NFR-014; feedback.md CR-112 as retained]
- **CR-115 (this change request).** Email one-time-code login as described in capability 2. Note, tag, search and residency behaviour are unchanged. [feedback.md CR-115]
- **Label discrepancy.** The approved revision labels the email one-time-code content `CR-113`, and the earlier drafted spec labelled it `CR-114`. The current request is labelled `CR-115` in its header. The content is the same. This document uses `CR-115`. Recorded as `CON-BE-005` (prior label `CR-113`) and `CON-BE-008` (label `CR-114` in the drafted spec).

**Stack.** Node.js 20, Express, PostgreSQL 16, hosted on AWS. [platform.md; feedback.md preflight answers]. Transactional email is specified as AWS SES in the approved revision, but the CR-115 text does not name a transport. `ASM-BE-027`. Session and token handling is specified as reuse of the application's existing mechanism in the approved revision. The preflight answers do not state this. `ASM-BE-028`.

**Revision state and identifier continuity.**

- This revision continues identifiers from the highest observed in the approved revision: `GAP-BE-044`, `ASM-BE-026`, `CON-BE-008`, `Q-BE-006`, `INV-BE-006`, `HANDOFF-BE-003`. No identifier is reused.
- Identifiers carried forward whose text is not in any supplied document: `GAP-BE-004`, `GAP-BE-005`, `GAP-BE-007`, `GAP-BE-042`, `GAP-BE-043`, and `Q-BE-004`. They remain open. No section below depends on them.
- Run mode. `RUN-CONTEXT` declares `bootstrap` with no existing files, but `feedback.md` supplies an approved revision and instructs the run to keep what the change request does not touch. This document treats the supplied approved revision as the baseline. `ASM-BE-026`.

**Open blocking questions.** `Q-BE-003` (who may verify and receive access) and `Q-BE-005` (AWS region from which SES sends, and the residency of email transmission) are open. The answers are not in the CR-115 feedback.

**Open Critical contradictions.** `CON-BE-003` (registration scope against the shared-workspace default) and `CON-BE-006` (email transmission against BR-001). Both fall under core rule 8: `CON-BE-003` touches an authorisation rule, and `CON-BE-006` touches the residency of personal data.

**Halting.** These items map to `HALT-BLOCKING-QUESTION` and `HALT-CRITICAL-CONTRADICTION`. The orchestrator decides whether to halt. This document records the work completed. Dependent subsections are marked `BLOCKED`.

<!-- /SECTION: backend.overview -->
<!-- SECTION: backend.domain-model | status: DRAFT-UNVERIFIED -->
## 2. Domain model

Three sub-domains are evidenced: the **note-taking domain** (Note, Tag), the **login domain** (User, OTP challenge, login lock), and the **residency-compliance domain** (InfraResource, DriftAlert). The login domain is new in this revision. [feedback.md CR-115] No input relates these sub-domains beyond "notes_api's infrastructure hosts the note-taking data." [requirements.md BR-013]

```mermaid
erDiagram
  NOTE ||--o{ NOTE_TAG : "tagged by"
  TAG  ||--o{ NOTE_TAG : "applied to"
  USER {
    string user_id
    string email "PII; unique; normalisation per GAP-BE-031"
    datetime created_at "set on first successful verification (ASM-BE-025, GAP-BE-041)"
  }
  OTP_CHALLENGE {
    string challenge_id
    string email "keyed by email, not user_id"
    string code_digest "storage form per GAP-BE-033"
    datetime issued_at
    datetime expires_at "issued_at + 10 minutes (US-006-06)"
    int failed_attempts
    datetime consumed_at "nullable"
  }
  LOGIN_LOCK {
    string email
    datetime locked_until "set at 5 wrong codes; 15 minutes (US-006-06)"
  }
  NOTE {
    string id
    string title
    string body
    string status "active | archived"
    boolean pinned "default false"
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
    string resource_type "db | compute | backup-storage"
    string region
    string compliance_status "pass | fail | unverified"
    datetime last_scanned_at
  }
  DRIFT_ALERT {
    string id
    string resource_id
    string detected_region
    datetime raised_at
  }
```

- **Note.** Title, body, status (active/archived), `pinned`, created and updated timestamps. [stories.md AC-002-01-01, US-002-02, US-002-04, US-002-05, US-002-06] Title and body are mandatory and non-empty at creation. [requirements.md BR-005]
- **`pinned` (CR-112).** Boolean, `NOT NULL`, default `false`. Settable on create and changeable via PATCH. Pinning does not alter title, body or tags. [requirements.md BR-018; stories.md AC-002-01-01, AC-002-01-02, AC-002-02-02]
- **Tag.** A name, many-to-many with Note through NOTE_TAG. Adding an existing tag is idempotent, with no duplicate entry. [stories.md US-002-03] Tag name format and length: `GAP-BE-009`. Cascade when a tag's last note is archived or deleted: `GAP-BE-016`.
- **User.** A person who has verified an email address. Created on first successful verification, not on code request. `ASM-BE-025`. The rule is carried from the approved revision; no CR-115 input states it. `email` is personal data. `ASM-BE-019`. Handling policy: `GAP-BE-033`. Normalisation and uniqueness: `GAP-BE-031`.
- **OTP challenge.** One issued one-time code for one email address, keyed by email because the user may not yet exist. Expiry is 10 minutes from issue. [stories.md US-006-06] The code is single-use. `INV-BE-002`. Expiry is enforced by `INV-BE-004`. Code length and character set: `GAP-BE-026`, `GAP-BE-027`. Storage form: `GAP-BE-033`.
- **Login lock.** The lock state for an email address. A lock is entered after five wrong codes and lasts 15 minutes. [stories.md US-006-06; stories.md US-004-03] Keyed by normalised email: `ASM-BE-015`. Whether the wrong-code count resets, and whether a locked address may still request codes: `GAP-BE-024`.
- **InfraResource.** A provisioned notes_api component (DB, compute, backup storage) with its reported region and compliance status. Unrecognised resource types are flagged `unverified`, never silently omitted. [stories.md AC-001-04-01, AC-001-04-02]
- **DriftAlert.** A record of a post-launch detection of an InfraResource in a non-EU region. [stories.md US-001-06]

**Cardinality and ownership.**

- A User and a Note have no relationship in the diagram. No input states whether a Note belongs to a user, is private, or is shared. `GAP-BE-003`. The default applied is `Q-BE-001` (class: degradable): notes form a shared team workspace, and any authenticated team member may read and write any note.
- **BLOCKED.** `CON-BE-003` (Critical) calls that default into question. Under CR-115 any holder of an email address that verifies can obtain a credential, so "team member" no longer names a bounded group. Until `Q-BE-003` is answered, the shared-workspace default applies only to users created by a route the human has approved. Note ownership columns and the note-access rule are blocked. See `backend.auth-model`.

**PII.** Personal data now exists in this domain: `USER.email`, `OTP_CHALLENGE.email` and `LOGIN_LOCK.email`, which hold the same value. Note `title` and `body` are free text that may incidentally contain PII. No masking or handling policy is supplied for any of them. `GAP-BE-017` (general PII policy), `GAP-BE-033` (at-rest treatment) and `GAP-BE-035` (validation scope for the email and code fields). Per core rule 12, no encryption, redaction or retention claim is made in their absence.

<!-- /SECTION: backend.domain-model -->
<!-- SECTION: backend.module-map | status: DERIVED -->
## 3. Module map

| Module | Boundary | Depends on |
|---|---|---|
| `auth` | Email one-time-code login (CR-115): code issuance and delivery, verification, resend limits, lockout, code expiry, user creation on first verification, credential issuance through the existing session/token mechanism. Owns User, OTP_CHALLENGE, LOGIN_LOCK. | external: email transport (`ASM-BE-027`; region `BLOCKED` on `Q-BE-005`, `CON-BE-006`) |
| `notes` | Note CRUD, pin/unpin via PATCH, tag assignment and removal, archive/unarchive. Owns Note, Tag, NOTE_TAG. | `auth` (caller identity) |
| `search` | Read-only query over Note: keyword, tag filter, pagination, archived-exclusion default, pinned-first ordering. | `notes`, `auth` |
| `residency-compliance` | Provisioning region validation, backup-destination validation, CI/CD residency gate, compliance report, launch-gate status, post-launch drift scan and alerting. Owns InfraResource, DriftAlert. | none at runtime; operates on the infrastructure and CI/CD layer |
| `api-docs` | Publishes the OpenAPI document, including `pinned`, the ordering change, and both login endpoints. Computes the endpoint-coverage metric. Carries the data-residency note and the Scope section. | `notes`, `search`, `auth`, `residency-compliance` |
| `scope-guardrail` | Intercepts every inbound request ahead of routing. Rejects attachment/multipart, WebSocket/SSE-upgrade and mobile-specific patterns with a structured out-of-scope response. Backs the contract test suite. | sits upstream of all modules; cross-cutting |

**Dependency direction.** `search → notes`, `search → auth`, `notes → auth`, `api-docs → notes, search, auth, residency-compliance`, `auth → email transport` (external). Requests flow through `scope-guardrail` first, then `auth` for protected routes, then `notes`, `search` or `api-docs`.

**Guardrail before authentication.** The guardrail runs before authentication, so an out-of-scope call returns its documented response without a credential. `ASM-BE-018`. The response form for excluded paths is in conflict across inputs: `CON-BE-007`.

**CR-112 impact on boundaries.** No new module. Pinned-first ordering is owned by `notes` for `GET /notes` and by `search` for `GET /notes/search`. Both apply the same rule. `INV-BE-001`.

**CR-115 impact on boundaries.** `auth` is the new module. It is a new dependency of `notes` and `search`, which previously had no identity dependency. It is the only module that calls the email transport. That dependency carries the residency question `CON-BE-006`.

<!-- /SECTION: backend.module-map -->
<!-- SECTION: backend.api-surface | status: DRAFT-UNVERIFIED -->
## 4. API surface index

Full contracts (request and response schemas, every error, idempotency, rate limits) belong in the per-module `api-contract` sections. Those are not produced in this document. Schema IDs (`SCHEMA-`) for the login payloads are deferred to the `auth` module contract.

**Versioning.** An endpoint whose request or response shape changes is re-pinned from `@1` to `@2` (`ASM-BE-010`). New endpoints start at `@1` (`ASM-BE-021`). CR-115 adds no shape change to existing endpoints, so their pins stay at `@2`. Whether authenticated endpoints should bump because their caller obligation changes: `GAP-BE-037`.

| API ID | Method | Path | Auth | Module | Notes |
|---|---|---|---|---|---|
| `API-AUTH-001@1` | POST | `/auth/otp/send` | unauthenticated; rate-limited | `auth` | **New (CR-115).** Issues a one-time code to an email address and sends it via the email transport. Also the resend path. The resend limit is more than 3 requests within 10 minutes; exceeding it returns 429. [stories.md US-006-06, US-004-03] Limit key: `Q-BE-006`, default per email (`ASM-BE-029`). Response must not reveal whether an account exists (see `backend.auth-model`). Path: `ASM-BE-013`. Body and 429 detail: `GAP-BE-028`. Transport region: `BLOCKED` on `Q-BE-005`, `CON-BE-006`. |
| `API-AUTH-002@1` | POST | `/auth/otp/verify` | unauthenticated; subject to lockout | `auth` | **New (CR-115).** Verifies a code for an email address. A wrong or expired code is rejected. Expiry is 10 minutes after issue. [stories.md US-006-06; `ASM-BE-023`] Five wrong codes lock the address for 15 minutes and the caller receives a locked-account response. [stories.md US-004-03, US-006-06] Locked-response status and body: `GAP-BE-038`. On success, creates the User if absent (`INV-BE-003`, `GAP-BE-041`) and returns a credential from the existing session/token mechanism (`ASM-BE-028`, `GAP-BE-030`). |
| `API-NOTES-001@2` | POST | `/notes` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned` (boolean, default `false`). Returns `pinned` in the 201 body. Title and body rules unchanged. [stories.md AC-002-01-01, AC-002-01-02; requirements.md BR-005, BR-018] |
| `API-NOTES-002@2` | PATCH | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned`. Partial update: fields not sent are never overwritten. [requirements.md BR-006] Returns `pinned`. Pinning leaves title, body and tags unchanged. [stories.md AC-002-02-02] PATCH, not PUT: `ASM-BE-001`. Concurrent-write policy: `GAP-BE-040`. |
| `API-NOTES-003@2` | GET | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Returns `pinned` with tags and archive status. A missing or deleted note returns 404 with no partial data. [stories.md US-002-06] Whether a delete operation exists: `GAP-BE-046`. |
| `API-NOTES-004@2` | GET | `/notes` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Default active-notes list. Pinned-first, then most recently updated within each group. [requirements.md BR-019; `CON-BE-002`] Parameters: `GAP-BE-001`. Page size: `GAP-BE-015`. |
| `API-NOTES-005@1` | POST | `/notes/{id}/tags` | authenticated team member | `notes` | Add tag, idempotent: no duplicate entry on repeat. [stories.md US-002-03] `ASM-BE-002`. |
| `API-NOTES-006@1` | DELETE | `/notes/{id}/tags/{tag}` | authenticated team member | `notes` | Remove tag. `ASM-BE-002`. |
| `API-NOTES-007@1` | POST | `/notes/{id}/archive` | authenticated team member | `notes` | Archive; data retained. [requirements.md BR-007] Repeat archive returns a conflict/error response. Status: `GAP-BE-048`. [stories.md US-002-04] |
| `API-NOTES-008@1` | POST | `/notes/{id}/unarchive` | authenticated team member | `notes` | Restores full prior state, including `pinned`. [stories.md US-002-05; requirements.md BR-008] |
| `API-SEARCH-001@2` | GET | `/notes/search` | authenticated team member | `search` | **Changed from @1 (CR-112).** Keyword (`q`), `tags`, `page`, `limit`, `include_archived`. Returns `pinned`. Pinned-first, then relevance. [stories.md US-003-01; requirements.md BR-019] Archived notes excluded by default. [requirements.md BR-009] Returns 503 with a retry-after indication when the search index is unavailable, rather than hanging. [stories.md US-003-03] At least one of `q` or `tags` required: `ASM-BE-006`, `CON-BE-001`. |
| `API-APIDOCS-001@1` | GET | `/openapi.{json\|yaml}` (exact path `GAP-BE-008`) | unauthenticated (`ASM-BE-004`) | `api-docs` | Describes `pinned`, the ordering change (NFR-014), and both login endpoints with their 429, expiry and locked-account responses. [stories.md US-004-01, US-004-03] Unsupported `Accept` yields 406 or a documented fallback. |

`residency-compliance` and `scope-guardrail` own **no end-user HTTP endpoint** evidenced by any input. Residency behaviour is infrastructure-as-code and pipeline-hook work. Report delivery: `GAP-BE-002`. Excluded-path behaviour is specified in `backend.error-model` and is in conflict across inputs: `CON-BE-007`.

**Consumer obligation under CR-115.** Every authenticated endpoint above requires a credential from `API-AUTH-002@1`. Client integrators must complete the login flow before calling them. This is the interface change CR-115 declares. [feedback.md CR-115] Web-app obligations: `HANDOFF-BE-002`. The web-app must also surface the 429, expired-code and locked-account responses. `HANDOFF-BE-003`, to be recorded in `HANDOFFS.md`.

<!-- /SECTION: backend.api-surface -->
<!-- SECTION: backend.auth-model | status: DRAFT-UNVERIFIED -->
## 5. Auth model

### 5.1 Authentication: email one-time code (CR-115)

**Flow.**

1. **Send.** The caller submits an email address to `API-AUTH-001@1`. The service issues a code, records an OTP challenge, and sends the code through the email transport. [feedback.md CR-115] If the resend limit is exceeded (more than 3 requests within 10 minutes), the service returns 429 and sends no code. [stories.md US-006-06, US-004-03]
2. **Verify.** The caller submits the email address and the code to `API-AUTH-002@1`.
3. **Success.** On a correct, unexpired, unconsumed code, the service creates the User if none exists for that email, consumes the challenge, and returns a credential. [INV-BE-002, INV-BE-003; ASM-BE-025; GAP-BE-041]
4. **Wrong code.** A wrong code increments the challenge's failed-attempt count. At five wrong codes the email address is locked for 15 minutes and the caller receives a locked-account response. [stories.md US-006-06, US-004-03] Whether the count is per challenge or cumulative, and whether it resets: `GAP-BE-024`. Locked-response status and body: `GAP-BE-038`.
5. **Expiry.** A code expires 10 minutes after issue. A submission after that is rejected with an expired-code outcome. [stories.md US-006-06] This is a web-app story; the backend reading is `ASM-BE-023`.
6. **Locked during verification.** No successful verification occurs for an email address while its lock is in effect. `INV-BE-005`.

**Example request, masked per core rule 12.**

```json
{ "email": "a***@e***.com", "code": "<one-time code>" }
```

**Resend limit.** Stated: more than 3 requests within 10 minutes triggers a rate-limit response and no code is sent. [stories.md US-006-06] The key (per email, per client, or both) and the body of the 429 response are not stated: `Q-BE-006` (default per email, `ASM-BE-029`) and `GAP-BE-023`. The approved revision carried these as open; the stated window is now recorded.

**Credential.** The approved revision specifies reuse of the existing session/token mechanism. No supplied document describes that mechanism. Format, lifetime, refresh, revocation and logout are `GAP-BE-030`. Until that is closed, no client can rely on token expiry behaviour.

**Account enumeration.** `DERIVED`. Because the user is created on first successful verification and unknown emails are not rejected at send, `API-AUTH-001@1` has no account-existence branch. Its response must not differ by account existence. Lock-state responses are keyed by email (`ASM-BE-015`), so they may reveal lock state for an address whether or not an account exists. That exposure is recorded in `GAP-BE-024` and `GAP-BE-038`.

**Not stated.** No input states a code length, code character set, or whether an earlier code is invalidated when a new one is issued. `GAP-BE-026`, `GAP-BE-027`. None is invented here.

### 5.2 Email transmission: BLOCKED

**BLOCKED** pending `Q-BE-005` and `CON-BE-006` (Critical). No input states the region from which the email transport sends. BR-001 requires that "all notes_api infrastructure (database, compute, backups)" be provisioned in approved EU regions and does not name email delivery. [requirements.md BR-001] Each one-time code is personal data in transit. The approved revision's transport (AWS SES) is `ASM-BE-027`. Until `Q-BE-005` is answered, no section may state that email transmission is EU-resident.

### 5.3 Note access: BLOCKED

**BLOCKED** pending `Q-BE-003` and `CON-BE-003` (Critical). The shared-workspace default (`Q-BE-001`, degradable) is not valid for users created by an unapproved route. The note-access rule is therefore not written in this revision.

### 5.4 Actors and permission matrix

Actors: anonymous caller; authenticated caller (holds a CR-115 credential); pipeline and infrastructure actor (CI/CD, IaC); compliance auditor (report reader); DevOps alert recipient.

| Operation | Anonymous | Authenticated | Pipeline / IaC | Auditor | Note |
|---|---|---|---|---|---|
| `API-AUTH-001` send | allowed, rate-limited | allowed | n/a | n/a | `DERIVED` |
| `API-AUTH-002` verify | allowed, lockout applies | allowed | n/a | n/a | `DERIVED` |
| `API-APIDOCS-001` | allowed (`ASM-BE-004`) | allowed | n/a | n/a | `DERIVED` |
| Note create, read, update, archive, tag, search | denied (401) | allowed | n/a | n/a | role of authenticated caller: `Q-BE-003` |
| Note access by resource ownership | — | `BLOCKED` | — | — | `Q-BE-003`, `CON-BE-003` |
| Residency report read | n/a | n/a | n/a | `GAP-BE-002` | report delivery and access unstated |
| Residency gate and launch sign-off | n/a | n/a | enforced | sign-off by Engineering Lead [requirements.md BR-003] | |

The 401 and 403 bodies are documented in the `api-docs` contract (`ASM-BE-004`). Their exact form is `GAP-BE-047`.

<!-- /SECTION: backend.auth-model -->
<!-- SECTION: backend.data-lifecycle | status: DRAFT-UNVERIFIED -->
## 6. Data lifecycle

| Data | Stored | Retention | Encrypted at rest | Deletion |
|---|---|---|---|---|
| Note (title, body, status, `pinned`, timestamps) | yes [stories.md US-002-01 to US-002-06] | not stated: `GAP-BE-017` | not stated: `GAP-BE-033` | archive hides, never deletes [requirements.md BR-007]. Whether hard delete exists: `GAP-BE-046` |
| Tag and NOTE_TAG | yes | not stated | not stated | cascade on last-note archive or delete: `GAP-BE-016` |
| User (email) | yes; personal data [`ASM-BE-019`] | not stated: `GAP-BE-017` | not stated: `GAP-BE-033` | not stated |
| OTP challenge (email, code digest, counters) | yes | not stated; expiry is 10 minutes [stories.md US-006-06], retention after expiry unstated: `GAP-BE-052` | code storage form: `GAP-BE-033` | consumed or expired challenges: retention unstated |
| Login lock (email, locked-until) | yes | expires after 15 minutes; post-expiry retention unstated: `GAP-BE-052` | not stated | not stated |
| InfraResource, DriftAlert, compliance report | yes [stories.md AC-001-04-01, US-001-06] | not stated | not stated | not stated. Storage location: `GAP-BE-050` |
| Backups | EU-only storage [stories.md AC-001-02-01] | not stated | not stated | not stated |

**Backup location.** The backup artefact is stored in an EU-region bucket or service. [stories.md AC-001-02-01] A backup destination outside the EU fails fast with a residency violation error and writes no data. [stories.md AC-001-02-02]

**Stated at-rest claims.** None. Per core rule 12, no encryption, redaction or retention claim is made where no input states one.

<!-- /SECTION: backend.data-lifecycle -->
<!-- SECTION: backend.nfr | status: DERIVED -->
## 7. Non-functional requirements

Each target below cites its source. No figure is invented.

| Area | Target | Source | Gap |
|---|---|---|---|
| Search latency | p95 < 300 ms under typical load | NFR-006; stories.md US-003-03 | "typical load" undefined: `GAP-BE-051` |
| Search pagination | p95 < 300 ms up to 100 results per page | NFR-007 | |
| Search at scale | latency SLA met on a large dataset (e.g. 100k notes) via indexed lookup | stories.md US-003-03 (example dataset, not a target) | index design unstated |
| CRUD correctness | 100% functional-test pass rate; zero data loss | NFR-005 | |
| Availability | 99.9% monthly uptime for CRUD and search | NFR-013 | |
| OTP send and verify latency and throughput | none stated | none | `GAP-BE-051` |
| Concurrency | two concurrent PATCHes to one note produce a standard last-write/conflict response with no broadcast or merge | stories.md US-005-03 | exact policy: `GAP-BE-040` |
| Injection | 0 critical injection vulnerabilities per release, on title, body, tag fields | NFR-012 | email and code fields are not named: `GAP-BE-035` |
| Drift alert | alert within 15 minutes of drift detection | NFR-003 | |
| Launch gate | report refreshed and 100% accurate at each launch gate check | NFR-004 | |
| Residency gate | 0 non-EU deploys reach production | NFR-002 | |
| Scope guardrail | 0 out-of-scope endpoints detected per CI build | NFR-010 | |
| Out-of-scope response | 100% of out-of-scope calls return a documented 4xx with an explanatory message | NFR-011 | conflicting form: `CON-BE-007` |
| Docs coverage | 100% of implemented endpoints present in the published spec | NFR-008, BR-012 | |

Design for scale that no input requests is not included.

<!-- /SECTION: backend.nfr -->
<!-- SECTION: backend.error-model | status: DRAFT-UNVERIFIED -->
## 8. Error model

**Global envelope.** The full envelope shape is `GAP-BE-047`. Two properties are fixed by inputs: each error carries a machine-readable code and a human-readable reason [stories.md US-005-04, US-004-03], and every error case in an endpoint definition is documented. [requirements.md BR-012; stories.md US-004-02]

**Status-code policy.**

| Status | Used for | Source |
|---|---|---|
| 200 | successful read, update, archive, unarchive, tag add or remove, empty search result, out-of-range page | stories.md US-002-02, US-002-03, US-003-02, US-003-05 |
| 201 | note created | stories.md AC-002-01-01 |
| 400 | validation error: missing title, missing `q` and `tags`, invalid `include_archived`, malformed filter | stories.md AC-002-01-03, US-003-01, US-003-04, US-003-06 |
| 401 | missing or invalid credential | stories.md US-004-03 |
| 403 | authenticated but not permitted | stories.md US-004-03; permission rule `BLOCKED` (`Q-BE-003`) |
| 404 | note not found; attachment path | stories.md US-002-02, US-005-02 |
| 406 | unsupported `Accept` on the spec endpoint, or a documented fallback | stories.md US-004-01 |
| 409 (or other) | repeat archive: "conflict/error response" | stories.md US-002-04. Exact status: `GAP-BE-048` |
| 415 or 400 | multipart or file payload to a note endpoint | stories.md US-005-02 |
| 429 | resend limit exceeded on send | stories.md US-006-06, US-004-03 |
| locked-account response | after five wrong codes, for 15 minutes. Status and body: `GAP-BE-038` | stories.md US-004-03, US-006-06 |
| expired-code outcome | code submitted more than 10 minutes after issue. Status: not stated, `GAP-BE-049` | stories.md US-006-06 |
| 503 with retry-after | search index degraded or unavailable | stories.md US-003-03 |

**Out-of-scope responses.** A call to an excluded path returns a structured out-of-scope response that references the exclusion, not a bare 404. [stories.md US-005-04; requirements.md BR-016] Stories US-005-02 requires 404 for an attachment path. The two forms conflict: `CON-BE-007`. Until resolved, the form in `CON-BE-007` is not chosen here.

**Error-code registry.** `OUT_OF_SCOPE` is the only code named by inputs, as an example. [stories.md US-005-04] The full registry: `GAP-BE-049`.

<!-- /SECTION: backend.error-model -->
<!-- SECTION: backend.observability | status: DRAFT-UNVERIFIED -->
## 9. Observability

**Logged.** Post-launch residency scan results: a pass is logged as a pass. [stories.md AC-001-06-01] A detected non-EU resource is alerted with resource details. [stories.md AC-001-06-02]

**Alerted.**

- Post-launch non-EU drift, sent immediately to the DevOps team with resource details. [stories.md AC-001-06-02] Alert within 15 minutes of detection. [NFR-003]
- Launch gate failure, which blocks release. [stories.md AC-001-05-02]
- Residency gate failure, which fails the build. [stories.md AC-001-03-02]

**Metered.**

- Endpoint coverage: percentage of implemented endpoints present in the published spec. [stories.md US-004-04]
- Scope guardrail: count of out-of-scope endpoints detected per build (target 0). [NFR-010]
- Out-of-scope request count: produced by Product-Manager classification, not by the service. [requirements.md BR-017; stories.md US-005-06]

**Redacted.** Not stated. Whether email addresses and codes appear in application logs, and how they are masked: `GAP-BE-017`. No redaction claim is made. Core rule 12 applies.

**Not stated.** Log retention, tracing, and log destination. `GAP-BE-050`.

<!-- /SECTION: backend.observability -->
<!-- SECTION: backend.migration-plan | status: DRAFT-UNVERIFIED -->
## 10. Migration plan

Migration tooling is not named. `GAP-BE-050` covers the tool and storage location for infrastructure records. Reversibility below is derived from the change, not from an input.

| Order | Migration | Source | Reversibility | Status |
|---|---|---|---|---|
| M-1 | create `user`, `otp_challenge`, `login_lock` tables (email-keyed; no existing data) | domain-model (section 2); CR-115 | `DERIVED`: reversible by dropping the new tables; no existing data is lost | DRAFT-UNVERIFIED |
| M-2 | add `pinned` to `note`: boolean, `NOT NULL`, default `false` | CR-112; BR-018 | `DERIVED`: reversible by dropping the column; pinned values are lost on rollback | DRAFT-UNVERIFIED |
| M-3 | add note ownership or access columns | `Q-BE-003`, `CON-BE-003` | not writable | **BLOCKED** |
| M-4 | create `infra_resource`, `drift_alert` | residency domain | location and tool unstated: `GAP-BE-050` | BLOCKED on `GAP-BE-050` |

**Ordering.** M-1 and M-2 must complete before `API-AUTH-001@1`, `API-AUTH-002@1` and the CR-112 endpoints are deployed. M-3 must not be written until `Q-BE-003` and `CON-BE-003` are closed. Changing the note ownership model after data exists is a data-shape change.

**Residency of migrations.** Every database for notes_api is EU-provisioned. [requirements.md BR-001] Migration runs against non-EU databases are not permitted. [requirements.md BR-002]

<!-- /SECTION: backend.migration-plan -->
<!-- SECTION: backend.coverage | status: DERIVED -->
## 11. Coverage

Every supplied story ID is listed. Stories EP-006 are web-app stories and are out of backend scope; their backend dependencies are noted.

| Story | Backend coverage | Section | Status |
|---|---|---|---|
| US-001-01 | infrastructure provisioning restricted to approved EU regions. Approved region list not supplied | 1, 3, 6 | partly covered: `GAP-BE-044` |
| US-001-02 | EU-only backup destination; fail-fast on non-EU config | 6, 9 | covered in part: `GAP-BE-044` |
| US-001-03 | CI/CD residency gate; missing metadata treated as failure | 1, 3, 9 | covered in part: `GAP-BE-045` |
| US-001-04 | compliance report with per-resource status and percentage | 2, 9 | covered in part: `GAP-BE-002` |
| US-001-05 | launch gate at 100% EU-verified | 1, 9 | covered; sign-off by Engineering Lead [BR-003] |
| US-001-06 | post-launch drift alert | 9 | covered |
| US-002-01 | `POST /notes`; `pinned` default | 4 | covered |
| US-002-02 | `PATCH /notes/{id}`; partial update | 4 | covered |
| US-002-03 | tag add and remove | 4 | covered |
| US-002-04 | archive; repeat-archive status | 4, 8 | covered in part: `GAP-BE-048` |
| US-002-05 | unarchive restores full state | 4 | covered |
| US-002-06 | `GET /notes/{id}`; 404 on missing or deleted note | 4 | covered in part: `GAP-BE-046` |
| US-003-01 | keyword search, pinned-first | 4 | covered |
| US-003-02 | tag filter | 4 | covered |
| US-003-03 | latency target; 503 with retry-after | 7, 8 | covered in part: `GAP-BE-051` |
| US-003-04 | combined keyword and tag filter | 4 | covered; `CON-BE-001` |
| US-003-05 | pagination; 400 on invalid page or limit | 4, 8 | covered |
| US-003-06 | archived exclusion default; `include_archived` | 4 | covered |
| US-004-01 | published OpenAPI document; 406 or fallback | 4, 8 | covered |
| US-004-02 | schemas in the spec | 4 | deferred to module `api-contract` sections |
| US-004-03 | documented 4xx, 429, locked-account response | 5, 8 | covered in part: `GAP-BE-038`, `GAP-BE-028` |
| US-004-04 | coverage metric | 9 | covered |
| US-004-05 | examples for search and tagging | 4 | deferred to module `api-contract` sections |
| US-004-06 | data-handling note on storage location | 6 | covered in part: `GAP-BE-050` |
| US-005-01 | Scope section in published docs | 1 | covered |
| US-005-02 | no attachment route; multipart rejected | 4, 8 | covered; `CON-BE-007` |
| US-005-03 | no real-time channel; concurrency response | 7 | covered in part: `GAP-BE-040` |
| US-005-04 | structured out-of-scope error | 8 | covered; `CON-BE-007` |
| US-005-05 | contract test suite | 9 | covered |
| US-005-06 | PM classification log | 1, 9 | out of system scope [BR-017] |
| US-006-01 | web-app notes list | — | out of backend scope; uses `API-NOTES-004` |
| US-006-02 | web-app editor | — | out of backend scope; uses `API-NOTES-001`, `API-NOTES-002` |
| US-006-03 | web-app search | — | out of backend scope; uses `API-SEARCH-001` |
| US-006-04 | web-app tags | — | out of backend scope; uses `API-NOTES-005`, `API-NOTES-006` |
| US-006-05 | web-app archive and unarchive | — | out of backend scope; uses `API-NOTES-007`, `API-NOTES-008` |
| US-006-06 | web-app sign-in: resend, expiry, lockout | 5 | backend behaviour covered in section 5; web-app handled by `HANDOFF-BE-003` |

**Uncovered at the API level.** Stories US-001-01 through US-001-06 are infrastructure and pipeline stories. Their backend coverage is through `residency-compliance`, not through an endpoint, so no `API-` ID applies. Each is listed with its gap.

**Checks performed.** Every supplied story ID appears above (36 of 36). Every identifier minted in this revision carries the `BE` surface code, except the `API-` and `SCHEMA-` identifiers, which are system-wide by design. No schema is restated.

<!-- /SECTION: backend.coverage -->
