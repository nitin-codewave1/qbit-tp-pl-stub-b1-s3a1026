---
id: agent-developer/notes_api/backend/BACKEND-SPEC.md
type: api_contract
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/BACKEND-SPEC.md
depends_on: [US-004-03, US-006-06, US-005-01, US-005-04, US-005-06, US-002-02, US-002-04, US-002-05, US-002-06, US-002-03, US-001-06, US-003-01, US-004-01, US-001-03, US-001-04, US-001-05, US-005-05, US-001-02, US-003-02, US-003-04, US-003-05, US-003-06, US-005-02, US-003-03, US-004-04, US-001-01, US-002-01, US-004-02, US-004-05, US-004-06, US-005-03, US-006-01, US-006-02, US-006-03, US-006-04, US-006-05]
commit_kind: fork
confidence: agent
run_id: d2d1121b-4c4e-54fc-a65f-69e9b55e380c
okf_id: backend/BACKEND-SPEC.md
---

<!-- SECTION: backend.overview | status: DERIVED -->
## Overview

**Responsibility.** `notes_api` is the single backend service for a team note-taking product. It is responsible for:

1. **Application API.** Create, read, update, pin/unpin, tag, archive/unarchive and search team notes, published as a versioned OpenAPI document. [stories.md EP-002, EP-003, EP-004]
2. **Email one-time-code login (CR-114).** A caller requests a one-time code for an email address, verifies it, and receives a credential. The service enforces resend rate limits, locks an email address after five wrong codes for 15 minutes, and expires codes 10 minutes after issue. Codes are emailed through AWS SES. [feedback.md CR-114; feedback.md preflight answers; stories.md US-004-03, US-006-06] This is an interface change: consumers must adapt. [feedback.md CR-114]
3. **EU data-residency compliance.** Provisioning `notes_api`'s own database, compute and backups in approved EU regions, gating deploys on a residency check, generating a compliance report, enforcing a launch gate, and alerting on post-launch region drift. [stories.md EP-001; requirements.md BR-001 to BR-004, NFR-001 to NFR-004]

**Not responsible for.**

- Rendering any user interface. The web-app surface (stories.md EP-006) is a separate run. This document defines only the API it consumes. [stories.md EP-006]
- Attachment and file-upload endpoints of any kind. [requirements.md BR-014]
- Any real-time channel (WebSocket, SSE, polling-push) for collaborative editing. [requirements.md BR-015]
- Mobile-specific endpoints. [stories.md US-005-01, US-005-04]
- Any login method other than email one-time code. No input names a password, social or SSO login. [feedback.md CR-114]
- Logging and classifying stakeholder feature requests. This is a Product-Manager intake process. [requirements.md BR-017; stories.md US-005-06]

**Change requests in this revision.**

- **CR-112 (retained from the approved revision).** Notes carry a boolean `pinned` flag, default `false`, settable on create and via PATCH, returned in note responses. Pinned notes sort before unpinned notes in list and search results. This is an interface change. [feedback.md CR-112 as retained in the approved revision; requirements.md BR-018, BR-019, NFR-014]
- **CR-114 (this run).** Email one-time-code login as in capability 2. Note, tag, search and residency behaviour are unchanged by CR-114. [feedback.md CR-114]
- **Labelling discrepancy.** The approved revision labels the email one-time-code change `CR-113`. The current change request is `CR-114`. The content is the same. The discrepancy is recorded as `CON-BE-005`. This document uses `CR-114`, the label on the current request.

**Stack.** Node.js 20, Express, PostgreSQL 16, hosted on AWS. Transactional email via AWS SES. Sessions and tokens reuse the application's existing session/token mechanism. [platform.md; feedback.md preflight answers]

**Revision state and ID continuity.**

- The approved revision's own continuation maxima are lower than the identifiers it actually uses. For example, its GAP list reaches `GAP-BE-040` and its invariants reach `INV-BE-004`. This revision continues from the highest identifier observed: `GAP-BE-041` onward, `ASM-BE-023` onward, `CON-BE-005` onward, `Q-BE-005` onward, `INV-BE-005` onward. No identifier is reused.
- **Open blocking questions.** `Q-BE-003` (who may verify and receive access) and `Q-BE-005` (SES region and residency of email transmission) are open.
- **Open Critical contradictions.** `CON-BE-003` (registration scope against the shared-workspace default) and `CON-BE-006` (email transmission against BR-001).
- **Undefined reference.** The approved revision lists `Q-BE-004` as a second open blocking question, but no supplied document gives its text. It is carried forward as open and unanswered. No section below is written on its basis.
- Sections that depend on the open blocking questions or Critical contradictions are marked `BLOCKED` or carry a `BLOCKED` sub-block. Under `HALT-BLOCKING-QUESTION` and `HALT-CRITICAL-CONTRADICTION` the orchestrator is expected to halt this run. This document records the work completed before that point.

