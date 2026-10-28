---
id: agent-developer/notes_api/backend/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/notes-SPEC.md
depends_on: [notes_api:US-002-01, notes_api:US-002-02, notes_api:US-002-03, notes_api:US-002-04, notes_api:US-002-05, notes_api:US-002-06]
commit_kind: fork
confidence: agent
run_id: cbf4f6bb-f33c-5c3c-8b4c-5b95b8794990
okf_id: notes_api:backend/modules/notes-SPEC.md
---

<!-- SECTION: backend.notes.frontmatter | status: DERIVED -->
```yaml
id: notes
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [CON-BE-003, Q-BE-003, CON-BE-300, Q-BE-300, Q-BE-012]
scope: [API-NOTES-232390, API-NOTES-045877, API-NOTES-847551, API-NOTES-359413, API-NOTES-150096, API-NOTES-810640, API-NOTES-917869]
```
<!-- /SECTION: backend.notes.frontmatter -->
<!-- SECTION: backend.notes.purpose | status: DRAFT-UNVERIFIED -->
## 1. Purpose

**Responsibility.** The `notes` module owns the note lifecycle. [backend.module-map; stories.md EP-002]

1. Create a note with a mandatory title and body. `pinned` defaults to false. Operation `API-NOTES-232390`. [stories.md US-002-01; requirements.md BR-005, BR-018]
2. Read one note by id, including tags, archive status and the pinned flag. Operation `API-NOTES-045877`. [stories.md US-002-06]
3. Partially edit a note, including pinning and unpinning through `pinned`, without overwriting any field that was not sent. Operation `API-NOTES-847551`. [stories.md US-002-02; requirements.md BR-006, BR-018]
4. Add and remove tags. Adding an existing tag creates no duplicate. Operations `API-NOTES-359413` and `API-NOTES-150096`. [stories.md US-002-03]
5. Archive and unarchive a note. Archiving is a status change that never deletes data and is fully reversible. Operations `API-NOTES-810640` and `API-NOTES-917869`. [stories.md US-002-04, US-002-05; requirements.md BR-007, BR-008]
6. Own the NOTE, TAG and NOTE_TAG data. [backend.module-map]
7. Own the NFR-019 retention deletion of notes. That part is `BLOCKED` on `CON-BE-300` and `Q-BE-300` (BE-NOTES-FR-021). [backend.module-map; requirements.md NFR-019]

**Not responsible for.**

- Keyword and tag search, pagination, the archived filter and pinned-first ordering. `search` owns them and only reads `notes` data. [backend.module-map; requirements.md BR-009 to BR-011, BR-019]
- Sign-in, the credential, and user creation. `auth` owns them (`Q-BE-012`).
- The note access rule and note ownership. They are `BLOCKED` on `CON-BE-003` and `Q-BE-003`.
- The OpenAPI document, example payloads and the global error envelope. `api-docs` and `backend.error-model` own them.
- Rejection of attachment, real-time and mobile-specific calls. `scope-guardrail` owns it.
- Screens for listing, editing, tagging and archiving. The web-app surface owns them. [stories.md US-006-01 to US-006-05]
- Any note-deletion operation. None is in scope (`ASM-BE-565`).

**Operations owned.** Seven, referenced by id only: `API-NOTES-232390`, `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096`, `API-NOTES-810640`, `API-NOTES-917869`.

**Identifier note.** This module mints from its reserved block: `ASM-BE-553` onward, `GAP-BE-564` onward, `INV-BE-062` onward, `Q-BE-351` onward. It mints no `CON-` item. All other ids are cited from the project spec.
<!-- /SECTION: backend.notes.purpose -->
<!-- SECTION: backend.notes.data-model | status: DRAFT-UNVERIFIED -->
## 2. Data model

No input states column types, lengths or constraints. The platform is PostgreSQL 16. [platform.md] The types below are a proposal (`ASM-BE-553`). Entity names follow `backend.domain-model`.

**notes**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `id` | uuid | NOT NULL | server-generated | primary key; never client-supplied (`ASM-BE-555`) |
| `title` | text | NOT NULL | none | `CHECK (length(btrim(title)) > 0)` (`ASM-BE-556`) |
| `body` | text | NOT NULL | none | `CHECK (length(btrim(body)) > 0)` (`ASM-BE-556`) |
| `status` | text | NOT NULL | `'active'` | `CHECK (status IN ('active','archived'))` (`ASM-BE-554`) |
| `pinned` | boolean | NOT NULL | `false` | BR-018 gives a default and no null state (`ASM-BE-034`) |
| `created_at` | timestamptz | NOT NULL | set at insert | never updated after insert |
| `updated_at` | timestamptz | NOT NULL | set at insert | `CHECK (updated_at >= created_at)` |

