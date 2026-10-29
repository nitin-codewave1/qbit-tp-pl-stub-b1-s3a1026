---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.8.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06, notes_api:US-006-02, notes_api:US-006-04, notes_api:US-006-05]
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
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

`blocked_by` cites `Q-BE-002` defined in the project spec (`backend.auth-model`). It blocks only `backend.notes.authorization`.
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: VERIFIED -->
## Purpose

**Responsible for** (`backend.module-map`; US-002-01 to US-002-06, US-006-02, US-006-04, US-006-05; BR-005 to BR-008, BR-018):
- Creating, reading and editing notes (title, body, `pinned`).
- Adding tags to a note without duplicates.
- Archiving and unarchiving a note. Archive is a flag and never deletes data (BR-007, BR-008).

**Not responsible for:**
- Search, keyword or tag filtering, pagination, pinned-first ordering. Owned by `search`.
- Listing active notes, archived-only notes or distinct tags. No story defines these operations (project-spec `GAP-BE-005`, `GAP-BE-006`).
- Login codes, lockout and sessions. Owned by `auth`.
- Refusal of attachment, real-time and mobile calls, including the 415 on multipart payloads (US-005-02). Owned by `scope-guard`.
- The OpenAPI document. Owned by `contract-docs`.
- The web_app editor screens (US-006-02, US-006-04, US-006-05). Only the backend side is here.
- Any hard-delete operation. No input defines one.
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## Data model

PostgreSQL 16 (`platform.md`), EU region (BR-001).

### `notes`

| Column | Type | Null | Default | Constraint | Source |
|---|---|---|---|---|---|
| `id` | `uuid` | NOT NULL | generated | PRIMARY KEY | US-002-01 "unique ID"; `ASM-BE-001` |
| `title` | `text` | NOT NULL | none | non-blank check | BR-005; `ASM-BE-005` |
| `body` | `text` | NOT NULL | none | non-blank check | BR-005; `ASM-BE-005` |
| `pinned` | `boolean` | NOT NULL | `false` | none | US-002-01, BR-018 |
| `status` | `text` | NOT NULL | `'active'` | CHECK in (`active`, `archived`) | BR-007, US-002-05; `ASM-BE-002` |
| `created_at` | `timestamptz` | NOT NULL | now (UTC) | none | US-002-01; `ASM-BE-003` |
| `updated_at` | `timestamptz` | NOT NULL | now (UTC) | `updated_at >= created_at` | US-002-01, AC-002-02-01; `ASM-BE-003` |

### `note_tags`

| Column | Type | Null | Constraint | Source |
|---|---|---|---|---|
| `note_id` | `uuid` | NOT NULL | FK → `notes.id`, `ON DELETE RESTRICT` | `ASM-BE-004` |
| `tag_name` | `text` | NOT NULL | non-empty check; PRIMARY KEY (`note_id`, `tag_name`) | US-002-03; `ASM-BE-006` |

**Indexes**
- `notes(id)` primary key serves every get, patch, tag-add, archive and unarchive by id.
- `note_tags(note_id, tag_name)` primary key serves "tags of a note" and the duplicate check on add.
- Indexes serving search (keyword, tag lookup, status and pinned ordering) are owned by `search`, not here.

**Open**
- Maximum lengths of title and body are unstated (`GAP-BE-005`).
- Tag charset, case and maximum count are unstated (project-spec `GAP-BE-010`).
- Ownership columns are not defined (blocked by `Q-BE-002`). No owner column is asserted.
- No PII column is defined by any input for this module.
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

States of `notes.status`: `active`, `archived` (`ASM-BE-002`).

| From | Trigger | Guard | To | Illegal-transition result |
|---|---|---|---|---|
| (none) | create (`API-NOTES-232390`) | valid title and body | `active` | 400 on invalid input |
| `active` | archive (`API-NOTES-810640`) | note exists | `archived` | 404 if unknown |
| `archived` | archive (`API-NOTES-810640`) | none | stays `archived` | 409 "already archived" (AC of US-002-04) |
| `archived` | unarchive (`API-NOTES-917869`) | note exists | `active` | 404 if unknown (US-002-05) |
| `active` | unarchive (`API-NOTES-917869`) | none | stays `active` | 200, note unchanged (`ASM-BE-007`) |

