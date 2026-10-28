---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06]
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: DERIVED -->
```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-003, CON-BE-003]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-359413, API-NOTES-150096, API-NOTES-810640, API-NOTES-917869]
```
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: DRAFT-UNVERIFIED -->
## 1. Purpose

**Responsibility.** The `notes` module owns the note-taking data (NOTE, TAG, NOTE_TAG) and these operations: create, get, partial edit, add tag, remove tag, archive, unarchive. [backend.module-map; stories.md US-002-01 to US-002-06]

1. **Create** a note with a mandatory non-empty title and body; `pinned` defaults to false and may be set true on create. `API-NOTES-232390`. [BR-005, BR-018; AC-002-01-01 to AC-002-01-03]
2. **Get** one note by id with its tags, archive status and pinned flag, archived notes included. `API-NOTES-045877`. [US-002-06]
3. **Edit** a note by partial update; a field not sent is never overwritten. `API-NOTES-847551`. [BR-006, BR-018; US-002-02]
4. **Tag** a note and remove a tag. Adding an existing tag creates no duplicate. `API-NOTES-359413`, `API-NOTES-150096`. [US-002-03]
5. **Archive** and **unarchive** a note. Archiving hides the note from active views and never deletes data. Unarchive restores full prior state. `API-NOTES-810640`, `API-NOTES-917869`. [BR-007, BR-008; US-002-04, US-002-05]

**Not responsible for.**

- Search, list ordering and pagination, including the pinned-first order (`INV-BE-001`). These belong to `search`. [backend.module-map]
- Sign-in, codes, locks and the credential check itself. These belong to `auth`. `notes` only depends on `auth` to validate the caller on protected routes. [backend.module-map]
- The OpenAPI document and its coverage (`api-docs`), and rejection of attachment, real-time and mobile calls (`scope-guardrail`).
- The global error envelope and registry, owned by `backend.error-model`; this module uses it.
- Note ownership columns and the note access rule. These are `BLOCKED` on `Q-BE-003` and `CON-BE-003`.
- Retention deletion of notes. NFR-019 states notes are not deleted by retention. [NFR-019]
- Any user interface (the web-app surface).

**Operations owned.** The seven ids in the frontmatter `scope`, referenced by id only.

**Identifier note.** This module mints from its reserved block: `ASM-BE-451` onward, `GAP-BE-667` onward, `INV-BE-060` onward, `Q-BE-351` onward. It mints no `CON-`; it cites `CON-BE-003`. All other ids are cited from the project spec.
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

PostgreSQL 16 is the platform. [platform.md] No input states column types, nullability or constraints, so those below are a proposal (`ASM-BE-451`). Entity and column names follow `backend.domain-model`. No ORM or migration tool is named (`GAP-BE-089`). The tables are created by migration M001. [backend.migration-plan]

**notes**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | none | primary key; server-assigned (`ASM-BE-453`) |
| `title` | text | NOT NULL | none | `CHECK (btrim(title) <> '')` (`ASM-BE-454`); maximum length `GAP-BE-035` |
| `body` | text | NOT NULL | none | `CHECK (btrim(body) <> '')` (`ASM-BE-454`); maximum length `GAP-BE-035` |
| `status` | text | NOT NULL | `'active'` | `CHECK (status IN ('active','archived'))` (`ASM-BE-452`) |
| `pinned` | boolean | NOT NULL | `false` | `ASM-BE-034`; [BR-018] |
| `created_at` | timestamptz | NOT NULL | `now()` | |
| `updated_at` | timestamptz | NOT NULL | `now()` | set on every change to the note row or its tags (`ASM-BE-455`) |

No ownership column is defined. It is `BLOCKED` on `Q-BE-003` and `CON-BE-003` (migration M005). [backend.domain-model]

**tags**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `name` | text | NOT NULL | none | primary key; format and case rule `GAP-BE-035` |

**note_tags**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `note_id` | uuid | NOT NULL | none | foreign key to `notes(id)` |
| `tag_name` | text | NOT NULL | none | foreign key to `tags(name)` |
| (pair) | | | | primary key `(note_id, tag_name)`; this is what prevents a duplicate tag entry (`INV-BE-061`) |

**Referential integrity.**

