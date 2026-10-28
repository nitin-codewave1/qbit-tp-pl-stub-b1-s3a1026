---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 825ae1d3-abc3-5c68-8294-c18eb9926d6e
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: DERIVED -->
```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-003, Q-BE-300, CON-BE-003, CON-BE-300]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-359413, API-NOTES-150096, API-NOTES-810640, API-NOTES-917869]
```
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: DERIVED -->
## 1. Purpose

**Responsibility.** The `notes` module owns the Note, Tag and NOTE_TAG data and these lifecycle operations: create, read by id, partial edit, pin and unpin (through the `pinned` field of the edit operation), tag add, tag remove, archive and unarchive. [backend.module-map; stories.md US-002-01 to US-002-06; requirements.md BR-005 to BR-008, BR-018]

It implements the following operations, referred to by id only:

| Operation | Method and path | Story |
|---|---|---|
| `API-NOTES-232390` | POST `/notes` | US-002-01 |
| `API-NOTES-045877` | GET `/notes/{id}` | US-002-06 |
| `API-NOTES-847551` | PATCH `/notes/{id}` | US-002-02 |
| `API-NOTES-359413` | POST `/notes/{id}/tags` | US-002-03 |
| `API-NOTES-150096` | DELETE `/notes/{id}/tags/{tag}` | US-002-03 |
| `API-NOTES-810640` | POST `/notes/{id}/archive` | US-002-04 |
| `API-NOTES-917869` | POST `/notes/{id}/unarchive` | US-002-05 |

The tag, archive and unarchive paths are proposals because no input supplies them (`GAP-BE-303`).

**Not responsible for.**

- **Caller identity, code issuance, lockout and credentials.** These belong to the `auth` module. `notes` consumes caller identity only. [backend.module-map] The credential form is `Q-BE-012` and is not specified here.
- **The note list and keyword or tag search.** These belong to the `search` module, including the archived-exclusion default and pinned-first ordering (`INV-BE-001`). `notes` only guarantees the stored `pinned` and `status` values they read. [backend.module-map; requirements.md BR-009, BR-019]
- **Publishing the OpenAPI document, its coverage metric and the Scope section.** These belong to `api-docs`.
- **Rejecting attachment, real-time and mobile-specific calls, and multipart payloads.** These belong to `scope-guardrail`. [stories.md US-005-02]
- **Infrastructure region, backups, the residency gate and drift alerts.** These belong to `residency-compliance`.
- **Any user interface.** The web-app screens in EP-006 belong to the web-app surface.
- **Deleting notes.** No delete operation is specified. The NFR-019 retention deletion of notes is `BLOCKED` (section 9).
- **The global error envelope and the error-code registry.** These belong to `backend.error-model`. This module refers to codes by name only.

**Story coverage.** All six `US-002-*` stories are covered by requirements in section 5. `US-002-03` has acceptance criteria for add only, so tag removal is `DRAFT-UNVERIFIED` (`GAP-BE-373`).
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

The platform is PostgreSQL 16. [platform.md] No input states column types, lengths or constraints beyond those cited per row. The types below are proposals (`ASM-BE-361`). The entities and column names come from `backend.domain-model`. The migration tool is not named (`GAP-BE-089`). Migration order is in `backend.migration-plan` (migrations 001 and 002).

**NOTES**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | server-generated | Primary key. Server-assigned and opaque (`ASM-BE-363`). A "unique ID" is returned on create. [AC-002-01-01] |
| `title` | text | NOT NULL | none | `CHECK (btrim(title) <> '')`. Mandatory and non-empty. [BR-005] Whitespace-only counts as empty (`ASM-BE-359`). The maximum length is `GAP-BE-372`. |
| `body` | text | NOT NULL | none | `CHECK (btrim(body) <> '')`. [BR-005] The maximum length is `GAP-BE-372`. |
| `status` | text | NOT NULL | `'active'` | `CHECK (status IN ('active','archived'))`. A new note is active (`ASM-BE-364`). [stories.md US-002-04, US-002-05] |
| `pinned` | boolean | NOT NULL | `false` | Default per BR-018 and AC-002-01-01. `NOT NULL` per `ASM-BE-034`. |
| `created_at` | timestamptz | NOT NULL | now | Set once at create. Timezone handling is `ASM-BE-362`. |
| `updated_at` | timestamptz | NOT NULL | now | `CHECK (updated_at >= created_at)`. Set on every successful mutation (`ASM-BE-362`). [AC-002-02-01] |

There is no owner column and no version column. A note has no relationship to a user in any input (`GAP-BE-003`). The ownership columns are `BLOCKED` on `CON-BE-003` and `Q-BE-003`. [requirements.md BR-031] There is no version column because concurrent PATCH uses last-write-wins (`ASM-BE-360`).