<!-- /SECTION: backend.overview -->
<!-- SECTION: backend.domain-model | status: DRAFT-UNVERIFIED -->
## Domain model

Three sub-domains are evidenced: the **note-taking domain** (Note, Tag), the **login domain** (User, OTP challenge, login lock), and the **residency-compliance domain** (InfraResource, DriftAlert). The login domain is new in this revision. [feedback.md CR-114] No input relates these sub-domains beyond "notes_api's infrastructure hosts the note-taking data." [requirements.md BR-013]

```mermaid
erDiagram
  NOTE ||--o{ NOTE_TAG : "tagged by"
  TAG  ||--o{ NOTE_TAG : "applied to"
  USER {
    string user_id
    string email "PII; unique, normalisation per GAP-BE-031"
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
    datetime locked_until "issued at 5 wrong codes; 15 minutes (US-004-03)"
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
- **User.** A person who has verified an email address. Created on first successful verification, not on code request. The rule is carried from the approved revision. Its cited source, the preflight answers, does not contain it. [ASM-BE-025; GAP-BE-041] `email` is personal data. This classification is `ASM-BE-019`. The handling policy is `GAP-BE-033`. Normalisation and uniqueness are `GAP-BE-031`.
- **OTP challenge (CR-114).** One issued one-time code for one email address, keyed by email because the user may not exist yet. Expiry is 10 minutes from issue. [stories.md US-006-06] The code is single-use. [INV-BE-002; ASM-BE-025] Expiry is enforced by `INV-BE-004`. The code length and storage form are `GAP-BE-026`/`GAP-BE-027` and `GAP-BE-033` respectively.
- **Login lock (CR-114).** The lock state for an email address. A lock is entered after five wrong codes and lasts 15 minutes. [stories.md US-004-03, US-006-06] Keyed by normalised email: `ASM-BE-015`. Whether the wrong-code count resets, and whether a locked address can still request codes, is `GAP-BE-024`.
- **InfraResource.** A provisioned notes_api component (DB, compute, backup storage) with its reported region and compliance status. Unrecognised resource types are flagged `unverified`, never silently omitted. [stories.md AC-001-04-01, AC-001-04-02]
- **DriftAlert.** A record of a post-launch detection of an InfraResource in a non-EU region. [stories.md US-001-06]

**Cardinality and ownership.**

- A User and a Note have no relationship in the diagram. No input states whether a Note belongs to a user, is private, or is shared. `GAP-BE-003` records this. The default applied is `Q-BE-001` (class: degradable): notes are a shared team workspace, and any authenticated team member may read and write any note.
- `CON-BE-003` (Critical) calls that default into question. Under CR-114 any holder of an email address that verifies can obtain a credential, so "team member" no longer names a bounded group. Until `Q-BE-003` is answered, the shared-workspace default applies only to users created by a route the human has approved. See `backend.auth-model`.

**PII.** Personal data now exists in this domain: `USER.email`, `OTP_CHALLENGE.email` and `LOGIN_LOCK.email`, which hold the same value. Note `title` and `body` are free text that may incidentally contain PII. No masking or handling policy is supplied for any of them. `GAP-BE-017` (general PII policy), `GAP-BE-033` (at-rest treatment) and `GAP-BE-035` (injection and validation scope for the email and code fields) record this. Per core rule 12, no encryption, redaction or retention claim is made in their absence.

<!-- /SECTION: backend.domain-model -->
<!-- SECTION: backend.module-map | status: DERIVED -->
## Module map

| Module | Boundary | Depends on |
|---|---|---|
| `auth` | Email one-time-code login (CR-114): code issuance and delivery via SES, verification, resend limits, lockout, code expiry, user creation on first verification, credential issuance through the existing session/token mechanism. Owns User, OTP_CHALLENGE and LOGIN_LOCK. | external: AWS SES (see `backend.auth-model`, email transmission) |
| `notes` | Note CRUD, pin/unpin (via PATCH), tag assignment and removal, archive/unarchive lifecycle. Owns Note, Tag and NOTE_TAG. | `auth` (identity of the caller) |
| `search` | Read-only query surface over Note: keyword, tag filter, pagination, archived-exclusion default, pinned-first ordering. | `notes`, `auth` |
| `residency-compliance` | Infra provisioning region validation, backup-destination validation, CI/CD residency gate, compliance report, launch-gate status, post-launch drift scan and alerting. Owns InfraResource and DriftAlert. | none at runtime (operates on the infrastructure and CI/CD layer) |
| `api-docs` | Publishes the OpenAPI document, including `pinned`, the ordering change and the login endpoints. Computes the endpoint-coverage metric. Carries the data-residency documentation note and the Scope section. | `notes`, `search`, `auth`, `residency-compliance` |
| `scope-guardrail` | Intercepts every inbound request ahead of routing. Rejects attachment/multipart, WebSocket/SSE-upgrade and mobile-specific patterns with a structured out-of-scope response. Backs the contract test suite. | sits upstream of all modules; cross-cutting |

**Dependency direction.** `search → notes`. `search → auth`. `notes → auth`. `api-docs → notes, search, auth, residency-compliance`. `auth → SES` (external). Requests flow through `scope-guardrail` first, then to `auth` for protected routes, then to `notes`, `search` or `api-docs`.

**Ordering of guardrail and authentication.** The guardrail runs before authentication, so an out-of-scope call returns its documented out-of-scope response without a credential. `ASM-BE-018`. The response form for excluded paths is in conflict across inputs: `CON-BE-007`.

**CR-112 impact on boundaries.** No new module. Pinned-first ordering is owned by `notes` for `GET /notes` and by `search` for `GET /notes/search`. Both apply the same rule (`INV-BE-001`).

**CR-114 impact on boundaries.** `auth` is the new module. It is a new dependency of `notes` and `search`, which previously had no identity dependency. It is the only module that calls SES. The SES dependency carries a residency question: `CON-BE-006`.

<!-- /SECTION: backend.module-map -->
<!-- SECTION: backend.api-surface | status: DRAFT-UNVERIFIED -->
## API surface index

Full contracts (request and response schemas, every error, idempotency, rate limits) belong in the per-module `api-contract` sections. Those are not produced in this run. This index is their introduction point. Schema IDs (`SCHEMA-`) for the login payloads are deferred to the `auth` module contract.

**Versioning.** Endpoints whose request or response shape changes are re-pinned from `@1` to `@2` (`ASM-BE-010`). New endpoints start at `@1` (`ASM-BE-021`). CR-114 adds no shape change to existing endpoints, so their pins stay at `@2`. Whether CR-114 should also bump authenticated endpoints, because their caller obligation changes, is `GAP-BE-037`.

| API ID | Method | Path | Auth | Module | Notes |
|---|---|---|---|---|---|
| `API-AUTH-001@1` | POST | `/auth/otp/send` | unauthenticated; rate-limited (`GAP-BE-023`) | `auth` | **New (CR-114).** Issues a one-time code to an email address and emails it via SES. Also the resend path (`ASM-BE-014`). Response does not reveal whether an account exists (`backend.auth-model`). Returns 429 when the resend limit is exceeded (US-004-03); body and code `GAP-BE-028`. Path: `ASM-BE-013`. Email transmission region: `CON-BE-006`. |
| `API-AUTH-002@1` | POST | `/auth/otp/verify` | unauthenticated; subject to lockout (`GAP-BE-024`) | `auth` | **New (CR-114).** Verifies a code for an email address. Wrong or expired code: rejected (`ASM-BE-012`; expiry 10 minutes, `ASM-BE-023`). Five wrong codes lock the address for 15 minutes; locked response returns a locked-account response (US-004-03; status and body `GAP-BE-038`). On success creates the User if absent (`INV-BE-003`, `GAP-BE-041`) and returns a credential issued by the existing session/token mechanism (`GAP-BE-030`). |
| `API-NOTES-001@2` | POST | `/notes` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned` (boolean, default `false`). Returns `pinned` in the 201 body. Title and body rules unchanged. [stories.md AC-002-01-01, AC-002-01-02; requirements.md BR-005, BR-018] |
| `API-NOTES-002@2` | PATCH | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Accepts optional `pinned`. Partial update: unsent fields are never overwritten (BR-006). Returns `pinned`. Pinning leaves title, body and tags unchanged. [stories.md AC-002-02-02; requirements.md BR-006, BR-018] `ASM-BE-001`: PATCH, not PUT. Concurrent-write policy: `GAP-BE-040`. |
| `API-NOTES-003@2` | GET | `/notes/{id}` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Returns `pinned` with tags and archive status. [stories.md US-002-06] |
| `API-NOTES-004@2` | GET | `/notes` | authenticated team member | `notes` | **Changed from @1 (CR-112).** Default active-notes list. Pinned-first, then most recently updated within each group (`CON-BE-002`). Parameters `GAP-BE-001`. Page size `GAP-BE-015`. |
| `API-NOTES-005@1` | POST | `/notes/{id}/tags` | authenticated team member | `notes` | Add tag, idempotent. [stories.md US-002-03] `ASM-BE-002`. |
| `API-NOTES-006@1` | DELETE | `/notes/{id}/tags/{tag}` | authenticated team member | `notes` | Remove tag. `ASM-BE-002`. |
| `API-NOTES-007@1` | POST | `/notes/{id}/archive` | authenticated team member | `notes` | Archive. Pinned state preserved (`backend.data-lifecycle`). Repeat archive returns a conflict/error response. [stories.md US-002-04] |
| `API-NOTES-008@1` | POST | `/notes/{id}/unarchive` | authenticated team member | `notes` | Restores full prior state including `pinned`. [stories.md US-002-05; requirements.md BR-008] |
| `API-SEARCH-001@2` | GET | `/notes/search` | authenticated team member | `search` | **Changed from @1 (CR-112).** Keyword (`q`), `tags`, `page`, `limit`, `include_archived`. Returns `pinned`. Pinned-first, then relevance. [stories.md US-003-01; requirements.md BR-019] At least one of `q` or `tags` required (`ASM-BE-006`, `CON-BE-001`). |
| `API-APIDOCS-001@1` | GET | `/openapi.{json\|yaml}` (exact path `GAP-BE-008`) | unauthenticated (`ASM-BE-004`) | `api-docs` | Describes `pinned`, the ordering change (NFR-014), and both login endpoints. Unsupported `Accept` yields 406 or a documented fallback. [stories.md US-004-01, US-004-03] |