- `note_tags.note_id` references `notes(id)` with `ON DELETE RESTRICT`. Notes are never hard-deleted, so no cascade is needed. [BR-007; NFR-019]
- `note_tags.tag_name` references `tags(name)` with `ON DELETE RESTRICT`. Whether an unused tag is removed is `GAP-BE-035`.

**Indexes.**

- Primary key on `notes(id)` serves get, patch, tag and archive operations.
- btree on `note_tags(tag_name)` serves the tag-filtered lookup that `search` performs (read-only). [BR-010; US-003-02]
- Indexes for keyword search are owned by `search` (migration M003). [backend.migration-plan]

**Time.** Every instant is `timestamptz`.

**Encryption and PII.** No input states encryption at rest (`GAP-BE-304`), so none is claimed. Note content may contain personal data; no PII handling policy exists (`GAP-BE-017`).
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

Entity: `notes`, column `status`. States: `active`, `archived`. Neither is terminal: the lifecycle is reversible, and no hard-delete state exists. [BR-007, BR-008; NFR-019] There is no `deleted` state.

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `active` | `API-NOTES-232390` | title and body non-empty | row created with `pinned` as supplied or `false` (`ASM-BE-452`) |
| `active` | `archived` | `API-NOTES-810640` | note exists | `status` set; all data intact; `updated_at` set |
| `archived` | `active` | `API-NOTES-917869` | note exists | `status` set; all prior data preserved |
| `active` | `active` | `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096` | note exists; valid input | edit, pin or tag change |
| `archived` | `archived` | `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096` | note exists; valid input | same as for an active note (`ASM-BE-456`) |

**Illegal transitions.**

| Attempt | Result | Status and code |
|---|---|---|
| archive an already-archived note | refused, no data modified | conflict/error; status not stated (`GAP-BE-309`); proposed code `NOTE_ALREADY_ARCHIVED`. [US-002-04] |
| unarchive an already-active note | refused | not stated (`GAP-BE-667`) |
| any operation on a non-existent note id | refused, no data modified | 404 `NOTE_NOT_FOUND`. [US-002-02, US-002-05, US-002-06] |

**Badge derivation.** The archived/active indicator a consumer shows is a function of `status` alone. Nothing else feeds it. [US-006-01, US-002-06]
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-NOTES-FR-001 Create a note
**Given** a payload with a non-empty `title` and `body`,
**When** `API-NOTES-232390` is called,
**Then** a note is stored with a unique id, `created_at` and `updated_at`, `status` `active` and `pinned` `false`, and 201 is returned with the created note.
- **Traces to.** US-002-01; BR-005, BR-018; AC-002-01-01.
- **Verification.** `test-notes-create-defaults` (planned).
- **API.** `API-NOTES-232390`.
- **Status.** Field limits `GAP-BE-035`. Access check `BLOCKED` on `Q-BE-003`.

### BE-NOTES-FR-002 Create a pinned note
**Given** a valid payload with `pinned` true,
**When** `API-NOTES-232390` is called,
**Then** 201 is returned and the stored note has `pinned` true.
- **Traces to.** US-002-01; BR-018; AC-002-01-02.
- **Verification.** `test-notes-create-pinned` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-003 Reject an invalid create
**Given** a payload missing `title`, or with an empty or null `title` or `body`,
**When** `API-NOTES-232390` is called,
**Then** 400 `VALIDATION_FAILED` with a validation error is returned and no note is persisted.
- **Traces to.** US-002-01; BR-005; AC-002-01-03; NFR-012.
- **Verification.** `test-notes-create-missing-title`, `test-notes-create-empty-body`, `test-notes-create-null-fields` (planned).
- **API.** `API-NOTES-232390`.
- **Status.** Whitespace-only counts as empty (`ASM-BE-454`).

### BE-NOTES-FR-004 Get a note
**Given** an existing note id,
**When** `API-NOTES-045877` is called,
**Then** 200 is returned with the full note, including its tags, `status` and `pinned`. An archived note is returned too.
- **Traces to.** US-002-06; NFR-005.
- **Verification.** `test-notes-get-active`, `test-notes-get-archived` (planned).
- **API.** `API-NOTES-045877`.