**TAGS**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `name` | text | NOT NULL | none | Primary key. Compared exactly, with no case folding (`ASM-BE-369`). Format and length are `GAP-BE-035` and `GAP-BE-378`. |

A TAGS row is created when a tag is first added to any note. A tag left with no notes is retained (`GAP-BE-378`).

**NOTE_TAGS**

| Column | Type | Null | Default | Constraint and notes |
|---|---|---|---|---|
| `note_id` | uuid | NOT NULL | none | FK to `notes(id)`, `ON DELETE RESTRICT` (`ASM-BE-366`). |
| `tag_name` | text | NOT NULL | none | FK to `tags(name)`, `ON DELETE RESTRICT` (`ASM-BE-366`). |

Primary key `(note_id, tag_name)`. This composite key prevents a duplicate tag entry on a note. [stories.md US-002-03] `INV-BE-062`.

**Indexes.**

| Index | Query it serves |
|---|---|
| Primary key on `notes(id)` | Read, edit, tag, archive and unarchive by id. |
| Primary key on `note_tags(note_id, tag_name)` | Listing a note's tags on read. Duplicate prevention on add. |
| btree on `note_tags(tag_name, note_id)` | Reverse lookup of notes by tag. The `search` module's tag filter reads it. [stories.md US-003-02] |

Indexes for keyword search, and for pinned-first and archived-excluded listing, belong to the `search` module and are not specified here. [backend.module-map]

**Timestamps.** All instants are `timestamptz` and are stored as UTC instants (`ASM-BE-362`).

**PII and at-rest treatment.** `title` and `body` are free text and may contain PII incidentally. No handling policy or encryption statement is supplied (`GAP-BE-017`, `GAP-BE-033`, `GAP-BE-304`). No encryption, redaction or retention claim is made here.

**Region.** All note data, active and archived, is stored in the EU-region database. [requirements.md BR-001, BR-013] That provisioning is owned by `residency-compliance`.
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

**States.** `active` and `archived`. [stories.md US-002-04, US-002-05; requirements.md BR-007, BR-008]

**Terminal states.** There are none. Archiving is reversible and never deletes data. [BR-007, BR-008] The only possible terminal transition is the NFR-019 deletion of notes, which is `BLOCKED` (below).

| From | To | Trigger | Guard | Effect | Result |
|---|---|---|---|---|---|
| none | `active` | `API-NOTES-232390` | title and body valid | row created with `pinned` default false; `created_at` and `updated_at` set | 201. [AC-002-01-01] |
| none | none | `API-NOTES-232390` | title or body missing or empty | nothing persisted | 400. [AC-002-01-03; BR-005] |
| `active` | `archived` | `API-NOTES-810640` | status is `active` | `status` set; all other data intact | 200. [stories.md US-002-04] |
| `archived` | `archived` | `API-NOTES-810640` | status is `archived` | nothing changes | conflict or error; status `GAP-BE-309`; code `ALREADY_ARCHIVED` is proposed in `backend.error-model`. |
| `archived` | `active` | `API-NOTES-917869` | status is `archived` | `status` set; title, body, tags and `pinned` as before | 200. [stories.md US-002-05; BR-008] |
| `active` | `active` | `API-NOTES-917869` | status is `active` | not specified by any input | `GAP-BE-377`; default `ASM-BE-357`. |
| any | same | `API-NOTES-847551` | note exists; values valid | listed fields updated; unlisted fields untouched | 200. [AC-002-02-01; BR-006] |
| any | same | any id operation | no note with that id | nothing modified | 404. [AC-002-02-03; stories.md US-002-05, US-002-06] Derived for the tag, archive and unarchive operations. |
| any | none | retention deletion (NFR-019) | not decided | would remove the row | `BLOCKED` on `CON-BE-300` and `Q-BE-300`. |

**`pinned` is a flag, not a state.** It is orthogonal to `status`. It is changed only through `API-NOTES-232390` (initial value) and `API-NOTES-847551`. Setting `pinned` to its current value is a successful no-op (derived from "may be set", BR-018). Archive and unarchive neither read nor change `pinned`.

**Tags are membership, not state.** Adding a tag already on the note succeeds with no change. [stories.md US-002-03] Removal of a tag not on the note is `ASM-BE-358`.

**Archived notes.** No input forbids edit, pin or tag changes on an archived note. They behave as on an active note (`ASM-BE-356`, `GAP-BE-376`).

**Atomicity.** The guard on status is evaluated and the transition applied as one atomic step. Two concurrent archive calls on one active note therefore yield one success and one conflict. This is derived from the conflict rule in US-002-04. Concurrent PATCH is `ASM-BE-360`.
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

Error codes named below are the proposals in `backend.error-model` and are referred to by name only. The shape of requests and responses is in the contract (section 6) and is not restated.

### BE-NOTES-FR-001 Create a note

**Given** a payload with a valid title and body,
**When** `API-NOTES-232390` is called,
**Then** the API returns 201 with the created note, including a unique ID, timestamps and `pinned` = false by default.