`residency-compliance` and `scope-guardrail` own **no end-user HTTP endpoint** evidenced by any input. Residency behaviour is infrastructure-as-code and pipeline-hook work. Report delivery is `GAP-BE-002`. The guardrail's behaviour on excluded paths is specified in `backend.error-model` and is in conflict across inputs: `CON-BE-007`.

**Consumer obligation under CR-114.** After this revision, every authenticated endpoint above requires a credential obtained from `API-AUTH-002@1`. Client integrators must complete the login flow before calling them. This is the interface change CR-114 declares. [feedback.md CR-114] The web-app obligation is in `HANDOFF-BE-002`.

<!-- /SECTION: backend.api-surface -->
<!-- SECTION: backend.auth-model | status: DRAFT-UNVERIFIED -->
## Auth model

### Authentication: email one-time code (CR-114)

**Flow.**

1. **Send.** The caller submits an email address to `API-AUTH-001@1`. The service issues a code, records an OTP challenge, and emails the code via SES. [feedback.md CR-114; feedback.md preflight answers] Resend limit: `GAP-BE-023`. Exceeding it returns 429. [stories.md US-004-03]
2. **Verify.** The caller submits the email address and the code to `API-AUTH-002@1`.
3. **Success.** On a correct, unexpired, unconsumed code, the service creates the User if none exists for that email, consumes the challenge, and returns a credential. [INV-BE-002, INV-BE-003; ASM-BE-025; GAP-BE-041]
4. **Wrong code.** A wrong code increments the challenge's failed-attempt count. At five wrong codes the email address is locked for 15 minutes and the caller receives a locked-account response. [stories.md US-004-03, US-006-06] Lock status, body and unlock detail: `GAP-BE-024`, `GAP-BE-038`.
5. **Expiry.** A code expires 10 minutes after issue. A submission more than 10 minutes after issue is rejected with an expired-code outcome. [stories.md US-006-06] This is the only source for the duration. It is a web-app story, so the backend reading is `ASM-BE-023`.
6. **Locked during verification.** No successful verification occurs for an email address while its lock is in effect. [INV-BE-005; derived from "locked for 15 minutes", stories.md US-006-06]