### BE-NOTES-FR-005 Get a missing note
**Given** an id for a note that does not exist or never existed,
**When** `API-NOTES-045877` is called,
**Then** 404 `NOTE_NOT_FOUND` is returned with no partial data.
- **Traces to.** US-002-06.
- **Verification.** `test-notes-get-missing` (planned).
- **API.** `API-NOTES-045877`.
- **Status.** A malformed (non-uuid) id returns 400 or 404; not stated (`GAP-BE-668`).

### BE-NOTES-FR-006 Partial edit
**Given** an existing note and valid updated fields,
**When** `API-NOTES-847551` is called,
**Then** 200 is returned with the updated note and a new `updated_at`, and every field not sent is unchanged.
- **Traces to.** US-002-02; BR-006; AC-002-02-01.
- **Verification.** `test-notes-patch-partial`, `test-notes-patch-preserves-unsent` (planned).
- **API.** `API-NOTES-847551`.
- **Status.** Accepted fields `ASM-BE-457`. PUT is not offered (`GAP-BE-309`).

### BE-NOTES-FR-007 Pin and unpin through PATCH
**Given** a PATCH containing only `pinned`,
**When** `API-NOTES-847551` is called,
**Then** 200 is returned, `pinned` is updated, and title, body and tags are unchanged.
- **Traces to.** US-002-02; BR-018; AC-002-02-02.
- **Verification.** `test-notes-patch-pinned-only` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-008 Edit a missing note
**Given** a non-existent note id,
**When** `API-NOTES-847551` is called,
**Then** 404 `NOTE_NOT_FOUND` is returned and no data is modified.
- **Traces to.** US-002-02; AC-002-02-03.
- **Verification.** `test-notes-patch-missing` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-009 Reject an invalid edit
**Given** a PATCH that sets `title` or `body` to empty or null, sends an empty payload, or contains an unknown field,
**When** `API-NOTES-847551` is called,
**Then** 400 `VALIDATION_FAILED` is returned and the note is unchanged.
- **Traces to.** BR-005 and BR-006 by derivation (`ASM-BE-457`); NFR-012.
- **Verification.** `test-notes-patch-empty-title`, `test-notes-patch-unknown-field` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-010 Add a tag
**Given** an existing note and a tag name,
**When** `API-NOTES-359413` is called,
**Then** 200 is returned and the note's tag list includes the tag.
- **Traces to.** US-002-03.
- **Verification.** `test-notes-tag-add` (planned).
- **API.** `API-NOTES-359413`.
- **Status.** Tag format `GAP-BE-035`. Request body shape is in the operation record.

### BE-NOTES-FR-011 Add a duplicate tag
**Given** a tag already on the note,
**When** `API-NOTES-359413` is called with the same tag,
**Then** 200 is returned and no duplicate entry is created (`INV-BE-061`).
- **Traces to.** US-002-03.
- **Verification.** `test-notes-tag-add-duplicate`, `test-notes-tag-add-concurrent` (planned).
- **API.** `API-NOTES-359413`.

### BE-NOTES-FR-012 Remove a tag
**Given** an existing note carrying the tag,
**When** `API-NOTES-150096` is called,
**Then** the tag is removed from the note and the note's other tags are unchanged.
- **Traces to.** US-002-03 (add or remove); US-006-04 consumer view.
- **Verification.** `test-notes-tag-remove` (planned).
- **API.** `API-NOTES-150096`.
- **Status.** Success status, and the result when the note lacks the tag, are `GAP-BE-669`.

### BE-NOTES-FR-013 Archive a note
**Given** an active, existing note,
**When** `API-NOTES-810640` is called,
**Then** 200 is returned, `status` becomes `archived`, and all note data remains intact.
- **Traces to.** US-002-04; BR-007.
- **Verification.** `test-notes-archive`, `test-notes-archive-preserves-data` (planned).
- **API.** `API-NOTES-810640`.

### BE-NOTES-FR-014 Archive an archived note
**Given** an already-archived note,
**When** `API-NOTES-810640` is called,
**Then** a conflict/error response indicating it is already archived is returned and nothing is modified.
- **Traces to.** US-002-04.
- **Verification.** `test-notes-archive-twice` (planned).
- **API.** `API-NOTES-810640`.
- **Status.** Status code `GAP-BE-309`.