No `owner` column is defined. It is `BLOCKED` on `CON-BE-003` and `Q-BE-003`, because no input relates a note to a user (`GAP-BE-003`). Maximum lengths of `title` and `body` are not stated (`GAP-BE-035`), so none are set.

**tags**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `name` | text | NOT NULL | none | primary key; compared exactly (`ASM-BE-567`) |

Name format, length and case rules are `GAP-BE-035` and `GAP-BE-571`.

**note_tags**

| Column | Type | Null | Default | Constraint |
|---|---|---|---|---|
| `note_id` | uuid | NOT NULL | none | FK → `notes(id)` `ON DELETE CASCADE` (`ASM-BE-566`) |
| `tag_name` | text | NOT NULL | none | FK → `tags(name)` `ON DELETE RESTRICT` |

Primary key `(note_id, tag_name)`. This key is what prevents a duplicate tag entry on a note. [stories.md US-002-03]

**Indexes.**

| Index | Query it serves |
|---|---|
| primary key on `notes(id)` | get, patch, tag, archive and unarchive by id |
| primary key on `note_tags(note_id, tag_name)` | list the tags of one note; duplicate prevention |
| btree on `note_tags(tag_name, note_id)` | notes carrying a given tag, read by `search` for tag filters |

Indexes serving keyword search and the 300 ms SLA belong to `search` (migration 003 in `backend.migration-plan`). They are not defined here.

**Time.** Every instant is `timestamptz`, stored as a UTC instant (`ASM-BE-553`).

**Encryption and PII.** Note content may be sensitive. No input states encryption at rest, so none is claimed (`GAP-BE-304`, `GAP-BE-033`). No note field is declared PII by any input. The handling policy is `GAP-BE-017`.

**Migrations.** `notes`, `tags` and `note_tags` are created by migration 001 `notes-core` in `backend.migration-plan`. This module adds no migration. The retention deletion of notes is migration 006 and is `BLOCKED` on `CON-BE-300`.
<!-- /SECTION: backend.notes.data-model -->
<!-- SECTION: backend.notes.state-machine | status: DRAFT-UNVERIFIED -->
## 3. State machine

The only status column is `notes.status`. States: `active` (initial, `ASM-BE-554`) and `archived`. **Neither state is terminal**, because archiving must always be reversible. [requirements.md BR-008]

| From | To | Trigger | Guard | Effect |
|---|---|---|---|---|
| none | `active` | `API-NOTES-232390` | title and body valid | row inserted; `pinned` as supplied or false |
| `active` | `archived` | `API-NOTES-810640` | note exists | `status` and `updated_at` change; nothing else |
| `archived` | `active` | `API-NOTES-917869` | note exists | `status` and `updated_at` change; all original data preserved |
| `archived` | `archived` | `API-NOTES-810640` | | refused as conflict (`ALREADY_ARCHIVED`, status `GAP-BE-309`); no data modified |
| `active` | `active` | `API-NOTES-917869` | | refused as the same conflict (`ASM-BE-559`); no data modified |
| `active` or `archived` | same | `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096` | note exists | status unchanged. Behaviour on an archived note is `ASM-BE-558`. |

**Illegal transitions.** Archive of an archived note returns the conflict response. [stories.md US-002-04] Unarchive of an active note follows `ASM-BE-559`. The codes are in `backend.error-model`.

**Deletion.** The inputs describe one deletion: the NFR-019 retention deletion of notes. It would add a terminal state to a lifecycle that BR-007, BR-008 and NFR-005 define as non-destructive. It is therefore not defined here and is `BLOCKED` on `CON-BE-300` and `Q-BE-300`.

**Badge derivation.** The archived/active indicator is a function of `status` alone. No other field participates. [stories.md US-006-01 shows an indicator and no input derives it from anything else]
<!-- /SECTION: backend.notes.state-machine -->
<!-- SECTION: backend.notes.requirements | status: DRAFT-UNVERIFIED -->
## 4. Requirements