**Example request, masked per core rule 12.**

```json
{ "email": "a***@e***.com", "code": "<one-time code>" }
```

**Credential.** The approved answer to the question of which token type to issue is to reuse the existing session/token mechanism. [feedback.md preflight answers] No input describes that mechanism. Format, lifetime, refresh, revocation and logout are `GAP-BE-030`. Until that is closed, no client can rely on token expiry behaviour.

**Account enumeration.** `DERIVED`. Because the approved revision creates the user on first successful verification and does not reject unknown emails, `API-AUTH-001@1` has no account-existence branch. Its response must not differ by account existence. Responses that depend on lock state are keyed by email (`ASM-BE-015`), so they may reveal lock state for an email whether or not an account exists. That exposure is `GAP-BE-024` and `GAP-BE-038`.

**Numbers not stated.** No input states a resend count, resend window, code length, or the invalidation of earlier codes when a new one is issued. These are `GAP-BE-023`, `GAP-BE-026` and `GAP-BE-027`. None is invented here.

### Email transmission: BLOCKED pending `Q-BE-005` and `CON-BE-006`

No input states the AWS region from which SES sends. BR-001 requires that "all notes_api infrastructure (database, compute, backups)" be provisioned in approved EU regions, and it does not name email delivery. [requirements.md BR-001] The OTP send path transmits each email address to SES. Whether that transmission is within the residency requirement is unresolved. Because it is a PII path, the contradiction is classed Critical under core rule 8.