- **Terminal states:** none. Every state is reversible (BR-008).
- Edit (`API-NOTES-847551`) and tag add (`API-NOTES-359413`) do not change `status`. They are permitted in either state (`ASM-BE-008`).
- Machine-readable error codes for 404 and 409 are open (project-spec `GAP-BE-011`). Only HTTP statuses are asserted here.
- Pinned is an independent boolean and is not a state.
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**BE-NOTES-FR-001**
- Given a valid title and body, when `API-NOTES-232390` is called, then the response is 201 with the created note including a unique id, timestamps, `pinned=false` and `status=active`.
- **Traces to.** US-002-01, AC-002-01-01, US-006-02, BR-005.
- **Verification.** `notes.create.valid_returns_201_defaults` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-002**
- Given a valid payload with `pinned=true`, when `API-NOTES-232390` is called, then the response is 201 and the stored note has `pinned=true`.
- **Traces to.** US-002-01, AC-002-01-02, BR-018.
- **Verification.** `notes.create.pinned_true_persisted` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-003**
- Given a payload with a missing or empty title or body, when `API-NOTES-232390` is called, then the response is 400 with a validation error and no note is persisted.
- **Traces to.** US-002-01, AC-002-01-03, BR-005.
- **Verification.** `notes.create.missing_title_returns_400_no_row` (planned).
- **API.** `API-NOTES-232390`.

**BE-NOTES-FR-004**
- Given a create payload that carries tags, when `API-NOTES-232390` is called, then the tags are stored on the note, a tag repeated within the request is stored once, and the response lists the tags.
- **Traces to.** US-002-01 (operation record: optional tags), US-003-02 context, US-002-03.
- **Verification.** `notes.create.tags_deduplicated` (planned).
- **API.** `API-NOTES-232390`. Assumption `ASM-BE-009`.

**BE-NOTES-FR-005**
- Given an existing note and valid updated fields, when `API-NOTES-847551` is called, then the response is 200 with the updated note and a refreshed `updated_at`.
- **Traces to.** US-002-02, AC-002-02-01, US-006-02.
- **Verification.** `notes.patch.valid_returns_200_updated_at` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-006**
- Given a PATCH that contains only `pinned`, when it is submitted, then the response is 200, `pinned` is updated, and title, body and tags are unchanged. Fields not supplied are never overwritten.
- **Traces to.** US-002-02, AC-002-02-02, BR-006, BR-018.
- **Verification.** `notes.patch.pinned_only_preserves_other_fields` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-007**
- Given a non-existent note id, when `API-NOTES-847551` is called, then the response is 404 and no data is modified.
- **Traces to.** US-002-02, AC-002-02-03.
- **Verification.** `notes.patch.unknown_id_returns_404` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-008**
- Given a PATCH with an empty title or body, an unknown field, a non-boolean `pinned`, or an empty body, when it is submitted, then the response is 400 and the note is unchanged.
- **Traces to.** BR-005, BR-006, NFR-012. Assumption `ASM-BE-005`.
- **Verification.** `notes.patch.invalid_input_returns_400_unchanged` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-009**
- Given two concurrent PATCH requests to one note, when both are processed, then each applies atomically and the later write wins, with no broadcast or merge logic.
- **Traces to.** US-005-03 (second criterion; the scope boundary itself is owned by `scope-guard`). Assumption `ASM-BE-010`.
- **Verification.** `notes.patch.concurrent_last_write_wins` (planned).
- **API.** `API-NOTES-847551`.

**BE-NOTES-FR-010**
- Given an existing note and a tag name, when a tag is added, then the response is 200 and the note's tag list includes the tag.
- **Traces to.** US-002-03, US-006-04.
- **Verification.** `notes.tags.add_returns_200_with_tag` (planned).
- **API.** `API-NOTES-359413`.

**BE-NOTES-FR-011**
- Given a tag already on the note, when the same tag is added again, then the response is 200 and no duplicate entry exists.
- **Traces to.** US-002-03, US-006-04.
- **Verification.** `notes.tags.repeat_add_no_duplicate` (planned).
- **API.** `API-NOTES-359413`.

**BE-NOTES-FR-012**
- Given an unknown note id or an empty tag name, when a tag is added, then the response is 404 or 400 respectively and nothing is stored.
- **Traces to.** US-002-03. Assumption `ASM-BE-006`.
- **Verification.** `notes.tags.unknown_note_404_empty_tag_400` (planned).
- **API.** `API-NOTES-359413`.