### BE-NOTES-FR-001 Create a note
**Given** a valid title and body,
**When** `API-NOTES-232390` is called,
**Then** the note is persisted with a unique server-assigned id, `created_at` and `updated_at` set, `status` `active`, and `pinned` false, and the response is 201 with the created note.
- **Traces to.** US-002-01; AC-002-01-01; BR-005, BR-018.
- **Verification.** `test-notes-create-defaults` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-002 Create a pinned note
**Given** a valid payload with `pinned` true,
**When** `API-NOTES-232390` is called,
**Then** the response is 201 and the stored note has `pinned` true.
- **Traces to.** US-002-01; AC-002-01-02; BR-018.
- **Verification.** `test-notes-create-pinned` (planned).
- **API.** `API-NOTES-232390`.

### BE-NOTES-FR-003 Reject an invalid create
**Given** a payload with a missing, null or empty title or body (whitespace-only counts as empty, `ASM-BE-556`),
**When** `API-NOTES-232390` is called,
**Then** the response is 400 with `VALIDATION_ERROR` and no note is persisted.
- **Traces to.** US-002-01; AC-002-01-03; BR-005.
- **Verification.** `test-notes-create-missing-title`, `test-notes-create-missing-body`, `test-notes-create-whitespace-only` (planned).
- **API.** `API-NOTES-232390`. Field limits are `GAP-BE-035`.

### BE-NOTES-FR-004 Read a note
**Given** an existing note id,
**When** `API-NOTES-045877` is called,
**Then** the response is 200 with full note details including tags, archive status and the pinned flag.
- **Traces to.** US-002-06.
- **Verification.** `test-notes-get-existing` (planned).
- **API.** `API-NOTES-045877`.

### BE-NOTES-FR-005 Read an unknown note
**Given** an id that was never assigned to a note,
**When** `API-NOTES-045877` is called,
**Then** the response is 404 `NOT_FOUND` and no partial data is exposed. "Deleted" in the source story is treated as never-existing (`ASM-BE-565`). A malformed id is `GAP-BE-572`.
- **Traces to.** US-002-06.
- **Verification.** `test-notes-get-unknown-404` (planned).
- **API.** `API-NOTES-045877`.

### BE-NOTES-FR-006 Partial edit
**Given** an existing note and valid updated fields,
**When** `API-NOTES-847551` is called,
**Then** the response is 200 with the updated note, `updated_at` is set to the time of the change (`ASM-BE-562`), and every field absent from the request is unchanged.
- **Traces to.** US-002-02; AC-002-02-01; BR-006.
- **Verification.** `test-notes-patch-partial-preserves-fields` (planned).
- **API.** `API-NOTES-847551`. Whether the body may carry tags is `GAP-BE-564`. PUT versus PATCH is `GAP-BE-309`.

### BE-NOTES-FR-007 Pin and unpin
**Given** a PATCH containing only `pinned`,
**When** `API-NOTES-847551` is called,
**Then** the response is 200, `pinned` is updated, and title, body and tags are unchanged.
- **Traces to.** US-002-02; AC-002-02-02; BR-018.
- **Verification.** `test-notes-patch-pinned-only` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-008 Edit an unknown note
**Given** a non-existent note id,
**When** `API-NOTES-847551` is called,
**Then** the response is 404 `NOT_FOUND` and no data is modified.
- **Traces to.** US-002-02; AC-002-02-03.
- **Verification.** `test-notes-patch-unknown-404` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-009 Reject an invalid edit
**Given** an existing note and a PATCH that sets title or body to null, empty or whitespace-only (`ASM-BE-557`), or sets `pinned` to a non-boolean,
**When** `API-NOTES-847551` is called,
**Then** the response is 400 `VALIDATION_ERROR` and the note is unchanged.
- **Traces to.** BR-005 (extended to edits by `ASM-BE-557`); US-002-02; NFR-005. Not cited as an edit rule in any input.
- **Verification.** `test-notes-patch-empty-title-rejected`, `test-notes-patch-invalid-pinned` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-010 Add a tag
**Given** an existing note and a tag name,
**When** `API-NOTES-359413` is called,
**Then** the response is 200 and the note's tag list includes the tag.
- **Traces to.** US-002-03.
- **Verification.** `test-notes-tag-add` (planned).
- **API.** `API-NOTES-359413`. Name rules are `GAP-BE-035` and `GAP-BE-571`.