To unblock: an answer to `Q-BE-005` naming the SES region and whether BR-001 covers transactional email, recorded by the human.

### Registration access: BLOCKED pending `Q-BE-003` and `CON-BE-003`

The approved answer creates a user on any first successful verification. No input restricts which email addresses may verify. Combined with the shared-workspace default (`Q-BE-001`), any holder of any email address would receive read and write access to every note. Whether such users should receive that access, a domain allow-list, an invitation, or some other restriction, is the human's decision. This section will not assume one.

To unblock: a decision on `Q-BE-003`, recorded as a locked section by the human.

### Actors

| Actor | Calls `notes_api`? | Basis |
|---|---|---|
| Team member (existing account) | Yes: all note, tag, search and archive operations | stories.md EP-002, EP-003 |
| Prospective user (no account) | Yes: `API-AUTH-001@1` and `API-AUTH-002@1` only | feedback.md CR-114 |
| Integration developer / third-party client | Yes: same operations on behalf of a team member. Also reads the OpenAPI document. | stories.md EP-004 |
| Web-app user | Yes: consumes the login and note endpoints through the web app. Web surface is a separate run. | stories.md US-006-06; `HANDOFF-BE-002` |
| Platform/DevOps Engineer | No: operates infrastructure and backup configuration outside this API | stories.md EP-001 |
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
| User created by CR-114 verification | n/a | n/a | **BLOCKED** (`Q-BE-003`) | **BLOCKED** | **BLOCKED** | **BLOCKED** | **BLOCKED** | **BLOCKED** | ✅ |

Pinning and unpinning are PATCH on the note and fall under Note: update. No separate pin permission exists in any input. No row differentiates team members by note ownership. `GAP-BE-007`: no formal RBAC catalogue exists beyond this matrix.

<!-- /SECTION: backend.auth-model -->
<!-- SECTION: backend.data-lifecycle | status: DRAFT-UNVERIFIED -->
## Data lifecycle

| Data | Stored | Retention | Encrypted at rest | Deletion |
|---|---|---|---|---|
| Note (active), incl. `pinned` | Yes | Not stated (`GAP-BE-005`) | Not stated (`GAP-BE-004`) | No documented flow hard-deletes an active note |
| Note (archived), incl. `pinned` | Yes; retained with all data | Not stated (`GAP-BE-005`) | Not stated (`GAP-BE-004`) | Archiving never deletes underlying data. [requirements.md BR-007] Unarchive restores full prior state. [BR-008] |
| Tag and NOTE_TAG | Yes | Not stated | Not stated | Removal per tag operation; cascade on last note `GAP-BE-016` |
| User (email, created on first verification) | Yes, per the approved revision (`GAP-BE-041`) | Not stated (`GAP-BE-017`) | Not stated (`GAP-BE-033`) | Not stated. No deletion or account-closure flow is in any input. |
| OTP challenge (includes email and code digest) | Yes | Not stated after expiry | Code stored as digest; storage form `GAP-BE-033` | Not stated. Expiry at 10 minutes. [stories.md US-006-06] |
| Login lock (email) | Yes | Ends 15 minutes after lock; row retention not stated | Not stated (`GAP-BE-033`) | Not stated |
| Backups of the notes database | Yes, to EU storage | Not stated | Not stated | Not stated; storage location required by US-001-02 and BR-001 |
| InfraResource and DriftAlert records | Yes | Not stated | Not stated | Not stated |
| Email content and SES delivery records | Yes, through SES | Not stated | Not stated | Not stated (`CON-BE-006`) |