### BE-NOTES-FR-015 Unarchive a note
**Given** an archived note,
**When** `API-NOTES-917869` is called,
**Then** 200 is returned, `status` becomes `active`, and all original data is preserved.
- **Traces to.** US-002-05; BR-008.
- **Verification.** `test-notes-unarchive`, `test-notes-archive-roundtrip` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-016 Unarchive a missing note
**Given** a note id that does not exist,
**When** `API-NOTES-917869` is called,
**Then** 404 `NOTE_NOT_FOUND` is returned.
- **Traces to.** US-002-05.
- **Verification.** `test-notes-unarchive-missing` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-017 Archive and tag operations on a missing note
**Given** a non-existent note id,
**When** `API-NOTES-810640`, `API-NOTES-359413` or `API-NOTES-150096` is called,
**Then** 404 `NOTE_NOT_FOUND` is returned and nothing is modified.
- **Traces to.** US-002-02 and US-002-05 (the stated 404 pattern); `ASM-BE-458`.
- **Verification.** `test-notes-archive-missing`, `test-notes-tag-missing` (planned).
- **API.** the three named ids.

### BE-NOTES-FR-018 Injection safety
**Given** any title, body or tag value,
**When** it is stored or read,
**Then** it is validated and handled so that it cannot alter a query or be executed.
- **Traces to.** NFR-012.
- **Verification.** `test-notes-injection-strings` (planned), plus the security scan per release.
- **API.** all seven ids.

### BE-NOTES-FR-019 Concurrent edits
**Given** two PATCH requests to the same note processed at about the same time,
**When** both are applied,
**Then** no live broadcast or merge logic runs, and a standard last-write or conflict response results.
- **Traces to.** US-005-03.
- **Verification.** `test-notes-patch-concurrent` (planned).
- **API.** `API-NOTES-847551`.
- **Status.** Which outcome is `GAP-BE-309`; default `ASM-BE-459`.
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 401, 403 | Create a note with a mandatory non-empty title and body, optionally pinned (US-002-01, BR-005, BR-018) |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 401, 403, 404 | Get a single note by id, including archived notes (US-002-06) |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 401, 403, 404 | Partially edit a note; fields not sent are never overwritten (US-002-02, BR-006, BR-018) |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 401, 403, 404, 409 | Archive a note without deleting data (US-002-04, BR-007) |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 401, 403, 404 | Add a tag to a note; an existing tag creates no duplicate (US-002-03) |
| `API-NOTES-150096` | DELETE | `/notes/{id}/tags/{name}` | `notes` | 200 | 401, 403, 404 | Remove a tag from a note (US-002-03) |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 401, 403, 404 | Restore an archived note to active with all data preserved (US-002-05, BR-008) |

### `API-NOTES-232390` — POST `/notes`

Create a note with a mandatory non-empty title and body, optionally pinned (US-002-01, BR-005, BR-018)

**Request:** 
- `title` (string, required): Note title; mandatory and non-empty at creation (BR-005)
- `body` (string, required): Note body; mandatory and non-empty at creation (BR-005)
- `pinned` (boolean): Whether the note is pinned; defaults to false (BR-018)
**Response:** 
- `id` (string, required): Identifier of the created note
- `title` (string, required): Note title
- `body` (string, required): Note body
- `status` (string, required): Note status, active or archived; a new note is active
- `pinned` (boolean, required): Whether the note is pinned
- `tags` (array, required): Tags applied to the note
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
- `pinned` (boolean, required): Whether the note is pinned
- `tags` (array, required): Tags applied to the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-847551` — PATCH `/notes/{id}`

Partially edit a note; fields not sent are never overwritten (US-002-02, BR-006, BR-018)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `title` (string): New title; omitted fields stay unchanged
- `body` (string): New body; omitted fields stay unchanged
- `pinned` (boolean): Pin or unpin the note (BR-018)
**Response:** 
- `id` (string, required): Note identifier
- `title` (string, required): Note title after edit
- `body` (string, required): Note body after edit
- `status` (string, required): active or archived
- `pinned` (boolean, required): Whether the note is pinned
- `tags` (array, required): Tags applied to the note
- `created_at` (string, required): Creation timestamp
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note without deleting data (US-002-04, BR-007)

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Note identifier
- `status` (string, required): archived after success
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add a tag to a note; an existing tag creates no duplicate (US-002-03)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `name` (string, required): Tag name to apply; format rules are unspecified
**Response:** 
- `id` (string, required): Note identifier
- `tags` (array, required): Tags now applied to the note, without duplicates
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-150096` — DELETE `/notes/{id}/tags/{name}`

