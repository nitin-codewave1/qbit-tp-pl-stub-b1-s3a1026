---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06]
commit_kind: fork
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [CON-BE-003, CON-BE-300, Q-BE-003]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-359413, API-NOTES-150096, API-NOTES-810640, API-NOTES-917869]
```
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: DRAFT-UNVERIFIED -->
## 1. Purpose

**Responsibility.** The `notes` module owns the note lifecycle: create, read one, partial edit (including pin and unpin through `pinned`), add and remove tags, archive and unarchive. It owns the data of NOTE, TAG and NOTE_TAG. [backend.module-map; stories.md US-002-01 to US-002-06; requirements.md BR-005 to BR-008, BR-018]

Operations owned, by id only: `API-NOTES-232390`, `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096`, `API-NOTES-810640`, `API-NOTES-917869`.

**Not responsible for.**
- Keyword and tag search, pagination, pinned-first ordering and the search index. These belong to `search`. `search` reads NOTE and TAG data and never writes it. [backend.module-map; BR-019]
- Listing notes. No operation for it is assigned to this module (`GAP-BE-564`).
- Credential issue, verification and the login flow. These belong to `auth`. This module only requires a valid credential on its routes. The credential form is `Q-BE-012` and the existing authentication is `GAP-BE-085`.
- The global error envelope and the status-code policy. They belong to `backend.error-model` and are applied here, not redefined.
- The OpenAPI document, the Scope section and the rejection of attachment, real-time and mobile calls. These belong to `api-docs` and `scope-guardrail`.
- Note ownership columns and the note access rule. They are `BLOCKED` on `CON-BE-003` and `Q-BE-003`.
- Retention deletion of notes. NFR-019 as supplied would delete notes after 1 day, which conflicts with BR-007, BR-008 and NFR-005. It is `BLOCKED` on `CON-BE-300` and `Q-BE-300`, and no deletion is specified here.
- Any screen or UI. [backend.overview]

**Dependencies.** `notes` → `auth` (credential validation, blocked on `Q-BE-012` and `GAP-BE-085`). `search` depends on `notes`, not the reverse. [backend.module-map]

**Identifier note.** This module mints from its reserved block only: `ASM-BE-356` onward, `GAP-BE-564` onward, `INV-BE-060` onward, `Q-BE-351` onward. It mints no `CON-`. Every other id is cited from the project spec.
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, nullability or constraints. The platform is PostgreSQL 16 [platform.md]. The types below are a proposal (`ASM-BE-356`). Entity and column names follow `backend.domain-model`. No ORM or migration tool is named (`GAP-BE-089`).

**notes**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | none | primary key; server-assigned, never client-supplied (`ASM-BE-358`) |
| `title` | text | NOT NULL | none | `CHECK (btrim(title) <> '')` (`ASM-BE-359`); maximum length `GAP-BE-035` |
| `body` | text | NOT NULL | none | `CHECK (btrim(body) <> '')` (`ASM-BE-359`); maximum length `GAP-BE-035` |
| `status` | text | NOT NULL | `'active'` | `CHECK (status IN ('active','archived'))` (`ASM-BE-357`) |
| `pinned` | boolean | NOT NULL | `false` | BR-018 gives the default and no null state (`ASM-BE-034`) |
| `created_at` | timestamptz | NOT NULL | none | set once at creation, never updated (`INV-BE-067`) |
| `updated_at` | timestamptz | NOT NULL | none | `CHECK (updated_at >= created_at)`; set per `ASM-BE-366` |

- Ownership columns are not defined. They are `BLOCKED` on `CON-BE-003` and `Q-BE-003`, and a later migration would add them (`backend.migration-plan`).
- No `archived_at` column is added. No input requires one, and archive is a `status` flag only. [BR-007]

**tags**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `tag_name` | text | NOT NULL | none | primary key; compared exactly as supplied (`ASM-BE-370`); format and length `GAP-BE-035` |

A tag row persists after its last note is untagged (`ASM-BE-368`).

**note_tags**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `note_id` | uuid | NOT NULL | none | FK → `notes(id)` `ON DELETE RESTRICT` (`ASM-BE-367`) |
| `tag_name` | text | NOT NULL | none | FK → `tags(tag_name)` `ON DELETE RESTRICT` (`ASM-BE-367`) |

Primary key: `(note_id, tag_name)`. This is what prevents a duplicate tag on a note. [US-002-03; `INV-BE-064`]

**Indexes.**
- The `note_tags` primary key serves reading all tags of a note (used by get, add tag, remove tag).
- btree `(tag_name, note_id)` on `note_tags` serves the tag filter that `search` runs. [US-003-02]
- No index on `notes` is added here. Indexes for keyword search, pinned-first ordering and listing belong to `search` and to `GAP-BE-564`.

**Referential integrity.** `ON DELETE RESTRICT` is used because this module never hard-deletes a note (`INV-BE-066`). A future delete path would otherwise cascade-destroy tags links silently.

**Time.** Every instant is `timestamptz`, stored and compared in UTC.

**PII and encryption.** No column is declared PII. A note body may hold personal data, but no input says so (`GAP-BE-017`). No encryption at rest is claimed (`GAP-BE-304`).

**Residency.** All note rows, active and archived, are stored in an EU region. Archived notes sit in the same table and the same region as active ones. [BR-001; US-004-06]
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

Entity: NOTE, by the `status` column. States: `active`, `archived`. **Neither state is terminal**: archive is fully reversible. [BR-008] There is no deleted state in this module (`INV-BE-066`).

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `active` | `API-NOTES-232390` | title and body non-empty; `pinned` boolean if supplied | row inserted; `pinned` defaults to `false`; `ASM-BE-357` |
| `active` | `archived` | `API-NOTES-810640` | note exists and `status = 'active'` | `status` set to `archived`; all other data intact [BR-007; US-002-04] |
| `archived` | `active` | `API-NOTES-917869` | note exists and `status = 'archived'` | `status` set to `active`; title, body, pinned and tags unchanged [BR-008; US-002-05] |
| `active` | `active` | `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096` | note exists; input valid | supplied fields or tag links change; `status` unchanged |
| `archived` | `archived` | `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096` | note exists; input valid | same as above (`ASM-BE-362`, `Q-BE-351`) |

**Illegal transitions.**

| Attempt | Result | Status |
|---|---|---|
| archive an `archived` note | refused, conflict, no change [US-002-04] | status `GAP-BE-309`; error code `NOTE_ALREADY_ARCHIVED` (proposed in `backend.error-model`) |
| unarchive an `active` note | refused, conflict, no change (`ASM-BE-363`, `Q-BE-352`) | status `GAP-BE-309`; error code `NOTE_NOT_ARCHIVED` (`GAP-BE-565`) |
| any operation on a note that does not exist | refused, no change | 404 `NOT_FOUND` [US-002-02, US-002-05, US-002-06]; for archive and tag operations `ASM-BE-369` |

**Reads.** `API-NOTES-045877` reads in either state and changes none. [US-002-06]

**Concurrency.** Each state change is one conditional `UPDATE ... WHERE id = $1 AND status = $2`. If it matches no row, a follow-up read distinguishes not found from conflict. Two concurrent PATCH requests are applied last-write-wins on the fields each one supplies (`ASM-BE-365`, `Q-BE-353`). There is no broadcast and no merge logic. [US-005-03; BR-015]
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

Statuses of error responses follow `backend.error-model`. Where an input does not state a status, the item names the gap.

### BE-NOTES-FR-001 Create a note
**Given** an authenticated caller and a payload with a non-empty `title` and `body`,
**When** `API-NOTES-232390` is called,
**Then** a note is stored with a server-assigned unique id, `status` `active`, `pinned` `false`, and `created_at` and `updated_at` set, and 201 is returned with the created note.
- **Traces to.** US-002-01; AC-002-01-01; BR-005; BR-018.
- **Verification.** `test-notes-create-defaults` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-002 Create a pinned note
**Given** a valid payload with `pinned` `true`,
**When** `API-NOTES-232390` is called,
**Then** 201 is returned and the stored note has `pinned` `true`.
- **Traces to.** US-002-01; AC-002-01-02; BR-018.
- **Verification.** `test-notes-create-pinned` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-003 Reject an invalid create
**Given** a payload with a missing, null, empty or whitespace-only `title` or `body`, or a `pinned` that is not a boolean,
**When** `API-NOTES-232390` is called,
**Then** 400 `VALIDATION_ERROR` is returned and no note is persisted.
- **Traces to.** US-002-01; AC-002-01-03; BR-005; `ASM-BE-359`; `ASM-BE-371`. Length limits are `GAP-BE-035`.
- **Verification.** `test-notes-create-missing-title`, `test-notes-create-missing-body`, `test-notes-create-whitespace`, `test-notes-create-pinned-not-boolean` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-004 Get a note
**Given** an existing note id, active or archived,
**When** `API-NOTES-045877` is called,
**Then** 200 is returned with the full note: id, title, body, tags, archive status, the `pinned` flag, and timestamps.
- **Traces to.** US-002-06; AC-002-06 criteria in stories.md.
- **Verification.** `test-notes-get-active`, `test-notes-get-archived` (planned).
- **API.** `API-NOTES-045877`.

### BE-NOTES-FR-005 Get a note that does not exist
**Given** an id for no note,
**When** `API-NOTES-045877` is called,
**Then** 404 `NOT_FOUND` is returned with no partial data in the body.
- **Traces to.** US-002-06.
- **Verification.** `test-notes-get-not-found` (planned).
- **API.** `API-NOTES-045877`.

### BE-NOTES-FR-006 Edit a note
**Given** an existing note and valid values for any of `title`, `body`, `pinned`,
**When** `API-NOTES-847551` is called,
**Then** 200 is returned with the updated note, `updated_at` is advanced, and `created_at` is unchanged.
- **Traces to.** US-002-02; BR-006; BR-018.
- **Verification.** `test-notes-patch-fields`, `test-notes-patch-updated-at` (planned).
- **API.** `API-NOTES-847551`.
- **Status.** PUT versus PATCH is `GAP-BE-309`; only PATCH is defined. Tag edits through PATCH are `GAP-BE-566`.

### BE-NOTES-FR-007 Partial update preserves other fields
**Given** an existing note,
**When** `API-NOTES-847551` is called with a payload containing only `pinned`,
**Then** 200 is returned, `pinned` is updated, and `title`, `body` and tags are unchanged. A field absent from the payload is never written.
- **Traces to.** US-002-02; AC-002-02-02; BR-006; `INV-BE-008`.
- **Verification.** `test-notes-patch-pinned-only`, `test-notes-patch-omitted-fields-untouched` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-008 Edit a note that does not exist
**Given** an id for no note,
**When** `API-NOTES-847551` is called,
**Then** 404 `NOT_FOUND` is returned and no data is modified.
- **Traces to.** US-002-02; AC-002-02-03.
- **Verification.** `test-notes-patch-not-found` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-009 Reject an invalid edit
**Given** a PATCH payload that is empty, contains a field other than `title`, `body`, `pinned` (including `id`, `status`, `created_at`, `updated_at`), sets `title` or `body` empty, or sets `pinned` to a non-boolean,
**When** `API-NOTES-847551` is called,
**Then** 400 `VALIDATION_ERROR` is returned and nothing is written.
- **Traces to.** BR-005; BR-006; NFR-012; `ASM-BE-360`, `ASM-BE-361`, `ASM-BE-371`. Not directly stated in an input for the empty payload and unknown-field cases.
- **Verification.** `test-notes-patch-empty-body`, `test-notes-patch-unknown-field`, `test-notes-patch-blank-title`, `test-notes-patch-status-field-rejected` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-010 Add a tag
**Given** an existing note and a valid tag name not on the note,
**When** `API-NOTES-359413` is called,
**Then** 200 is returned and the note's tag list includes the tag. The tag row is created if it does not exist.
- **Traces to.** US-002-03; first acceptance criterion.
- **Verification.** `test-notes-tag-add` (planned).
- **API.** `API-NOTES-359413`.
- **Status.** Tag name format, length and case rules are `GAP-BE-035`; comparison is exact per `ASM-BE-370`.

### BE-NOTES-FR-011 Adding an existing tag creates no duplicate
**Given** a tag already on the note,
**When** `API-NOTES-359413` is called with the same tag name,
**Then** 200 is returned, no second link is created, and `updated_at` is not advanced (`ASM-BE-366`).
- **Traces to.** US-002-03; second acceptance criterion; `INV-BE-064`.
- **Verification.** `test-notes-tag-add-duplicate`, `test-notes-tag-add-concurrent` (planned).
- **API.** `API-NOTES-359413`.

### BE-NOTES-FR-012 Remove a tag
**Given** an existing note carrying the tag,
**When** `API-NOTES-150096` is called,
**Then** the link is removed, the tag no longer appears on the note, and the note's other tags and data are unchanged.
- **Traces to.** US-002-03 (story text: "add or remove tags"); `INV-BE-065`.
- **Verification.** `test-notes-tag-remove` (planned).
- **API.** `API-NOTES-150096`.
- **Status.** The success status and response body are `GAP-BE-567`. A tag not on the note returns 404 `NOT_FOUND` (`ASM-BE-364`).

### BE-NOTES-FR-013 Archive a note
**Given** an existing note with `status` `active`,
**When** `API-NOTES-810640` is called,
**Then** 200 is returned, `status` is `archived`, and title, body, `pinned` and tags are intact. No data is deleted.
- **Traces to.** US-002-04; first acceptance criterion; BR-007; `INV-BE-066`.
- **Verification.** `test-notes-archive` (planned).
- **API.** `API-NOTES-810640`.

### BE-NOTES-FR-014 Archive an already-archived note
**Given** a note with `status` `archived`,
**When** `API-NOTES-810640` is called,
**Then** a conflict response with error code `NOTE_ALREADY_ARCHIVED` is returned and nothing changes.
- **Traces to.** US-002-04; second acceptance criterion.
- **Verification.** `test-notes-archive-twice` (planned).
- **API.** `API-NOTES-810640`.
- **Status.** The status code is `GAP-BE-309`.

### BE-NOTES-FR-015 Unarchive a note
**Given** an existing note with `status` `archived`,
**When** `API-NOTES-917869` is called,
**Then** 200 is returned, `status` is `active`, and all original data is preserved.
- **Traces to.** US-002-05; first acceptance criterion; BR-008; `INV-BE-065`.
- **Verification.** `test-notes-unarchive`, `test-notes-archive-unarchive-roundtrip` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-016 Unarchive a note that does not exist
**Given** an id for no note,
**When** `API-NOTES-917869` is called,
**Then** 404 `NOT_FOUND` is returned.
- **Traces to.** US-002-05; second acceptance criterion.
- **Verification.** `test-notes-unarchive-not-found` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-017 Unarchive a note that is already active
**Given** a note with `status` `active`,
**When** `API-NOTES-917869` is called,
**Then** a conflict response with error code `NOTE_NOT_ARCHIVED` is returned and nothing changes.
- **Traces to.** Not stated in any input. Derived by symmetry with US-002-04 (`ASM-BE-363`, `Q-BE-352`).
- **Verification.** `test-notes-unarchive-active` (planned).
- **API.** `API-NOTES-917869`.
- **Status.** The status code is `GAP-BE-309`; the error code is `GAP-BE-565`.

### BE-NOTES-FR-018 Missing note on archive and tag operations
**Given** an id for no note,
**When** `API-NOTES-810640`, `API-NOTES-359413` or `API-NOTES-150096` is called,
**Then** 404 `NOT_FOUND` is returned and nothing is created or changed.
- **Traces to.** Derived from the 404 policy in `backend.error-model`; no input states it for these operations (`ASM-BE-369`).
- **Verification.** `test-notes-archive-not-found`, `test-notes-tag-add-note-not-found`, `test-notes-tag-remove-note-not-found` (planned).
- **API.** `API-NOTES-810640`, `API-NOTES-359413`, `API-NOTES-150096`.

### BE-NOTES-FR-019 Authentication required
**Given** a request with no valid credential,
**When** any of the seven operations is called,
**Then** 401 `UNAUTHENTICATED` is returned and nothing is read or changed.
- **Traces to.** US-004-03 (401 documented for authenticated endpoints); `backend.auth-model`.
- **Verification.** `test-notes-requires-auth` (planned).
- **API.** all seven.
- **Status.** The credential form is `Q-BE-012`.

### BE-NOTES-FR-020 Input is safe against injection
**Given** note title, body or tag values containing SQL or script metacharacters,
**When** any write operation is called,
**Then** every value is bound as a parameter, never concatenated into a statement, and the value is stored and returned verbatim.
- **Traces to.** NFR-012.
- **Verification.** `test-notes-injection-title`, `test-notes-injection-tag`, `test-notes-injection-path-param` (planned).
- **API.** `API-NOTES-232390`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096`.