**Standing rule.** No encryption, redaction or retention claim is made where no input states one. Core rule 12 applies.

<!-- /SECTION: backend.data-lifecycle -->
<!-- SECTION: backend.nfr | status: VERIFIED -->
## Non-functional requirements

Each target below is cited to its input. Targets are not restated as this run's own figures.

| Area | Target | Applies to | Source |
|---|---|---|---|
| Residency: infrastructure | 100% of components EU-verified | DB, compute, backups | NFR-001 |
| Residency: pipeline | 0 non-EU deploys reach production; check runs before every production deploy | CI/CD | NFR-002 |
| Residency: drift alert | Alert fired within 15 minutes of drift detection | post-launch scan | NFR-003 |
| Residency: report | Refreshed and 100% accurate at each launch-gate check | compliance report | NFR-004 |
| CRUD correctness | 100% functional-test pass rate; zero data loss | create, edit, get, archive, unarchive | NFR-005 |
| Search latency | p95 < 300 ms under typical load | `GET /notes/search` | NFR-006 |
| Search pagination | p95 < 300 ms maintained up to page sizes of 100 results | `GET /notes/search` | NFR-007 |
| Spec coverage | 100% endpoint coverage in published spec, with schemas and error responses | OpenAPI document | NFR-008 |
| Spec examples | 100% of search and tagging endpoints have example payloads | OpenAPI document | NFR-009 |
| Scope guardrail | 0 out-of-scope endpoints detected per build | contract test suite | NFR-010 |
| Out-of-scope responses | 100% of out-of-scope calls return documented 4xx with explanatory message | guardrail | NFR-011 |
| Injection | 0 critical injection vulnerabilities per release | title, body, tag fields. Email and code fields are not named; `GAP-BE-035`. | NFR-012 |
| Availability | 99.9% uptime monthly | CRUD and search operations | NFR-013 |
| Ordering documentation | Pinned field and pinned-first ordering documented as an interface change | OpenAPI document | NFR-014 |

**Not stated.** No NFR covers the login flow: latency, availability, or email delivery time. NFR-013 names CRUD and search only. These are `GAP-BE-043`. No target is invented for them.

<!-- /SECTION: backend.nfr -->
<!-- SECTION: backend.error-model | status: DRAFT-UNVERIFIED -->
## Error model

**Global error envelope.** Not stated by any input. `GAP-BE-042`. Until it is supplied, no client may rely on a field name, a code field, or a message field.

**Error-code registry.** Only one code is named by any input: `OUT_OF_SCOPE`, given as an example of a "structured error code" in US-005-04. Its full set is `GAP-BE-042`.

**Status-code policy.** Each status below is cited to the story that names it.

| Status | Where | Source |
|---|---|---|
| 200 | Successful read or update | stories.md US-002-02, US-002-05, US-003-01 |
| 201 | Successful create | stories.md AC-002-01-01, AC-002-01-02 |
| 400 | Validation failure (missing title; malformed filters; invalid `include_archived`; negative or non-numeric `page`/`limit`) | stories.md AC-002-01-03, US-003-01, US-003-02, US-003-04, US-003-05, US-003-06 |
| 401 | Missing or invalid credential on protected routes | stories.md EP-002 (implied by authentication requirement); documented per US-004-03 |
| 403 | Authenticated but not authorised | stories.md US-004-03 (documented, role mapping `GAP-BE-007`) |
| 404 | Non-existent note ID; unknown route | stories.md AC-002-02-03, US-002-05, US-002-06; `CON-BE-007` for excluded paths |
| 406 | Unsupported `Accept` on the OpenAPI document (or a documented fallback) | stories.md US-004-01 |
| 409 or conflict | Archiving an already-archived note | stories.md US-002-04 |
| 415 | Multipart upload to a notes endpoint (or 400) | stories.md US-005-02 |
| 429 | Email login resend limit exceeded | stories.md US-004-03; limit `GAP-BE-023`, body `GAP-BE-028` |
| 503 with retry-after | Search index degraded or unavailable | stories.md US-003-03 |
| Locked-account response | Email address locked after five wrong codes | stories.md US-004-03, US-006-06; status and body `GAP-BE-038` |
| Expired-code outcome | Code submitted more than 10 minutes after issue | stories.md US-006-06; status `GAP-BE-042` |
| Out-of-scope response | Attachment, real-time or mobile-specific call | stories.md US-005-02 (404), US-005-04 (explanatory message, not a bare 404); `CON-BE-007` |