- **Traces to.** US-002-01; AC-002-01-01; BR-005; BR-018.
- **Verification.** `test-notes-create-defaults` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-002 Create a pinned note

**Given** a valid payload with `pinned` = true,
**When** `API-NOTES-232390` is called,
**Then** the API returns 201 and the stored note has `pinned` = true.

- **Traces to.** US-002-01; AC-002-01-02; BR-018.
- **Verification.** `test-notes-create-pinned` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-003 Reject a create with no valid title

**Given** a payload whose title is missing, null or empty,
**When** `API-NOTES-232390` is called,
**Then** the API returns 400 with a validation error (`VALIDATION_FAILED`) and no note is persisted.

- **Traces to.** US-002-01; AC-002-01-03; BR-005.
- **Verification.** `test-notes-create-missing-title` (planned).
- **API.** `API-NOTES-232390`.
- **Note.** Whitespace-only is treated as empty (`ASM-BE-359`).

### BE-NOTES-FR-004 Reject a create with no valid body

**Given** a payload whose body is missing, null or empty,
**When** `API-NOTES-232390` is called,
**Then** the API returns 400 with a validation error (`VALIDATION_FAILED`) and no note is persisted.

- **Traces to.** US-002-01; BR-005 ("a note must always have a title and body at creation; empty/null payloads are rejected"). AC-002-01-03 states the title case only, so the body case is derived from BR-005.
- **Verification.** `test-notes-create-missing-body` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-005 Reject a non-boolean pinned on create

**Given** a payload whose `pinned` is present and is not a boolean,
**When** `API-NOTES-232390` is called,
**Then** the API returns 400 (`VALIDATION_FAILED`) and no note is persisted.

- **Traces to.** US-002-01; BR-018; NFR-012. The rule itself is `ASM-BE-368`.
- **Verification.** `test-notes-create-pinned-type` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-006 Read a note

**Given** an existing note id,
**When** `API-NOTES-045877` is called,
**Then** the API returns 200 with the full note, including its tags, its archive status and the `pinned` flag.

- **Traces to.** US-002-06; AC-002-06-01 as given by the story's first criterion; BR-018.
- **Verification.** `test-notes-get-full` (planned).
- **API.** `API-NOTES-045877`.

### BE-NOTES-FR-007 Read a note that does not exist

**Given** an id for which no note exists,
**When** `API-NOTES-045877` is called,
**Then** the API returns 404 (`NOT_FOUND`) and exposes no partial data.

- **Traces to.** US-002-06 second criterion.
- **Verification.** `test-notes-get-not-found` (planned).
- **API.** `API-NOTES-045877`.
- **Note.** The story also says "deleted". No delete operation is in scope (`ASM-BE-365`), so a deleted note is treated as never-existing.

### BE-NOTES-FR-008 Edit a note

**Given** an existing note id and valid updated fields,
**When** `API-NOTES-847551` is called,
**Then** the API returns 200 with the updated note and an updated timestamp.

- **Traces to.** US-002-02; AC-002-02-01.
- **Verification.** `test-notes-patch-update` (planned).
- **API.** `API-NOTES-847551`.
- **Note.** The story names PUT/PATCH. Only PATCH is specified (`GAP-BE-309`).

### BE-NOTES-FR-009 Partial update leaves other fields untouched

**Given** an existing note and a PATCH containing only the `pinned` field,
**When** `API-NOTES-847551` is called,
**Then** the API returns 200, `pinned` is updated, and title, body and tags are unchanged.
More generally, no field absent from a PATCH is overwritten.

- **Traces to.** US-002-02; AC-002-02-02; BR-006; BR-018; `INV-BE-008`.
- **Verification.** `test-notes-patch-pinned-only`, `test-notes-patch-partial-preserves` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-010 Edit a note that does not exist

**Given** a note id for which no note exists,
**When** `API-NOTES-847551` is called,
**Then** the API returns 404 (`NOT_FOUND`) and no data is modified.

- **Traces to.** US-002-02; AC-002-02-03.
- **Verification.** `test-notes-patch-not-found` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-011 Reject an invalid edit

**Given** an existing note and a PATCH that sets `title` or `body` to null or empty, or `pinned` to a non-boolean,
**When** `API-NOTES-847551` is called,
**Then** the API returns 400 (`VALIDATION_FAILED`) and no data is modified.

- **Traces to.** BR-005 applied to every stored value (`ASM-BE-359`); BR-006; US-002-02. `INV-BE-060`, `INV-BE-064`.
- **Verification.** `test-notes-patch-invalid-values` (planned).
- **API.** `API-NOTES-847551`.
- **Note.** Empty PATCH bodies, unknown fields and attempts to set immutable fields are `GAP-BE-374`.

### BE-NOTES-FR-012 Concurrent edits