### BE-NOTES-FR-021 A rejected request changes nothing
**Given** any request that returns a 4xx,
**When** the response is sent,
**Then** the stored notes, tags and links are exactly as before the request.
- **Traces to.** US-002-01 (no note persisted); US-002-02 (no data modified); NFR-005; `INV-BE-063`.
- **Verification.** `test-notes-rejected-requests-do-not-write` (planned).
- **API.** all seven.
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 401, 403, 415 | Create a note with mandatory title and body, optional pinned flag (BR-005, BR-018; US-002-01) |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 401, 403, 404 | Get a single note by id, including archived notes (US-002-06) |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 401, 403, 404 | Partially edit a note; fields not sent are never overwritten, includes pin/unpin via pinned (BR-006, BR-018; US-002-02) |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 401, 403, 404, 409 | Archive a note without deleting any data (BR-007; US-002-04) |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 401, 403, 404 | Add a tag to a note; adding an existing tag creates no duplicate (US-002-03) |
| `API-NOTES-150096` | DELETE | `/notes/{id}/tags/{tag}` | `notes` | 200 | 401, 403, 404 | Remove a tag from a note (US-002-03) |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 401, 403, 404 | Restore an archived note to active with all data preserved (BR-008; US-002-05) |

### `API-NOTES-232390` — POST `/notes`

