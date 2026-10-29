---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.9.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-002, Q-BE-003]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-359413, API-NOTES-810640, API-NOTES-917869]
```

`blocked_by` lists the two blocking questions defined in `backend.auth-model`. They block only `backend.notes.authorization`. Every other section is written.
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: DERIVED -->
## Purpose

**Responsible for.** This is the `notes` module in `backend.module-map`. It owns the lifecycle of a note and its tags, per US-002-01 to US-002-06 and BR-005 to BR-008 and BR-018. That means:
- Creating a note with a required title and body, an optional pinned flag and optional tags (`API-NOTES-232390`).
- Reading one note by id (`API-NOTES-045877`).
- Editing title, body and the pinned flag with partial-update semantics (`API-NOTES-847551`).
- Adding tags to a note without duplicates (`API-NOTES-359413`).
- Archiving a note (`API-NOTES-810640`) and unarchiving it (`API-NOTES-917869`). Archive is a flag. Nothing is ever deleted (BR-007, BR-008).

The stack is Node.js 20, Express and PostgreSQL 16 on AWS, EU region only (`platform.md`; BR-001).

**Not responsible for.**
- Search, filtering, pagination and ordering of results. These belong to `search` (US-003-01 to US-003-06; BR-009 to BR-011, BR-019). `search` reads notes and tags from this module.
- Login codes, locks, sessions and email delivery. These belong to `auth`.
- The OpenAPI document and its coverage check. These belong to `contract-docs`.
- Refusal of attachment, real-time and mobile calls, including the 415 for multipart uploads to note endpoints (US-005-02). These belong to `scope-guard`.
- Infrastructure region checks. These belong to `residency`.
- Listing notes, tag removal and distinct-tag listing. No input defines these operations (`GAP-BE-002`, `GAP-BE-003`). This module does not invent them.
- The web_app screens (US-006-01 to US-006-05). This module is only their backend dependency.
- Hard deletion of notes. No operation deletes a note (BR-007). The 404 criterion for a "deleted" note in US-002-06 therefore reduces to a never-existing id.
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Both tables are created by migrations 1 and 2 of `backend.migration-plan`. The database is PostgreSQL 16 (`platform.md`).

### Table `notes`

| Column | Type | Null | Default | Constraint | Source |
|---|---|---|---|---|---|
| `id` | `uuid` | NOT NULL | server-generated | PRIMARY KEY | US-002-01 ("unique ID"); type is `ASM-BE-011` |
| `title` | `text` | NOT NULL | none | non-empty after trimming | BR-005; whitespace handling is `ASM-BE-014` |
| `body` | `text` | NOT NULL | none | non-empty after trimming | BR-005; whitespace handling is `ASM-BE-014` |
| `pinned` | `boolean` | NOT NULL | `false` | none | BR-018, AC-002-01-01 |
| `status` | `text` | NOT NULL | `'active'` | CHECK in (`'active'`, `'archived'`) | BR-007, US-002-04, US-002-05; representation is `ASM-BE-012` |
| `created_at` | `timestamptz` | NOT NULL | `now()` | never updated after insert | US-002-01 |
| `updated_at` | `timestamptz` | NOT NULL | `now()` | set on each modification | AC-002-02-01, `ASM-BE-013` |

- Timestamps are stored with timezone handling by `timestamptz`.
- No owner column exists. Ownership is undecided (`Q-BE-002`), so adding one is a later migration.
- Title and body length limits are unstated (`GAP-BE-023`), so no length constraint is asserted.
- Whether a note's content may contain PII, and its at-rest treatment, are unstated (`GAP-BE-006`). Neither is asserted.

### Table `note_tags`

| Column | Type | Null | Default | Constraint | Source |
|---|---|---|---|---|---|
| `note_id` | `uuid` | NOT NULL | none | FOREIGN KEY → `notes(id)` ON DELETE CASCADE | `ASM-BE-018` |
| `tag_name` | `text` | NOT NULL | none | PRIMARY KEY with `note_id` | US-002-03; tag rules open (`GAP-BE-003`) |

**ON DELETE.** CASCADE is inert in practice. No operation deletes a note (BR-007), so the clause only keeps referential integrity if a row is ever removed out of band (`ASM-BE-018`).

### Indexes

| Index | Query it serves |
|---|---|
| `notes` primary key on `id` | Fetch, edit, archive, unarchive and tag-add by id (all six operations). |
| `note_tags` primary key on (`note_id`, `tag_name`) | Prevents a duplicate tag on a note (US-002-03), and reads a note's tags for the get response. |

Search-oriented indexes (keyword, tag lookup, status and pinned ordering) are owned by `search`. This module defines none (`backend.migration-plan`, migration 4).
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**States.** `active` and `archived` (US-002-04, US-002-05). A new note is `active` (AC-002-01-01).

`pinned` is a boolean flag set at creation or by edit (BR-018). It is independent of status, so a note may be pinned and archived at once. It is not a state.

| From | To | Trigger | Guard | Source |
|---|---|---|---|---|
| (none) | `active` | `API-NOTES-232390` | valid title and body | AC-002-01-01 |
| `active` | `archived` | `API-NOTES-810640` | note exists | US-002-04, BR-007 |
| `archived` | `active` | `API-NOTES-917869` | note exists | US-002-05, BR-008 |

**Illegal transitions.**

| Attempt | Result | Source |
|---|---|---|
| Archive an already-archived note | Conflict response, no change. Status 409 per `backend.error-model`. The error-code name is open (`GAP-BE-004`). | US-002-04 |
| Archive or unarchive an unknown id | 404, no change | US-002-05; unknown-id handling for archive is `ASM-BE-016` |
| Unarchive an already-active note | 409, no change (`ASM-BE-016`, `GAP-BE-025`). No input states this case. | none |

**Terminal states.** None. Archiving is fully reversible without data loss (BR-008), and no operation deletes a note (BR-007).

Edit, tag-add and get are not transitions and do not change status. Whether edit and tag-add are allowed on an archived note is unstated (`ASM-BE-015`, `GAP-BE-027`).
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Request and response field lists are in the rendered contract, not restated here (core rule 6).

### BE-NOTES-FR-001 — Create a note
- **Given** a request with a non-empty title and body
- **When** `API-NOTES-232390` is called
- **Then** the service returns 201 with the created note, which has a unique id, `created_at` and `updated_at`, `pinned=false` and status `active`.
- **Traces to.** US-002-01, AC-002-01-01, BR-005
- **Verification.** `notes.create.returns_201_with_defaults` (planned)
- **API.** `API-NOTES-232390`

### BE-NOTES-FR-002 — Create a pinned note
- **Given** a valid payload with `pinned=true`
- **When** `API-NOTES-232390` is called
- **Then** the service returns 201 and the stored note has `pinned=true`.
- **Traces to.** US-002-01, AC-002-01-02, BR-018
- **Verification.** `notes.create.pinned_true_is_stored` (planned)
- **API.** `API-NOTES-232390`

### BE-NOTES-FR-003 — Reject a create without a title
- **Given** a payload with a missing or empty title
- **When** `API-NOTES-232390` is called
- **Then** the service returns 400 with a validation error and persists no note.
- **Traces to.** US-002-01, AC-002-01-03, BR-005
- **Verification.** `notes.create.missing_title_returns_400_no_row` (planned)
- **API.** `API-NOTES-232390`

### BE-NOTES-FR-004 — Reject a create without a body
- **Given** a payload with a missing or empty body
- **When** `API-NOTES-232390` is called
- **Then** the service returns 400 with a validation error and persists no note.
- **Traces to.** BR-005 ("a note must always have a title and body at creation; empty/null payloads are rejected")
- **Verification.** `notes.create.missing_body_returns_400_no_row` (planned)
- **API.** `API-NOTES-232390`

### BE-NOTES-FR-005 — Create with tags
- **Given** a valid create request that includes tags
- **When** `API-NOTES-232390` is called
- **Then** each distinct tag is attached to the new note, and a tag repeated within the request is stored once (`ASM-BE-019`).
- **Traces to.** US-002-03, and the tags field in the `API-NOTES-232390` operation record. Tag rules are open (`GAP-BE-003`).
- **Verification.** `notes.create.tags_attached_without_duplicates` (planned)
- **API.** `API-NOTES-232390`

### BE-NOTES-FR-006 — Edit a note
- **Given** an existing note and valid updated fields
- **When** `API-NOTES-847551` is called
- **Then** the service returns 200 with the updated note and an updated `updated_at`.
- **Traces to.** US-002-02, AC-002-02-01
- **Verification.** `notes.edit.returns_200_and_bumps_updated_at` (planned)
- **API.** `API-NOTES-847551`

### BE-NOTES-FR-007 — Partial update
- **Given** an existing note and a PATCH containing only some editable fields
- **When** `API-NOTES-847551` is called
- **Then** only the supplied fields change, and title, body, pinned and tags not supplied are unchanged.
- **Traces to.** BR-006, US-002-02, AC-002-02-02
- **Verification.** `notes.edit.unsupplied_fields_unchanged` (planned)
- **API.** `API-NOTES-847551`

### BE-NOTES-FR-008 — Pin and unpin
- **Given** an existing note
- **When** `API-NOTES-847551` is called with only the `pinned` field
- **Then** the service returns 200, `pinned` is set to the supplied value, and title, body and tags are unchanged.
- **Traces to.** BR-018, AC-002-02-02
- **Verification.** `notes.edit.pinned_only_patch` (planned)
- **API.** `API-NOTES-847551`

### BE-NOTES-FR-009 — Reject empty edits of required fields
- **Given** a PATCH that sets title or body to empty or null
- **When** `API-NOTES-847551` is called
- **Then** the service returns 400 and the note is unchanged (`ASM-BE-014`).
- **Traces to.** BR-005 (extended to edit by assumption)
- **Verification.** `notes.edit.empty_title_or_body_returns_400` (planned)
- **API.** `API-NOTES-847551`

### BE-NOTES-FR-010 — Unknown note id
- **Given** an id that does not identify a note
- **When** any of `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-810640` or `API-NOTES-917869` is called
- **Then** the service returns 404 and modifies nothing. For the get operation, no partial data is exposed.
- **Traces to.** AC-002-02-03, US-002-05, US-002-06
- **Verification.** `notes.unknown_id.returns_404_all_operations` (planned)
- **API.** the five operations listed above

### BE-NOTES-FR-011 — Add tags
- **Given** an existing note and a tag name
- **When** `API-NOTES-359413` is called
- **Then** the service returns 200 and the note's tag list includes the tag.
- **Traces to.** US-002-03
- **Verification.** `notes.tags.add_returns_200_and_lists_tag` (planned)
- **API.** `API-NOTES-359413`

### BE-NOTES-FR-012 — Repeated tag add
- **Given** a tag already on the note
- **When** `API-NOTES-359413` adds the same tag again
- **Then** the service returns 200 and creates no duplicate entry.
- **Traces to.** US-002-03
- **Verification.** `notes.tags.repeat_add_no_duplicate` (planned)
- **API.** `API-NOTES-359413`

### BE-NOTES-FR-013 — Adding several tags is all-or-nothing
- **Given** a request that adds several tags and contains one invalid tag
- **When** `API-NOTES-359413` is called
- **Then** the service returns 400 and adds none of them (`ASM-BE-019`).
- **Traces to.** US-002-03, and the "one or more tags" wording of the `API-NOTES-359413` operation record. Validity rules are open (`GAP-BE-003`).
- **Verification.** `notes.tags.invalid_tag_adds_none` (planned)
- **API.** `API-NOTES-359413`

### BE-NOTES-FR-014 — Archive
- **Given** an active, existing note
- **When** `API-NOTES-810640` is called
- **Then** the service returns 200, the status becomes `archived`, and the note's data remains intact.
- **Traces to.** US-002-04, BR-007
- **Verification.** `notes.archive.sets_status_keeps_data` (planned)
- **API.** `API-NOTES-810640`

### BE-NOTES-FR-015 — Archive an archived note
- **Given** an already-archived note
- **When** `API-NOTES-810640` is called again
- **Then** the service returns 409 and changes nothing.
- **Traces to.** US-002-04
- **Verification.** `notes.archive.already_archived_returns_409` (planned)
- **API.** `API-NOTES-810640`

### BE-NOTES-FR-016 — Unarchive
- **Given** an archived note
- **When** `API-NOTES-917869` is called
- **Then** the service returns 200, the status becomes `active`, and all original data is preserved.
- **Traces to.** US-002-05, BR-008
- **Verification.** `notes.unarchive.restores_active_keeps_data` (planned)
- **API.** `API-NOTES-917869`

### BE-NOTES-FR-017 — Unarchive an active note
- **Given** a note that is already `active`
- **When** `API-NOTES-917869` is called
- **Then** the service returns 409 and changes nothing (`ASM-BE-016`, pending `Q-BE-012`).
- **Traces to.** US-002-05 (symmetry with the archive rule by assumption)
- **Verification.** `notes.unarchive.already_active_returns_409` (planned)
- **API.** `API-NOTES-917869`

### BE-NOTES-FR-018 — Retrieve a note
- **Given** an existing note, active or archived
- **When** `API-NOTES-045877` is called
- **Then** the service returns 200 with the full note, including its tags, archive status and pinned flag.
- **Traces to.** US-002-06, AC for US-002-06 ("full note details including tags, archive status and the pinned flag"), BR-007
- **Verification.** `notes.get.returns_full_note_including_archived` (planned)
- **API.** `API-NOTES-045877`

### BE-NOTES-FR-019 — Concurrent edits
- **Given** two PATCH requests to the same note processed concurrently
- **When** both reach `API-NOTES-847551`
- **Then** each applies only its supplied fields, the later write to a field wins, and no merge or broadcast logic runs (`ASM-BE-017`, `GAP-BE-026`).
- **Traces to.** US-005-03 (second criterion), BR-006
- **Verification.** `notes.edit.concurrent_patches_last_write_wins` (planned)
- **API.** `API-NOTES-847551`

### BE-NOTES-FR-020 — Input safety
- **Given** note title, body or tag fields carrying injection payloads
- **When** any write operation of this module is called
- **Then** inputs are validated and sanitised, and a security scan of this module finds 0 critical injection vulnerabilities.
- **Traces to.** NFR-012
- **Verification.** `notes.security.injection_payloads_rejected_or_neutralised` (planned)
- **API.** `API-NOTES-232390`, `API-NOTES-847551`, `API-NOTES-359413`

### BE-NOTES-FR-021 — Timestamps
- **Given** any successful modification of a note
- **When** the operation completes
- **Then** `updated_at` is set to the time of the change, and `created_at` is unchanged (`ASM-BE-013`).
- **Traces to.** US-002-01, AC-002-02-01
- **Verification.** `notes.timestamps.created_fixed_updated_advances` (planned)
- **API.** `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-810640`, `API-NOTES-917869`
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400 | Create a note with a required title and body, optional tags and pinned flag. The note gets a unique id, timestamps, pinned=false by default and active status (US-002-01, BR-005, BR-018). |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 404 | Get a single note by id, including its status, pinned flag and tags (US-002-02, BR-006). |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 404 | Edit a note's title, body or pinned flag. Setting pinned pins or unpins the note (BR-006, BR-018). |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 404, 409 | Archive a note by setting its archive flag. The note is never deleted (BR-007, US-002-04). |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 404 | Add one or more tags to a note. Repeating an add creates no duplicate entry on that note (US-002-03). |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 404 | Unarchive a note, restoring it to active status without data loss (BR-008, US-002-05). |

### `API-NOTES-232390` — POST `/notes`

Create a note with a required title and body, optional tags and pinned flag. The note gets a unique id, timestamps, pinned=false by default and active status (US-002-01, BR-005, BR-018).

**Request:** 
- `title` (string, required): Note title; missing or empty returns 400 (AC-002-01-03, BR-005).
- `body` (string, required): Note body text (BR-005).
- `pinned` (boolean): Pinned flag; defaults to false (US-002-01, BR-018).
- `tags` (array): Initial tag names for the note. Tag rules are undecided (GAP-BE-003).
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Whether the note is pinned.
- `status` (string, required): Active or archived status of the note.
- `tags` (array, required): Tag names on the note, without duplicates.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-045877` — GET `/notes/{id}`