**Given** two clients submit PATCH requests to the same note concurrently,
**When** both requests are processed,
**Then** the API applies a standard last-write response, with no live broadcast and no merge logic.

- **Traces to.** US-005-03 second criterion (owned by `scope-guardrail`; the behaviour lives in this module's PATCH handler).
- **Verification.** `test-notes-patch-concurrent` (planned).
- **API.** `API-NOTES-847551`.
- **Note.** The story allows "last-write/conflict". Last-write is chosen by default (`ASM-BE-360`, `Q-BE-354`, `GAP-BE-379`).

### BE-NOTES-FR-013 Add a tag

**Given** an existing note and a tag name,
**When** `API-NOTES-359413` is called,
**Then** the API returns 200 and the note's tag list includes the new tag.

- **Traces to.** US-002-03; AC for add as given by the story's first criterion; NFR-012.
- **Verification.** `test-notes-tag-add` (planned).
- **API.** `API-NOTES-359413`.
- **Note.** Tag name format and length are `GAP-BE-035`. The create-with-tags case is `ASM-BE-367`.

### BE-NOTES-FR-014 Add a tag already on the note

**Given** a tag that already exists on the note,
**When** `API-NOTES-359413` is called with the same tag,
**Then** the API returns 200 and creates no duplicate tag entry.

- **Traces to.** US-002-03 second criterion. `INV-BE-062`.
- **Verification.** `test-notes-tag-add-idempotent` (planned).
- **API.** `API-NOTES-359413`.

### BE-NOTES-FR-015 Add a tag to a note that does not exist

**Given** an id for which no note exists,
**When** `API-NOTES-359413` is called,
**Then** the API returns 404 (`NOT_FOUND`) and creates no tag or link.

- **Traces to.** Derived from the 404 rule in US-002-02 and US-002-05. No input states it for tags.
- **Verification.** `test-notes-tag-add-note-not-found` (planned).
- **API.** `API-NOTES-359413`.
- **Status.** Derived, not cited.

### BE-NOTES-FR-016 Remove a tag

**Given** an existing note carrying a tag,
**When** `API-NOTES-150096` is called for that tag,
**Then** the tag is no longer in the note's tag list, and the note's other data is unchanged.

- **Traces to.** US-002-03 (the story title says "add or remove"; it has no removal criteria); US-006-04 second criterion (web-app expectation).
- **Verification.** `test-notes-tag-remove` (planned).
- **API.** `API-NOTES-150096`.
- **Status.** The success status is not stated (`GAP-BE-373`). A tag not on the note returns 404 (`ASM-BE-358`).

### BE-NOTES-FR-017 Archive a note

**Given** an active, existing note,
**When** `API-NOTES-810640` is called,
**Then** the API returns 200, marks the note archived, and its data remains intact.

- **Traces to.** US-002-04; BR-007.
- **Verification.** `test-notes-archive` (planned).
- **API.** `API-NOTES-810640`.

### BE-NOTES-FR-018 Archive an already-archived note

**Given** an already-archived note,
**When** `API-NOTES-810640` is called again,
**Then** the API returns a conflict or error response indicating the note is already archived (`ALREADY_ARCHIVED`), and nothing changes.

- **Traces to.** US-002-04 second criterion.
- **Verification.** `test-notes-archive-twice` (planned).
- **API.** `API-NOTES-810640`.
- **Status.** The status code is not stated (`GAP-BE-309`).

### BE-NOTES-FR-019 Archive a note that does not exist

**Given** an id for which no note exists,
**When** `API-NOTES-810640` is called,
**Then** the API returns 404 (`NOT_FOUND`).

- **Traces to.** Derived from the 404 rule in US-002-05. No input states it for archive.
- **Verification.** `test-notes-archive-not-found` (planned).
- **API.** `API-NOTES-810640`.
- **Status.** Derived, not cited.

### BE-NOTES-FR-020 Unarchive a note

**Given** an archived note,
**When** `API-NOTES-917869` is called,
**Then** the API returns 200, the note's status changes to active, and all original data is preserved, including title, body, tags and `pinned`.

- **Traces to.** US-002-05; BR-008; `INV-BE-061`.
- **Verification.** `test-notes-unarchive-restores`, `test-notes-archive-roundtrip` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-021 Unarchive a note that does not exist

**Given** an id for which no note exists,
**When** `API-NOTES-917869` is called,
**Then** the API returns 404 (`NOT_FOUND`).

- **Traces to.** US-002-05 second criterion.
- **Verification.** `test-notes-unarchive-not-found` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-022 Validate and sanitise input

**Given** any request to a note, tag, archive or unarchive operation,
**When** the request is processed,
**Then** the title, body and tag fields are validated and sanitised, so that injection payloads are neither executed nor stored as executable content.

- **Traces to.** NFR-012 (target: 0 critical injection vulnerabilities per release).
- **Verification.** `test-notes-injection-payloads` (planned), plus the release security scan named in NFR-012.
- **API.** all seven operations.
- **Note.** No library or technique is named in any input (`GAP-BE-089`).

### BE-NOTES-FR-023 Require authentication

**Given** a request to any of the seven operations with no valid credential,
**When** the request reaches the operation,
**Then** the API returns 401 (`UNAUTHENTICATED`) and nothing is read or changed.

- **Traces to.** Derived from US-006-06, US-004-03 and `backend.auth-model`, where unauthenticated callers are refused on note operations by derivation.
- **Verification.** `test-notes-requires-auth` (planned).
- **API.** all seven operations.
- **Status.** Derived, not cited. The credential form is `Q-BE-012`.

### BE-NOTES-FR-024 Note access rule

`BLOCKED`. The rule for which authenticated caller may read or change which note (403 and the ownership predicate) cannot be written. BR-031 is corrupted as supplied. BR-030 leaves open who may obtain a credential at all. The shared-workspace default of `Q-BE-001` is a placeholder only, and `CON-BE-003` (Critical) calls it into question.

- **Traces to.** BR-030; BR-031.
- **Unblocked by.** The answer to `Q-BE-003` and a corrected BR-031.
- **API.** all seven operations.

### Story to requirement mapping

| Story | Requirements |
|---|---|
| US-002-01 | FR-001, FR-002, FR-003, FR-004, FR-005 |
| US-002-02 | FR-008, FR-009, FR-010, FR-011, FR-012 |
| US-002-03 | FR-013, FR-014, FR-015, FR-016 |
| US-002-04 | FR-017, FR-018, FR-019 |
| US-002-05 | FR-020, FR-021 |
| US-002-06 | FR-006, FR-007 |
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 401, 403, 415 | Create a note with a mandatory non-empty title and body, optionally pinned. |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 401, 403, 404 | Read a single note by id. |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 401, 403, 404, 415 | Partially edit a note's title or body, and pin or unpin it via the pinned field; omitted fields are never overwritten. |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 401, 403, 404, 409 | Archive a note, hiding it from default listings without deleting any data. Path proposed (GAP-BE-303). |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 401, 403, 404, 415 | Add a tag to a note; adding an existing tag creates no duplicate. Path proposed because none is supplied (GAP-BE-303). |
| `API-NOTES-150096` | DELETE | `/notes/{id}/tags/{tag}` | `notes` | 200 | 401, 403, 404 | Remove a tag from a note. Path proposed because none is supplied (GAP-BE-303). |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 401, 403, 404 | Unarchive a note, restoring it to active with all data preserved. Path proposed (GAP-BE-303). |

### `API-NOTES-232390` — POST `/notes`

Create a note with a mandatory non-empty title and body, optionally pinned.

**Request:** 
- `title` (string, required): Note title; mandatory and non-empty (BR-005, AC-002-01-03).
- `body` (string, required): Note body; mandatory and non-empty (BR-005).
- `pinned` (boolean): Whether the note is pinned; defaults to false (BR-018).
**Response:** 
- `id` (string, required): Identifier of the created note.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `status` (string, required): Note status, active or archived; a new note is active.
- `pinned` (boolean, required): Pinned flag.
- `tags` (array, required): Tag names applied to the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-045877` — GET `/notes/{id}`

Read a single note by id.

**Request:** none
**Response:** 
- `id` (string, required): Note identifier.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `status` (string, required): Note status, active or archived.
- `pinned` (boolean, required): Pinned flag.
- `tags` (array, required): Tag names applied to the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-847551` — PATCH `/notes/{id}`

Partially edit a note's title or body, and pin or unpin it via the pinned field; omitted fields are never overwritten.

**Request:** 
- `title` (string): New title; omitted means unchanged (BR-006).
- `body` (string): New body; omitted means unchanged (BR-006).
- `pinned` (boolean): true pins, false unpins the note (BR-018).
**Response:** 
- `id` (string, required): Note identifier.
- `title` (string, required): Note title after the update.
- `body` (string, required): Note body after the update.
- `status` (string, required): Note status, active or archived.
- `pinned` (boolean, required): Pinned flag after the update.
- `tags` (array, required): Tag names applied to the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note, hiding it from default listings without deleting any data. Path proposed (GAP-BE-303).

**Request:** none
**Response:** 
- `id` (string, required): Note identifier.
- `status` (string, required): Note status, now archived.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add a tag to a note; adding an existing tag creates no duplicate. Path proposed because none is supplied (GAP-BE-303).

**Request:** 
- `name` (string, required): Tag name to add; format and length rules not supplied.
**Response:** 
- `id` (string, required): Note identifier.
- `tags` (array, required): Tag names on the note after the addition, without duplicates.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-150096` — DELETE `/notes/{id}/tags/{tag}`

Remove a tag from a note. Path proposed because none is supplied (GAP-BE-303).

**Request:** none
**Response:** 
- `id` (string, required): Note identifier.
- `tags` (array, required): Tag names remaining on the note.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Unarchive a note, restoring it to active with all data preserved. Path proposed (GAP-BE-303).

**Request:** none
**Response:** 
- `id` (string, required): Note identifier.
- `status` (string, required): Note status, now active.
- `updated_at` (string, required): Last update timestamp.
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

These hold after every operation of this module. The project-level `INV-BE-008` (PATCH never overwrites an absent field) and `INV-BE-009` (archive and unarchive preserve all note data) also apply and are not restated.

- **`INV-BE-060`.** Every stored note has a non-empty title and a non-empty body. [requirements.md BR-005]
- **`INV-BE-061`.** `status` is exactly `active` or `archived`. Archive and unarchive change `status` and `updated_at` only; title, body, tags, `pinned` and `created_at` are unchanged. [requirements.md BR-007, BR-008]
- **`INV-BE-062`.** No `(note_id, tag_name)` pair appears twice. [stories.md US-002-03]
- **`INV-BE-063`.** `pinned` is never null; it is a boolean. [requirements.md BR-018; `ASM-BE-034`]
- **`INV-BE-064`.** An operation that returns a 4xx response has modified no stored note, tag or link. [AC-002-01-03; AC-002-02-03] This is the "no note persisted" and "no data modified" rule generalised.
- **`INV-BE-065`.** `updated_at` is at or after `created_at`, and is advanced by every successful change to the note (`ASM-BE-362`).
- **`INV-BE-066`.** Note ids are unique. No operation of this module changes an id. [AC-002-01-01; `ASM-BE-363`]

`INV-BE-001` (pinned notes sort first in list and search) depends on `INV-BE-063` and is enforced by the `search` module.
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: DRAFT-UNVERIFIED -->
## 7. Authorization

**Source.** This section applies `backend.auth-model`. No input defines a roles model (`GAP-BE-085`). A role has no permission on an operation unless an input grants it (`ASM-BE-302`).

| Operation | Unauthenticated caller | Team member | Other roles |
|---|---|---|---|
| `API-NOTES-232390` create | D* (401) | `B` | - |
| `API-NOTES-045877` read | D* (401) | `B` | - |
| `API-NOTES-847551` edit and pin | D* (401) | `B` | - |
| `API-NOTES-359413` tag add | D* (401) | `B` | - |
| `API-NOTES-150096` tag remove | D* (401) | `B` | - |
| `API-NOTES-810640` archive | D* (401) | `B` | - |
| `API-NOTES-917869` unarchive | D* (401) | `B` | - |

Legend: `D*` refused, derived from US-006-06 and US-004-03 (`BE-NOTES-FR-023`). `B` blocked on `CON-BE-003` and `Q-BE-003`. `-` no grant stated, so refused under `ASM-BE-302`.

Stories grant these actions to a "team member". Who counts as a team member is open, so the cell stays `B`, not `A`.

**Ownership predicate.** `BLOCKED` on `CON-BE-003`, `Q-BE-003` and the corrupted BR-031. The placeholder default is `Q-BE-001`: notes form a shared workspace and any authenticated team member may read and write any note. That default is not applied as a rule here, and no ownership column exists in section 2 until it is decided.

**What would unblock it.** The answer to `Q-BE-003`, and a corrected BR-031 that states the access rule.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: BLOCKED -->
## 8. Jobs

**Blocked.** The only scheduled work any input implies for this module is the retention deletion in NFR-019. As supplied it reads positionally "notes 1 days" (`ASM-BE-303`), which deletes every note after one day. That contradicts BR-007, BR-008 and NFR-005 (`CON-BE-300`, Critical). Whether it is intended is `Q-BE-300` (blocking). The mechanism, schedule and effect on references are `GAP-BE-101`.

**Would unblock it.** An answer to `Q-BE-300`, a corrected NFR-019, and an operations decision on the scheduler.

**Not blocked, stated for completeness.**

- No sweep, retry or other background work is specified for archive, unarchive, tags or pinning.
- **Idempotency.** Tag add is idempotent by rule (FR-014). Archive is deliberately not idempotent (FR-018). PATCH sets values and repeating it gives the same stored result. No idempotency key is supplied for create, so a retried POST can create a second note (`GAP-BE-375`).
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

All tests are planned. None exist.

**Unit.**

| Test | Covers |
|---|---|
| `test-notes-validate-title-body` | FR-003, FR-004, FR-011; `INV-BE-060` (empty, null, whitespace-only) |
| `test-notes-validate-pinned-type` | FR-005, FR-011; `INV-BE-063` |
| `test-notes-partial-merge` | FR-009; `INV-BE-008` |
| `test-notes-state-guard` | FR-017, FR-018, FR-020; state machine guards |
| `test-notes-updated-at-advances` | `INV-BE-065` |

**Integration** (PostgreSQL 16 test database).

| Test | Covers |
|---|---|
| `test-notes-create-defaults`, `test-notes-create-pinned`, `test-notes-create-missing-title`, `test-notes-create-missing-body`, `test-notes-create-pinned-type` | FR-001 to FR-005 |
| `test-notes-get-full`, `test-notes-get-not-found` | FR-006, FR-007 |
| `test-notes-patch-update`, `test-notes-patch-pinned-only`, `test-notes-patch-partial-preserves`, `test-notes-patch-not-found`, `test-notes-patch-invalid-values`, `test-notes-patch-concurrent` | FR-008 to FR-012 |
| `test-notes-tag-add`, `test-notes-tag-add-idempotent`, `test-notes-tag-add-note-not-found`, `test-notes-tag-remove` | FR-013 to FR-016; `INV-BE-062` |
| `test-notes-archive`, `test-notes-archive-twice`, `test-notes-archive-not-found`, `test-notes-unarchive-restores`, `test-notes-unarchive-not-found`, `test-notes-archive-roundtrip` | FR-017 to FR-021; `INV-BE-061` |
| `test-notes-no-write-on-4xx` | `INV-BE-064`: after each 4xx case, row counts and stored values are unchanged |
| `test-notes-requires-auth` | FR-023 |
| `test-notes-injection-payloads` | FR-022; NFR-012 |
| `test-notes-concurrent-archive` | state-machine atomicity: one success, one conflict |

**Contract.**

| Test | Covers |
|---|---|
| `test-notes-contract-conformance` | Each of the seven operations answers as the rendered OpenAPI entry says, including every documented error status. [requirements.md BR-012, NFR-008] |
| `test-notes-crud-pass-rate` | NFR-005: 100% functional-test pass rate for create, edit, get, archive, unarchive, with zero data loss |
| `test-notes-search-reads-pinned-status` | The `search` module's reads of `pinned` and `status` match what this module stores. Shared with the `search` module's plan. |

**Not testable yet.** The note access rule (FR-024, `Q-BE-003`) and the retention deletion (section 8, `Q-BE-300`). Tests for them are written after those items are decided.

**Release gates.** The injection scan named in NFR-012. No latency, throughput or availability target is supplied for these operations beyond the 99.9% monthly figure for CRUD and search in NFR-013, so no performance test is specified (`GAP-BE-380`).
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: DERIVED -->
## 10. Gaps, contradictions, assumptions and questions

### Contradiction check

| Direction | Result |
|---|---|
| story and design | The design brief contains AC-002-01-01 to AC-002-01-03 only for this module. They match US-002-01. No contradiction found. |
| story and requirement | No new contradiction. US-002-06 says "deleted", while BR-007 excludes hard delete (handled by `ASM-BE-365`). |
| requirement and requirement | `CON-BE-300` (BR-007, BR-008, NFR-005 against NFR-019 as supplied). Carried from the project spec. |
| design and design | None found. |

No new `CON-` was minted. Open contradictions that affect this module: `CON-BE-003` (Critical), `CON-BE-300` (Critical), `CON-BE-019` (the change request carries three labels; no identifier renumbered).

### Gaps

| ID | What is missing | Degrades | What was done | Closing artefact |
|---|---|---|---|---|
| `GAP-BE-372` | Maximum length of title and body, and any other field constraint. US-004-02 says such constraints are documented but gives no value. | data model; FR-003, FR-004, FR-011 | No length limit is asserted. | Field-level requirements. |
| `GAP-BE-373` | Success status for tag removal, and behaviour on removing a tag not on the note. US-002-03 has criteria for add only. | FR-016 | FR-016 is `DRAFT-UNVERIFIED`; absent tag is 404 under `ASM-BE-358`. | Acceptance criteria for removal. |
| `GAP-BE-374` | Behaviour of PATCH with an empty body, with unknown fields, and with attempts to set `id`, `status`, `created_at` or `updated_at`. Whether PATCH accepts a `tags` field (US-002-02 says "or tags"). | FR-008, FR-011 | Tags change only through the two tag operations; the rest is not asserted. | Contract decision for PATCH. |
| `GAP-BE-375` | No idempotency key or duplicate-create protection for POST `/notes`. | FR-001; section 8 | A retried create can create a second note. | A decision on client-supplied idempotency keys. |
| `GAP-BE-376` | Whether edit, pin and tag changes are allowed on an archived note. | state machine; FR-008, FR-013 | Allowed under `ASM-BE-356`. | Answer to `Q-BE-351`. |
| `GAP-BE-377` | Behaviour of unarchive on an active note. | state machine; FR-020 | Same conflict as archive of an archived note under `ASM-BE-357`; no requirement written. | Answer to `Q-BE-352`. |
| `GAP-BE-378` | Tag name normalisation (case, whitespace) and whether a tag with no notes is kept. | data model; FR-013 | Exact comparison (`ASM-BE-369`); orphan tags retained. | Tag naming rule from the product owner. |
| `GAP-BE-379` | Whether concurrent PATCH returns last-write or a conflict. US-005-03 allows either. | FR-012 | Last-write (`ASM-BE-360`). | Answer to `Q-BE-354`. |
| `GAP-BE-380` | No rate-limit class and no latency or throughput target for note operations beyond NFR-013's 99.9% monthly. | contract; test plan | None asserted. | NFR figures for note operations. |

Carried from the project spec and affecting this module: `GAP-BE-003`, `GAP-BE-017`, `GAP-BE-033`, `GAP-BE-035`, `GAP-BE-085`, `GAP-BE-089`, `GAP-BE-101`, `GAP-BE-300`, `GAP-BE-303`, `GAP-BE-304`, `GAP-BE-309`, `GAP-BE-310` (within-group ordering, owned by `search`).

### Assumptions

| ID | Assumption | Reason | Confirmed by |
|---|---|---|---|
| `ASM-BE-356` | Edit, pin and tag changes on an archived note behave as on an active note. | No input forbids them, and BR-008 says only that unarchive restores the prior state. | `Q-BE-351` |
| `ASM-BE-357` | Unarchive of an active note returns the same conflict as archive of an archived note. | Mirrors US-002-04's second criterion. Nothing else is cited. | `Q-BE-352` |
| `ASM-BE-358` | Removing a tag not on the note returns 404 (`NOT_FOUND`). | Add is explicitly idempotent while removal has no criteria. A 404 keeps the two distinguishable. | `Q-BE-353` |
| `ASM-BE-359` | A title or body of whitespace only counts as empty. | BR-005 says "empty/null" rejected; a whitespace-only value carries no content. | Product owner |
| `ASM-BE-360` | Concurrent PATCH is last-write-wins; there is no version column. | US-005-03 allows last-write or conflict and neither is specified further. | `Q-BE-354` |
| `ASM-BE-361` | Column types: `uuid` for `id`, `text` for title, body and tag name, `boolean`, `timestamptz`. | Platform is PostgreSQL 16; no input states types. | Platform decision |
| `ASM-BE-362` | Timestamps are stored as UTC instants. `updated_at` is set on PATCH, archive, unarchive, and on any tag change that alters the tag set. A repeated add of an existing tag changes nothing. | AC-002-02-01 requires an updated timestamp on edit only. | Confirmation |
| `ASM-BE-363` | The note id is server-assigned and opaque; clients cannot supply it. | AC-002-01-01 says the API returns a unique ID. | Confirmation |
| `ASM-BE-364` | A new note has status `active`. | US-002-04 archives an "active, existing note" and the create criteria carry no status. | Confirmation |
| `ASM-BE-365` | No delete operation for notes is in scope. "Deleted" in US-002-06 is treated as never-existing. | BR-007 keeps data; no input describes a delete. | `Q-BE-300` |
| `ASM-BE-366` | `note_tags` foreign keys use `ON DELETE RESTRICT`. | No delete is in scope (`ASM-BE-365`), so the choice has no visible effect. | When deletion is specified |
| `ASM-BE-367` | POST `/notes` accepts no tags and creates no tag rows. | The create criteria carry title, body and `pinned` only. | Contract decision |
| `ASM-BE-368` | A non-boolean `pinned` on create or PATCH is refused with 400. | Validation applies to every typed field, as in AC-002-01-03 and NFR-012. | Confirmation |
| `ASM-BE-369` | Tag names are compared exactly, with no case folding. | No normalisation rule is supplied. Example tag names in the stories are lowercase. | `GAP-BE-378` |

Project-level assumptions used: `ASM-BE-034`, `ASM-BE-302`, `ASM-BE-303`.

### Questions

Unattended run: degradable questions take their stated default silently; blocking questions halt. None is asked in this document.

| ID | Class | Question | Default |
|---|---|---|---|
| `Q-BE-351` | degradable | Are edit, pin and tag changes allowed on an archived note? | Allowed (`ASM-BE-356`). |
| `Q-BE-352` | degradable | What does unarchive of an active note return? | Conflict, as in `ASM-BE-357`. |
| `Q-BE-353` | degradable | What does removing a tag that is not on the note return? | 404 (`ASM-BE-358`). |
| `Q-BE-354` | degradable | Does concurrent PATCH return last-write or a conflict? | Last-write (`ASM-BE-360`). |

Open blocking items that apply to this module and are owned by the project spec: `Q-BE-003`, `Q-BE-300`, `CON-BE-003`, `CON-BE-300`. Under core rule 10.1 they halt the run. Everything independent of them is written above. The access rule (FR-024), the ownership predicate and the retention job are `BLOCKED` in place.
<!-- /SECTION: backend.notes.gaps -->
