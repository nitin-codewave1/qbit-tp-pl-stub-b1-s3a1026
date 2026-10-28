---
id: agent-developer/notes_api/backend/BACKEND-SPEC.md
type: api_contract
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.16.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/BACKEND-SPEC.md
depends_on: [notes_api:US-001-01, notes_api:US-001-02, notes_api:US-001-03, notes_api:US-001-04, notes_api:US-001-05, notes_api:US-001-06, notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06, notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06, notes_api:US-004-01, notes_api:US-004-02, notes_api:US-004-03, notes_api:US-004-04, notes_api:US-004-05, notes_api:US-004-06, notes_api:US-005-01, notes_api:US-005-02, notes_api:US-005-03, notes_api:US-005-04, notes_api:US-005-05, notes_api:US-005-06, notes_api:US-006-01, notes_api:US-006-02, notes_api:US-006-03, notes_api:US-006-04, notes_api:US-006-05, notes_api:US-006-06]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: notes_api:backend/BACKEND-SPEC.md
---

<!-- SECTION: backend.overview | status: VERIFIED -->
## Overview

**Run outcome.** This run ends with `HALT-BLOCKING-QUESTION`. `Q-BE-001`, `Q-BE-002` and `Q-BE-003` are blocking and the run is unattended. All sections are written, and `backend.auth-model` is `BLOCKED`.

**Responsibilities.** The service is `notes_api`. Its stack is Node.js 20, Express and PostgreSQL 16, hosted on AWS (`platform.md`). It is responsible for:

- Note create, edit, get, archive and unarchive, with a `pinned` flag, `title`, `body` and tags (EP-002; US-002-01 to US-002-06; BR-005 to BR-008, BR-018).
- Keyword and tag search, combined filters, pagination, archived exclusion and pinned-first ordering (EP-003; BR-009 to BR-011, BR-019).
- Email one-time-code login: a request-code endpoint and a verify-code endpoint (BR-029, BR-032, BR-033; US-006-06). Codes are delivered by email through AWS SES (`feedback.md`, preflight answer).
- A published OpenAPI 3.x document covering every implemented endpoint, with schemas, 4xx/5xx errors, examples, data-residency notes and a Scope section (EP-004, EP-005; BR-012, BR-013; US-004-01 to US-004-06, US-005-01).
- Standardised refusal of out-of-scope calls (BR-014 to BR-016; US-005-02 to US-005-05).
- EU data residency for the database, compute and backups, plus the associated checks, report and drift alerting (EP-001; BR-001 to BR-004).

**Not responsible for:**

- Attachment or file-upload endpoints (BR-014).
- Any real-time channel such as WebSocket, SSE or polling-push (BR-015).
- Mobile-specific endpoints (BR-016, EP-005).
- The web_app UI (US-006-01 to US-006-05). It is a separate surface (`feedback.md`, preflight answer: React 18 + Vite).
- Logging and classifying stakeholder requests (BR-017: product-manager intake log).
- Changing existing login methods. NFR-016 says they "remain unchanged"; what they are is `GAP-BE-012`.

**Inbound handoffs.** `RUN-CONTEXT.upstream.handoffs` lists four files: `HANDOFF-WEBAPP-159759`, `HANDOFF-WEBAPP-653797`, `HANDOFF-WEBAPP-752894` and `HANDOFF-WEBAPP-846910`. `feedback.md` instructs this run to answer them. Their contents were not included in the supplied inputs, so none could be read or answered here. See `GAP-BE-001`. The lane-needs step must record each as answered or unanswerable.

**Open items.** The register of open items is in `backend.coverage`.
<!-- /SECTION: backend.overview -->
<!-- SECTION: backend.domain-model | status: DRAFT-UNVERIFIED -->
## Domain model

```mermaid
erDiagram
    NOTE ||--o{ NOTE_TAG : "carries (0..n tags per note)"
    NOTE {
        id unique
        title required
        body required
        pinned boolean_default_false
        archived_state active_or_archived
        created_at timestamp
        updated_at timestamp
    }
    NOTE_TAG {
        note_id fk
        tag_name unique_per_note
    }
    LOGIN_CODE_CHALLENGE {
        email_address key
        code six_digits
        issued_at timestamp
        used boolean
    }
    LOGIN_SEND_ATTEMPT {
        email_address key
        requested_at timestamp
    }
    ACCOUNT_LOCK {
        email_address key
        locked_until timestamp
    }
    LOGIN_CODE_CHALLENGE }o--|| LOGIN_SEND_ATTEMPT : "issued by (assumed 1:1 per accepted request)"
    LOGIN_CODE_CHALLENGE }o--o| ACCOUNT_LOCK : "5 wrong codes create lock (keyed by email)"
```

**Sources**

- `NOTE` fields: US-002-01 (unique ID, timestamps, `pinned=false` default) and BR-005, BR-018, BR-007.
- Archive is a flag or state, never a hard delete: BR-007, BR-008.
- `NOTE_TAG` uniqueness per note (a duplicate add creates no second entry): US-002-03.
- Challenge, attempt and lock entities: NFR-019 names "code challenges", "send attempts" and "account locks". Their attributes are derived from BR-020 to BR-025, BR-029 and BR-032.

**Ownership and cardinality: undecided.**