### BE-NOTES-FR-011 Add an existing tag
**Given** a note that already carries the tag,
**When** `API-NOTES-359413` is called with the same name,
**Then** the response is 200, no duplicate entry is created, and `updated_at` is unchanged (`ASM-BE-562`).
- **Traces to.** US-002-03; AC for the same story.
- **Verification.** `test-notes-tag-add-idempotent` (planned).
- **API.** `API-NOTES-359413`.

### BE-NOTES-FR-012 Remove a tag
**Given** an existing note that carries the tag,
**When** `API-NOTES-150096` is called,
**Then** the tag is no longer on the note, and title, body, pinned and status are unchanged.
- **Given** the tag is not on the note, **Then** the response is 404 `NOT_FOUND` (`ASM-BE-560`, `Q-BE-353`).
- **Traces to.** US-002-03; US-006-04 (consumer need). The success status and the absent-tag case are not stated in an input (`GAP-BE-565`, `GAP-BE-573`).
- **Verification.** `test-notes-tag-remove`, `test-notes-tag-remove-absent` (planned).
- **API.** `API-NOTES-150096`.

### BE-NOTES-FR-013 Archive a note
**Given** an active, existing note,
**When** `API-NOTES-810640` is called,
**Then** the response is 200, `status` becomes `archived`, and title, body, pinned, tags and `created_at` are intact.
- **Traces to.** US-002-04; BR-007.
- **Verification.** `test-notes-archive-preserves-data` (planned).
- **API.** `API-NOTES-810640`.

### BE-NOTES-FR-014 Archive an archived note
**Given** an already-archived note,
**When** `API-NOTES-810640` is called,
**Then** a conflict response with `ALREADY_ARCHIVED` is returned and no data is modified. The status is `GAP-BE-309`.
- **Traces to.** US-002-04.
- **Verification.** `test-notes-archive-twice-conflict` (planned).
- **API.** `API-NOTES-810640`.

### BE-NOTES-FR-015 Unarchive a note
**Given** an archived note,
**When** `API-NOTES-917869` is called,
**Then** the response is 200, `status` becomes `active`, and all original data is preserved.
- **Traces to.** US-002-05; BR-008.
- **Verification.** `test-notes-unarchive-restores-all-data`, `test-notes-archive-unarchive-roundtrip` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-016 Unarchive an unknown note
**Given** a note id that does not exist,
**When** `API-NOTES-917869` is called,
**Then** the response is 404 `NOT_FOUND`.
- **Traces to.** US-002-05.
- **Verification.** `test-notes-unarchive-unknown-404` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-017 Unarchive an active note
**Given** an active note,
**When** `API-NOTES-917869` is called,
**Then** the same conflict response as FR-014 is returned and no data is modified (`ASM-BE-559`, `Q-BE-352`).
- **Traces to.** Not cited. Mirrors US-002-04's second criterion. Candidate for deletion if `Q-BE-352` rejects the default.
- **Verification.** `test-notes-unarchive-active-conflict` (planned).
- **API.** `API-NOTES-917869`.

### BE-NOTES-FR-018 Unknown note on tag and archive operations
**Given** a note id that does not exist,
**When** `API-NOTES-359413`, `API-NOTES-150096` or `API-NOTES-810640` is called,
**Then** the response is 404 `NOT_FOUND` and nothing is created or modified (`ASM-BE-563`).
- **Traces to.** Pattern of US-002-02, US-002-05, US-002-06. Not cited for these three operations.
- **Verification.** `test-notes-tag-add-unknown-404`, `test-notes-tag-remove-unknown-404`, `test-notes-archive-unknown-404` (planned).
- **API.** `API-NOTES-359413`, `API-NOTES-150096`, `API-NOTES-810640`.

### BE-NOTES-FR-019 Input safety
**Given** note title, body or tag fields containing injection payloads,
**When** any operation of this module is called,
**Then** the values are validated and handled as data only, and the security scan reports 0 critical injection vulnerabilities per release.
- **Traces to.** NFR-012.
- **Verification.** `test-notes-injection-payloads-inert` (planned); the release security scan.
- **API.** all seven operations.

### BE-NOTES-FR-020 Concurrent edits
**Given** two PATCH requests to one note at the same time,
**When** both are processed,
**Then** each request is applied atomically, so no request is applied half-way, with last-write-wins semantics and no broadcast or merge logic (`ASM-BE-561`, `Q-BE-354`).
- **Traces to.** US-005-03 (second criterion); NFR-005.
- **Verification.** `test-notes-patch-concurrent-atomic` (planned).
- **API.** `API-NOTES-847551`.