Remove a tag from a note (US-002-03)

**Request:** 
- `id` (string, required): Path parameter: note identifier
- `name` (string, required): Path parameter: tag name to remove
**Response:** 
- `id` (string, required): Note identifier
- `tags` (array, required): Tags remaining on the note
- `updated_at` (string, required): Last update timestamp

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Restore an archived note to active with all data preserved (US-002-05, BR-008)

**Request:** 
- `id` (string, required): Path parameter: note identifier
**Response:** 
- `id` (string, required): Note identifier
- `status` (string, required): active after success
- `updated_at` (string, required): Last update timestamp
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

The project invariants `INV-BE-001` (pinned-first order, enforced by `search`), `INV-BE-008` and `INV-BE-009` apply here and are defined in `backend.domain-model`. This module adds:

- `INV-BE-060`. Every stored note has a non-empty `title` and `body`, and `status` is `active` or `archived`. [BR-005]
- `INV-BE-061`. A note carries a given tag at most once. [US-002-03]
- `INV-BE-062`. No operation of this module deletes a note row. Archive sets `status` and nothing else is removed. [BR-007; NFR-019]
- `INV-BE-063`. After archive then unarchive, title, body, tags, `pinned` and `created_at` equal their values before the archive. [BR-008]
- `INV-BE-064`. A failed or refused operation (validation, 404, conflict) modifies no data. [AC-002-01-03, AC-002-02-03]
- `INV-BE-065`. `updated_at` is never earlier than `created_at`, and changes whenever a field or tag of the note changes (`ASM-BE-455`). [AC-002-02-01]
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: BLOCKED -->
## 7. Authorisation

**Blocked.** The role-by-operation matrix and the ownership predicate cannot be written.

- **Roles.** No input defines API caller roles; the existing authentication is unnamed (`GAP-BE-085`). `backend.auth-model` is `BLOCKED` for the same reason.
- **Ownership.** No input relates a note to a user (`GAP-BE-003`). BR-031, which sets the note access rule, is corrupted. The default is a shared workspace (`Q-BE-001`), which `CON-BE-003` (Critical) calls into question.
- **Credential.** What the caller presents is `Q-BE-012`.
- **Default.** Until an input grants a permission, a role has none (`ASM-BE-302`). Authentication and authorisation failures return 401 and 403, documented per endpoint. [US-004-03]

**Unblocked by:** answers to `Q-BE-003` and `Q-BE-012`, the resolution of `CON-BE-003`, and a statement of the existing authentication method and its roles. Note ownership columns follow in migration M005.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: UNKNOWN -->
## 8. Jobs

**Scheduled work.** None. Notes are not deleted by retention (NFR-019), and archiving is an explicit user action, not a sweep. [BR-007]

**Retries.** None specified. A database error returns a server error; the response is not an input-defined status (`GAP-BE-670`).

**Idempotency.** No input states idempotency keys.

| Operation | Behaviour |
|---|---|
| `API-NOTES-232390` | not idempotent; each call creates a note. De-duplication by key is not stated (`GAP-BE-671`). |
| `API-NOTES-847551` | the same body applied twice gives the same stored state; `updated_at` changes |
| `API-NOTES-359413` | idempotent: a repeat returns 200 and creates no duplicate. [US-002-03] |
| `API-NOTES-150096` | repeat behaviour `GAP-BE-669` |
| `API-NOTES-810640` | a repeat is refused as already archived. [US-002-04] |
| `API-NOTES-917869` | repeat behaviour `GAP-BE-667` |
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

**Unit.**

- Title and body validation: missing, empty, null, whitespace-only (`ASM-BE-454`).
- Partial-update merge keeps every unsent field (`INV-BE-008`).
- Status transitions: allowed and refused combinations in `backend.notes.state-machine`.
- Tag de-duplication.