- Whether a note belongs to one user or to a shared team workspace is undecided (BR-031, which also refers to `HANDOFF-NOTESAPI-981573`). The diagram therefore has no owner relation. See `Q-BE-002`.
- Whether an `ACCOUNT` entity exists is unstated. `ASM-BE-001` assumes the lock, attempt and challenge records are keyed by email address.
- Tags are modelled as names attached to a note. Whether tag names are a separate shared entity, and their case and charset rules, are unstated (`GAP-BE-008`).
- Residency artefacts (scan results, compliance report) are infrastructure records, not application entities. See the `residency` module in `backend.module-map`.
<!-- /SECTION: backend.domain-model -->
<!-- SECTION: backend.module-map | status: DERIVED -->
## Module map

Modules are derived from the epics. `ASM-BE-007` covers the placement of `residency` in this surface.

| Module | Boundary | Source |
|---|---|---|
| `notes` | Note CRUD, `pinned`, tags, archive and unarchive | EP-002, BR-005 to BR-008, BR-018 |
| `search` | Keyword, tag and combined filters, pagination, archived exclusion, pinned-first ordering, 503 on degraded index | EP-003, BR-009 to BR-011, BR-019 |
| `auth` | Request-code and verify-code, resend limit, lock, expiry, single-use, SES delivery | BR-020 to BR-033 |
| `contract-docs` | Published OpenAPI document, spec-versus-route coverage check, residency data-handling note, Scope section | EP-004, BR-012, BR-013, US-005-01 |
| `scope-guard` | Refusal of attachment, real-time and mobile calls with a structured `OUT_OF_SCOPE` error; contract tests for their absence | EP-005, BR-014 to BR-016, NFR-010, NFR-011 |
| `residency` | Region-constrained provisioning, EU-only backup validation, CI residency gate, compliance report, launch gate, drift scan and alert | EP-001, BR-001 to BR-004 |

**Dependency direction** (A → B means A depends on B):

- `search → notes` (reads notes and tags).
- `contract-docs → notes, search, auth, scope-guard`. It reads the route registry and does not call them at runtime.
- `scope-guard`: request middleware applied to the whole HTTP surface. It has no module dependencies.
- `auth → AWS SES` (external; `feedback.md`).
- `notes → auth` for the per-request access check. This edge is pending `Q-BE-002` and `Q-BE-003`.
- `residency`: no dependencies on the other modules. It inspects infrastructure and is not on the request path.

There are no cycles. `notes` does not depend on `search`. Neither depends on `contract-docs`.
<!-- /SECTION: backend.module-map -->
<!-- SECTION: backend.api-surface | status: VERIFIED -->
## Api surface

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-AUTH-015365` | POST | `/auth/request-code` | `auth` | 202 | 400, 415 | Request a one-time login code, delivered by email through AWS SES. Limited to 3 requests per 10 minutes; not refused while the account is locked. The code is valid for 10 minutes and single-use. |
| `API-AUTH-592570` | POST | `/auth/verify-code` | `auth` | 200 | 400, 415 | Verify a one-time login code for an email address. Rejects wrong, expired, already-used codes, and refuses verification while the account is locked (15 minutes after 5 wrong codes). A successful verify consumes the code. |
| `API-CONTRACTDOCS-304307` | GET | `/openapi` | `contract-docs` | 200 | 406 | Retrieve the published OpenAPI 3.x document covering every implemented endpoint, including schemas, 4xx/5xx errors, examples, the data-residency handling note and the Scope section. |
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 415 | Create a note with title, body and optional tags; pinned defaults to false |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 400, 404 | Get a single note by id, including its tags and archived state |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 404, 415 | Partially edit a note's title, body or pinned flag (pin and unpin) |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 400, 404, 409 | Archive a note (flag only, no hard delete) |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 404, 415 | Add a tag to a note; adding a duplicate creates no second entry |
| `API-NOTES-150096` | DELETE | `/notes/{id}/tags/{tag}` | `notes` | 200 | 400, 404 | Remove a tag from a note |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 400, 404 | Unarchive a note, restoring it with all data intact |
| `API-RESIDENCY-174124` | POST | `/residency/backup-validations` | `residency` | 200 | 400, 422, 500 | Validate a backup configuration is EU-only; a non-EU configuration fails fast and writes no data (US-001-02). |
| `API-RESIDENCY-034853` | POST | `/residency/ci-gate-checks` | `residency` | 200 | 400, 500 | Evaluate a deployment against the CI residency gate, blocking non-EU deploys (US-001-03). |
| `API-RESIDENCY-913995` | GET | `/residency/compliance-report` | `residency` | 200 | 404, 500 | Get the residency compliance report listing each resource, its region, pass/fail status and overall percent compliant (US-001-04). |
| `API-RESIDENCY-922826` | POST | `/residency/drift-scans` | `residency` | 201 | 500, 503 | Run a residency drift scan; non-EU resources are recorded and trigger an alert to the DevOps team (US-001-06). |
| `API-RESIDENCY-726277` | GET | `/residency/drift-scans/latest` | `residency` | 200 | 404, 500 | Get the result of the most recent drift scan (US-001-06). |
| `API-RESIDENCY-395094` | GET | `/residency/launch-gate` | `residency` | 200 | 404, 500 | Get the launch gate status, passing only when the residency report shows full EU compliance (US-001-05). |
| `API-RESIDENCY-026899` | POST | `/residency/provisioning-requests` | `residency` | 201 | 400, 422, 500 | Submit a resource provisioning request; accepted only if the target region is an approved EU region, otherwise rejected (US-001-01). |
| `API-SEARCH-580505` | GET | `/notes/search` | `search` | 200 | 400, 503 | Search notes by keyword, tag, or both combined. Excludes archived notes unless requested. Pinned notes come first. Results are paginated. Returns 503 with Retry-After when the search index is degraded. |

### `API-AUTH-015365` — POST `/auth/request-code`

Request a one-time login code, delivered by email through AWS SES. Limited to 3 requests per 10 minutes; not refused while the account is locked. The code is valid for 10 minutes and single-use.

**Request:** 
- `email` (string, required): Email address the one-time login code is sent to. Must be a valid email address.
**Response:** 
- `message` (string, required): Human-readable confirmation that a code was sent. Never contains the code.
- `expires_in_seconds` (integer): Validity window of the issued code in seconds (10 minutes per BR-024).

### `API-AUTH-592570` — POST `/auth/verify-code`

Verify a one-time login code for an email address. Rejects wrong, expired, already-used codes, and refuses verification while the account is locked (15 minutes after 5 wrong codes). A successful verify consumes the code.

**Request:** 
- `email` (string, required): Email address the code was issued for.
- `code` (string, required): Six-digit one-time code received by email.
**Response:** 
- `verified` (boolean, required): True when the code was accepted. The credential type, lifetime and transport issued on success are undecided (Q-BE-003) and are not specified here.

### `API-CONTRACTDOCS-304307` — GET `/openapi`

Retrieve the published OpenAPI 3.x document covering every implemented endpoint, including schemas, 4xx/5xx errors, examples, the data-residency handling note and the Scope section.

**Request:** 
- `Accept` (string): Content-negotiation header (not a body or query field) selecting the document format. An unsupported value yields 406 or a default-format fallback with a clear content type (US-004-01).
**Response:** 
- `openapi` (string, required): OpenAPI 3.x version string of the published document.
- `info` (object, required): Document metadata, including the Scope section (in-scope and excluded capabilities: attachments, real-time, mobile) and the data-residency handling note.
- `paths` (object, required): Every implemented endpoint with parameters, request bodies, success responses, 4xx/5xx error responses and examples; 100% of implemented endpoints must be present (NFR-008).
- `components` (object, required): Reusable schemas, including the error envelope, referenced by the paths.

### `API-NOTES-232390` — POST `/notes`

Create a note with title, body and optional tags; pinned defaults to false

**Request:** 
- `title` (string, required): Note title; must be non-empty
- `body` (string, required): Note body; must be non-empty
- `tags` (array): Optional list of tag names to attach
- `pinned` (boolean): Pinned flag; defaults to false
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): Whether the note is archived
- `tags` (array, required): Tag names on the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-045877` — GET `/notes/{id}`

