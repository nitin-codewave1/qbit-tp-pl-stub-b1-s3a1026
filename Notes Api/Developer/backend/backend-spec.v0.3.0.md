---
id: agent-developer/notes_api/backend/BACKEND-SPEC.md
type: api_contract
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/BACKEND-SPEC.md
depends_on: [US-005-01, US-005-04, US-005-06, US-002-02, US-002-04, US-002-05, US-002-06, US-002-03, US-001-06, US-004-03, US-003-01, US-004-01, US-001-03, US-001-04, US-001-05, US-005-05, US-002-01, US-003-02, US-003-05, US-005-02, US-003-03, US-004-04, US-001-01, US-001-02, US-003-04, US-003-06, US-004-02, US-004-05, US-004-06, US-005-03, US-006-01, US-006-02, US-006-03, US-006-04, US-006-05]
commit_kind: fork
confidence: agent
run_id: e7713dfa-c8a3-566f-855e-2dd74751d350
okf_id: backend/BACKEND-SPEC.md
---

<!-- SECTION: backend.overview | status: DERIVED -->
## Service overview

`notes_api` is the single backend service for a team note-taking product. Its in-scope capability has three parts.

1. **Application API.** Create, read, update, pin/unpin, tag, archive/unarchive and search team notes, published as a versioned OpenAPI contract. [stories.md EP-002, EP-003, EP-004]
2. **Email one-time-code login (CR-113).** A caller requests a one-time code for an email address, verifies it, and receives a credential. The service enforces resend rate limits, locks an account after repeated wrong codes, and expires codes. Codes are emailed through AWS SES. A user record is created on first successful verification. [feedback.md CR-113; feedback.md preflight answers] This is an interface change: consumers must adapt.
3. **EU data-residency compliance.** Provisioning `notes_api`'s own infrastructure (database, compute, backups) in approved EU regions, gating deploys on a residency check, generating a compliance report, enforcing a launch gate, and alerting on post-launch region drift. [stories.md EP-001; requirements.md BR-001 to BR-004, NFR-001 to NFR-004]

**Change request CR-112 (retained from the approved revision).** Notes carry a boolean `pinned` flag, default `false`, settable on create and via PATCH, returned in note responses. Pinned notes sort before unpinned notes in list and search results. This is an interface change. [feedback.md CR-112; requirements.md BR-018, BR-019, NFR-014]

**Change request CR-113 (incorporated in this revision).** Email one-time-code login as described in capability 2. Only the login flow is new. Note, tag, search and residency behaviour are unchanged by CR-113.

**Stack.** Node.js 20, Express, PostgreSQL 16, hosted on AWS. Transactional email via AWS SES. [platform.md; feedback.md preflight answers]

**Explicitly not in scope**, each cited against the input that excludes it.

- Attachment and file-upload endpoints of any kind. [requirements.md BR-014]
- Any real-time channel (WebSocket, SSE, polling-push) for collaborative editing. [requirements.md BR-015]
- Mobile-specific endpoints. [stories.md US-005-01, US-005-04]
- Rendering any user interface. The web-app surface (stories.md EP-006) is a separate run. This spec defines only the API it consumes. The web pin toggle (CR-112) and the web login flow (CR-113) are handed off (`HANDOFF-BE-001`, `HANDOFF-BE-002`).
- Any login method other than email one-time code. No input names a password, social or SSO login, so none is in scope.
- Logging and classifying stakeholder feature requests. This is a Product-Manager intake log, not a `notes_api` function. [requirements.md BR-017; stories.md US-005-06]

**Revision state and ID continuity.**

- `RUN-CONTEXT.existing` is empty, but the approved revision is supplied in the feedback. To avoid reusing any identifier, new IDs continue from that revision's maxima: `GAP-BE-022`, `ASM-BE-011`, `CON-BE-002`, `Q-BE-002`, `INV-BE-001`, `HANDOFF-BE-001`.
- Two blocking questions are open: `Q-BE-003` and `Q-BE-004`. One Critical contradiction is open: `CON-BE-003`. The parts of this document that depend on them are `BLOCKED` and are marked as such. Under `HALT-BLOCKING-QUESTION` and `HALT-CRITICAL-CONTRADICTION` the orchestrator is expected to halt this run. This document records the work completed before that point.

<!-- /SECTION: backend.overview -->
<!-- SECTION: backend.domain-model | status: DRAFT-UNVERIFIED -->
## Domain model

Three sub-domains are evidenced: the **note-taking domain** (Note, Tag), the **login domain** (User, OTP challenge, login lock), and the **residency-compliance domain** (InfraResource, DriftAlert). The login domain is new in this revision. No input relates these sub-domains beyond "notes_api's infrastructure hosts the note-taking data." [requirements.md BR-013]