### BE-NOTES-FR-021 Retention deletion of notes — `BLOCKED`
NFR-019 read positionally says notes are deleted after 1 day (`ASM-BE-303`). That contradicts BR-007, BR-008 and NFR-005 (`CON-BE-300`). **Unblocked by** an answer to `Q-BE-300` and a corrected NFR-019. Until then no deletion is specified, built or scheduled.
- **Traces to.** NFR-019; BR-007; BR-008; NFR-005.
- **Verification.** none until unblocked.

### Story coverage

| Story | Requirements |
|---|---|
| US-002-01 | FR-001, FR-002, FR-003 |
| US-002-02 | FR-006, FR-007, FR-008, FR-009, FR-020 |
| US-002-03 | FR-010, FR-011, FR-012, FR-018 |
| US-002-04 | FR-013, FR-014 |
| US-002-05 | FR-015, FR-016, FR-017 |
| US-002-06 | FR-004, FR-005 |

All six owned stories are covered. FR-019 and FR-021 trace to NFRs.
<!-- /SECTION: backend.notes.requirements -->
<!-- SECTION: backend.notes.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-NOTES-232390` | POST | `/notes` | `notes` | 201 | 400, 401, 403, 500 | Create a note (US-002-01). Title and body mandatory and non-empty; pinned defaults to false. |
| `API-NOTES-045877` | GET | `/notes/{id}` | `notes` | 200 | 401, 403, 404, 500 | Read a single note by id (US-002-06). Unknown id returns not found with no partial data. |
| `API-NOTES-847551` | PATCH | `/notes/{id}` | `notes` | 200 | 400, 401, 403, 404, 500 | Partially edit a note, including pinning/unpinning via pinned (US-002-02). Fields not sent are never overwritten. |
| `API-NOTES-810640` | POST | `/notes/{id}/archive` | `notes` | 200 | 401, 403, 404, 409, 500 | Archive a note (US-002-04). Status change only; no data is deleted. Already-archived returns a conflict (status proposed, unstated in inputs). |
| `API-NOTES-359413` | POST | `/notes/{id}/tags` | `notes` | 200 | 400, 401, 403, 404, 500 | Add a tag to a note (US-002-03). Adding an existing tag creates no duplicate. |
| `API-NOTES-150096` | DELETE | `/notes/{id}/tags/{name}` | `notes` | 200 | 400, 401, 403, 404, 500 | Remove a tag from a note (US-002-03). |
| `API-NOTES-917869` | POST | `/notes/{id}/unarchive` | `notes` | 200 | 401, 403, 404, 500 | Unarchive a note, restoring it to active with all data preserved (US-002-05). |

### `API-NOTES-232390` — POST `/notes`

Create a note (US-002-01). Title and body mandatory and non-empty; pinned defaults to false.

**Request:** 
- `title` (string, required): Note title, mandatory and non-empty.
- `body` (string, required): Note body, mandatory and non-empty.
- `pinned` (boolean): Whether the note is pinned; defaults to false.
**Response:** 
- `id` (string, required): Note identifier.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `status` (string, required): active or archived; new notes are active.
- `pinned` (boolean, required): Pinned flag.
- `tags` (array, required): Tag names applied to the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-045877` — GET `/notes/{id}`

Read a single note by id (US-002-06). Unknown id returns not found with no partial data.

**Request:** 
- `id` (string, required): Path parameter: note identifier.
**Response:** 
- `id` (string, required): Note identifier.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `status` (string, required): active or archived.
- `pinned` (boolean, required): Pinned flag.
- `tags` (array, required): Tag names applied to the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-847551` — PATCH `/notes/{id}`

Partially edit a note, including pinning/unpinning via pinned (US-002-02). Fields not sent are never overwritten.

**Request:** 
- `id` (string, required): Path parameter: note identifier.
- `title` (string): New title; must be non-empty if sent.
- `body` (string): New body; must be non-empty if sent.
- `pinned` (boolean): Pin (true) or unpin (false) the note.
**Response:** 
- `id` (string, required): Note identifier.
- `title` (string, required): Note title.
- `body` (string, required): Note body.
- `status` (string, required): active or archived.
- `pinned` (boolean, required): Pinned flag.
- `tags` (array, required): Tag names applied to the note.
- `created_at` (string, required): Creation timestamp.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-810640` — POST `/notes/{id}/archive`

