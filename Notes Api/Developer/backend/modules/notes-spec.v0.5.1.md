---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06, notes_api:US-006-01, notes_api:US-006-02, notes_api:US-006-04, notes_api:US-006-05]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-002, Q-BE-003]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-810640, API-NOTES-917869, API-NOTES-359413, API-NOTES-150096]
```

`blocked_by` lists the two project-level blocking questions that stop `backend.notes.authorization` and the ownership columns (project spec `backend.auth-model`, `backend.migration-plan` row 005). All other sections are written.
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: DERIVED -->
## Purpose

**Responsible for** (project spec `backend.module-map`; EP-002, BR-005 to BR-008, BR-018):

- Creating, reading, partially editing, archiving and unarchiving notes. A note has a title, a body, a `pinned` flag, an active/archived state and timestamps.
- Adding and removing tags on a note, with no duplicate tag on one note (US-002-03).
- Providing the persisted note and tag data that the `search` module reads.
- Preserving all note data through archive and unarchive (BR-007, BR-008).

**Not responsible for:**

- Search, keyword or tag filtering, pagination, pinned-first ordering of results, and exclusion of archived notes from results. All are owned by `search` (BR-009 to BR-011, BR-019).
- Login, codes, locks and credentials (`auth`).
- Refusing attachment, multipart, real-time and mobile calls, including the 415 `OUT_OF_SCOPE` response for file payloads (`scope-guard`; BR-014 to BR-016). This module only records that its endpoints sit behind that middleware.
- Publishing the OpenAPI document and the coverage check (`contract-docs`).
- Retention sweeps. No sweep deletes a note (`INV-BE-001`, NFR-019).
- Listing notes. No operation for it is assigned to this module and no story defines one (`GAP-BE-005`, `GAP-BE-006`). US-006-01 and US-006-05 depend on this.
- Any UI (US-006-01, US-006-02, US-006-04 and US-006-05 describe web_app behaviour; the web_app is a separate surface).
- Ownership of notes and the per-request access check. Both are undecided (`Q-BE-002`).

**Inbound handoffs.** The four inbound handoff files were not supplied, so they could not be read or answered here (`GAP-BE-001`).
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Storage is PostgreSQL 16 in an approved EU region (`platform.md`, BR-001). The data-lifecycle rules are in project spec `backend.data-lifecycle`.

### Table `notes`

| Column | Type | Nullable | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid (`ASM-BE-013`) | NOT NULL | server-generated | PRIMARY KEY; unique ID per US-002-01 |
| `title` | text | NOT NULL | none | must not be the empty string (BR-005); maximum length unstated (`GAP-BE-008`) |
| `body` | text | NOT NULL | none | must not be the empty string (BR-005, `ASM-BE-010` of the project spec); maximum size unstated (`GAP-BE-008`) |
| `pinned` | boolean | NOT NULL | `false` | BR-018 |
| `status` | text (`ASM-BE-014`) | NOT NULL | `'active'` | CHECK `status IN ('active','archived')`; US-002-04, US-002-05, BR-007 |
| `created_at` | timestamptz (`ASM-BE-015`) | NOT NULL | current time | immutable (`INV-BE-009`) |
| `updated_at` | timestamptz (`ASM-BE-015`) | NOT NULL | current time | set on every change (`ASM-BE-015`) |

Ownership columns (owner or workspace) are **not defined**. They wait on `Q-BE-002` (BR-031) and are migration 005 in the project plan.

### Table `note_tags`

| Column | Type | Nullable | Default | Constraint |
|---|---|---|---|---|
| `note_id` | uuid | NOT NULL | none | FOREIGN KEY → `notes.id`, `ON DELETE RESTRICT` (`ASM-BE-016`) |
| `tag_name` | text | NOT NULL | none | tag format and charset unstated (`GAP-BE-008`); compared exactly as supplied (`ASM-BE-017`) |

- Table-level constraint: PRIMARY KEY (`note_id`, `tag_name`). This enforces no duplicate tag on a note (US-002-03).

### Indexes

| Index | Serves |
|---|---|
| `notes` primary key on `id` | get, patch, archive and unarchive by id |
| `note_tags` primary key on (`note_id`, `tag_name`) | duplicate prevention on add, remove by name, and loading a note's tags |
| `note_tags` index on `tag_name` | tag-filter lookups used by `search` (US-003-02) |

Search indexes over title, body and tags are `search`'s concern (migration 003 in the project plan).

### Handling notes

- Timestamps are stored in UTC, as `timestamptz` (`ASM-BE-015`).
- Whether note content counts as PII, and its at-rest treatment, are unstated (`GAP-BE-010`). Nothing is asserted about encryption.
- There is no money column.
- There is no hard-delete path (`INV-BE-010`, BR-007).
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

The entity is `notes`, with column `status`.

**States:** `active` (initial; set on create) and `archived`.

| From | To | Trigger | Guard | Result |
|---|---|---|---|---|
| (none) | `active` | create → `API-NOTES-232390` | valid payload | 201, `status = active` (US-002-01) |
| `active` | `archived` | `API-NOTES-810640` | note exists | 200, data intact (US-002-04) |
| `archived` | `active` | `API-NOTES-917869` | note exists | 200, all original data preserved (US-002-05, BR-008) |

**Illegal transitions**

| Attempt | Status | Code | Source |
|---|---|---|---|
| Archive an already-archived note | 409 | `NOTE_ALREADY_ARCHIVED` | US-002-04, `ASM-BE-004` (project spec) |
| Unarchive an already-active note | 409 | `NOTE_NOT_ARCHIVED` (proposed code) | `ASM-BE-018`, `GAP-BE-029` |
| Archive or unarchive a non-existent note | 404 | `NOTE_NOT_FOUND` | US-002-05 (unarchive); archive by analogy (`ASM-BE-018`) |

**Terminal states: none.** Archive is always reversible (BR-008). No state deletes data (BR-007).

**Orthogonal attributes.** `pinned`, title, body and tags can change in either state. No input forbids it (`ASM-BE-019`, `GAP-BE-028`). The state itself changes only through the two transition operations. `API-NOTES-847551` cannot change `status`.

**Derivation.** The archived/active indicator shown in the web_app (US-006-01) is a function of `status` alone. No other field is involved.
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Error codes and the envelope are defined in project spec `backend.error-model`.

**BE-NOTES-FR-001 — Create with defaults**
- Given a payload with a valid title and body and no `pinned` value,
- When a client calls `API-NOTES-232390`,
- Then the response is 201 with the created note: a unique ID, `created_at`, `updated_at`, `pinned = false` and `status = active`.
- **Traces to.** US-002-01, AC-002-01-01, BR-005, BR-018, US-006-02.
- **Verification.** `notes.create.defaults` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-002 — Create pinned**
- Given a valid payload with `pinned = true`,
- When a client calls `API-NOTES-232390`,
- Then the response is 201 and the stored note has `pinned = true`.
- **Traces to.** US-002-01, AC-002-01-02, BR-018.
- **Verification.** `notes.create.pinned_true` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-003 — Create validation**
- Given a payload whose title is missing or empty (or whose body is missing or empty, `ASM-BE-010` of the project spec),
- When a client calls `API-NOTES-232390`,
- Then the response is 400 `VALIDATION_ERROR` and no note and no tag is persisted.
- **Traces to.** US-002-01, AC-002-01-03, BR-005, US-006-02.
- **Verification.** `notes.create.rejects_missing_title`, `notes.create.rejects_missing_body` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-004 — Create with tags**
- Given a valid payload that includes tags,
- When a client calls `API-NOTES-232390`,
- Then the note and its tags are stored in one atomic operation, with duplicate tag names within the request stored once (`ASM-BE-023`). If any tag is invalid, the response is 400 and nothing is persisted.
- **Traces to.** US-002-01, US-002-03, US-006-04. The operation record states "optional tags".
- **Verification.** `notes.create.with_tags_atomic` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-005 — Get a note**
- Given an existing note ID,
- When a client calls `API-NOTES-045877`,
- Then the response is 200 with the full note: title, body, tags, `status` and `pinned`. Archived notes are returned too.
- **Traces to.** US-002-06, AC-002-06-01 (per the story text), US-006-01 (title and archive indicator).
- **Verification.** `notes.get.returns_full_note`, `notes.get.returns_archived_note` (planned).
- **API.** `API-NOTES-045877`.

**BE-NOTES-FR-006 — Get a missing note**
- Given an ID for a note that never existed,
- When a client calls `API-NOTES-045877`,
- Then the response is 404 `NOTE_NOT_FOUND` and the body contains no note data.
- **Traces to.** US-002-06.
- **Verification.** `notes.get.404_no_partial_data` (planned).
- **API.** `API-NOTES-045877`.

**BE-NOTES-FR-007 — Partial edit**
- Given an existing note and valid values for any of title, body or `pinned`,
- When a client calls `API-NOTES-847551`,
- Then the response is 200 with the updated note and a refreshed `updated_at`. Fields not supplied are unchanged.
- **Traces to.** US-002-02, AC-002-02-01, BR-006, US-006-02.
- **Verification.** `notes.patch.updates_supplied_fields_only` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-008 — Pin and unpin**
- Given a PATCH containing only `pinned`,
- When it is submitted to `API-NOTES-847551`,
- Then the response is 200, `pinned` is updated, and title, body and tags are unchanged.
- **Traces to.** US-002-02, AC-002-02-02, BR-018.
- **Verification.** `notes.patch.pinned_only_preserves_others` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-009 — Edit a missing note**
- Given a non-existent note ID,
- When a client calls `API-NOTES-847551`,
- Then the response is 404 `NOTE_NOT_FOUND` and no data is modified.
- **Traces to.** US-002-02, AC-002-02-03.
- **Verification.** `notes.patch.404_no_change` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-010 — Edit validation**
- Given a PATCH that sets title or body to the empty string, contains an unknown field (including `tags`), or has an empty body,
- When it is submitted to `API-NOTES-847551`,
- Then the response is 400 `VALIDATION_ERROR` and no data is modified. This is an assumption (`ASM-BE-020`, `GAP-BE-030`, `CON-BE-003`).
- **Traces to.** BR-005, BR-006, US-006-02 (the editor shows a validation error for an empty title).
- **Verification.** `notes.patch.rejects_empty_title`, `notes.patch.rejects_unknown_fields` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-011 — Add a tag**
- Given an existing note and a tag name,
- When a client calls `API-NOTES-359413`,
- Then the response is 200 and the note's tag list includes the tag. If the tag is already on the note, the response is still 200 and no duplicate entry is created.
- **Traces to.** US-002-03, US-006-04.
- **Verification.** `notes.tags.add_new`, `notes.tags.add_duplicate_is_noop` (planned).
- **API.** `API-NOTES-359413`.

**BE-NOTES-FR-012 — Add a tag to a missing note, or with an invalid name**
- Given a non-existent note ID, or a tag name that fails validation,
- When a client calls `API-NOTES-359413`,
- Then the response is 404 `NOTE_NOT_FOUND` or 400 `VALIDATION_ERROR` respectively, and nothing is persisted. The statuses are by analogy with US-002-02 (`ASM-BE-021`).
- **Traces to.** US-002-03. The 404 and 400 statuses are extrapolated from AC-002-02-03 and AC-002-01-03.
- **Verification.** `notes.tags.add_404`, `notes.tags.add_invalid_name_400` (planned).
- **API.** `API-NOTES-359413`.

**BE-NOTES-FR-013 — Remove a tag**
- Given an existing note,
- When a client calls `API-NOTES-150096` for a tag on the note,
- Then the response is 200 and the tag no longer appears on the note. Removing a tag the note does not carry is also 200 (idempotent), and a missing note is 404 `NOTE_NOT_FOUND`. These outcomes are assumptions (`ASM-BE-021`, `GAP-BE-031`).
- **Traces to.** US-006-04, US-002-02 (editing tags). There are no acceptance criteria for removal (`GAP-BE-007`).
- **Verification.** `notes.tags.remove_present`, `notes.tags.remove_absent_is_noop`, `notes.tags.remove_404` (planned).
- **API.** `API-NOTES-150096`.

**BE-NOTES-FR-014 — Archive**
- Given an active, existing note,
- When a client calls `API-NOTES-810640`,
- Then the response is 200, `status = archived`, and title, body, `pinned` and tags are unchanged.
- **Traces to.** US-002-04, US-006-05, BR-007.
- **Verification.** `notes.archive.preserves_data` (planned).
- **API.** `API-NOTES-810640`.

**BE-NOTES-FR-015 — Archive an archived note**
- Given an already-archived note,
- When a client calls `API-NOTES-810640`,
- Then the response is 409 `NOTE_ALREADY_ARCHIVED` and no data changes. A missing note gives 404 (`ASM-BE-018`).
- **Traces to.** US-002-04.
- **Verification.** `notes.archive.conflict_when_archived`, `notes.archive.404` (planned).
- **API.** `API-NOTES-810640`.

**BE-NOTES-FR-016 — Unarchive**
- Given an archived note,
- When a client calls `API-NOTES-917869`,
- Then the response is 200, `status = active`, and all original data is preserved.
- **Traces to.** US-002-05, US-006-05, BR-008.
- **Verification.** `notes.unarchive.roundtrip_identical` (planned).
- **API.** `API-NOTES-917869`.

**BE-NOTES-FR-017 — Unarchive a missing or active note**
- Given a non-existent note ID, or a note that is already active,
- When a client calls `API-NOTES-917869`,
- Then a missing note gives 404 `NOTE_NOT_FOUND` (US-002-05). An active note gives 409 `NOTE_NOT_ARCHIVED` (`ASM-BE-018`, `GAP-BE-029`).
- **Traces to.** US-002-05.
- **Verification.** `notes.unarchive.404`, `notes.unarchive.conflict_when_active` (planned).
- **API.** `API-NOTES-917869`.

**BE-NOTES-FR-018 — Note representation for the web UI**
- Given any operation that returns a note,
- When the web_app displays it,
- Then the response carries the title and the archive state (`status`), which are the fields the list and editor show. The representation's field shapes are defined once, in the contract.
- **Traces to.** US-006-01, US-006-02, NFR-014.
- **Verification.** `notes.contract.note_representation_has_title_and_status` (planned).
- **API.** `API-NOTES-045877`.

### Story coverage within this module

| Story | Requirements | Status |
|---|---|---|
| US-002-01 | FR-001 to FR-004 | Covered |
| US-002-02 | FR-007 to FR-010 | Covered. Tags in the edit story are served by the tag endpoints (`CON-BE-003`). |
| US-002-03 | FR-011, FR-012 | Covered for add. Removal is FR-013. |
| US-002-04 | FR-014, FR-015 | Covered |
| US-002-05 | FR-016, FR-017 | Covered |
| US-002-06 | FR-005, FR-006 | Covered |
| US-006-01 | FR-018 | **Partially covered.** There is no list operation (`GAP-BE-005`). The UI part belongs to web-app. |
| US-006-02 | FR-001, FR-003, FR-007, FR-010 | Covered for the API. The "no API call is made" criterion is a web_app behaviour. |
| US-006-04 | FR-004, FR-011, FR-013 | Covered for the API (`GAP-BE-007`) |
| US-006-05 | FR-014 to FR-017 | **Partially covered.** There is no archived-only listing (`GAP-BE-006`). The UI part belongs to web-app. |
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 415 | Create a note with title, body and optional tags; pinned defaults to false |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 400, 404 | Get a single note by id, including its tags and archived state |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 404, 415 | Partially edit a note's title, body or pinned flag (pin and unpin) |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 400, 404, 409 | Archive a note (flag only, no hard delete) |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 404, 415 | Add a tag to a note; adding a duplicate creates no second entry |
| `API-NOTES-150096` | DELETE | `/notes/{id}/tags/{tag}` | `notes` | 200 | 400, 404 | Remove a tag from a note |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 400, 404 | Unarchive a note, restoring it with all data intact |

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
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DERIVED -->
## Invariants

The project-level invariants `INV-BE-001` (no retention job deletes a note), `INV-BE-002` (an archived note is identical after unarchive) and `INV-BE-003` (all stored data resides in approved EU regions) also apply. They are referenced here and not restated.

- **INV-BE-004.** After every operation, every note has a non-empty title, a non-empty body, a non-null `pinned`, and a `status` of `active` or `archived` (BR-005, BR-018, US-002-04). Derived from FR-001 to FR-010.
- **INV-BE-005.** A note never carries the same tag name twice (US-002-03). Derived from the (`note_id`, `tag_name`) primary key.
- **INV-BE-006.** Archive and unarchive change only `status` and `updated_at`. Title, body, `pinned` and the tag set are identical before and after (BR-007, BR-008).
- **INV-BE-007.** An edit changes only the fields it supplies. Every unspecified field is unchanged (BR-006, AC-002-02-02).
- **INV-BE-008.** A request answered with 400, 404 or 409 persists no change to any note or tag (AC-002-01-03, AC-002-02-03).
- **INV-BE-009.** `created_at` never changes after creation, and `updated_at` is never earlier than `created_at`. Derived from US-002-01 and AC-002-02-01.
- **INV-BE-010.** No operation of this module hard-deletes a note (BR-007). A note ID that existed never becomes reusable. Derived from BR-007 and `ON DELETE RESTRICT` (`ASM-BE-016`).
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The role-by-operation matrix and the ownership predicate for the seven operations cannot be written. Three decisions are missing from the inputs, all tracked at the project level:

1. **Note ownership and access rule.** BR-031 states that the note ownership fields and the access check "cannot be written until this is decided", and calls the shared-workspace default only a placeholder. See `Q-BE-002`.
2. **Whether notes endpoints require authentication and what credential grants access.** No input states a token type, lifetime or transport, and US-004-03 mentions 401/403 only conditionally. See `Q-BE-003`, `GAP-BE-004`.
3. **Which actors exist.** No role list is supplied. See `Q-BE-001`.

**What would unblock it:**

- Human answers to `Q-BE-002` and `Q-BE-003`.
- The contents of the four inbound handoff files, if they answer these (`GAP-BE-001`).
- Clean source text for BR-030 and BR-031 (`GAP-BE-003`).

**Consequences until then:**

- The 401/403 rows for these operations are not asserted.
- Migration 005 (ownership columns) is not written.
- The requirements in `backend.notes.requirements` hold regardless of who calls. They state nothing about who may call.

Nothing is assumed about an authorization default.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: VERIFIED -->
## Jobs

**Scheduled work: none.** This module owns no sweep, retry or scheduler.

- Retention deletes only code challenges, send attempts and account locks, and never notes (NFR-019, `INV-BE-001`). Those sweeps belong to `auth`, and their schedule is a project-level gap (`GAP-BE-016`).
- Backups and the residency scan belong to the `residency` module.

**Idempotency**

| Operation | Behaviour | Source |
|---|---|---|
| `API-NOTES-359413` (add tag) | Idempotent: a repeat returns 200 and creates no duplicate | US-002-03 |
| `API-NOTES-810640` (archive) | Not idempotent: a repeat returns 409 | US-002-04 |
| `API-NOTES-150096` (remove tag) | Idempotent by assumption (`ASM-BE-021`) | `GAP-BE-031` |
| `API-NOTES-917869` (unarchive) | Not idempotent on an active note (`ASM-BE-018`) | `GAP-BE-029` |
| `API-NOTES-232390` (create) | No idempotency key is defined. A retried create makes a second note. | `GAP-BE-032` |
| `API-NOTES-847551` (edit) | Repeating the same values yields the same stored state | BR-006 |

**Concurrency.** Concurrent edits are last-write-wins with no version check (`ASM-BE-005` of the project spec). An edit touches only the fields supplied, so two concurrent edits of different fields both persist (BR-006, `INV-BE-007`). There is no live broadcast or merge logic (US-005-03).
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DERIVED -->
## Test plan

All test names are proposals ("planned"). They cover NFR-005: 100% CRUD functional-test pass rate with zero data loss.

### Unit
- `notes.validation.title_body_required_nonempty`, `notes.validation.pinned_must_be_boolean`.
- `notes.patch.builds_update_from_supplied_fields_only` (BR-006).
- `notes.state.archive_guard`, `notes.state.unarchive_guard` (the transition table).
- `notes.tags.dedupe_within_request` (`ASM-BE-023`).

### Integration (against PostgreSQL 16)
- All verification names in `backend.notes.requirements`, covering FR-001 to FR-018.
- `notes.roundtrip.archive_unarchive_data_identical` (`INV-BE-006`, `INV-BE-002`).
- `notes.concurrent_patch.different_fields_both_persist` (`INV-BE-007`).
- `notes.failed_requests_persist_nothing` (`INV-BE-008`).
- `notes.create.atomic_with_tags` (FR-004).
- `notes.migration.constraints_enforced` (CHECK on `status`, the (`note_id`, `tag_name`) primary key, `ON DELETE RESTRICT`).
- `notes.no_hard_delete_path` (`INV-BE-010`).

### Contract
- Responses of all seven operations validate against the published OpenAPI document (`contract-docs`, BR-012).
- Each documented 4xx/5xx for these operations is observed in the integration run (US-004-03).
- `notes.contract.no_attachment_routes`: confirmation that `scope-guard` rejects multipart on these paths. The suite is owned by `scope-guard` (NFR-010) and is referenced here only.
- Error-code assertions for 400, 404 and 409 branch on `code`, not on status alone. 415 is overloaded across modules (project spec `backend.error-model`).

### Not testable until unblocked
- Authorization and ownership tests (`backend.notes.authorization`).
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, assumptions and questions

This section lists what this module mints, and the project-level items it depends on, by reference.

### Depends on (defined in the project spec)
- Blocking: `Q-BE-002` (ownership and access rule), `Q-BE-003` (credential).
- Open gaps: `GAP-BE-001` (inbound handoffs unread), `GAP-BE-004`, `GAP-BE-005` (no list-notes operation), `GAP-BE-006` (no archived-only listing), `GAP-BE-007` (tag removal has no acceptance criteria), `GAP-BE-008` (field constraints), `GAP-BE-010` (encryption at rest), `GAP-BE-014` (PUT versus PATCH), `GAP-BE-016`.
- Degradable questions already answered by default: `Q-BE-007` (PATCH with partial-update semantics).
- Assumptions in force: `ASM-BE-004`, `ASM-BE-005`, `ASM-BE-006`, `ASM-BE-010`.

### Contradictions (non-critical)
- **CON-BE-003** (story ↔ operation records). US-002-02 and AC-002-02-01 say a user can edit "title, body, or tags" via PUT/PATCH. The operation record for `API-NOTES-847551` covers title, body and `pinned` only, and tags change through `API-NOTES-359413` and `API-NOTES-150096`. This spec reads tag editing as served by the tag endpoints. The consequence of the other reading: PATCH would also have to accept a tag list, with replace-versus-merge semantics that no input defines. Not Critical under core 8 (no data-shape, state-machine, authorization, or money/PII change).

### Assumptions
- `ASM-BE-013`: The note ID is a UUID. The story says only "unique ID" (US-002-01). Confirm with the contract owner or platform input.
- `ASM-BE-014`: `status` is a text column constrained to `active` or `archived`, defaulting to `active`. The inputs say only "archive flag" (BR-007) and "status changes to active" (US-002-05).
- `ASM-BE-015`: Timestamps are `timestamptz` stored in UTC. `updated_at` is refreshed on any change to title, body, `pinned`, the tag set, or `status`. US-002-02 ties the updated timestamp to edits of title, body or tags. Confirm whether archive and unarchive should change it (`GAP-BE-033`).
- `ASM-BE-016`: `note_tags.note_id` references `notes.id` with `ON DELETE RESTRICT`, because no input defines hard delete (BR-007, `GAP-BE-016`).
- `ASM-BE-017`: Tag names are compared exactly as supplied (case-sensitive, no normalisation). The inputs state no tag rules (`GAP-BE-008`).
- `ASM-BE-018`: Archive of a missing note returns 404 (by analogy with US-002-05). Unarchive of an already-active note returns 409 `NOTE_NOT_ARCHIVED`, a code proposed here.
- `ASM-BE-019`: Edits and tag changes are allowed on archived notes. No input forbids them (`GAP-BE-028`).
- `ASM-BE-020`: Edit validation. An empty title or body, an unknown field (including `tags`), or an empty request body returns 400 `VALIDATION_ERROR` and changes nothing.
- `ASM-BE-021`: Tag add returns 200 whether the tag is new or a repeat (US-002-03). Tag removal returns 200 for a present or absent tag and 404 only for a missing note. Tag add on a missing note returns 404, and an invalid tag name returns 400.
- `ASM-BE-022`: Tags are returned in ascending order of name, so responses are deterministic. No input states an order.
- `ASM-BE-023`: Create with tags is atomic (note and tags together or neither), and duplicate tag names within one request are stored once.

### Gaps
Each names the artefact that would close it.
- `GAP-BE-028`: Whether an archived note may be edited, pinned, or have tags changed is unstated. Closing artefact: an acceptance criterion (`Q-BE-008`).
- `GAP-BE-029`: The response to unarchiving an already-active note is unstated. Closing artefact: an acceptance criterion (`Q-BE-009`).
- `GAP-BE-030`: Edit edge cases are unstated: an empty title or body on edit, whitespace-only strings (at create and edit), unknown fields, and an empty PATCH body. Closing artefact: edit validation rules in US-002-02.
- `GAP-BE-031`: The removal contract is unstated: success status, behaviour for an absent tag, and the returned body. Extends `GAP-BE-007`. Closing artefact: acceptance criteria for tag removal.
- `GAP-BE-032`: Idempotency for create (for example a client-supplied key) and the rate-limit class of every operation are not defined. Closing artefact: an NFR or a contract decision.
- `GAP-BE-033`: Whether tag changes and archive transitions refresh `updated_at`, and the order of tags in responses, are unstated. Closing artefact: an acceptance criterion.
- `GAP-BE-034`: There is no maximum number of tags per note and no maximum count of notes. Closing artefact: field constraints (`Q-BE-010`).

### Questions (all degradable; defaults taken because the run is unattended)
- `Q-BE-008` (degradable): May archived notes be edited or have their tags changed? Default: yes (`ASM-BE-019`).
- `Q-BE-009` (degradable): What does unarchiving an already-active note return? Default: 409 `NOTE_NOT_ARCHIVED` (`ASM-BE-018`).
- `Q-BE-010` (degradable): Is there a maximum number of tags per note? Default: none enforced by this spec. A limit would be stated in the contract once an input supplies it (`GAP-BE-034`).
<!-- /SECTION: backend.notes.gaps -->