Get a single note by id, including its status, pinned flag and tags (US-002-02, BR-006).

**Request:** 
- `id` (string, required): Note id.
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Whether the note is pinned.
- `status` (string, required): Active or archived status of the note.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-847551` — PATCH `/notes/{id}`

Edit a note's title, body or pinned flag. Setting pinned pins or unpins the note (BR-006, BR-018).

**Request:** 
- `id` (string, required): Note id.
- `title` (string): New title; must not be empty if supplied (BR-005).
- `body` (string): New body text.
- `pinned` (boolean): New pinned flag value (BR-018).
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Whether the note is pinned.
- `status` (string, required): Active or archived status.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note by setting its archive flag. The note is never deleted (BR-007, US-002-04).

**Request:** 
- `id` (string, required): Note id.
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Whether the note is pinned.
- `status` (string, required): Archived status after the operation.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add one or more tags to a note. Repeating an add creates no duplicate entry on that note (US-002-03).

**Request:** 
- `id` (string, required): Note id.
- `tags` (array, required): Tag names to add. Tag rules are undecided (GAP-BE-003).
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Whether the note is pinned.
- `status` (string, required): Active or archived status.
- `tags` (array, required): Tag names on the note after the add, without duplicates.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Unarchive a note, restoring it to active status without data loss (BR-008, US-002-05).

**Request:** 
- `id` (string, required): Note id.
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Whether the note is pinned.
- `status` (string, required): Active status after the operation.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

| ID | Must be true after every operation | Source |
|---|---|---|
| `INV-BE-001` | Every note has a non-null, non-empty title and body. | BR-005; edit extension is `ASM-BE-014` |
| `INV-BE-002` | `pinned` is a boolean and never null. | BR-018 |
| `INV-BE-003` | `status` is exactly `active` or `archived`. | BR-007, US-002-04, US-002-05 |
| `INV-BE-004` | No operation of this module removes a note row. | BR-007 |
| `INV-BE-005` | A note carries each tag name at most once. | US-002-03 |
| `INV-BE-006` | Archive and unarchive change only `status` and `updated_at`. Title, body, pinned and tags are untouched. | BR-007, BR-008, AC for US-002-05 |
| `INV-BE-007` | A PATCH changes only the fields it supplies. | BR-006 |
| `INV-BE-008` | `id` and `created_at` never change after creation, and `updated_at` is never earlier than `created_at`. | US-002-01 |
| `INV-BE-009` | A request answered with 400, 404 or 409 leaves all note data unchanged. | AC-002-01-03, AC-002-02-03, US-002-04 |
| `INV-BE-010` | Every note id is unique. | US-002-01 |
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The roles, the permission matrix and the ownership predicate cannot be written from the supplied inputs.

**Unblocking inputs** (both defined in `backend.auth-model`):
1. `Q-BE-002`: are notes owned by one user or shared in a team workspace, and what is the access rule per operation? BR-031 is a placeholder.
2. `Q-BE-003`: what is the session model, and do notes operations require a session at all?

Until both are answered, this document asserts no role, no ownership column and no 401 or 403 rule for the six operations of this module. The 401 and 403 responses are documented as "pending" in `backend.error-model`.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**Scheduled work.** None.
- Notes are not deleted by retention, and archived notes follow BR-007 and BR-008 (NFR-019).
- No input defines a sweep, purge or retry job for notes.
- Retention of login records belongs to `auth`.

**Retries.** No server-side retries. No input defines any.

**Idempotency.** No input defines an idempotency key for any note operation, so none is introduced.

| Operation | Behaviour on repeat |
|---|---|
| `API-NOTES-232390` create | Not idempotent. Each valid call creates a new note. No client key exists. |
| `API-NOTES-045877` get | Safe, read only. |
| `API-NOTES-847551` edit | Re-sending the same values leaves the same field values. Whether `updated_at` moves on a no-change edit is unstated (`ASM-BE-013`). |
| `API-NOTES-359413` add tags | Idempotent. A repeated add returns 200 and creates no duplicate (US-002-03). |
| `API-NOTES-810640` archive | A repeat returns 409 and leaves state unchanged (US-002-04). |
| `API-NOTES-917869` unarchive | A repeat on an active note returns 409 and leaves state unchanged (`ASM-BE-016`). |
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DERIVED -->
## Test plan

All test names are proposed. None exists yet.

**Unit**
- Validation of title and body: missing, null, empty, whitespace-only (`notes.validation.title_body_rules`).
- Status transitions and illegal transitions (`notes.state.transitions`).
- Partial-update merge: supplied fields only (`notes.edit.merge_only_supplied`).
- Tag set handling: de-duplication within a request and against stored tags (`notes.tags.set_semantics`).

**Integration** (against PostgreSQL 16, `platform.md`)
- The FR tests named in `backend.notes.requirements`, FR-001 to FR-021.
- Constraint tests: the status CHECK rejects other values, the composite key rejects a duplicate tag, and a failed request leaves no row (`INV-BE-001` to `INV-BE-010`).
- Concurrent PATCH test on one note (FR-019).
- Round trip: archive then unarchive returns a note equal to the original, apart from `updated_at` (FR-014, FR-016).

**Contract**
- Every response and every documented error status of the six operations matches the published OpenAPI document (BR-012, NFR-008). The check is run by the `contract-docs` coverage check.
- The scope-guard contract suite asserts that no attachment, real-time or mobile route exists beside these routes (NFR-010). It is owned by `scope-guard` and only referenced here.

**Security**
- Injection payloads against title, body and tag inputs (FR-020, NFR-012).

**Not planned.** Authorization tests, until `Q-BE-002` and `Q-BE-003` are answered.
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: VERIFIED -->
## Gaps, contradictions and assumptions

### Open points defined in the project spec and relied on here
- `Q-BE-002` and `Q-BE-003` (blocking). Note ownership and the session model. They block `backend.notes.authorization`.
- `GAP-BE-002`. No list-notes operation.
- `GAP-BE-003`. Tag rules and tag removal.
- `GAP-BE-004`. Error envelope, code names and retry values.
- `GAP-BE-006`. Encryption, backup and log retention unstated.
- `ASM-BE-010`. The handoff summaries in `feedback.md` match the unsupplied files.

### Inbound handoffs touching this module
- `HANDOFF-WEBAPP-526592` (list active and archived-only notes) cannot be answered here (`GAP-BE-002`).
- `HANDOFF-WEBAPP-701819` (tag removal and distinct-tag listing) cannot be answered here (`GAP-BE-003`).

Both dispositions rest on `ASM-BE-010`. No operation for either is invented in this module.

### Gaps raised by this module

- `GAP-BE-023`: no input states limits on title or body size, the maximum number of tags per note, or the order in which tags are returned.
  - Degrades: data-model constraints, `API-NOTES-232390` and `API-NOTES-359413`.
  - Fallback: no limit is asserted.
  - Closing artefact: a requirement or a platform statement giving the limits.
- `GAP-BE-024`: AC-002-02-01 and AC-002-02-03 say "PUT/PATCH", but only a PATCH operation exists for this module.
  - Degrades: `API-NOTES-847551`.
  - Fallback: PATCH only, with partial semantics (BR-006).
  - Closing artefact: confirmation that PUT is not required.
- `GAP-BE-025`: no input states what unarchiving an already-active note returns.
  - Degrades: FR-017 and the state machine.
  - Fallback: `ASM-BE-016`.
  - Closing artefact: an acceptance criterion for US-002-05.
- `GAP-BE-026`: US-005-03 asks for a "standard last-write/conflict response" on concurrent PATCH, without saying which one.
  - Degrades: FR-019.
  - Fallback: `ASM-BE-017`.
  - Closing artefact: a concurrency decision, such as a version or ETag check or an explicit last-write rule.
- `GAP-BE-027`: no input says whether an archived note may be edited or have tags added.
  - Degrades: the state machine and FR-006, FR-011.
  - Fallback: `ASM-BE-015`.
  - Closing artefact: a business rule on archived notes.

### Contradictions
None raised. Four checks were run:
- Story against requirement: US-002-02 says "edit tags", while the PATCH operation record covers title, body and pinned. The tags-edit part is met by `API-NOTES-359413` for additions, and removal is the open `GAP-BE-003`. This is a gap, not a contradiction.
- Requirement against requirement: BR-005, BR-006, BR-007, BR-008 and BR-018 are consistent.
- Story against design: the design brief repeats the acceptance criteria of US-002-01 without conflict.
- Design against design: nothing to compare.

### Questions (all degradable; each default applies in an unattended run)
- `Q-BE-011`: may an archived note be edited or have tags added? Default: yes (`ASM-BE-015`).
- `Q-BE-012`: what does unarchiving an active note return? Default: 409 (`ASM-BE-016`).
- `Q-BE-013`: what are the limits on title, body and tags per note, and in what order are tags returned? Default: none asserted (`GAP-BE-023`).

### Assumptions
- `ASM-BE-011`: note ids are server-generated UUIDs. Confirmation: a platform or design decision on id type.
- `ASM-BE-012`: archive state is stored as a `status` text column with values `active` and `archived`, which is equivalent to the "archive flag" of BR-007.
- `ASM-BE-013`: `updated_at` is set on every change to a note, including pin, archive, unarchive and a tag add that adds a new tag. A tag add that changes nothing does not move it. Whether a no-change edit moves it is not decided here.
- `ASM-BE-014`: title and body that are empty or whitespace-only count as empty. BR-005 states its rule for creation only, and this module applies it to edits too.
- `ASM-BE-015`: edit and tag-add are allowed on archived notes. Archiving hides a note from active views and does not freeze it (BR-007). Confirmation: `Q-BE-011`.
- `ASM-BE-016`: archive and unarchive of an unknown id return 404, and unarchive of an active note returns 409, mirroring US-002-04. Confirmation: `Q-BE-012`.
- `ASM-BE-017`: concurrent PATCH requests are applied per supplied field, last write wins, with no version check and no concurrency conflict response.
- `ASM-BE-018`: `note_tags.note_id` uses ON DELETE CASCADE, which is inert because no operation deletes a note.
- `ASM-BE-019`: tags repeated within one request are stored once. A request adding several tags is all-or-nothing, so one invalid tag fails the whole request with 400. Tag validity rules are open (`GAP-BE-003`).
- `ASM-BE-020`: a create or PATCH body that carries `id`, `status`, `created_at`, `updated_at` or, for PATCH, `tags` is rejected with 400, because silently ignoring them would hide data loss. Tag changes go through `API-NOTES-359413`.
<!-- /SECTION: backend.notes.gaps -->