**Out-of-scope responses.** Two inputs disagree. US-005-02 says an attachment path returns "404 Not Found". US-005-04 says an out-of-scope call returns "a message referencing the scope exclusion rather than a bare 404". BR-016 requires "a clear, documented error, not a silent failure or 404 ambiguity". `CON-BE-007` (non-critical) records the conflict. The guardrail's response is not written until it is resolved, and the status code is not chosen here.

**Illegal state transitions.** Repeated archive returns a conflict-type response (US-002-04). Other illegal transitions are not named by any input. `GAP-BE-042`.

<!-- /SECTION: backend.error-model -->
<!-- SECTION: backend.observability | status: DRAFT-UNVERIFIED -->
## Observability

**Alerts stated by inputs.**

- **Drift alert.** A non-EU resource discovered post-launch raises an alert to the DevOps team immediately, with resource details. [stories.md AC-001-06-02] Detection-to-alert target: 15 minutes. [NFR-003]
- **Coverage metric.** The endpoint-coverage metric is computed and exposed. [stories.md US-004-04; NFR-008]
- **Contract tests.** The scope-guardrail suite runs on every CI build. [NFR-010; stories.md US-005-05]
- **Compliance report.** Generated for each launch-gate check. [NFR-004]

**Logged.** No input states what the API logs. Whether one-time codes or email addresses appear in logs is not stated. Core rule 12 applies: no assertion is made. `GAP-BE-017` (PII policy), `GAP-BE-035` (validation and injection scope).

**Redacted.** Not stated. Email and code fields are PII-bearing under `backend.domain-model` but no redaction rule is supplied.

**Metered.** Not stated, beyond the endpoint-coverage metric and the compliance metrics above. No login metrics (send counts, verification failures, lockouts) are named by any input.

**Lockout alerting.** No input requires an alert on lockout or on resend-limit breaches. None is added.

<!-- /SECTION: backend.observability -->
<!-- SECTION: backend.migration-plan | status: DRAFT-UNVERIFIED -->
## Migration plan

**Environment state.** No input states whether any notes table is already populated in production. This plan assumes a greenfield database for the new login tables (`ASM-BE-024`). Where a migration would alter populated data, that is noted.

Ordered migrations:

1. **Add `pinned` to `notes` (CR-112).** `BOOLEAN NOT NULL DEFAULT false`. Reversible by dropping the column, but rollback discards every stored pinned value. Precondition: `ASM-BE-024`.
2. **Index for pinned-first ordering.** Serves `GET /notes` and `GET /notes/search` (`INV-BE-001`). Reversible by dropping the index. No data change.
3. **Create `otp_challenge` (CR-114).** Additive. Reversible by dropping the table, which discards in-flight challenges only. Expiry 10 minutes, so retention beyond expiry is not stated (`backend.data-lifecycle`).
4. **Create `login_lock` (CR-114).** Additive. Reversible by dropping the table, which lifts all current locks. Keyed by normalised email (`ASM-BE-015`).
5. **Create `user` (CR-114).** Additive. Reversible by dropping the table, but once users exist that is destructive: it removes verified email addresses. Treat as irreversible after first use.
6. **Unique constraint on normalised email in `user`.** **BLOCKED** pending `GAP-BE-031`. Normalisation is not decided, so the constraint cannot be written.

**Interface rollout.** Steps 3 to 6 do not change any existing response. Step 1 and the `API-NOTES-*@2` pins change response shape, and consumers must adapt. [feedback.md CR-112, CR-114] Rolling back after consumers have adapted breaks them.

<!-- /SECTION: backend.migration-plan -->
<!-- SECTION: backend.coverage | status: VERIFIED -->
## Coverage

Every story in the supplied set is listed. Stories with `EP-006` belong to the web-app surface and are answered by this surface only where they consume the API. Stories that are process rather than system functions are excluded explicitly.