Create a note with mandatory title and body, optional pinned flag (BR-005, BR-018; US-002-01)

**Request:** 
- `title` (string, required): Note title; mandatory and non-empty at creation (BR-005)
- `body` (string, required): Note body; mandatory and non-empty at creation (BR-005)
- `pinned` (boolean): Whether the note is pinned; defaults to false (BR-018)
- `tags` (array): Optional tag names to apply on creation
**Response:** 
- `id` (string, required): Note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `status` (string, required): active or archived; a new note is active
- `pinned` (boolean, required): Pinned flag
- `tags` (array, required): Tag names applied to the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-045877` — GET `/notes/{id}`

Get a single note by id, including archived notes (US-002-06)

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `status` (string, required): active or archived
- `pinned` (boolean, required): Pinned flag
- `tags` (array, required): Tag names applied to the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-847551` — PATCH `/notes/{id}`

Partially edit a note; fields not sent are never overwritten, includes pin/unpin via pinned (BR-006, BR-018; US-002-02)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `title` (string): New title; omitted means unchanged
- `body` (string): New body; omitted means unchanged
- `pinned` (boolean): Set true to pin, false to unpin; omitted means unchanged
**Response:** 
- `id` (string, required): Note identifier
- `title` (string, required): Note title
- `body` (string, required): Note body
- `status` (string, required): active or archived
- `pinned` (boolean, required): Pinned flag
- `tags` (array, required): Tag names applied to the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note without deleting any data (BR-007; US-002-04)

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Note identifier
- `status` (string, required): archived after success
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add a tag to a note; adding an existing tag creates no duplicate (US-002-03)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `tag` (string, required): Tag name to add
**Response:** 
- `id` (string, required): Note identifier
- `tags` (array, required): Resulting tag names on the note, without duplicates
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-150096` — DELETE `/notes/{id}/tags/{tag}`

Remove a tag from a note (US-002-03)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `tag` (string, required): Path parameter: tag name to remove
**Response:** 
- `id` (string, required): Note identifier
- `tags` (array, required): Resulting tag names on the note
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Restore an archived note to active with all data preserved (BR-008; US-002-05)

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Note identifier
- `status` (string, required): active after success
- `updated_at` (string, required): Last update timestamp
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DERIVED -->
## 6. Invariants

These hold after every operation of this module. `INV-BE-008` and `INV-BE-009` are defined in `backend.domain-model` and apply here unchanged; they are cited, not restated.

- `INV-BE-060`. Every stored note has a non-empty (non-whitespace) `title` and `body`. [BR-005; `ASM-BE-359`, `ASM-BE-360`]
- `INV-BE-061`. `pinned` is always `true` or `false`, never null. [BR-018; `ASM-BE-034`]
- `INV-BE-062`. `status` is always `active` or `archived`.
- `INV-BE-063`. A request that returns a 4xx modifies no stored data. [US-002-01; US-002-02]
- `INV-BE-064`. A note carries a given tag at most once. [US-002-03]
- `INV-BE-065`. After archive followed by unarchive, a note's title, body, `pinned` and tag set equal their values before the archive. Neither operation changes any of them. [BR-007; BR-008]
- `INV-BE-066`. This module never hard-deletes a note row, a tag link made by a successful add, or a tag row. The only deletion it performs is removing a tag link on `API-NOTES-150096`. [BR-007; NFR-005]
- `INV-BE-067`. `created_at` never changes and `updated_at >= created_at`.
- `INV-BE-068`. A note's id never changes and is never client-supplied. [`ASM-BE-358`]
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: DRAFT-UNVERIFIED -->
## 7. Authorization

No input defines roles beyond the actors in `backend.auth-model`. Following `ASM-BE-302`, a role has no permission an input does not grant.

| Operation | Anonymous | Authenticated team member |
|---|---|---|
| `API-NOTES-232390` create | deny, 401 | allow |
| `API-NOTES-045877` get | deny, 401 | allow |
| `API-NOTES-847551` edit | deny, 401 | allow |
| `API-NOTES-359413` add tag | deny, 401 | allow |
| `API-NOTES-150096` remove tag | deny, 401 | allow |
| `API-NOTES-810640` archive | deny, 401 | allow |
| `API-NOTES-917869` unarchive | deny, 401 | allow |

- The allow entries for the team member rest on the default of `Q-BE-001`: a shared workspace where any authenticated member may read and write any note.
- **Ownership predicate: `BLOCKED`.** No input relates a note to a user (`GAP-BE-003`). BR-031, which carries the note access rule, is corrupted in the supplied inputs. `CON-BE-003` (Critical) questions the shared-workspace default. Until `Q-BE-003` is answered, no ownership column, no access predicate and no 403 for notes is specified. What would unblock it: a stated note access rule.
- The 401 for a missing or invalid credential depends on the credential form (`Q-BE-012`) and the existing authentication (`GAP-BE-085`).
- A 403 is documented as part of the response set for authenticated endpoints [US-004-03], but no input names a case that triggers it for notes.
- Infrastructure actors (Platform/DevOps Engineer, Release/CI Pipeline Owner, Auditor, Engineering Lead) are not API roles for this module. [backend.auth-model]
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: UNKNOWN -->
## 8. Jobs

**Scheduled work: none specified.** This module defines no sweep, retry or background task.

- **Retention deletion of notes: `BLOCKED`.** NFR-019 as supplied reads "notes 1 days", which would delete every note after one day. That conflicts with BR-007, BR-008 and NFR-005 (`CON-BE-300`). Whether it is intended is `Q-BE-300`. The mechanism is `GAP-BE-101`. No note deletion is specified here until `Q-BE-300` is answered.
- **Retries.** None. Writes are single transactions; the server does not retry a failed write.
- **Idempotency.**
  - `API-NOTES-359413` is naturally idempotent: the `(note_id, tag_name)` primary key plus `INSERT ... ON CONFLICT DO NOTHING` means repeated or concurrent calls leave exactly one link.
  - `API-NOTES-150096` leaves the same end state when repeated, but a repeat returns 404 because the tag is no longer on the note (`ASM-BE-364`).
  - `API-NOTES-810640` and `API-NOTES-917869` reach the same end state when repeated, but a repeat returns a conflict (US-002-04).
  - `API-NOTES-847551` repeats with the same payload produce the same stored fields.
  - `API-NOTES-232390` is not idempotent. No idempotency key is supplied by any input, so a retried create makes a second note (`GAP-BE-568`).
- **Concurrency control.** Last-write-wins on supplied fields (`ASM-BE-365`, `Q-BE-353`). No optimistic-lock version column is added.
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

All names are proposals (planned). Test names under each requirement are listed in `backend.notes.requirements`.

**Unit.**
- Payload validation: missing, null, empty and whitespace-only `title` and `body`; non-boolean `pinned`; unknown PATCH fields; empty PATCH body (BE-NOTES-FR-003, BE-NOTES-FR-009).
- PATCH builder emits `SET` only for supplied fields (`test-notes-patch-omitted-fields-untouched`, `INV-BE-008`).
- State guard: archive allowed only from `active`, unarchive only from `archived`.
- `updated_at` rule: advanced on any change, not advanced on a duplicate tag add (`ASM-BE-366`).

**Integration** (real PostgreSQL 16, migrations applied from empty):
- Full lifecycle: create → edit → tag → archive → get → unarchive → get, asserting `INV-BE-065`.
- Constraint checks: `status` check, non-blank checks, `note_tags` primary key, `ON DELETE RESTRICT`.
- Concurrency: two simultaneous tag adds of one tag give one link; two simultaneous PATCH requests on different fields keep both fields; two simultaneous archive calls give one success and one conflict.
- Rejected requests leave the database unchanged (`INV-BE-063`).
- Authentication: every operation without a credential returns 401. The credential mechanism is `Q-BE-012`, so the test fixture is `BLOCKED` until answered.
- Search compatibility: a note created, tagged and archived here is visible to the `search` module's read path as defined there (checked in the search module's plan; not re-specified).

**Contract.**
- Each of the seven operations matches the generated OpenAPI document for its request and response shapes and its documented error statuses [BR-012; NFR-008].
- Each error response uses the envelope and codes of `backend.error-model`; 415 is never returned by this module.
- Absence check: no attachment or real-time route exists on the notes paths. The suite is owned by `scope-guardrail` [BR-014; NFR-010].
- Examples for tagging operations are present in the document [NFR-009].

**Not testable until inputs exist.** Ownership and 403 behaviour (`CON-BE-003`), retention deletion (`CON-BE-300`), and the statuses marked `GAP-BE-309` and `GAP-BE-567`.
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps, contradictions, assumptions

This module mints no `CON-`. It is affected by two open Critical contradictions and several open items defined in the project spec; they are cited here and not redefined.

### Cited from the project spec

| ID | How it affects this module |
|---|---|
| `CON-BE-003` (Critical) | ownership columns and access predicate are `BLOCKED` |
| `CON-BE-300` (Critical) | note retention deletion is `BLOCKED` |
| `Q-BE-001`, `Q-BE-003`, `Q-BE-012`, `Q-BE-300` | shared workspace default, eligibility, credential form, notes retention |
| `GAP-BE-003`, `GAP-BE-017`, `GAP-BE-035`, `GAP-BE-085`, `GAP-BE-089`, `GAP-BE-101`, `GAP-BE-300`, `GAP-BE-304`, `GAP-BE-309` | no user–note relation, no PII policy, no field rules, no existing auth, no ORM, no deletion mechanism, no envelope, no encryption statement, open statuses |
| `ASM-BE-034`, `ASM-BE-302` | `pinned` is `NOT NULL`; no permission unless granted |

### Questions

| ID | Class | Question | Default applied |
|---|---|---|---|
| `Q-BE-351` | degradable | May an archived note be edited, pinned, or have tags changed? | Yes, as for an active note (`ASM-BE-362`). It adds no state and no transition, only a guard, so it does not meet the blocking test. |
| `Q-BE-352` | degradable | What does unarchive of an already-active note return? | A conflict with `NOTE_NOT_ARCHIVED` (`ASM-BE-363`) |
| `Q-BE-353` | degradable | Is a concurrent PATCH last-write-wins or a conflict? | Last-write-wins on supplied fields (`ASM-BE-365`) |

### Gaps

| ID | What is missing | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-564` | No operation lists notes. The overview names listing as a responsibility and the module map does not assign it. | whole-notes browsing; `BE-NOTES` has no list requirement | a decision on which module owns list, and its operation record |
| `GAP-BE-565` | The error code for unarchiving an active note is not in the registry. `NOTE_NOT_ARCHIVED` is a proposed name. | BE-NOTES-FR-017 | an addition to the `backend.error-model` registry |
| `GAP-BE-566` | US-002-02 says a note's tags can be edited, but the supplied operations add and remove tags separately. Whether PATCH also accepts `tags` is not stated. | BE-NOTES-FR-006, BE-NOTES-FR-009 | a decision; default in `ASM-BE-361` is that PATCH does not touch tags |
| `GAP-BE-567` | Success status and body of tag removal are not stated (200 with the note, or 204). | BE-NOTES-FR-012 | a status decision |
| `GAP-BE-568` | No idempotency key or duplicate-create rule for `API-NOTES-232390`. | jobs; client retry behaviour | a rule from the product owner |
| `GAP-BE-569` | Rate-limit class for the seven operations is not stated. | contract completeness | a rate-limit policy |
| `GAP-BE-570` | No delete operation exists, but US-002-06 refers to "a deleted ... note". Whether any note is ever deleted is unstated. | `INV-BE-066`; get 404 wording | an answer to `Q-BE-300` and a delete decision |
| `GAP-BE-571` | No maximum number of tags per note and no maximum title or body size beyond `GAP-BE-035`. | data model limits | field-level requirements |