Get a single note by id, including its tags and archived state

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): Whether the note is archived
- `tags` (array, required): Tag names on the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-847551` — PATCH `/notes/{id}`

Partially edit a note's title, body or pinned flag (pin and unpin)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `title` (string): New title; must be non-empty if supplied
- `body` (string): New body; must be non-empty if supplied
- `pinned` (boolean): Set true to pin, false to unpin
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): Whether the note is archived
- `tags` (array, required): Tag names on the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Updated timestamp, refreshed by the edit

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note (flag only, no hard delete)

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): True after archiving
- `tags` (array, required): Tag names on the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add a tag to a note; adding a duplicate creates no second entry

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `tag` (string, required): Tag name to attach
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): Whether the note is archived
- `tags` (array, required): Tag names on the note after the add
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-150096` — DELETE `/notes/{id}/tags/{tag}`

Remove a tag from a note

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `tag` (string, required): Path parameter: tag name to remove
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): Whether the note is archived
- `tags` (array, required): Tag names on the note after the removal
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Unarchive a note, restoring it with all data intact

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Unique note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `pinned` (boolean, required): Whether the note is pinned
- `archived` (boolean, required): False after unarchiving
- `tags` (array, required): Tag names on the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-RESIDENCY-174124` — POST `/residency/backup-validations`

Validate a backup configuration is EU-only; a non-EU configuration fails fast and writes no data (US-001-02).

**Request:** 
- `backup_target` (string, required): Backup storage bucket or service identifier.
- `region` (string, required): Region of the backup target.
- `replica_regions` (array): Regions of any backup replicas or copies.
**Response:** 
- `valid` (boolean, required): True when all backup locations are in approved EU regions.
- `regions_checked` (array, required): Regions that were evaluated.

### `API-RESIDENCY-034853` — POST `/residency/ci-gate-checks`

Evaluate a deployment against the CI residency gate, blocking non-EU deploys (US-001-03).

**Request:** 
- `deployment_id` (string, required): Identifier of the CI deployment being evaluated.
- `target_regions` (array, required): Regions the deployment would touch.
**Response:** 
- `deployment_id` (string, required): Identifier of the evaluated deployment.
- `verdict` (string, required): Gate result: pass or fail.
- `violations` (array, required): Non-EU resources or regions causing a failure; empty on pass.

### `API-RESIDENCY-913995` — GET `/residency/compliance-report`

Get the residency compliance report listing each resource, its region, pass/fail status and overall percent compliant (US-001-04).

**Request:** none
**Response:** 
- `resources` (array, required): Each resource with its region and pass/fail status.
- `percent_compliant` (number, required): Overall percentage of infrastructure verified in EU regions.
- `generated_at` (string, required): Timestamp when the report was generated.

### `API-RESIDENCY-922826` — POST `/residency/drift-scans`

Run a residency drift scan; non-EU resources are recorded and trigger an alert to the DevOps team (US-001-06).

**Request:** none
**Response:** 
- `scan_id` (string, required): Identifier of the scan run.
- `scanned_at` (string, required): Timestamp of the scan.
- `drift_detected` (boolean, required): True when any resource was found in a non-EU region.
- `non_eu_resources` (array, required): Details of resources found outside EU regions; empty when none.
- `alert_sent` (boolean, required): Whether an alert was raised for this scan.

### `API-RESIDENCY-726277` — GET `/residency/drift-scans/latest`

Get the result of the most recent drift scan (US-001-06).

**Request:** none
**Response:** 
- `scan_id` (string, required): Identifier of the scan run.
- `scanned_at` (string, required): Timestamp of the scan.
- `drift_detected` (boolean, required): True when any resource was found in a non-EU region.
- `non_eu_resources` (array, required): Resources found outside EU regions; empty when none.

### `API-RESIDENCY-395094` — GET `/residency/launch-gate`

Get the launch gate status, passing only when the residency report shows full EU compliance (US-001-05).

**Request:** none
**Response:** 
- `verdict` (string, required): Launch gate result: pass or fail.
- `percent_compliant` (number, required): Compliance percentage from the current report.
- `blocking_resources` (array, required): Non-compliant resources blocking launch; empty on pass.

### `API-RESIDENCY-026899` — POST `/residency/provisioning-requests`

Submit a resource provisioning request; accepted only if the target region is an approved EU region, otherwise rejected (US-001-01).

**Request:** 
- `resource_type` (string, required): Kind of infrastructure resource to provision (database, compute, backup storage).
- `resource_name` (string, required): Name of the resource to provision.
- `region` (string, required): Target cloud region; must be an approved EU region.
**Response:** 
- `resource_name` (string, required): Name of the accepted resource.
- `region` (string, required): Region the resource was accepted for.
- `status` (string, required): Outcome of the provisioning request.

### `API-SEARCH-580505` — GET `/notes/search`

Search notes by keyword, tag, or both combined. Excludes archived notes unless requested. Pinned notes come first. Results are paginated. Returns 503 with Retry-After when the search index is degraded.

**Request:** 
- `q` (string): Keyword matched against note title and body. Malformed values return 400 VALIDATION_ERROR.
- `tags` (array): Tag filter. Combined with q when both are given. A note must carry all supplied tags (ASM-BE-009, pending Q-BE-004). Sent as a query parameter.
- `page` (integer): Page number for pagination. Invalid values return 400 VALIDATION_ERROR.
- `limit` (integer): Page size. Default 20 and maximum 100 are assumed (ASM-BE-011, pending Q-BE-005). Invalid values return 400 VALIDATION_ERROR.
- `include_archived` (boolean): When true, archived notes are included in results. Default false, so archived notes are excluded.
**Response:** 
- `items` (array, required): Matching notes on the requested page, pinned notes first. Each item shows the note id, title, body, tags, pinned flag, archived state and timestamps.
- `page` (integer, required): Page number of this result set.
- `limit` (integer, required): Page size applied to this result set.
- `total` (integer, required): Total number of notes matching the filters across all pages (proposed; the total count is not stated in the inputs).
<!-- /SECTION: backend.api-surface -->
<!-- SECTION: backend.auth-model | status: BLOCKED -->
## Authentication and authorisation

**Blocked.** The permission matrix (role × resource × operation) cannot be written because three decisions are missing from the inputs:

1. **Who may request, verify or receive a login code.** The statement of BR-030 is garbled in the source and the rule is explicitly "open until decided" (it refers to `HANDOFF-NOTESAPI-939780`). It notes that under CR-125 any address that verifies can obtain a credential, which may conflict with the shared-team-workspace default. See `Q-BE-001`.
2. **How a note's access follows from its owner.** BR-031 says the note ownership columns and the access rule "cannot be written until this is decided", and calls the shared-workspace default "only a placeholder". See `Q-BE-002`.
3. **What credential a successful verify issues, and whether note and search endpoints require it.** No input states a token type, lifetime or transport. US-004-03 mentions 401 and 403 only conditionally ("requires authentication/authorization"). See `Q-BE-003`, `GAP-BE-004`.

**What would unblock it:**

- A human answer to `Q-BE-001`, `Q-BE-002` and `Q-BE-003`, or the contents of `HANDOFF-WEBAPP-159759`, `HANDOFF-WEBAPP-653797`, `HANDOFF-WEBAPP-752894` and `HANDOFF-WEBAPP-846910` if they answer them (`GAP-BE-001`).
- Clean source text for BR-030 and BR-031 (`GAP-BE-003`).

**Independent of the blocked decisions** (specified in the `auth` module): the 3-requests-per-10-minutes limit, the 15-minute lock after 5 wrong codes, and 10-minute expiry. These come from BR-020 to BR-029 and BR-032.
<!-- /SECTION: backend.auth-model -->
<!-- SECTION: backend.data-lifecycle | status: DRAFT-UNVERIFIED -->
## Data lifecycle

| Data | Stored | Retention and deletion | Source |
|---|---|---|---|
| Notes, including archived | PostgreSQL 16, in an approved EU region | Never deleted by retention. Archive is a flag that preserves all data and is reversible. | BR-001, BR-007, BR-008, NFR-019, `platform.md` |
| Tags per note | PostgreSQL | Follow the note | US-002-03 |
| Login code challenges | PostgreSQL | Deleted after 1 day (`ASM-BE-002`) | NFR-019 |
| Send attempts | PostgreSQL | Deleted after 1 day (`ASM-BE-002`) | NFR-019 |
| Account locks | PostgreSQL | Deleted after 5 days (`ASM-BE-002`) | NFR-019 |
| Database backups | EU-region storage bucket or service only | Schedule and retention are unstated (`GAP-BE-010`) | US-001-02, BR-001 |
| Login code emails | Sent via AWS SES. The region is garbled in NFR-017 (`GAP-BE-002`). | n/a | NFR-017, `feedback.md` |

**Invariants**

- `INV-BE-001`: No retention job deletes a note (NFR-019).
- `INV-BE-002`: An archived note's data is identical after unarchive (BR-008).
- `INV-BE-003`: All stored data and backups reside in approved EU regions (BR-001, NFR-001).

**Unstated, therefore not asserted:**

- **Encryption at rest** for the database or backups (`GAP-BE-010`).
- **The retention-sweep schedule** (`GAP-BE-016`).
- **A hard-delete operation.** No input defines one, although US-002-06 mentions a "deleted … note" (`GAP-BE-016`).
- **Whether the email address in transit leaves the EU.** NFR-017 states this condition but its text is corrupted (`GAP-BE-003`).
<!-- /SECTION: backend.data-lifecycle -->
<!-- SECTION: backend.nfr | status: VERIFIED -->
## Non-functional requirements

| Target | Value | Source |
|---|---|---|
| Search latency | p95 < 300 ms at 50 requests per second | NFR-006, US-003-03 |
| Search latency with pagination | p95 < 300 ms up to page size 100 | NFR-007 |
| Search dataset size | SLA still met at about 100k notes, via indexed lookup | US-003-03 |
| Availability (CRUD and search) | 99.9% monthly | NFR-013 |
| Search degraded behaviour | 503 with a `Retry-After` header; must not hang | US-003-03 |
| Residency of infrastructure | 100% of components EU-verified | NFR-001 |
| Residency in CI/CD | 0 non-EU deploys reach production | NFR-002 |
| Residency report | Refreshed and 100% accurate at each launch gate check | NFR-004 |
| Drift alert | Fired within 15 minutes of drift detection | NFR-003 |
| Backup config violation | Job fails within 30 seconds, writes no data | US-001-02 (AC-001-02-02) |
| Spec coverage | 100% of implemented endpoints in the published spec | NFR-008 |
| Out-of-scope endpoints | 0 detected per build | NFR-010 |
| Injection security | 0 critical injection findings per release | NFR-012 |
| CRUD correctness | 100% functional-test pass, zero data loss | NFR-005 |

**No source exists for:**

- Latency or throughput of non-search operations.
- Concurrency limits.
- Availability of the login endpoints separately from CRUD and search.

No figures are invented for these (`GAP-BE-017`).
<!-- /SECTION: backend.nfr -->
<!-- SECTION: backend.error-model | status: DRAFT-UNVERIFIED -->
## Error model

**Envelope.** No input defines an error body. This spec proposes one (`ASM-BE-006`) with `code` (a machine-readable registry value), `message` (human-readable) and optional `details` (field-level entries for validation). The only supplied code is `OUT_OF_SCOPE` (US-005-04). The registry names below, other than `OUT_OF_SCOPE`, are proposals.

**Status-code policy** (statuses are supplied; code names are proposed):

| Condition | Status | Code | Source |
|---|---|---|---|
| Invalid, missing or empty request fields, including malformed `q`, `tags`, `page`, `limit`, `include_archived` | 400 | `VALIDATION_ERROR` | AC-002-01-03, US-003-01 to US-003-06 |
| Note does not exist (get, edit, unarchive, tag) | 404 | `NOTE_NOT_FOUND` | US-002-02, US-002-05, US-002-06 |
| Archive of an already-archived note | 409 (`ASM-BE-004`) | `NOTE_ALREADY_ARCHIVED` | US-002-04 |
| Search index degraded or unavailable | 503 plus `Retry-After` | `SEARCH_UNAVAILABLE` | US-003-03 |
| Spec requested with unsupported `Accept` | 406, or a default-format fallback with a clear content type | `NOT_ACCEPTABLE` | US-004-01 |
| Authentication or authorisation failure | 401 / 403 | `UNAUTHENTICATED` / `FORBIDDEN` | US-004-03. Applicability blocked by `Q-BE-003`. |
| More than 3 code requests within 10 minutes | 415 | `LOGIN_CODE_RATE_LIMITED` | BR-028, BR-032, US-004-03 |
| Verify: wrong code | 415 | `LOGIN_CODE_INVALID` | BR-033 |
| Verify: expired code | 415 | `LOGIN_CODE_EXPIRED` | BR-033, BR-024 |
| Verify: already-used code | 415 | `LOGIN_CODE_USED` | BR-033, BR-023 |
| Verify: locked account | 415 | `ACCOUNT_LOCKED` | BR-033, BR-022, BR-025 |
| Multipart or file payload on a notes endpoint | 415 | `OUT_OF_SCOPE` | US-005-02, US-005-04 |
| Attachment path, real-time endpoint, mobile-specific path | 404 with a structured `OUT_OF_SCOPE` body (`CON-BE-002`) | `OUT_OF_SCOPE` | US-005-02, US-005-04, BR-016 |
| WebSocket upgrade or SSE connection | Rejected with "standard error status" (the status is unstated: `GAP-BE-015`) | `OUT_OF_SCOPE` (assumed) | US-005-03 |

**Notes**

- **415 is overloaded.** Status 415 is supplied for the login failures (BR-033) and also for multipart uploads (US-005-02). The registry `code` is the only discriminator, so clients must branch on `code`, not on status.
- **Lock behaviour.** During a lock only verification is refused. Requesting a code is not refused (BR-032). BR-027 says "login attempts … are rejected"; this is read as verification attempts (`ASM-BE-008`).
- **Rate-limit counting.** BR-021 limits "resending" to 3, while BR-028 and BR-032 limit "requests" to 3 (`CON-BE-001`).
- **Concurrent edits.** No version or conflict mechanism is supplied (`GAP-BE-014`, `ASM-BE-005`).
<!-- /SECTION: backend.error-model -->
<!-- SECTION: backend.observability | status: DRAFT-UNVERIFIED -->
## Observability

**Logged**

- Each periodic residency scan logs a pass when all resources are in EU regions (AC-001-06-01).
- The compliance report lists each resource, its region, its pass/fail status and an overall % compliant metric (AC-001-04-01).

**Alerted**

- A resource found in a non-EU region triggers an immediate alert to the DevOps team with resource details, within 15 minutes of drift detection (AC-001-06-02, NFR-003, BR-004). The alert channel is unstated (`GAP-BE-011`).

**Metered**

- Percentage of infrastructure verified in EU regions (NFR-001, NFR-004).
- Percentage of implemented endpoints present in the published spec, naming any missing endpoint (US-004-04, NFR-008).
- Out-of-scope endpoints detected per CI build (NFR-010).

**Redacted: not asserted.**

- NFR-018 requires that email addresses and one-time login codes be handled in a specified way in all application logs. Its verb is corrupted in the source (`GAP-BE-003`).
- This spec requires that no log entry contain a raw login code or an unmasked email address (`ASM-BE-012`), pending clean source text.
- The target metric is the share of log entries containing an unredacted email address or code (NFR-018).

**Not supplied:** service health metrics, tracing and alert rules for the request path (`GAP-BE-018`).
<!-- /SECTION: backend.observability -->
<!-- SECTION: backend.migration-plan | status: DRAFT-UNVERIFIED -->
## Migration plan

The ordering is derived from the domain model. Table and column detail belongs to each module spec.

| Order | Migration | Reversible? |
|---|---|---|
| 001 | `notes` table: title, body, `pinned` default false, archive state, timestamps | Structurally yes. Dropping it destroys data once populated, so it is irreversible after first use (`INV-BE-002`). |
| 002 | Tags: per-note tag entries with uniqueness per note | Same as 001 |
| 003 | Search index on title, body and tags, for the 300 ms SLA at about 100k notes (NFR-006, US-003-03) | Yes. Dropping an index loses no data. |
| 004 | Login tables: code challenges, send attempts, account locks | Yes, while no retention-critical data exists |
| 005 | Note ownership columns | **Blocked** by `Q-BE-002` (BR-031) |
| 006 | Retention sweep schedule | Blocked by `GAP-BE-016` |

Migrations run after the database exists in an approved EU region (BR-001). Migration tooling is unnamed in the inputs (`GAP-BE-011`).
<!-- /SECTION: backend.migration-plan -->
<!-- SECTION: backend.coverage | status: DERIVED -->
## Coverage

FR ids are minted in the module specs. Here each story maps to its owning module and governing business rules and NFRs.

| Story | Module | Governing rules and NFRs | Status |
|---|---|---|---|
| US-001-01 | residency | BR-001, NFR-001 | Covered. Region list missing (`GAP-BE-002`). |
| US-001-02 | residency | BR-001, NFR-001 | Covered |
| US-001-03 | residency | BR-002, NFR-002 | Covered. CI system unnamed (`GAP-BE-011`). |
| US-001-04 | residency | NFR-001, NFR-004 | Covered |
| US-001-05 | residency | BR-003 | Covered |
| US-001-06 | residency | BR-004, NFR-003 | Covered. Alert channel unnamed (`GAP-BE-011`). |
| US-002-01 | notes | BR-005, BR-018 | Covered |
| US-002-02 | notes | BR-006, BR-018 | Covered. PUT versus PATCH open (`GAP-BE-014`). |
| US-002-03 | notes | — | Covered for add. Remove has no acceptance criteria (`GAP-BE-007`). |
| US-002-04 | notes | BR-007 | Covered |
| US-002-05 | notes | BR-008 | Covered |
| US-002-06 | notes | BR-007 | Covered |
| US-003-01 | search | BR-009, BR-019 | Covered |
| US-003-02 | search | BR-010 | Covered. Tag charset open (`GAP-BE-008`). |
| US-003-03 | search | NFR-006 | Covered |
| US-003-04 | search | BR-010 | Covered |
| US-003-05 | search | BR-011, NFR-007 | Covered. Limits open (`GAP-BE-009`). |
| US-003-06 | search | BR-009 | Covered |
| US-004-01 | contract-docs | BR-012, NFR-008 | Covered |
| US-004-02 | contract-docs | BR-012 | Covered. Constraints open (`GAP-BE-008`). |
| US-004-03 | contract-docs | BR-012, NFR-016 | Covered |
| US-004-04 | contract-docs | BR-012, NFR-008 | Covered |
| US-004-05 | contract-docs | NFR-009 | Covered |
| US-004-06 | contract-docs | BR-013 | Covered |
| US-005-01 | contract-docs | — | Covered |
| US-005-02 | scope-guard | BR-014 | Covered. See `CON-BE-002`. |
| US-005-03 | scope-guard | BR-015 | Covered. Status unstated (`GAP-BE-015`). |
| US-005-04 | scope-guard | BR-016, NFR-011 | Covered. See `CON-BE-002`. |
| US-005-05 | scope-guard | NFR-010 | Covered |
| US-005-06 | — | BR-017 | **Uncovered by the backend.** It is a product-manager intake process, not a service behaviour (`GAP-BE-019`). |
| US-006-01 | notes | NFR-014 | Partially covered. No list endpoint in any backend story (`GAP-BE-005`). The UI part belongs to web-app. |
| US-006-02 | notes | BR-005 | Covered for the API. The UI part belongs to web-app. |
| US-006-03 | search | — | Covered for the API. The UI part belongs to web-app. |
| US-006-04 | notes | — | Covered for the API (see `GAP-BE-007`) |
| US-006-05 | notes | BR-007, BR-008 | Partially covered. No archived-only listing (`GAP-BE-006`). |
| US-006-06 | auth | BR-020 to BR-033, NFR-015, NFR-016 | Covered for limits, lock and expiry. Credential and eligibility blocked (`Q-BE-001`, `Q-BE-003`). |

**Contradiction sweep**

| Direction | Result |
|---|---|
| Story ↔ design | The design brief contains only acceptance-criteria records. No contradiction found. |
| Story ↔ requirement | `CON-BE-002`. No other contradiction found. |
| Requirement ↔ requirement | `CON-BE-001`. BR-005 ("title and body") versus AC-002-01-03 (only a missing title) is not a contradiction: both fields are required (`ASM-BE-010`). |
| Design ↔ design | No design content supplied. |

**Open items register**

*Contradictions (non-critical)*

- `CON-BE-001`: BR-021 limits "resend" to 3; BR-028 and BR-032 limit "requests" to 3. This spec reads the limit as 3 code requests within 10 minutes, including the first, because BR-032 is the most specific and latest rule. A different reading changes the limit by one request.
- `CON-BE-002`: US-005-02 says attachment paths return 404. US-005-04 and BR-016 require a body that references the scope exclusion, "not … 404 ambiguity". This spec reads both as one response: status 404 with an `OUT_OF_SCOPE` body. Picking the status alone instead would drop the required body.

*Blocking questions*

- `Q-BE-001` (blocking): Who may request, verify or receive a login code? Is it any address, or only a known set?
- `Q-BE-002` (blocking): Are notes owned per user, or shared by the team workspace? What is the access rule for each note read and write?
- `Q-BE-003` (blocking): What credential does a successful verify issue (type, lifetime, transport), and do the note and search endpoints require it?

*Degradable questions (defaults taken, unattended)*

- `Q-BE-004`: Multi-tag filter semantics. Default: a note must carry all supplied tags (`ASM-BE-009`).
- `Q-BE-005`: Default and maximum page size. Default: 20 and 100 (`ASM-BE-011`).
- `Q-BE-006`: Is a code submitted exactly 10 minutes after issue expired? Default: yes (`ASM-BE-003`).
- `Q-BE-007`: PUT, PATCH or both for edit? Default: PATCH with partial-update semantics (BR-006).

*Assumptions*

- `ASM-BE-001`: Challenge, attempt and lock records are keyed by email address. Confirm with `Q-BE-001`.
- `ASM-BE-002`: NFR-019's text is garbled; read as 1 day for challenges, 1 day for send attempts and 5 days for locks. Confirm with clean source text.
- `ASM-BE-003`: A code is rejected as expired once at least 10 minutes have elapsed since issue. BR-033's boundary sentence is corrupted, and BR-024 gives only "valid for 10 minutes".
- `ASM-BE-004`: Archiving an already-archived note returns 409.
- `ASM-BE-005`: Concurrent PATCH requests are last-write-wins, with no version check. US-005-03 says "last-write/conflict".
- `ASM-BE-006`: The error envelope and the registry code names are designed by this spec.
- `ASM-BE-007`: The `residency` module lives in this surface's repository as operational tooling, not as request-path code.
- `ASM-BE-008`: "Login attempts" in BR-027 means verification attempts, consistent with BR-022 and BR-032.
- `ASM-BE-009`: Multi-tag filters are all-of.
- `ASM-BE-010`: Both `title` and `body` are required and non-empty at creation (BR-005: "empty/null payloads are rejected").
- `ASM-BE-011`: Default and maximum page size are 20 and 100. NFR-007 mentions 100 only as a tested size.
- `ASM-BE-012`: No raw code or unmasked email address is logged, pending clean NFR-018 text.

*Gaps* (each names the artefact that would close it)

- `GAP-BE-001`: The four inbound handoff files were not supplied. Closing artefact: their contents.
- `GAP-BE-002`: The approved EU region list and the SES region are missing. Closing artefact: the region list; the SES region from a clean NFR-017.
- `GAP-BE-003`: The source text of BR-027, BR-030, BR-031, BR-033, NFR-017, NFR-018 and NFR-019 is corrupted by template substitution. Closing artefact: clean text of each.
- `GAP-BE-004`: Session or credential details after verify are missing. Closing artefact: the answer to `Q-BE-003`.
- `GAP-BE-005`: No list-notes story or endpoint exists, though BR-019 and US-006-01 refer to a list. Closing artefact: a list-notes story.
- `GAP-BE-006`: There is no archived-only listing for the archived-notes view (US-006-05). Closing artefact: a story or acceptance criterion.
- `GAP-BE-007`: Tag removal has no acceptance criteria. Closing artefact: a story or acceptance criterion.
- `GAP-BE-008`: Constraints on title length, body size and tag format are not stated (US-004-02 expects them). Closing artefact: field constraints.
- `GAP-BE-009`: Default and maximum page sizes are missing (BR-011). Closing artefact: the answer to `Q-BE-005`.
- `GAP-BE-010`: Encryption at rest, backup schedule and backup retention are unstated. Closing artefact: a platform security input.
- `GAP-BE-011`: The IaC tool, CI system, migration tooling, compliance-report format, scan interval and alert channel are unnamed. Closing artefact: `platform.md` additions.
- `GAP-BE-012`: The "existing login methods" in NFR-016 are not described. Closing artefact: a description of them.
- `GAP-BE-013`: Whether a successful verify or a lock expiry resets the wrong-code counter is unstated. Closing artefact: a rule in the login story.
- `GAP-BE-014`: PUT versus PATCH and concurrent-edit semantics are unclear. Closing artefact: the answer to `Q-BE-007`.
- `GAP-BE-015`: The status returned for WebSocket and SSE rejection is unstated. Closing artefact: an acceptance criterion.
- `GAP-BE-016`: There is no hard-delete operation, and no retention sweep schedule. Closing artefact: a story for each.
- `GAP-BE-017`: No latency or throughput source exists for non-search operations. Closing artefact: an NFR.
- `GAP-BE-018`: No observability requirements exist beyond residency and coverage. Closing artefact: an NFR.
- `GAP-BE-019`: US-005-06 is a product-manager process, not a service behaviour. Closing artefact: confirmation that it is out of backend scope.
<!-- /SECTION: backend.coverage -->
<!-- SECTION: backend.lane-needs | status: VERIFIED -->
## Lane needs

### Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-NOTESAPI-067561` | `business_rule` | Provide a clean statement of BR-030: who may request, verify or receive a login code (any email address, or only a known set). | The permission matrix in the auth model cannot be written. The source text of BR-030 is garbled and the rule is marked open (Q-BE-001). | yes | `backend.auth-model`, `backend.coverage` |
| `HANDOFF-NOTESAPI-499880` | `business_rule` | Decide whether notes are owned per user or shared by the team workspace, and give the access rule for each note read and write (BR-031). | The note ownership columns, migration 005 and the per-request access check depend on it (Q-BE-002). | yes | `backend.auth-model`, `backend.domain-model`, `backend.migration-plan` |
| `HANDOFF-NOTESAPI-162512` | `business_rule` | State what a successful code verification issues (credential type, lifetime, transport, refresh) and whether note and search endpoints require it. | The verify response and the 401/403 applicability cannot be specified without it (Q-BE-003, GAP-BE-004). | yes | `backend.auth-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-832585` | `story` | Add a story for listing active notes (pinned first) and for listing archived notes only. | No backend story or endpoint lists notes, although BR-019, US-006-01 and US-006-05 assume one (GAP-BE-005, GAP-BE-006). | no | `backend.api-surface`, `backend.coverage` |

### Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-159759` | `web_app` | Return remaining lock time (or a stated re-enable rule) on the OTP_ACCOUNT_LOCKED error. | The spec gives a 15-minute lock and the ACCOUNT_LOCKED code, but defines no remaining-time field or re-enable rule on the error. The registry names are proposals, so this needs a decision and is not answered in this document. |
| `HANDOFF-WEBAPP-653797` | `web_app` | State what a successful API-AUTH-692907 verification returns (body token, cookie or other), its lifetime, and whether any refresh operation exists. | What a successful verify issues, its lifetime and any refresh are undecided (Q-BE-003, GAP-BE-004). That needs a human decision, so auth-model is BLOCKED. |
| `HANDOFF-WEBAPP-752894` | `web_app` | Publish the final error-code registry and the precedence when several verify refusals apply at once (invalid, expired, used, locked), since all share one status. | The error registry is only proposed (ASM-BE-006). No input sets a precedence when several verify refusals apply, and several share status 415. Needs a human decision. |
| `HANDOFF-WEBAPP-846910` | `web_app` | Add contract operations that list active notes (pinned first, paginated or not) and list archived notes only. Search cannot substitute because it requires a keyword or tag and include_archived mixes active and archived notes. | No input defines a list-notes or archived-only list operation (GAP-BE-005, GAP-BE-006). Adding one needs a new story or a human decision. The spec does not invent endpoints. |
<!-- /SECTION: backend.lane-needs -->
