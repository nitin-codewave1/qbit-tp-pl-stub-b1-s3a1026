---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.7.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06, notes_api:US-006-02, notes_api:US-006-04, notes_api:US-006-05]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-002]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-359413, API-NOTES-810640, API-NOTES-917869]
```
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: VERIFIED -->
## Purpose

**Responsible for.** The note lifecycle in `notes_api`. The module covers these operations:
- Create a note: `API-NOTES-232390` (US-002-01, US-006-02).
- Get a note: `API-NOTES-045877` (US-002-06).
- Edit title, body and the `pinned` flag with partial-update semantics: `API-NOTES-847551` (US-002-02; BR-006, BR-018).
- Add tags, without duplicates: `API-NOTES-359413` (US-002-03, US-006-04).
- Archive: `API-NOTES-810640` (US-002-04; BR-007).
- Unarchive: `API-NOTES-917869` (US-002-05; BR-008).

It also owns the stored form of notes and note tags. Stories owned: US-002-01 to US-002-06, US-006-02, US-006-04, US-006-05 (backend operations only).

**Not responsible for.**
- Keyword, tag and combined search, pagination, archived exclusion and pinned-first ordering. The `search` module owns these (US-003-01 to US-003-06; BR-009 to BR-011, BR-019).
- Listing active or archived-only notes, and listing distinct tags. No input defines these operations (`GAP-BE-005`, `GAP-BE-006`).
- Rejection of attachment, real-time and mobile-specific calls. The `scope-guard` module owns it. This module only receives the 415 for multipart payloads on its endpoints, which `scope-guard` defines (US-005-02).
- Login codes, sessions and credentials (`auth` module; `Q-BE-003`).
- The web_app editor, list and archive screens. They belong to a separate surface (US-006-02, US-006-04, US-006-05).
- Hard deletion. No input defines it, and archive is a flag, never a delete (BR-007).
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## Data model

Platform: PostgreSQL 16 (`platform.md`). Table and column names are proposals (`ASM-BE-014`, `ASM-BE-015`, `ASM-BE-016`).

### Table `notes`

| Column | Type | Null | Default | Constraint | Source |
|---|---|---|---|---|---|
| `id` | `uuid` | NOT NULL | generated | PRIMARY KEY | US-002-01 ("unique ID"); `ASM-BE-014` |
| `title` | `text` | NOT NULL | none | non-blank | BR-005; `ASM-BE-018` |
| `body` | `text` | NOT NULL | none | non-blank | BR-005; `ASM-BE-018` |
| `pinned` | `boolean` | NOT NULL | `false` | none | US-002-01; BR-018 |
| `status` | `text` | NOT NULL | `'active'` | CHECK in (`active`, `archived`) | BR-007; US-002-05; `ASM-BE-015` |
| `created_at` | `timestamptz` | NOT NULL | `now()` | none | US-002-01; `ASM-BE-016` |
| `updated_at` | `timestamptz` | NOT NULL | `now()` | none | US-002-02; `ASM-BE-016` |

### Table `note_tags`

| Column | Type | Null | Constraint | Source |
|---|---|---|---|---|
| `note_id` | `uuid` | NOT NULL | FOREIGN KEY to `notes.id`, `ON DELETE RESTRICT` (`ASM-BE-017`) | US-002-03 |
| `tag_name` | `text` | NOT NULL | non-empty (`ASM-BE-019`) | US-002-03 |

Primary key is (`note_id`, `tag_name`). This is what makes a repeated add create no duplicate entry (US-002-03).

### Indexes

| Index | Query served |
|---|---|
| `notes(id)` primary key | `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-810640`, `API-NOTES-917869` |
| `note_tags(note_id, tag_name)` primary key | Tag read for a note and the duplicate check |
| `note_tags(tag_name)` | Tag filter, consumed by the `search` module (US-003-02) |

Indexes serving search ordering and keyword lookup belong to the `search` module (project migration 006).

### Treatment notes
- Timestamps are `timestamptz`, stored in UTC (`ASM-BE-016`).
- No ownership column exists. It is pending `Q-BE-002` and is project migration 007.
- No input classifies note title or body as PII or sensitive, and none states at-rest encryption (`GAP-BE-012`).
- The retention job does not delete notes (NFR-019).
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**States:** `active`, `archived` (US-002-05; BR-007; `ASM-BE-015`). A new note starts `active`.

| From | Trigger | Guard | To | Illegal-transition result |
|---|---|---|---|---|
| (none) | `API-NOTES-232390` | valid payload | `active` | 400 if invalid |
| `active` | `API-NOTES-810640` | note exists | `archived` | n/a |
| `archived` | `API-NOTES-810640` | none | stays `archived` | 409 `ALREADY_ARCHIVED` (US-002-04; project error model) |
| `archived` | `API-NOTES-917869` | note exists | `active` | n/a |
| `active` | `API-NOTES-917869` | none | stays `active` | 200, note unchanged (`ASM-BE-020`) |

**Notes.**
- Edits and tag adds do not change `status`. They are allowed in both states (`ASM-BE-021`).
- No state is terminal. Archive is fully reversible with all data kept (BR-007, BR-008).
- No hard-delete transition exists. A request for an unknown note returns 404 `NOTE_NOT_FOUND` in every state.
- The user-facing archived/active indicator is a function of `status` alone. No input derives it from another field.
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**BE-NOTES-FR-001.** Given a valid title and body, when `API-NOTES-232390` is called, then the response is 201 with a unique ID, timestamps and `pinned=false`, and the note is persisted with `status=active`.
- Traces to. US-002-01, US-006-02; BR-005, BR-018; AC-002-01-01
- Verification. `notes_create_defaults` (planned)
- API. `API-NOTES-232390`

**BE-NOTES-FR-002.** Given a valid payload with `pinned=true`, when `API-NOTES-232390` is called, then the response is 201 and the stored note has `pinned=true`.
- Traces to. US-002-01; BR-018; AC-002-01-02
- Verification. `notes_create_pinned` (planned)
- API. `API-NOTES-232390`

**BE-NOTES-FR-003.** Given a payload with a missing, empty or null title or body, when `API-NOTES-232390` is called, then the response is 400 `VALIDATION_ERROR` and no note is persisted.
- Traces to. US-002-01, US-006-02; BR-005; AC-002-01-03
- Verification. `notes_create_validation` (planned)
- API. `API-NOTES-232390`

**BE-NOTES-FR-004.** Given an existing note and valid updated fields, when `API-NOTES-847551` is called, then the response is 200 with the updated note and a refreshed `updated_at`.
- Traces to. US-002-02, US-006-02
- Verification. `notes_patch_updates_fields` (planned)
- API. `API-NOTES-847551`

**BE-NOTES-FR-005.** Given a PATCH that contains only `pinned`, when it is submitted, then the response is 200, `pinned` is updated, and title, body and tags are unchanged.
- Traces to. US-002-02; BR-006, BR-018; AC-002-02-02
- Verification. `notes_patch_pinned_only` (planned)
- API. `API-NOTES-847551`

**BE-NOTES-FR-006.** Given any PATCH, when it is processed, then fields absent from the request are never overwritten.
- Traces to. BR-006
- Verification. `notes_patch_partial_semantics` (planned)
- API. `API-NOTES-847551`

**BE-NOTES-FR-007.** Given a non-existent note ID, when `API-NOTES-847551` is called, then the response is 404 `NOTE_NOT_FOUND` and no data is modified.
- Traces to. US-002-02; AC-002-02-03
- Verification. `notes_patch_not_found` (planned)
- API. `API-NOTES-847551`

**BE-NOTES-FR-008.** Given two concurrent PATCH requests to one note, when both are processed, then the result is last-write-wins, with no broadcast or merge logic.
- Traces to. US-005-03 (AC2)
- Verification. `notes_patch_concurrent_last_write` (planned)
- API. `API-NOTES-847551`

**BE-NOTES-FR-009.** Given an existing note and a tag name, when `API-NOTES-359413` is called, then the response is 200 and the note's tag list includes the tag.
- Traces to. US-002-03, US-006-04
- Verification. `notes_tag_add` (planned)
- API. `API-NOTES-359413`

**BE-NOTES-FR-010.** Given a tag already on the note, when the same tag is added again, then the response is 200 and no duplicate entry is created.
- Traces to. US-002-03, US-006-04
- Verification. `notes_tag_add_idempotent` (planned)
- API. `API-NOTES-359413`

**BE-NOTES-FR-011.** Given an unknown note, when `API-NOTES-359413` is called, then the response is 404 `NOTE_NOT_FOUND`. Given an empty tag name, then the response is 400 `VALIDATION_ERROR` and no tag is stored (`ASM-BE-022`). Further tag validity rules are open (`GAP-BE-010`).
- Traces to. US-002-03
- Verification. `notes_tag_add_errors` (planned)
- API. `API-NOTES-359413`

**BE-NOTES-FR-012.** Given an active, existing note, when `API-NOTES-810640` is called, then the response is 200, `status` is `archived`, and all note data and tags remain intact.
- Traces to. US-002-04, US-006-05; BR-007
- Verification. `notes_archive` (planned)
- API. `API-NOTES-810640`

**BE-NOTES-FR-013.** Given an already-archived note, when `API-NOTES-810640` is called, then the response is 409 `ALREADY_ARCHIVED`. Given an unknown note, then the response is 404 `NOTE_NOT_FOUND` (`ASM-BE-022`).
- Traces to. US-002-04
- Verification. `notes_archive_conflict` (planned)
- API. `API-NOTES-810640`

**BE-NOTES-FR-014.** Given an archived note, when `API-NOTES-917869` is called, then the response is 200, `status` is `active`, and all original data is preserved.
- Traces to. US-002-05, US-006-05; BR-008
- Verification. `notes_unarchive_restores` (planned)
- API. `API-NOTES-917869`

**BE-NOTES-FR-015.** Given a note ID that does not exist, when `API-NOTES-917869` is called, then the response is 404 `NOTE_NOT_FOUND`. Given an already-active note, then the response is 200 with the note unchanged (`ASM-BE-020`).
- Traces to. US-002-05
- Verification. `notes_unarchive_edge_cases` (planned)
- API. `API-NOTES-917869`

**BE-NOTES-FR-016.** Given an existing note ID, when `API-NOTES-045877` is called, then the response is 200 with full note details including tags, archive status and the pinned flag. Given an unknown ID, then the response is 404 `NOTE_NOT_FOUND` and no partial data is exposed.
- Traces to. US-002-06, US-006-02, US-006-05
- Verification. `notes_get` (planned)
- API. `API-NOTES-045877`

**BE-NOTES-FR-017.** Given a request whose path ID is not a valid uuid, when any operation with a note ID is called, then the response is 400 `VALIDATION_ERROR` (`ASM-BE-025`).
- Traces to. NFR-012 (request validation)
- Verification. `notes_path_id_validation` (planned)
- API. `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-810640`, `API-NOTES-917869`

**BE-NOTES-FR-018.** Given a multipart or file payload on create or edit, when the request is received, then the response is 415 `UNSUPPORTED_MEDIA_TYPE` and nothing is persisted. The rule is defined by `scope-guard` and only referenced here.
- Traces to. US-005-02
- Verification. `notes_rejects_multipart` (planned)
- API. `API-NOTES-232390`, `API-NOTES-847551`

**BE-NOTES-FR-019.** Given any create, edit, tag or archive operation, when title, body or tag input is processed, then it is validated and sanitised so injection input cannot alter queries.
- Traces to. NFR-012
- Verification. `notes_injection_inputs` (planned)
- API. `API-NOTES-232390`, `API-NOTES-847551`, `API-NOTES-359413`
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 415 | Create a note with a title and body; the service assigns a unique ID, timestamps and pinned=false by default (US-002-01, US-006-02; BR-005, BR-018). |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 404 | Get a single note by ID, including its pinned flag, status and tags (US-002-06, US-006-02, US-006-05). |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 404, 409, 415 | Edit a note's title, body and/or pinned flag; updates the updated_at timestamp (US-002-02, US-006-02; BR-006, BR-018). Concurrent PATCH gets last-write or conflict (US-005-03). |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 404, 409 | Archive a note by setting the archive flag; the note is kept, never deleted (US-002-04, US-006-05; BR-007). |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 404 | Add one or more tags to a note; a repeated add creates no duplicate entry on that note (US-002-03, US-006-04). Tag rules are open (GAP-BE-010). |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 404, 409 | Unarchive a note, returning it to active status (US-002-05, US-006-05; BR-008). |

### `API-NOTES-232390` — POST `/notes`

Create a note with a title and body; the service assigns a unique ID, timestamps and pinned=false by default (US-002-01, US-006-02; BR-005, BR-018).

**Request:** 
- `title` (string, required): Note title; required, missing or empty returns 400 (BR-005).
- `body` (string, required): Note body; required, missing or empty returns 400 (BR-005).
- `pinned` (boolean): Initial pinned flag; defaults to false when omitted (US-002-01, BR-018).
**Response:** 
- `id` (string, required): Unique note ID assigned by the service (US-002-01).
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Pinned flag; false by default.
- `status` (string, required): Active or archived status of the note (US-002-05, US-002-06); a new note is active.
- `tags` (array, required): Tags carried by the note; empty on creation.
- `created_at` (string, required): Creation timestamp (US-002-01).
- `updated_at` (string, required): Last-update timestamp (US-002-01).

### `API-NOTES-045877` — GET `/notes/{id}`

Get a single note by ID, including its pinned flag, status and tags (US-002-06, US-006-02, US-006-05).

**Request:** 
- `id` (string, required): Note ID (path parameter).
**Response:** 
- `id` (string, required): Note ID.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Pinned flag.
- `status` (string, required): Active or archived status.
- `tags` (array, required): Tags carried by the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last-update timestamp.

### `API-NOTES-847551` — PATCH `/notes/{id}`

Edit a note's title, body and/or pinned flag; updates the updated_at timestamp (US-002-02, US-006-02; BR-006, BR-018). Concurrent PATCH gets last-write or conflict (US-005-03).

**Request:** 
- `id` (string, required): Note ID (path parameter).
- `title` (string): New title; if supplied must not be empty.
- `body` (string): New body; if supplied must not be empty.
- `pinned` (boolean): New pinned flag value (BR-018).
**Response:** 
- `id` (string, required): Note ID.
- `title` (string, required): Note title after the edit.
- `body` (string, required): Note body after the edit.
- `pinned` (boolean, required): Pinned flag after the edit.
- `status` (string, required): Active or archived status.
- `tags` (array, required): Tags carried by the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Updated last-update timestamp.

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note by setting the archive flag; the note is kept, never deleted (US-002-04, US-006-05; BR-007).

**Request:** 
- `id` (string, required): Note ID (path parameter).
**Response:** 
- `id` (string, required): Note ID.
- `status` (string, required): Archived status after the operation.
- `updated_at` (string, required): Last-update timestamp.

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add one or more tags to a note; a repeated add creates no duplicate entry on that note (US-002-03, US-006-04). Tag rules are open (GAP-BE-010).

**Request:** 
- `id` (string, required): Note ID (path parameter).
- `tags` (array, required): Tag names to add to the note; names already on the note are not duplicated.
**Response:** 
- `id` (string, required): Note ID.
- `tags` (array, required): The note's full tag set after the add, without duplicates.
- `updated_at` (string, required): Last-update timestamp.

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Unarchive a note, returning it to active status (US-002-05, US-006-05; BR-008).

**Request:** 
- `id` (string, required): Note ID (path parameter).
**Response:** 
- `id` (string, required): Note ID.
- `status` (string, required): Active status after the operation.
- `updated_at` (string, required): Last-update timestamp.
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DERIVED -->
## Invariants

- **INV-BE-001.** Every note has a non-blank title and body at all times (BR-005).
- **INV-BE-002.** `pinned` is always a boolean and defaults to false (BR-018).
- **INV-BE-003.** `status` is always `active` or `archived`. Archiving and unarchiving change no other stored field except `updated_at` (BR-007, BR-008).
- **INV-BE-004.** No note row is deleted by any operation in this module (BR-007).
- **INV-BE-005.** A note carries each tag name at most once (US-002-03).
- **INV-BE-006.** A PATCH never changes a field absent from the request (BR-006).
- **INV-BE-007.** `updated_at` is never earlier than `created_at`, and it is refreshed by every successful change (`ASM-BE-016`).
- **INV-BE-008.** A failed operation (400, 404, 409, 415, 500) leaves note and tag state unchanged.
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: BLOCKED -->
## Authorization

**BLOCKED.** The role matrix and the ownership predicate cannot be written without inventing a rule.
- Note ownership (per user or shared team workspace) is undecided. BR-031 is a placeholder. This is `Q-BE-002`.
- Whether these operations require a credential at all depends on what a successful verify issues. This is `Q-BE-003`.
- Who may obtain a credential is `Q-BE-001`.

**What would unblock it.** Answers to `Q-BE-002` and `Q-BE-003`: the ownership decision, the access rule per operation, and whether a credential is required. Until then:
- This module defines no 401 or 403 for its operations.
- It adds no ownership column. Migration 007 is not scheduled.
- It makes no claim that notes are user-scoped or shared.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: DERIVED -->
## Jobs

- **Scheduled work.** None. Retention deletion in NFR-019 covers login records only. Notes are not deleted by retention, and archived notes follow BR-007 and BR-008.
- **Retries.** None specified by any input.
- **Idempotency.**
  - `API-NOTES-359413` is naturally idempotent: the primary key on (`note_id`, `tag_name`) means a repeat creates no duplicate (US-002-03).
  - `API-NOTES-917869` on an active note is a no-op (`ASM-BE-020`).
  - `API-NOTES-810640` on an archived note returns 409 and changes nothing.
  - `API-NOTES-232390` has no idempotency key defined by any input, so a repeat call creates a second note. A key requirement would be a new decision (`GAP-BE-020`).
  - `API-NOTES-847551` is last-write-wins (`ASM-BE-024`).
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit** (validation and rules)
- Blank, null and missing title or body rejected (FR-003).
- Partial-update preservation (FR-005, FR-006).
- Tag set dedupe (FR-010).
- State transitions, including 409 on double archive and no-op unarchive (FR-013, FR-015).
- Path id validation (FR-017).

**Integration** (PostgreSQL 16)
- Create, get, patch, tag, archive, unarchive round trip with no data loss, matching the 100% CRUD functional pass-rate target (NFR-005).
- Concurrent PATCH last-write (FR-008).
- Rollback leaves state unchanged on failure (INV-BE-008).
- `ON DELETE RESTRICT` blocks removal of a tagged note (`ASM-BE-017`).

**Contract**
- Every operation's documented success and error statuses match behaviour: 201, 200, 400, 404, 409, 415 (BR-012; NFR-008).
- The `search` module's pinned-first ordering is exercised against `pinned` values set by this module (BR-019).

Test names are listed per requirement above.
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: VERIFIED -->
## Gaps, contradictions and assumptions

### Gaps

| ID | Statement | Closing artefact |
|---|---|---|
| `GAP-BE-017` | No operation removes a tag from a note, although US-002-03 and US-006-04 require removal. The operations given to this module include only an add. | An operation record for tag removal, or a decision that removal is out of scope. |
| `GAP-BE-018` | No maximum number of tags per note, and no title or body length limit, is stated. This also affects project `GAP-BE-010`. | Limits from product. |
| `GAP-BE-019` | Whether a hard-delete operation is needed. No input defines one, and US-002-06 mentions a "deleted" note. | A story or decision on deletion. |
| `GAP-BE-020` | No idempotency key is defined for note creation, so a client retry can create a duplicate note. | A decision on create idempotency. |

Also open, defined in the project spec and referenced here: `GAP-BE-005` (no list operations), `GAP-BE-006` (no tag listing), `GAP-BE-010` (tag rules), `GAP-BE-012` (encryption and content redaction), `Q-BE-002` (ownership), `Q-BE-003` (credential).

### Contradictions

| ID | Statement | Reading adopted | Closing artefact |
|---|---|---|---|
| `CON-BE-005` | US-002-02 says "edit an existing note's title, body, or tags". `API-NOTES-847551` covers title, body and `pinned` only. Tags are changed by `API-NOTES-359413`, and removal is `GAP-BE-017`. | Tags are not edited through PATCH. Non-critical: the stored tag shape is the same either way. | A decision on whether PATCH should accept tags. |

### Assumptions

| ID | Assumption | What would confirm it |
|---|---|---|
| `ASM-BE-014` | `id` is a `uuid`. The stories say only "unique ID" (US-002-01). | An identifier decision. |
| `ASM-BE-015` | `status` is a text column constrained to `active` or `archived`, defaulting to `active`. BR-007 says "archive flag" and US-002-05 says "status changes to active". | A data-model decision. |
| `ASM-BE-016` | Timestamps are `timestamptz` in UTC. `updated_at` is refreshed on any change to title, body, `pinned`, the tag set or `status`. | A timestamp decision. |
| `ASM-BE-017` | `note_tags.note_id` references `notes.id` with `ON DELETE RESTRICT`, because no input defines hard delete. | `GAP-BE-019` closed. |
| `ASM-BE-018` | An empty or whitespace-only title or body is invalid. An unknown field, a non-boolean `pinned` or an empty PATCH body returns 400 `VALIDATION_ERROR`. | A validation decision. |
| `ASM-BE-019` | Tag names are non-empty and compared exactly as supplied: case-sensitive, with no trimming or normalisation. | Tag rules (`GAP-BE-010`). |
| `ASM-BE-020` | Unarchiving an already-active note returns 200 with the note unchanged. | A decision on the active-unarchive case. |
| `ASM-BE-021` | Edits and tag adds are allowed on archived notes. No input forbids them. | A decision on archived-note edits. |
| `ASM-BE-022` | Tag add returns 200 for a new or repeated tag, 404 for an unknown note and 400 for an empty tag name. Archive of an unknown note returns 404 by analogy with US-002-05. When one add request carries several tags, it is atomic and duplicates within it are stored once. | A decision on tag and archive error handling. |
| `ASM-BE-023` | A note's tags are returned in ascending order of name, so responses are deterministic. No input states an order. | A response-ordering decision. |
| `ASM-BE-024` | Concurrent edits are last-write-wins, with no version column (US-005-03 AC2 allows "last-write/conflict"). | A concurrency decision. |
| `ASM-BE-025` | A path ID that is not a valid uuid returns 400 `VALIDATION_ERROR`. This depends on `ASM-BE-014`. | An identifier decision. |
| `ASM-BE-026` | The tag-add request supports one or more tag names, as the operation description states. The exact field names are those in the operation record and are not restated here. | The operation record. |
<!-- /SECTION: backend.notes.gaps -->