**BE-NOTES-FR-013**
- Given an active existing note, when `API-NOTES-810640` is called, then the response is 200, the note is marked archived, and all its data (title, body, tags, pinned) remains intact.
- **Traces to.** US-002-04, US-006-05, BR-007.
- **Verification.** `notes.archive.active_returns_200_data_intact` (planned).
- **API.** `API-NOTES-810640`.

**BE-NOTES-FR-014**
- Given an already-archived note, when `API-NOTES-810640` is called, then the response is a conflict (409) indicating it is already archived.
- **Traces to.** US-002-04.
- **Verification.** `notes.archive.already_archived_returns_409` (planned).
- **API.** `API-NOTES-810640`.

**BE-NOTES-FR-015**
- Given an archived note, when `API-NOTES-917869` is called, then the response is 200, the status is active, and all original data is preserved.
- **Traces to.** US-002-05, US-006-05, BR-008.
- **Verification.** `notes.unarchive.restores_full_state` (planned).
- **API.** `API-NOTES-917869`.

**BE-NOTES-FR-016**
- Given a note id that does not exist, when `API-NOTES-917869` or `API-NOTES-810640` is called, then the response is 404.
- **Traces to.** US-002-05; archive by analogy (`ASM-BE-007`).
- **Verification.** `notes.archive_unarchive.unknown_id_returns_404` (planned).
- **API.** `API-NOTES-917869`, `API-NOTES-810640`.

**BE-NOTES-FR-017**
- Given an existing note id, when `API-NOTES-045877` is called, then the response is 200 with full details including tags, archive status and the pinned flag.
- **Traces to.** US-002-06, US-006-02, US-006-05.
- **Verification.** `notes.get.existing_returns_full_note` (planned).
- **API.** `API-NOTES-045877`.

**BE-NOTES-FR-018**
- Given an id for a deleted or never-existing note, when `API-NOTES-045877` is called, then the response is 404 with no partial data.
- **Traces to.** US-002-06.
- **Verification.** `notes.get.unknown_returns_404_no_data` (planned).
- **API.** `API-NOTES-045877`.

**BE-NOTES-FR-019**
- Given a path id that is not a valid uuid, when any note operation is called, then the response is 400.
- **Traces to.** Assumption `ASM-BE-001`.
- **Verification.** `notes.any.malformed_id_returns_400` (planned).
- **API.** all six operations.
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 415 | Create a note with title, body and optional tags; assigns unique id, timestamps, pinned=false and status active (US-002-01, US-006-02). |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 404 | Get one note including status, pinned flag and tags (US-002-02, US-002-06, US-006-02). |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 404, 415 | Edit a note's title, body and/or pinned flag; pin and unpin are done here (US-002-02, BR-018, US-006-02). |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 404, 409 | Archive a note by setting its archived flag; never deletes (US-002-04, US-006-05, BR-007). |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 404 | Add a tag to a note; repeating the add creates no duplicate (US-002-03, US-006-04). |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 404 | Restore an archived note to active (US-002-05, US-006-05, BR-008). |

### `API-NOTES-232390` — POST `/notes`

Create a note with title, body and optional tags; assigns unique id, timestamps, pinned=false and status active (US-002-01, US-006-02).

**Request:** 
- `title` (string, required): Note title; required (BR-005).
- `body` (string, required): Note body; required (BR-005).
- `tags` (array): Tag names to attach; tag rules open (GAP-BE-010).
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Pinned flag; false by default.
- `status` (string, required): active or archived.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-045877` — GET `/notes/{id}`

Get one note including status, pinned flag and tags (US-002-02, US-002-06, US-006-02).

**Request:** 
- `id` (string, required): Note id (path parameter).
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Pinned flag.
- `status` (string, required): active or archived.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-847551` — PATCH `/notes/{id}`

Edit a note's title, body and/or pinned flag; pin and unpin are done here (US-002-02, BR-018, US-006-02).

**Request:** 
- `id` (string, required): Note id (path parameter).
- `title` (string): New title; must not be empty if sent.
- `body` (string): New body; must not be empty if sent.
- `pinned` (boolean): Set or clear the pinned flag.
**Response:** 
- `id` (string, required): Unique note id.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `pinned` (boolean, required): Pinned flag.
- `status` (string, required): active or archived.
- `tags` (array, required): Tag names on the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Updated timestamp.

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note by setting its archived flag; never deletes (US-002-04, US-006-05, BR-007).