Archive a note (US-002-04). Status change only; no data is deleted. Already-archived returns a conflict (status proposed, unstated in inputs).

**Request:** 
- `id` (string, required): Path parameter: note identifier.
**Response:** 
- `id` (string, required): Note identifier.
- `status` (string, required): archived after success.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-359413` — POST `/notes/{id}/tags`

Add a tag to a note (US-002-03). Adding an existing tag creates no duplicate.

**Request:** 
- `id` (string, required): Path parameter: note identifier.
- `name` (string, required): Tag name to add.
**Response:** 
- `id` (string, required): Note identifier.
- `tags` (array, required): Resulting tag names on the note, without duplicates.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-150096` — DELETE `/notes/{id}/tags/{name}`

Remove a tag from a note (US-002-03).

**Request:** 
- `id` (string, required): Path parameter: note identifier.
- `name` (string, required): Path parameter: tag name to remove.
**Response:** 
- `id` (string, required): Note identifier.
- `tags` (array, required): Resulting tag names on the note.
- `updated_at` (string, required): Last update timestamp.

### `API-NOTES-917869` — POST `/notes/{id}/unarchive`

Unarchive a note, restoring it to active with all data preserved (US-002-05).

**Request:** 
- `id` (string, required): Path parameter: note identifier.
**Response:** 
- `id` (string, required): Note identifier.
- `status` (string, required): active after success.
- `updated_at` (string, required): Last update timestamp.
<!-- /SECTION: backend.notes.api-contract -->
<!-- SECTION: backend.notes.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

Project-level invariants that apply to this module are cited, not restated: `INV-BE-008` (a PATCH never overwrites a field it does not contain) and `INV-BE-009` (archive and unarchive preserve all note data, threatened by `CON-BE-300`). `INV-BE-001` applies to list and search results and belongs to `search`.

- `INV-BE-062`. After every operation, every note has a non-empty title, a non-empty body, a `status` of `active` or `archived`, and a non-null boolean `pinned`. [requirements.md BR-005, BR-018]
- `INV-BE-063`. A note carries each tag at most once. [stories.md US-002-03]
- `INV-BE-064`. A request that returns a 4xx response leaves the note, its tags and its `updated_at` unchanged. [stories.md AC-002-01-03, AC-002-02-03, US-002-04]
- `INV-BE-065`. Archive and unarchive change only `status` and `updated_at`. Title, body, `pinned`, tags and `created_at` are unchanged. [requirements.md BR-007, BR-008]
- `INV-BE-066`. `created_at` never changes after creation, and `updated_at >= created_at`.
- `INV-BE-067`. A note id is assigned once by the server, is unique, and is never reused for another note (`ASM-BE-555`). [stories.md AC-002-01-01]
<!-- /SECTION: backend.notes.invariants -->
<!-- SECTION: backend.notes.authorization | status: BLOCKED -->
## 7. Authorization

**Blocked.** The ownership predicate and the role permissions cannot be written until `Q-BE-003` and `CON-BE-003` are decided. No input relates a note to a user (`GAP-BE-003`) and BR-031, the note access rule, is corrupted as supplied. The credential that proves the caller is `Q-BE-012`. **Unblocked by** an answer to `Q-BE-003` and a corrected BR-031.

**Provisional position, not a decision.** It is the `Q-BE-001` default recorded in `backend.auth-model`: any authenticated team member may call every operation of this module on any note.

| Operation | Unauthenticated caller | Authenticated team member | Other actors |
|---|---|---|---|
| `API-NOTES-232390`, `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-359413`, `API-NOTES-150096`, `API-NOTES-810640`, `API-NOTES-917869` | Not stated, so denied (`ASM-BE-302`) | Provisional allow under `Q-BE-001`, `BLOCKED` on `CON-BE-003` | Not stated, so denied (`ASM-BE-302`) |

**Ownership predicate.** None exists. Ownership columns and the per-note access check are `BLOCKED`.

**Dependency.** `notes` depends on `auth` for authentication. [backend.module-map] The unauthenticated and forbidden responses are `UNAUTHENTICATED` (401) and `FORBIDDEN` (403) in `backend.error-model`.
<!-- /SECTION: backend.notes.authorization -->
<!-- SECTION: backend.notes.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

**Scheduled work.** The only candidate is the NFR-019 retention deletion of notes. It is `BLOCKED` on `CON-BE-300` and `Q-BE-300`. Its scheduler and frequency are `GAP-BE-101`. No sweep, retry or background job is specified for this module.