```mermaid
erDiagram
  NOTE ||--o{ NOTE_TAG : "tagged by"
  TAG  ||--o{ NOTE_TAG : "applied to"
  USER {
    string user_id
    string email "PII; unique, normalisation per GAP-BE-031"
    datetime created_at "set on first successful verification"
  }
  OTP_CHALLENGE {
    string challenge_id
    string email "keyed by email, not user_id"
    string code_digest "storage form per GAP-BE-033"
    datetime issued_at
    datetime expires_at "duration per GAP-BE-025"
    int failed_attempts
    datetime consumed_at "nullable"
  }
  LOGIN_LOCK {
    string email
    datetime locked_until "duration per GAP-BE-024"
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
- **Tag.** A name, many-to-many with Note through NOTE_TAG. Adding an existing tag is idempotent, with no duplicate entry. [stories.md US-002-03] `GAP-BE-009` (tag name format and length). `GAP-BE-016` (cascade when a tag's last note is archived or deleted).
- **User (CR-113).** A person who has verified an email address. Created on first successful verification, not on code request. [feedback.md preflight answers] `email` is personal data. This classification is `ASM-BE-019`. The domain previously held no personal data field. Its handling policy is `GAP-BE-033`. Normalisation and uniqueness are `GAP-BE-031`.
- **OTP challenge (CR-113).** One issued one-time code for one email address. Keyed by email rather than user, because the user may not exist yet. A code is single-use: `INV-BE-002`. It expires: `INV-BE-004`. Its duration is `GAP-BE-025`.
- **Login lock (CR-113).** The lock state for an email address after repeated wrong codes. Keyed by normalised email: `ASM-BE-015`. Threshold, duration and unlock mechanism are `GAP-BE-024`.
- **InfraResource.** A provisioned notes_api component (DB, compute, backup storage) with its reported region and compliance status. Unrecognised resource types are flagged `unverified`, never silently omitted. [stories.md AC-001-04-01, AC-001-04-02]
- **DriftAlert.** A record of a post-launch detection of an InfraResource in a non-EU region. [stories.md US-001-06]

**Cardinality and ownership.**

- A User and a Note have no relationship in the diagram. No input states whether a Note belongs to a user, is private, or is shared. `GAP-BE-003` records this gap. Default applied (`Q-BE-001`, class: degradable): notes are a shared team workspace, and any authenticated team member may read and write any note.
- `CON-BE-003` (Critical) calls that default into question. Under CR-113 any holder of an email address can create a User, so "team member" no longer names a bounded group. Until `Q-BE-003` is answered, the shared-workspace default applies only to users created by a route the human has approved. See `backend.auth-model`.

**PII.** Personal data now exists in this domain: `USER.email`, and `OTP_CHALLENGE.email` and `LOGIN_LOCK.email` which hold the same value. Note `title` and `body` are free text that may incidentally contain PII. No masking or handling policy is supplied for any of them. `GAP-BE-017` (general PII policy), `GAP-BE-033` (at-rest treatment) and `GAP-BE-035` (injection scope for the email and code fields) record this. Per core rule 12, no encryption, redaction or retention claim is made in their absence.

<!-- /SECTION: backend.domain-model -->
<!-- SECTION: backend.module-map | status: DERIVED -->
## Module map

| Module | Boundary | Depends on |
|---|---|---|
| `auth` | Email one-time-code login (CR-113): code issuance and delivery via SES, verification, resend limits, lockout, code expiry, user creation on first verification, credential issuance. Owns User, OTP_CHALLENGE and LOGIN_LOCK. | external: AWS SES |
| `notes` | Note CRUD, pin/unpin (via PATCH), tag assignment and removal, archive/unarchive lifecycle. Owns Note, Tag and NOTE_TAG. | `auth` (identity of the caller) |
| `search` | Read-only query surface over Note: keyword, tag filter, pagination, archived-exclusion default, pinned-first ordering. | `notes`, `auth` |
| `residency-compliance` | Infra provisioning region validation, backup-destination validation, CI/CD residency gate, compliance report, launch-gate status, post-launch drift scan and alerting. Owns InfraResource and DriftAlert. | none at runtime (operates on the infra and CI/CD layer) |
| `api-docs` | Publishes the OpenAPI document, including `pinned` (NFR-014) and the login endpoints. Computes the endpoint-coverage metric. Carries the data-residency documentation note. | `notes`, `search`, `auth`, `residency-compliance` |
| `scope-guardrail` | Intercepts every inbound request ahead of routing. Rejects attachment/multipart, WebSocket/SSE-upgrade and mobile-specific patterns with a structured out-of-scope error. Backs the contract test suite. | sits upstream of all modules; cross-cutting |

**Dependency direction.** `search → notes`. `search → auth`. `notes → auth`. `api-docs → notes, search, auth, residency-compliance`. `auth → SES` (external). Requests flow through `scope-guardrail` first, then to `auth` for protected routes, then to `notes`, `search` or `api-docs`.

**Ordering of the guardrail and authentication.** The guardrail runs before authentication, so that an out-of-scope call returns its documented out-of-scope error without a credential. `ASM-BE-018`.

**CR-112 impact on boundaries.** No new module. Pinned-first ordering is owned by `notes` for `GET /notes` and by `search` for `GET /notes/search`. Both apply the same rule (`INV-BE-001`).

**CR-113 impact on boundaries.** One new module, `auth`. It is a new dependency of `notes` and `search`, which previously had no identity dependency. It is the only module that calls SES.

<!-- /SECTION: backend.module-map -->
<!-- SECTION: backend.api-surface | status: DRAFT-UNVERIFIED -->
## API surface index

Full contracts (request and response schemas, every error, idempotency, rate limits) belong in the per-module `api-contract` sections. Those are not produced in this run. This index is their introduction point. Schema IDs (`SCHEMA-`) for the login payloads are deferred to the `auth` module contract.

**Versioning.** Endpoints whose request or response shape changes are re-pinned from `@1` to `@2` (`ASM-BE-010`). New endpoints start at `@1` (`ASM-BE-021`). CR-113 adds no shape change to existing endpoints, so their pins stay at `@2`. Whether CR-113 should also bump authenticated endpoints, because their caller obligation changes, is `GAP-BE-037`.

| API ID | Method | Path | Auth | Module | Notes |
|---|---|---|---|---|---|
| `API-AUTH-001@1` | POST | `/auth/otp/send` | unauthenticated; rate-limited (`GAP-BE-023`) | `auth` | **New (CR-113).** Issues a one-time code to an email address and emails it via SES. Also the resend path (`ASM-BE-014`). Response does not reveal whether an account exists (`DERIVED`, see `backend.auth-model`). Path: `ASM-BE-013`. Documents 429 and locked responses per US-004-03; detail `GAP-BE-028`. |
| `API-AUTH-002@1` | POST | `/auth/otp/verify` | unauthenticated; subject to lockout (`GAP-BE-024`) | `auth` | **New (CR-113).** Verifies a code for an email address. On success creates the User if absent (`INV-BE-003`) and returns a credential (`ASM-BE-016`, lifecycle `GAP-BE-030`). Wrong or expired code: `ASM-BE-012`. Locked: `GAP-BE-038`. |
| `API-NOTES-001@2` | POST | `/notes` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned` (boolean, default `false`). Returns `pinned` in the 201 body. Title and body rules unchanged. [stories.md AC-002-01-01, AC-002-01-02; requirements.md BR-005, BR-018] |
| `API-NOTES-002@2` | PATCH | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned`. Partial update: unsent fields are never overwritten (BR-006). Returns `pinned`. Pinning leaves title, body and tags unchanged. [stories.md AC-002-02-02; requirements.md BR-006, BR-018] `ASM-BE-001`: PATCH, not PUT, per BR-006. Concurrent-write policy: `GAP-BE-040`. |
| `API-NOTES-003@2` | GET | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Returns `pinned` with tags and archive status. [stories.md US-002-06] |
| `API-NOTES-004@2` | GET | `/notes` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Default active-notes list. Pinned-first, then most recently updated within each group (`CON-BE-002`). `GAP-BE-001` (parameters). `GAP-BE-015` (page size). |
| `API-NOTES-005@1` | POST | `/notes/{id}/tags` | authenticated team member | `notes` | Add tag, idempotent. `ASM-BE-002`. |
| `API-NOTES-006@1` | DELETE | `/notes/{id}/tags/{tag}` | authenticated team member | `notes` | Remove tag. `ASM-BE-002`. |
| `API-NOTES-007@1` | POST | `/notes/{id}/archive` | authenticated team member | `notes` | Archive. Pinned state preserved (`backend.data-lifecycle`). [stories.md US-002-04] |
| `API-NOTES-008@1` | POST | `/notes/{id}/unarchive` | authenticated team member | `notes` | Restores full prior state including `pinned`. [stories.md US-002-05; requirements.md BR-008] |
| `API-SEARCH-001@2` | GET | `/notes/search` | authenticated team member | `search` | **Changed from @1 (CR-112).** Keyword (`q`), `tags`, `page`, `limit`, `include_archived`. Returns `pinned`. Pinned-first, then relevance (US-003-01; BR-019). At least one of `q` or `tags` required (`ASM-BE-006`, `CON-BE-001`). |
| `API-APIDOCS-001@1` | GET | `/openapi.{json\|yaml}` (exact path `GAP-BE-008`) | unauthenticated (`ASM-BE-004`) | `api-docs` | Describes `pinned`, the ordering change (NFR-014), and the login endpoints. Unsupported `Accept` yields 406 or a documented fallback. [stories.md US-004-01] |

`residency-compliance` and `scope-guardrail` own **no end-user HTTP endpoint** evidenced by any input. Residency behaviour is infrastructure-as-code and pipeline-hook work. Report delivery is `GAP-BE-002`. The guardrail's behaviour on excluded routes is specified in `backend.error-model`.

**Consumer obligation under CR-113.** After this revision, every authenticated endpoint above requires a credential obtained from `API-AUTH-002@1`. Client integrators must complete the login flow before calling them. This is the interface change CR-113 declares. The web-app obligation is in `HANDOFF-BE-002`.

<!-- /SECTION: backend.api-surface -->
<!-- SECTION: backend.auth-model | status: DRAFT-UNVERIFIED -->
## Auth model

### Authentication: email one-time code (CR-113)

**Flow.**

1. Caller submits an email address to `API-AUTH-001@1`. The service issues a code, records an OTP challenge, and emails the code via SES. [feedback.md CR-113, preflight]
2. Caller submits the email address and the code to `API-AUTH-002@1`.
3. On a correct, unexpired, unconsumed code, the service creates the User if none exists for that email, consumes the challenge, and returns a credential. [feedback.md preflight; `INV-BE-002`, `INV-BE-003`]
4. A wrong code increments the challenge's failed-attempt count. After the lockout threshold, the email is locked. [feedback.md CR-113; threshold `GAP-BE-024`]
5. A resend is a repeated call to step 1, subject to resend limits. [`ASM-BE-014`; limits `GAP-BE-023`]
6. An unconsumed code expires after its duration. [feedback.md CR-113; duration `GAP-BE-025`]

**Example request, masked per core rule 12.**

```json
{ "email": "a***@e***.com" }
```

**Single-use, expiry and lockout are cited; their numbers are not.** `GAP-BE-023` through `GAP-BE-027` mean no resend count, window, threshold, duration, code length or invalidation rule is stated by any input. None is invented here.

**Credential.** `ASM-BE-016`: verify returns the bearer token assumed in `Q-BE-002`. Format, lifetime, refresh, revocation and logout are `GAP-BE-030`. Until that is closed, no client can rely on token expiry behaviour.

**Account enumeration.** `DERIVED`: because the approved answer is to create the user on first successful verification and not to reject unknown emails, `API-AUTH-001@1` has no account-existence branch. Its response must therefore not differ by account existence. Responses that depend on lock state are keyed by email (`ASM-BE-015`), so they may reveal lock state for an email regardless of whether an account exists. That exposure is `GAP-BE-024` and `GAP-BE-038`.

### Registration access: BLOCKED

**BLOCKED pending `Q-BE-003` and `CON-BE-003`.** The approved answer creates a user on any first successful verification. No input restricts which email addresses may verify. Combined with the shared-workspace default (`Q-BE-001`), any holder of any email address would receive read and write access to every note. Whether such users should receive that access, a domain allow-list, an invitation, or some other restriction, is the human's decision. This section will not assume one.

To unblock: a decision on `Q-BE-003`, recorded as a locked section by the human.

### Actors

| Actor | Calls `notes_api`? | Basis |
|---|---|---|
| Team member (existing account) | Yes: all note, tag, search and archive operations | stories.md EP-002, EP-003 |
| Prospective user (no account) | Yes: `API-AUTH-001@1` and `API-AUTH-002@1` only | feedback.md CR-113, preflight |
| Integration developer / third-party client | Yes: same operations on behalf of a team member. Also reads the OpenAPI document. | stories.md EP-004 |
| Platform/DevOps Engineer | No: operates infra and backup config outside this API | stories.md EP-001 |
| Release/CI Pipeline Owner | No: residency gate runs in the pipeline | stories.md US-001-03 |
| Compliance/Security Auditor | Undetermined: reads the compliance report; delivery is `GAP-BE-002` | stories.md US-001-04 |
| Engineering Lead | Undetermined: launch-gate review, not stated as an API call | stories.md US-001-05 |
| QA Engineer | No: runs the contract test suite in CI | stories.md US-005-05 |
| Product Manager | No: external intake log per BR-017 | stories.md US-005-06 |

### Permission matrix (resources exposed by this API)

| Role | Login: send | Login: verify | Note: create | Note: read | Note: update (incl. `pinned`) | Archive / unarchive | Tag add / remove | Search | OpenAPI doc |
|---|---|---|---|---|---|---|---|---|---|
| Unauthenticated caller | ✅ rate-limited | ✅ subject to lockout | ❌ 401 | ❌ 401 | ❌ 401 | ❌ 401 | ❌ 401 | ❌ 401 | ✅ (`ASM-BE-004`) |
| Team member, existing account | n/a | n/a | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| User created by CR-113 verification | n/a | n/a | **BLOCKED** (`Q-BE-003`) | **BLOCKED** | **BLOCKED** | **BLOCKED** | **BLOCKED** | **BLOCKED** | ✅ |

Pinning and unpinning are PATCH on the note and fall under Note: update. No separate pin permission exists in any input. No row differentiates team members by note ownership. `GAP-BE-007`: no formal RBAC catalogue exists beyond this matrix.

<!-- /SECTION: backend.auth-model -->
<!-- SECTION: backend.data-lifecycle | status: DRAFT-UNVERIFIED -->
## Data lifecycle

| Data | Stored | Retention | Encrypted at rest | Deletion |
|---|---|---|---|---|
| Note (active), incl. `pinned` | Yes | Not stated (`GAP-BE-005`) | Not stated (`GAP-BE-004`) | Never hard-deleted by any documented flow |
| Note (archived), incl. `pinned` | Yes, same record, status changed | Not stated (`GAP-BE-005`) | `GAP-BE-004` | Archive is explicitly **not** deletion: "never deletes underlying data." [requirements.md BR-007] Unarchive restores "without data loss." [BR-008] |
| Tag | Yes, via NOTE_TAG | `GAP-BE-016` | `GAP-BE-004` | Not documented |
| Backup artifact (notes database) | Yes, EU-region storage | Not stated (`GAP-BE-005`) | `GAP-BE-004` | Not documented |
| InfraResource scan record | Implied by compliance report | Not stated (`GAP-BE-005`) | `GAP-BE-004` | Not documented |
| DriftAlert | Implied by post-launch alerting | Not stated (`GAP-BE-005`) | `GAP-BE-004` | Not documented |
| **User (CR-113)**, `email` is PII | Yes, created on first successful verification | `GAP-BE-034` | `GAP-BE-033` | Not documented. No deletion or erasure flow is stated. |
| **OTP challenge (CR-113)**, holds email and code digest | Yes | `GAP-BE-034` | `GAP-BE-033` | Expires after its duration (`GAP-BE-025`). Removal after expiry or consumption is not stated. |
| **Login lock (CR-113)**, holds email | Yes | `GAP-BE-034` | `GAP-BE-033` | Lifts after its duration (`GAP-BE-024`). Removal is not stated. |
| **Bearer credential (CR-113)** | Form and storage not stated | Lifetime `GAP-BE-030` | `GAP-BE-033` | Revocation and logout `GAP-BE-030` |
| **Email in transit to SES (CR-113)** | Held by SES | SES-side retention not stated (`GAP-BE-032`) | Not stated | Not stated |

**CR-113 lifecycle rules.**

- A one-time code is single-use (`INV-BE-002`). Consumption is a state change on the challenge, not a deletion.
- A user is created only by successful verification, never by a send call (`INV-BE-003`).
- Storing the code in a non-reversible digest form is `ASM-BE-017`. This is a proposal, not a stated fact. The storage form remains `GAP-BE-033`.

**CR-112 lifecycle effects.**

- `pinned` is a `NOT NULL` boolean on the note row. It adds no retention or deletion path.
- Archive preserves `pinned`; unarchive restores it. [requirements.md BR-007, BR-008]
- Whether archived notes sort pinned-first in `include_archived=true` search results is unspecified: `GAP-BE-019`.
- A pin change is an update and sets `updated_at`, affecting the within-group ordering: `ASM-BE-009`.

**Residency.** The following are cited. All notes_api database and hosting compute must be provisioned exclusively in approved EU regions. [requirements.md BR-001; stories.md AC-001-01-01] A provisioning request naming a non-EU region must be rejected with no resource created. [AC-001-01-02] Scheduled backups must land in an EU-region storage bucket or service. A non-EU backup destination fails fast before any data is written. [AC-001-02-01, AC-001-02-02]

Whether SES and the email data it processes fall within this residency rule is `Q-BE-004` (blocking). Until it is answered, no residency claim is made about the login path.

<!-- /SECTION: backend.data-lifecycle -->
<!-- SECTION: backend.nfr | status: VERIFIED -->
## Non-functional requirements

Targets below are reproduced from `requirements.md`. No figure is invented.

| ID | Category | Target | Scope |
|---|---|---|---|
| NFR-001 | Compliance | 100% of infrastructure components EU-verified | EP-001 |
| NFR-002 | Compliance | 0 non-EU deploys reach production | EP-001 |
| NFR-003 | Availability | Alert fired within 15 minutes of drift detection | EP-001 |
| NFR-004 | Compliance | Compliance report refreshed and 100% accurate at each launch-gate check | EP-001 |
| NFR-005 | Performance | 100% functional-test pass rate for CRUD operations, zero data loss | EP-002 |
| NFR-006 | Performance | Search p95 latency < 300ms | EP-003 |
| NFR-007 | Scalability | p95 < 300ms maintained up to page sizes of 100 results | EP-003 |
| NFR-008 | Compliance | 100% endpoint coverage in published OpenAPI spec | EP-004 |
| NFR-009 | Usability | 100% of search/tagging endpoints have example payloads | EP-004 |
| NFR-010 | Compliance | 0 out-of-scope endpoints detected per build, every CI build | EP-005 |
| NFR-011 | Usability | 100% of out-of-scope calls return a documented 4xx with explanatory message | EP-005 |
| NFR-012 | Security | 0 critical injection vulnerabilities per release (title, body, tag fields) | EP-002, EP-003 |
| NFR-013 | Availability | 99.9% uptime monthly for CRUD and search | EP-002, EP-003 |
| NFR-014 | (blank) | "The pinned field and the pinned-first ordering are documented in the published OpenAPI spec as an interface change, and consumers are notified that they must adapt." Target: (blank). | CR-112 |

**Notes on the table.**

- NFR-007 is the only numeric bound on search pagination. The default page size is `GAP-BE-015`.
- NFR-014 has no category and no target metric. It is treated as a documentation requirement enforced at contract publication. No numeric target is invented (`GAP-BE-021`).
- **Login has no NFR.** NFR-013 covers CRUD and search only. Availability and latency targets for `API-AUTH-001@1` and `API-AUTH-002@1` are `GAP-BE-036`.
- **NFR-012 may not cover the new fields.** It names title, body and tag. Whether it extends to the email and code fields is `GAP-BE-035`.
- **NFR-001 may not cover SES.** It names compute, DB and backups. Whether SES is "notes_api infrastructure" is `Q-BE-004`.
- Search latency at 100k notes depends on index design, which is `GAP-BE-039`.

<!-- /SECTION: backend.nfr -->
<!-- SECTION: backend.error-model | status: DRAFT-UNVERIFIED -->
## Error model

**Global envelope.** No input states an exact JSON shape. The closest evidence is US-005-04 ("a structured error code (e.g. `OUT_OF_SCOPE`)... with a human-readable reason") and BR-016. `ASM-BE-008`, unverified:

```json
{ "error": { "code": "VALIDATION_ERROR", "message": "human-readable text", "details": [] } }
```

`GAP-BE-006`: the shape is not confirmed by any input.

**Status-code policy.**

| Status | Used for | Source |
|---|---|---|
| 400 | Validation failure: missing title, invalid query, invalid email format on send (format rule `ASM-BE-008`) | US-002-01, US-003-01, US-003-02, US-003-05 |
| 401 | Unauthenticated call to a protected route. Wrong, expired or already-consumed code on verify. | `ASM-BE-012` for the verify case |
| 404 | Note does not exist. Attachment path (see `CON-BE-004`). | US-002-02, US-002-05, US-002-06 |
| 406 | Unsupported `Accept` on the OpenAPI document | US-004-01 |
| 409 | Note already archived | US-002-04 ("conflict/error") |
| 415 | Multipart upload to a notes endpoint | US-005-02, see `ASM-BE-022` |
| 429 | Resend limit exceeded on send; **also** lockout-related rate responses | US-004-03 |
| Locked-account status | Not stated (`GAP-BE-038`) | US-004-03 |
| 503 with `Retry-After` | Search index degraded or unavailable | US-003-03 |

**Out-of-scope errors.** Excluded routes return a documented 4xx with a body naming the scope exclusion (`OUT_OF_SCOPE`), per BR-016, NFR-011 and US-005-04. Attachment paths, which US-005-02 says must return 404, are a conflict with that rule (`CON-BE-004`). The reading adopted is BR-016's.

**Error-code registry.** Codes marked `ASM` have no cited name. They are proposals for the closing decision.

| Code | HTTP status | Trigger | Source |
|---|---|---|---|
| `VALIDATION_ERROR` | 400 | Failed request validation | `ASM-BE-008` |
| `UNAUTHENTICATED` | 401 | No or invalid credential on a protected route | `ASM` |
| `OTP_INVALID_OR_EXPIRED` | 401 | Wrong, expired or consumed code on verify | `ASM-BE-012` |
| `RATE_LIMITED` | 429 | Resend limit exceeded on send | US-004-03 (status cited; code name `ASM`) |
| `ACCOUNT_LOCKED` | `GAP-BE-038` | Email locked after repeated wrong codes | US-004-03 (response cited; status and name `ASM`) |
| `NOT_FOUND` | 404 | Note ID does not exist | US-002-06 (`ASM` for the code name) |
| `ALREADY_ARCHIVED` | 409 | Archive of an archived note | US-002-04 (name `ASM`) |
| `UNSUPPORTED_MEDIA_TYPE` | 415 | Multipart upload | US-005-02, `ASM-BE-022` |
| `OUT_OF_SCOPE` | 4xx (per `CON-BE-004`) | Excluded route or payload | US-005-04 (example name cited) |
| `SEARCH_UNAVAILABLE` | 503 | Search degraded | US-003-03 (name `ASM`) |

**Login error bodies must not leak account state.** `DERIVED` from the account-enumeration rule in `backend.auth-model`. The wrong-code body must not say whether the email has an account. The locked response's disclosure is `GAP-BE-038`.

<!-- /SECTION: backend.error-model -->
<!-- SECTION: backend.observability | status: DRAFT-UNVERIFIED -->
## Observability

No input states a logging, redaction, metering or alerting requirement for the API. Everything below except the NFR-003 drift alert is a **proposal** (`ASM-BE-020`) for the human to accept or change. None is a stated fact.

**Logged (proposed).**

- Every request, with method, route, status, latency and a request identifier.
- Login events: code issued, verify succeeded, verify failed (count, not code), email locked, resend limit hit, user created.
- Scope-guardrail rejections, with the rule matched.

**Redacted (proposed).**

- Codes are never logged, at any level.
- Credentials and tokens are never logged.
- Email addresses are masked in logs (`a***@e***.com` form), consistent with the PII masking rule for examples in core rule 12.
- Note `title` and `body` are not logged. Whether this is required is `GAP-BE-017`.

**Metered (proposed).**

- Login outcome counts by result, for detecting abuse of the send and verify endpoints.
- SES send failures and bounces. Whether SES delivery failures must be handled or alerted is `GAP-BE-032`.
- Search p95 latency, for NFR-006 evidence.
- The endpoint-coverage metric already computed by `api-docs` (US-004-04).

**Alerts.**

- Post-launch non-EU region drift: alert to the DevOps team within 15 minutes. [NFR-003; US-001-06] The channel is `GAP-BE-002`.
- All other alert conditions are not stated. In particular, no alert is stated for a lockout spike, SES failure or search 503 rate. Each is a proposal, not a requirement.

<!-- /SECTION: backend.observability -->
<!-- SECTION: backend.migration-plan | status: DRAFT-UNVERIFIED -->
## Migration plan

Ordered for a greenfield database. If the schema was already applied under the approved revision, `M-002` and `M-004` are the additive migrations and `M-001` is skipped.

| Order | Migration | Contents | Reversibility |
|---|---|---|---|
| M-001 | Create `user` | `user_id`, `email` (unique, normalised per `GAP-BE-031`), `created_at` (timestamptz). Holds PII (`ASM-BE-019`). | Reversible: drop table. Reversal loses every user created by verification. Safe only before first use. |
| M-002 | Create `note` | `id`, `title` (NOT NULL), `body` (NOT NULL), `status` (`active` / `archived`), `pinned` (boolean, NOT NULL, DEFAULT false), `created_at`, `updated_at` (timestamptz). Includes the CR-112 `pinned` column from creation. | Reversible: drop table. Loses all notes. |
| M-003 | Create `tag`, `note_tag` | `note_tag` with primary key `(note_id, tag_name)`. The key enforces idempotent tag add (US-002-03). | Reversible: drop tables. ON DELETE behaviour per `GAP-BE-016`. |
| M-004 | Create `otp_challenge` | `challenge_id`, `email`, `code_digest` (form per `GAP-BE-033`), `issued_at`, `expires_at`, `failed_attempts`, `consumed_at` (nullable). Index on `(email, issued_at)` for the latest-challenge lookup. | Reversible: drop table. Loses in-flight challenges only. Users are unaffected. |
| M-005 | Create `login_lock` | `email` (primary key, normalised), `locked_until` (nullable), `failed_count`. | Reversible: drop table. Loses lock state, which lifts any active lockout early. |
| M-006 | Create `infra_resource`, `drift_alert` | Residency records. Shape per `backend.domain-model`. | Reversible: drop tables. Loses compliance history. |
| M-007 | Note search index | Keyword and tag index for `GET /notes/search`, plus a composite index `(pinned DESC, updated_at DESC)` for `GET /notes`. Keyword index type is `GAP-BE-039`. | Reversible: drop index. No data loss. Dropping it degrades NFR-006 and does not affect correctness. |

**Index reasoning.** The pinned-first sort is served by `M-007`'s composite index so that ordering does not itself threaten NFR-006. [requirements.md NFR-006, NFR-007] The keyword index type is not chosen here, because no input names it (`GAP-BE-039`).

**Why `M-002` is not a separate additive migration.** The approved revision's `pinned` column would have been added with a `DEFAULT false` backfill. In a greenfield database no backfill is needed. In a database that already has notes, `ALTER TABLE note ADD COLUMN pinned boolean NOT NULL DEFAULT false` is additive and reversible by dropping the column, which loses pin state.

<!-- /SECTION: backend.migration-plan -->
<!-- SECTION: backend.coverage | status: DRAFT-UNVERIFIED -->
## Coverage

Every supplied story is listed. "Project-level" means addressed here but with the module-level spec not produced in this run.

| Story | Backend coverage | Where | Status and gap |
|---|---|---|---|
| US-001-01 | BR-001, NFR-001. Infrastructure region constraint. | `residency-compliance`, project-level | Project-level. No API. Module spec pending. |
| US-001-02 | BR-001, AC-001-02-01/02. Backup destination validation. | `residency-compliance`, project-level | Project-level. Mechanics in module spec. |
| US-001-03 | BR-002, NFR-002. CI/CD residency gate. | `residency-compliance`, project-level | Project-level. No API. |
| US-001-04 | BR-001, NFR-004. Compliance report. | `residency-compliance` | Report delivery `GAP-BE-002`. |
| US-001-05 | BR-003. Launch-gate sign-off. | Process; not a system function | Covered as process. Sign-off is by the Engineering Lead. |
| US-001-06 | BR-004, NFR-003. Drift alerting. | `residency-compliance` | Alert channel `GAP-BE-002`. |
| US-002-01 | BR-005, BR-018. `API-NOTES-001@2`. | `notes` | Covered. |
| US-002-02 | BR-006, BR-018. `API-NOTES-002@2`. | `notes` | Covered. Concurrency `GAP-BE-040`. |
| US-002-03 | `API-NOTES-005@1`, `API-NOTES-006@1`. | `notes` | Covered. `GAP-BE-009`, `GAP-BE-016`. |
| US-002-04 | BR-007. `API-NOTES-007@1`. | `notes` | Covered. |
| US-002-05 | BR-008. `API-NOTES-008@1`. | `notes` | Covered. |
| US-002-06 | `API-NOTES-003@2`. | `notes` | Covered. |
| US-003-01 | BR-019. `API-SEARCH-001@2`. | `search` | Covered. Ordering `CON-BE-002`. |
| US-003-02 | `API-SEARCH-001@2`, tag filter. | `search` | Covered. Tag format `GAP-BE-009`. |
| US-003-03 | NFR-006, NFR-007, NFR-013. 503 with `Retry-After`. | `search` | Covered. Index `GAP-BE-039`. |
| US-003-04 | `API-SEARCH-001@2`, combined filters. | `search` | Covered. `ASM-BE-006`. |
| US-003-05 | `API-SEARCH-001@2`, pagination. | `search` | Covered. Page size `GAP-BE-015`. |
| US-003-06 | BR-009. `include_archived`. | `search` | Covered. `GAP-BE-019`. |
| US-004-01 | `API-APIDOCS-001@1`. | `api-docs` | Covered. Path `GAP-BE-008`. |
| US-004-02 | Schemas, per module contract. | `api-docs` | Project-level. `SCHEMA-` IDs deferred. |
| US-004-03 | 401/403, 429 and locked responses for login. | `api-docs`, `auth` | Partial. `GAP-BE-028`, `GAP-BE-038`. |
| US-004-04 | BR-012, NFR-008. Coverage metric. | `api-docs` | Covered. |
| US-004-05 | NFR-009. Examples. | `api-docs` | Covered. Login examples must mask email. |
| US-004-06 | BR-013. Data-residency note. | `api-docs` | Covered in form. Residency of login path `Q-BE-004`. |
| US-005-01 | Scope section in the docs. | `api-docs` | Covered. |
| US-005-02 | BR-014. Attachment guardrail. | `scope-guardrail` | Conflict on attachment status: `CON-BE-004`. |
| US-005-03 | BR-015. Real-time guardrail. | `scope-guardrail` | Covered. Concurrent PATCH policy `GAP-BE-040`. |
| US-005-04 | BR-016, NFR-011. `OUT_OF_SCOPE`. | `scope-guardrail` | Covered. |
| US-005-05 | NFR-010. CI guardrail suite. | `scope-guardrail` | Covered. |
| US-005-06 | BR-017. PM intake log. | Not a backend function | Not backend. Intentionally excluded (see `backend.overview`). |
| US-006-01 | Consumes `API-NOTES-004@2`. | Web-app, out of run | Not backend. Handed off via `HANDOFF-BE-002`. |
| US-006-02 | Consumes `API-NOTES-001@2`, `API-NOTES-002@2`. | Web-app, out of run | Not backend. |
| US-006-03 | Consumes `API-SEARCH-001@2`. | Web-app, out of run | Not backend. |
| US-006-04 | Consumes `API-NOTES-005@1`, `API-NOTES-006@1`. | Web-app, out of run | Not backend. |
| US-006-05 | Consumes `API-NOTES-007@1`, `API-NOTES-008@1`. | Web-app, out of run | Not backend. |
| CR-113 (no story) | `API-AUTH-001@1`, `API-AUTH-002@1`. | `auth` | **Partial.** No story gives Given/When/Then for OTP. `GAP-BE-029`. |

**Uncovered by design:** US-005-06 and US-006-01 to US-006-05, for the reasons above.

**Uncovered by gap:** CR-113 has no user story (`GAP-BE-029`). Its requirements are derived from the change-request description and are therefore `DRAFT-UNVERIFIED`.

<!-- /SECTION: backend.coverage -->