**Request:** 
- `id` (string, required): Note id (path parameter).
**Response:** 
- `id` (string, required): Note id.
- `status` (string, required): archived.
- `updated_at` (string, required): Updated timestamp.

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add a tag to a note; repeating the add creates no duplicate (US-002-03, US-006-04).

**Request:** 
- `id` (string, required): Note id (path parameter).
- `tag` (string, required): Tag name to add; rules open (GAP-BE-010).
**Response:** 
- `id` (string, required): Note id.
- `tags` (array, required): Tag names on the note after the add, without duplicates.
- `updated_at` (string, required): Updated timestamp.

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Restore an archived note to active (US-002-05, US-006-05, BR-008).

**Request:** 
- `id` (string, required): Note id (path parameter).
**Response:** 
- `id` (string, required): Note id.
- `status` (string, required): active.
- `updated_at` (string, required): Updated timestamp.
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DERIVED -->
## Invariants

- **INV-BE-001.** A note `id` is unique and never changes. (US-002-01; `ASM-BE-001`)
- **INV-BE-002.** Every stored note has a non-blank title and body. (BR-005)
- **INV-BE-003.** A (`note_id`, `tag_name`) pair exists at most once. (US-002-03)
- **INV-BE-004.** Archive and unarchive never delete or alter title, body, tags or `pinned`; only `status` and `updated_at` change. (BR-007, BR-008)
- **INV-BE-005.** An edit changes only the fields the request supplies. (BR-006, AC-002-02-02)
- **INV-BE-006.** `status` is `active` or `archived`; `pinned` is a non-null boolean. (BR-007, BR-018)
- **INV-BE-007.** `updated_at >= created_at`, and `updated_at` is refreshed by any change to title, body, `pinned`, tags or `status`. (US-002-01, AC-002-02-01; `ASM-BE-003`)
- **INV-BE-008.** A failed operation (400, 404, 409) persists no change. (AC-002-01-03, AC-002-02-03)

Derivation: each invariant restates the cited rule as a post-condition on every operation.
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: BLOCKED -->
## Authorization

**Blocked.** The roles, the permission matrix and the ownership predicate cannot be written. Whether a note belongs to one user or to the shared team workspace, and the access rule for each note operation, is the blocking decision `Q-BE-002` (project spec, `backend.auth-model`). The session model, `Q-BE-003`, also decides whether these operations require a credential at all.

**What would unblock it**
- A decision on per-user ownership versus shared workspace, with the access rule per operation.
- A decision on the credential issued by verify, and whether note operations require it.

**Rows to fill once unblocked.** All six operations: `API-NOTES-232390`, `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-810640`, `API-NOTES-917869`.

No role is named, because no input defines one.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: VERIFIED -->
## Jobs

- **Scheduled work:** none. NFR-019 states that retention never deletes notes; archived notes follow BR-007 and BR-008. The login-record purge belongs to `auth`.
- **Sweeps and retries:** none defined by any input.
- **Idempotency keys:** none defined by any input.
- **Idempotency by behavior**
  - Tag add is idempotent: a repeat returns 200 with no duplicate (US-002-03).
  - Archive of an archived note is not idempotent: it returns 409 (US-002-04).
  - Create is not idempotent. No deduplication key is defined.
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**
- Title and body validation: missing, empty, whitespace-only (FR-003, FR-008).
- Partial-update merge preserves unsupplied fields (FR-006).
- Tag deduplication within a request and against stored tags (FR-004, FR-011).
- State transitions and guards per `backend.notes.state-machine` (FR-013 to FR-016).

**Integration** (PostgreSQL 16)
- Full lifecycle: create, edit, tag, archive, unarchive, get; all data intact at the end (FR-001, FR-005, FR-010, FR-013, FR-015, FR-017; INV-BE-004).
- Failure paths persist nothing: 400, 404, 409 (INV-BE-008).
- Concurrent PATCH on one note (FR-009).
- Composite primary key rejects a duplicate tag row (INV-BE-003).
- `updated_at` refresh on each change type (INV-BE-007).