**Retries.** None specified. No input states a retry policy.

**Idempotency.**

| Operation | Behaviour |
|---|---|
| `API-NOTES-359413` | Idempotent by definition: adding an existing tag returns 200 and creates no duplicate (FR-011). [stories.md US-002-03] |
| `API-NOTES-150096` | A repeat call on an absent tag follows `ASM-BE-560`. |
| `API-NOTES-810640`, `API-NOTES-917869` | Not idempotent. A repeat returns the conflict response (FR-014, FR-017). |
| `API-NOTES-847551` | Setting the same values twice has the same result. `updated_at` moves on each call (`ASM-BE-562`). |
| `API-NOTES-232390` | No idempotency key is defined. A client retry after a lost response can create a duplicate note (`GAP-BE-569`). |
<!-- /SECTION: backend.notes.jobs -->
<!-- SECTION: backend.notes.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

**Unit.**
- Validation of title and body: missing, null, empty, whitespace-only (FR-003, FR-009).
- PATCH merge logic: only sent fields change, `pinned`-only case (FR-006, FR-007; `INV-BE-008`).
- State guard for archive and unarchive (FR-013 to FR-017).
- `updated_at` rules (`ASM-BE-562`), using an injectable clock.
- Tag set semantics: no duplicate, absent-tag removal.

**Integration.** Against PostgreSQL 16 (the platform, platform.md).
- All FR tests named above.
- Archive then unarchive round trip restores all data (`INV-BE-065`).
- Every 4xx leaves the database unchanged (`INV-BE-064`).
- Concurrent PATCH atomicity and concurrent tag add (FR-020, `INV-BE-063`).
- Constraint tests: duplicate `note_tags` row, invalid `status`, null `pinned`.
- Pinned notes created and listed are verified in `search`'s tests, not here.

**Contract.**
- Each of the seven operations is present in the generated OpenAPI document with request and response schemas and its 4xx/5xx responses. [requirements.md BR-012, NFR-008]
- The error codes used by this module match `backend.error-model`.
- The seven operations are covered by the endpoint-coverage check. [stories.md US-004-04]
- No attachment, real-time or mobile-specific route is defined by this module. The guardrail suite itself belongs to `scope-guardrail`. [requirements.md NFR-010]

**Security.** Injection payloads in title, body and tag name (FR-019). [requirements.md NFR-012]

**Not testable until unblocked.** The access rule (`CON-BE-003`) and the retention deletion (`CON-BE-300`).
<!-- /SECTION: backend.notes.test-plan -->
<!-- SECTION: backend.notes.gaps | status: DERIVED -->
## 10. Gaps, contradictions, assumptions and questions

**Contradictions.** This module mints none. It depends on `CON-BE-003` (note access rule, Critical), `CON-BE-300` (retention deletion of notes, Critical) and `CON-BE-019` (differing change-request labels). Each is defined in the project spec.

**Project-level items this module depends on.** `Q-BE-001`, `Q-BE-003`, `Q-BE-012`, `Q-BE-300`, `GAP-BE-003`, `GAP-BE-017`, `GAP-BE-033`, `GAP-BE-035`, `GAP-BE-089`, `GAP-BE-101`, `GAP-BE-300`, `GAP-BE-303`, `GAP-BE-304`, `GAP-BE-309`, `ASM-BE-034`, `ASM-BE-302`, `ASM-BE-303`.

### Questions

This run is unattended. Each `degradable` question takes its stated default and is not asked.

| ID | Class | Question | Default taken |
|---|---|---|---|
| `Q-BE-351` | degradable | May an archived note be edited, pinned or tagged? | Yes, as an active note (`ASM-BE-558`) |
| `Q-BE-352` | degradable | What does unarchive of an active note return? | The archive conflict (`ASM-BE-559`) |
| `Q-BE-353` | degradable | What does removing a tag that is not on the note return? | 404 `NOT_FOUND` (`ASM-BE-560`) |
| `Q-BE-354` | degradable | Is concurrent PATCH last-write-wins or a conflict? | Last-write-wins (`ASM-BE-561`) |

### Gaps