**Integration** (PostgreSQL 16, as the platform states; no test framework is named, `GAP-BE-089`).

- Create, get, patch, tag, archive, unarchive against a real database, covering each status and error stated in `backend.notes.requirements`.
- The roundtrip archive then unarchive preserves all data (`INV-BE-063`).
- Concurrent duplicate tag add yields one `note_tags` row.
- A refused operation leaves the row unchanged (`INV-BE-064`).
- CRUD functional-test pass rate of 100% with zero data loss. [NFR-005]
- Injection strings in title, body and tag. [NFR-012]

**Contract.**

- Every operation of this module appears in the published OpenAPI document with request and response schemas and 4xx errors. [BR-012, NFR-008]
- Tagging operations carry example payloads. [NFR-009]
- Responses match the error envelope and the registry in `backend.error-model`.

Authorisation tests are not planned until `backend.notes.authorization` is unblocked.
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: DRAFT-UNVERIFIED -->
## 10. Open items

**Project items that bind this module** (defined in `backend.overview`, referenced here, not restated): `Q-BE-001`, `Q-BE-003`, `Q-BE-012`, `CON-BE-003`, `GAP-BE-003`, `GAP-BE-017`, `GAP-BE-035`, `GAP-BE-085`, `GAP-BE-089`, `GAP-BE-300`, `GAP-BE-304`, `GAP-BE-309`, `GAP-BE-310`, `ASM-BE-034`, `ASM-BE-300`, `ASM-BE-301`, `ASM-BE-302`.

**Gaps raised by this module**

| ID | Gap | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-667` | Result of unarchiving an already-active note (status and code). | state-machine, FR for unarchive | a decision from the product owner |
| `GAP-BE-668` | Response for a malformed (non-uuid) note id. | FR-005 and every `{id}` operation | a validation rule |
| `GAP-BE-669` | Success status of tag removal, and the result when the note lacks the tag. | FR-012, jobs | a decision from the product owner |
| `GAP-BE-670` | Status and body for an unexpected server error. | jobs | an error-handling policy |
| `GAP-BE-671` | Whether create supports a client idempotency key. | jobs | a decision from the product owner |

**Assumptions raised by this module**

| ID | Assumption | Reasoning and what would confirm it |
|---|---|---|
| `ASM-BE-451` | Column types: uuid keys, text strings, boolean, timestamptz. | PostgreSQL 16 is the platform and no types are supplied. Confirm by a platform decision. |
| `ASM-BE-452` | A new note has `status` `active`. | US-002-04 archives an "active, existing note"; the create criteria carry no status. Confirm by the product owner. |
| `ASM-BE-453` | The note id is a server-assigned opaque uuid, never client-supplied. | AC-002-01-01 says the API returns a unique ID. Confirm by the product owner. |
| `ASM-BE-454` | A title or body of whitespace only counts as empty. | BR-005 rejects "empty/null" payloads. Confirm by the product owner. |
| `ASM-BE-455` | `updated_at` changes on any edit, pin change or tag change. | AC-002-02-01 returns "an updated timestamp"; tag changes are an edit of the note per US-002-02. Confirm by the product owner. |
| `ASM-BE-456` | Edit, pin and tag changes on an archived note behave as on an active note. | No input forbids them. Confirm by the product owner. |
| `ASM-BE-457` | PATCH accepts only `title`, `body` and `pinned`. An empty payload, an unknown field, or an empty or null title or body is 400. Tags change only through the tag operations. | The supplied operation records list those fields; BR-005's non-empty rule is extended to edits to avoid content loss, in the spirit of BR-006. Confirm by the product owner. |
| `ASM-BE-458` | Archive and tag operations return 404 for a missing note, like the stated cases. | US-002-02, US-002-05 and US-002-06 state 404 for the other operations. Confirm by the product owner. |
| `ASM-BE-459` | A concurrent PATCH is last-write-wins on the supplied fields. | US-005-03 allows "last-write/conflict". Last-write is the narrower, non-blocking reading. Confirm by the product owner (`GAP-BE-309`). |
<!-- /SECTION: backend.notes.gaps -->