| Story | Epic | Covered by | Notes |
|---|---|---|---|
| US-001-01 | EP-001 | BR-001; NFR-001 | Residency-compliance module (IaC). No endpoint. |
| US-001-02 | EP-001 | BR-001 | Backup destination validation. No endpoint. |
| US-001-03 | EP-001 | BR-002; NFR-002 | CI/CD gate. No endpoint. |
| US-001-04 | EP-001 | NFR-004; BR-001 | Report delivery `GAP-BE-002`. |
| US-001-05 | EP-001 | BR-003 | Launch gate. Engineering Lead sign-off. |
| US-001-06 | EP-001 | BR-004; NFR-003 | Drift scan and alert. |
| US-002-01 | EP-002 | `API-NOTES-001@2`; BR-005; BR-018; NFR-005 | |
| US-002-02 | EP-002 | `API-NOTES-002@2`; BR-006; BR-018 | Concurrent writes `GAP-BE-040`. |
| US-002-03 | EP-002 | `API-NOTES-005@1`, `API-NOTES-006@1` | Idempotent add. `GAP-BE-016`. |
| US-002-04 | EP-002 | `API-NOTES-007@1`; BR-007 | Conflict response on repeat archive. |
| US-002-05 | EP-002 | `API-NOTES-008@1`; BR-008 | |
| US-002-06 | EP-002 | `API-NOTES-003@2` | |
| US-003-01 | EP-003 | `API-SEARCH-001@2`; BR-019 | |
| US-003-02 | EP-003 | `API-SEARCH-001@2`; BR-010 | |
| US-003-03 | EP-003 | NFR-006; NFR-007; NFR-013 | 503 with retry-after per AC. |
| US-003-04 | EP-003 | `API-SEARCH-001@2`; BR-010 | |
| US-003-05 | EP-003 | `API-SEARCH-001@2`; BR-011; NFR-007 | Page size `GAP-BE-015`. |
| US-003-06 | EP-003 | `API-SEARCH-001@2`; BR-009 | |
| US-004-01 | EP-004 | `API-APIDOCS-001@1`; `API-AUTH-001@1`; `API-AUTH-002@1`; BR-012; NFR-008 | Login endpoints listed in the document. |
| US-004-02 | EP-004 | `API-APIDOCS-001@1`; NFR-008 | Schemas deferred to contracts. |
| US-004-03 | EP-004 | `API-APIDOCS-001@1`; `API-AUTH-001@1`; `API-AUTH-002@1`; `backend.error-model` | 429, 401/403, locked-account response. `GAP-BE-028`, `GAP-BE-038`. |
| US-004-04 | EP-004 | BR-012; NFR-008 | Coverage metric in `api-docs`. |
| US-004-05 | EP-004 | NFR-009 | Examples for search and tagging. |
| US-004-06 | EP-004 | BR-013 | Data-handling note. Must be reconciled with `CON-BE-006`. |
| US-005-01 | EP-005 | NFR-011; `api-docs` (Scope section) | |
| US-005-02 | EP-005 | BR-014; `scope-guardrail` | Status conflict `CON-BE-007`. |
| US-005-03 | EP-005 | BR-015; `scope-guardrail`; `GAP-BE-040` | Concurrent-PATCH response not specified. |
| US-005-04 | EP-005 | BR-016; NFR-011 | Status conflict `CON-BE-007`. |
| US-005-05 | EP-005 | NFR-010 | Contract test suite. |
| US-005-06 | EP-005 | BR-017 | **Excluded.** Product-manager intake process, not a system function. |
| US-006-01 | EP-006 | `API-NOTES-004@2` | Web-app run. Consumed only. |
| US-006-02 | EP-006 | `API-NOTES-001@2`; `API-NOTES-002@2` | Web-app run. Consumed only. |
| US-006-03 | EP-006 | `API-SEARCH-001@2` | Web-app run. Consumed only. |
| US-006-04 | EP-006 | `API-NOTES-005@1`; `API-NOTES-006@1` | Web-app run. Consumed only. |
| US-006-05 | EP-006 | `API-NOTES-007@1`; `API-NOTES-008@1` | Web-app run. Consumed only. |
| US-006-06 | EP-006 | `API-AUTH-001@1`; `API-AUTH-002@1`; `ASM-BE-023`; `CON-BE-005` | Web-app run. `HANDOFF-BE-002`. Resend limit `Q-BA-001`, not supplied. |

**Uncovered stories.** None within this surface's scope. US-005-06 is excluded by classification above. Every EP-006 story is answered by the web-app run, with US-006-06 additionally bound to `HANDOFF-BE-002`.

**Coverage totals.** 36 stories supplied. 30 covered by this surface. 1 excluded by classification (US-005-06). 6 belong to EP-006 and are consumed here.

<!-- /SECTION: backend.coverage -->