### Assumptions

| ID | Assumption | Reason | Confirmed by |
|---|---|---|---|
| `ASM-BE-356` | Column types: uuid keys, text strings, boolean, timestamptz. | PostgreSQL 16 is the platform and no types are supplied. | a platform decision (`GAP-BE-089`) |
| `ASM-BE-357` | A new note has `status` `active`. | US-002-04 archives an "active, existing note" and the create criteria carry no status. | product owner |
| `ASM-BE-358` | The note id is a server-assigned, opaque uuid, never client-supplied. | AC-002-01-01 says the API returns a unique id. | product owner |
| `ASM-BE-359` | A title or body of whitespace only counts as empty. | BR-005 rejects "empty/null" payloads. | product owner |
| `ASM-BE-360` | The non-empty rule applies to PATCH as well as create. | BR-005 says "at creation" only; an edit that blanks a field would lose content, against BR-006's intent. | product owner |
| `ASM-BE-361` | PATCH accepts only `title`, `body`, `pinned`. An empty payload or an unknown field is 400. Tags change only through the tag operations. | The supplied operation records list these fields; see `GAP-BE-566`. | `GAP-BE-566` |
| `ASM-BE-362` | Edit, pin and tag changes on an archived note behave as on an active note. | No input forbids them. | `Q-BE-351` |
| `ASM-BE-363` | Unarchive of an active note is a conflict with `NOTE_NOT_ARCHIVED`. | Symmetry with US-002-04. | `Q-BE-352` |
| `ASM-BE-364` | Removing a tag not on the note returns 404 `NOT_FOUND`. | No input states it; consistent with the 404 policy. | a status decision (`GAP-BE-567`) |
| `ASM-BE-365` | Concurrent PATCH requests are last-write-wins on the fields each supplies. | US-005-03 allows "a standard last-write/conflict response". | `Q-BE-353` |
| `ASM-BE-366` | `updated_at` advances on any real change to title, body, `pinned`, tags or `status`. A duplicate tag add changes nothing and does not advance it. | AC-002-02-01 requires an updated timestamp on edit; no input covers other changes. | product owner |
| `ASM-BE-367` | Foreign keys use `ON DELETE RESTRICT`. | The module never deletes notes; BR-007 and NFR-005 forbid data loss. | a platform decision |
| `ASM-BE-368` | A tag row persists after its last note is untagged. | No input states the fate of an unused tag. | product owner |
| `ASM-BE-369` | Archive, add tag and remove tag on a missing note return 404 `NOT_FOUND`. | The 404 policy covers edit, unarchive and get; these follow it. | product owner |
| `ASM-BE-370` | Tag names are compared exactly as supplied (case-sensitive, no trimming or normalisation). | `GAP-BE-035` states no rule. | `GAP-BE-035` |
| `ASM-BE-371` | `pinned` must be a JSON boolean. No coercion from strings or numbers. | BR-018 calls it boolean. | product owner |
<!-- /SECTION: backend.notes.gaps -->