| ID | What is missing | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-564` | Whether the PATCH body may carry `tags`, and whether that replaces the tag set. US-002-02 says "edit ... tags" and AC-002-02-02 says tags are unchanged by a pinned-only PATCH; dedicated tag operations also exist. | FR-006, FR-010 | a contract decision |
| `GAP-BE-565` | Success status of `API-NOTES-150096`. | FR-012 | a status decision |
| `GAP-BE-566` | Whether an unknown tag filter or tag lifecycle in `search` expects tag rows to exist. Not stated. | `note_tags` index | a decision from `search` |
| `GAP-BE-567` | Maximum number of tags per note and maximum title and body length. | data model | field-level requirements |
| `GAP-BE-568` | Whether tag add or remove changes `updated_at`. Default in `ASM-BE-562`. | FR-010 to FR-012 | a product rule |
| `GAP-BE-569` | No idempotency key or duplicate protection for `API-NOTES-232390`. | jobs | a contract decision |
| `GAP-BE-570` | No list-notes operation is among this module's seven records, although `backend.overview` lists listing as a responsibility and BR-019 orders list results. Whether `search` serves the list or a list operation is missing is not stated. | `backend.overview` item 1 | a decision to add the operation or to confirm `search` serves it |
| `GAP-BE-571` | Tag name format, case rules, and whether a tag with no remaining notes is kept. Default in `ASM-BE-564`, `ASM-BE-567`. | data model | field-level requirements |
| `GAP-BE-572` | Response to a malformed note id (400 versus 404). | FR-005 | a status decision |
| `GAP-BE-573` | Behaviour of removing an absent tag is not cited. Default in `ASM-BE-560`. | FR-012 | a status decision |

### Assumptions

| ID | Assumption | Reason | Confirmed by |
|---|---|---|---|
| `ASM-BE-553` | Column types: uuid keys, text for strings, boolean, timestamptz. | PostgreSQL 16 is the platform and no types are supplied. | platform decision (`GAP-BE-089`) |
| `ASM-BE-554` | A new note has `status` `active`. | US-002-04 archives an "active, existing note" and the create criteria carry no status. | confirmation |
| `ASM-BE-555` | The note id is server-assigned, opaque and never client-supplied. | AC-002-01-01 says the API returns a unique id. | confirmation |
| `ASM-BE-556` | A title or body of whitespace only counts as empty. | BR-005 rejects "empty/null" payloads; whitespace carries no content. | product owner |
| `ASM-BE-557` | The non-empty rule for title and body also applies to PATCH. | BR-005 says "at creation" only; an edit that empties a field would lose content, against BR-006's intent. | product owner |
| `ASM-BE-558` | Edit, pin and tag changes on an archived note behave as on an active note. | No input forbids them; BR-008 says only that unarchive restores the prior state. | `Q-BE-351` |
| `ASM-BE-559` | Unarchive of an active note returns the archive conflict. | Mirrors US-002-04's second criterion; nothing else is cited. | `Q-BE-352` |
| `ASM-BE-560` | Removing a tag not on the note returns 404 `NOT_FOUND`. | Add is explicitly idempotent while removal has no criteria; 404 keeps the two distinguishable. | `Q-BE-353` |
| `ASM-BE-561` | Concurrent PATCH is last-write-wins, atomic per request, with no version column. | US-005-03 allows last-write or conflict and specifies neither further. | `Q-BE-354` |
| `ASM-BE-562` | `updated_at` is set on PATCH, archive, unarchive and any tag change that alters the tag set. A repeated add of an existing tag, and a refused request, change nothing. | AC-002-02-01 requires an updated timestamp on edit only. | product owner (`GAP-BE-568`) |
| `ASM-BE-563` | Tag and archive operations on an unknown note id return 404 `NOT_FOUND`. | US-002-02, US-002-05 and US-002-06 return 404 for unknown ids. | confirmation |
| `ASM-BE-564` | A `tags` row is created on first add and kept when no note carries it. | No input says tags are removed. | `GAP-BE-571` |
| `ASM-BE-565` | No delete operation for notes is in scope. "Deleted" in US-002-06 is treated as never-existing. | BR-007 keeps data; no input describes a delete operation. | `CON-BE-300` resolution |
| `ASM-BE-566` | `note_tags.note_id` is `ON DELETE CASCADE`. | If a retention deletion is ever approved, it must leave no dangling links. Dormant until `CON-BE-300` is resolved. | `CON-BE-300` resolution |
| `ASM-BE-567` | Tag names are compared exactly, character for character. | No case or normalisation rule is stated. | `GAP-BE-571` |
<!-- /SECTION: backend.notes.gaps -->