**Contract**
- Each response of the six operations validates against the published schema for its id: `API-NOTES-232390`, `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-810640`, `API-NOTES-917869`.
- Every status asserted in `backend.notes.requirements` appears in the operation's documented responses (BR-012).
- Multipart payloads on note endpoints are covered by the `scope-guard` suite, not here.

All test names above and in the requirements are proposals.
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: VERIFIED -->
## Gaps

**Id collision notice (`PROMPT-CONFLICT-BE-001`).** The reserved id block for this module starts at `ASM-BE-001`, `CON-BE-001`, `GAP-BE-002`, `INV-BE-001` and `Q-BE-001`. The project spec already defines items under some of the same numbers. Ids below are module-local to `notes`. Project-spec items are always cited with the words "project spec". The orchestrator should renumber one side.

### Gaps

- **GAP-BE-002.** No operation removes a tag, although US-002-03 and US-006-04 say "add or remove tags".
  - Degrades: `backend.notes.requirements`.
  - Done instead: only add is specified.
  - Closing artefact: a decision to add a remove-tag operation, with its operation record.
- **GAP-BE-003.** US-002-02 says a note's tags may be edited via PUT/PATCH, but the PATCH operation record covers title, body and `pinned` only, and AC-002-02-02 says tags stay unchanged on a pinned-only PATCH.
  - Degrades: FR-005.
  - Done instead: tags change only through the tag-add operation.
  - Closing artefact: a decision on whether PATCH accepts tags.
- **GAP-BE-004.** The `pinned` flag appears on create, patch and get, but no story states whether archiving clears it.
  - Done instead: archive leaves `pinned` unchanged (INV-BE-004).
  - Closing artefact: a one-line rule from the product owner.
- **GAP-BE-005.** Maximum lengths of title and body, and the maximum number of tags per note, are unstated.
  - Done instead: none are enforced beyond non-blank.
  - Closing artefact: constraints from the requirements owner (US-004-02 says constraints such as title length are documented, but gives none).

### Contradictions

- None raised. Checked: story ↔ design (the brief repeats the stories), story ↔ requirement, requirement ↔ requirement, design ↔ design.

### Assumptions

- **ASM-BE-001.** `id` is a `uuid`; a path id that is not a valid uuid returns 400. Basis: US-002-01 says only "unique ID". Confirm with an identifier decision.
- **ASM-BE-002.** `status` is a text column constrained to `active` or `archived`, default `active`. Basis: BR-007 says "archive flag" and US-002-05 says "status changes to active". Confirm with a data-model decision.
- **ASM-BE-003.** Timestamps are `timestamptz` in UTC. `updated_at` is refreshed on any change to title, body, `pinned`, tags or `status`. Confirm with a timestamp decision.
- **ASM-BE-004.** `note_tags.note_id` references `notes.id` with `ON DELETE RESTRICT`, because no input defines hard delete.
- **ASM-BE-005.** A blank or whitespace-only title or body is invalid. An unknown field, a non-boolean `pinned` or an empty PATCH body returns 400. Basis: BR-005, NFR-012. Confirm with a validation decision.
- **ASM-BE-006.** Tag names are non-empty and compared exactly as supplied (case-sensitive, no trimming). An empty tag name returns 400 and an unknown note returns 404. Depends on project-spec `GAP-BE-010`.
- **ASM-BE-007.** Unarchiving an already-active note returns 200 with the note unchanged. Archive of an unknown note returns 404 by analogy with US-002-05. Confirm with a product decision.
- **ASM-BE-008.** Edits and tag adds are allowed on archived notes. No input forbids them. Confirm with a product decision.
- **ASM-BE-009.** The create payload accepts an optional list of tags, as the operation record states. Duplicates within one request are stored once, and one request is atomic.
- **ASM-BE-010.** Concurrent edits are last-write-wins with no version column. Basis: US-005-03 allows "last-write/conflict". Confirm with a concurrency decision.
- **ASM-BE-011.** A note's tags are returned in ascending order of name so responses are deterministic. No input states an order.

### Carried from the project spec (not minted here)

Project-spec `Q-BE-002` (ownership, blocking), `Q-BE-003` (session model, blocking), `GAP-BE-005` and `GAP-BE-006` (missing list and distinct-tag operations), `GAP-BE-010` (tag rules), and `GAP-BE-011` (error envelope and code names).
<!-- /SECTION: backend.notes.gaps -->
