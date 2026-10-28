---
id: agent-developer/notes_api/backend/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/search-SPEC.md
depends_on: [notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06]
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/modules/search-SPEC.md
---

<!-- SECTION: backend.search.frontmatter | status: BLOCKED -->
## Frontmatter

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.search.frontmatter -->
<!-- SECTION: backend.search.purpose | status: BLOCKED -->
## Purpose

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.search.purpose -->
<!-- SECTION: backend.search.data-model | status: BLOCKED -->
## Data model

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.search.data-model -->
<!-- SECTION: backend.search.state-machine | status: BLOCKED -->
## State machine

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.search.state-machine -->
<!-- SECTION: backend.search.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: backend.search.requirements -->
<!-- SECTION: backend.search.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-SEARCH-580505` | GET | `/notes/search` | `search` | 200 | 400, 401, 403, 503 | Search notes and tags with pagination and ordering; pinned notes precede unpinned notes in results. Path and parameter names are proposed (no input supplies them, see GAP-BE-303). |

### `API-SEARCH-580505` — GET `/notes/search`

Search notes and tags with pagination and ordering; pinned notes precede unpinned notes in results. Path and parameter names are proposed (no input supplies them, see GAP-BE-303).

**Request:** 
- `q` (string): Free-text search term matched against note title, body and tag names. Proposed name; format and length limits unstated (GAP-BE-035).
- `tag` (string): Restrict results to notes carrying this tag name. Proposed name; tag format unstated (GAP-BE-035).
- `include_archived` (boolean): When true, archived notes are included in results; an invalid value returns a 400 validation failure.
- `page` (integer): Page number to return. Proposed name; invalid values return 400.
- `page_size` (integer): Number of results per page. Default and maximum not stated (GAP-BE-500); NFR-007 names page sizes of 100. Invalid values return 400.
**Response:** 
- `items` (array, required): Matching notes for the requested page, with every pinned note before every unpinned note (INV-BE-001). Empty array with 200 when nothing matches.
- `page` (integer, required): The page number returned.
- `page_size` (integer, required): The page size applied to this response.
- `total` (integer, required): Total number of notes matching the query across all pages.
<!-- /SECTION: backend.search.api-contract -->
<!-- SECTION: backend.search.invariants | status: DRAFT-UNVERIFIED -->
## 6. Invariants

- `INV-BE-001` (cited from the project spec). Every pinned note precedes every unpinned note in results. [BR-019]
- `INV-BE-110`. The operation changes no data: it writes no row to any table. [stories.md EP-003 (read-only operation); backend.module-map]
- `INV-BE-111`. Without `include_archived=true`, no archived note appears in results. [BR-009]
- `INV-BE-112`. No response contains an unbounded result set. [BR-011]
- `INV-BE-113`. A note appears at most once per response, whatever the number of matching tags (`ASM-BE-502`).
- `INV-BE-114`. A request that fails validation returns 400 and runs no query. (`ASM-BE-503`)
<!-- /SECTION: backend.search.invariants -->
<!-- SECTION: backend.search.authorization | status: BLOCKED -->
## 7. Authorization

**Blocked.** The role-by-operation matrix and the ownership predicate cannot be written.

- No input defines API roles; the existing authentication method is unnamed (`GAP-BE-085`).
- Whether a caller sees all notes or only their own depends on `Q-BE-001` and `Q-BE-003`; `CON-BE-003` (Critical) keeps the note access rule open. `backend.auth-model` is itself `BLOCKED`.
- Until an input grants a permission, a role has none (`ASM-BE-302`).
- The ownership predicate for search results, when decided, must be applied to the result set and to the total count, so the count discloses no note the caller may not read (`ASM-BE-504`).

**Unblocked by:** answers to `Q-BE-001`, `Q-BE-003` and `Q-BE-012`, resolution of `CON-BE-003`, and a statement of the existing authentication method.
<!-- /SECTION: backend.search.authorization -->
<!-- SECTION: backend.search.jobs | status: DRAFT-UNVERIFIED -->
## 8. Jobs

- **Scheduled work.** None stated by any input. If the full-text index is a stored or materialised structure that needs refresh, that is `GAP-BE-725`; with a PostgreSQL expression index (`ASM-BE-501`) no job is needed.
- **Retries.** The operation does not retry server-side. A client may retry on 503 after the retry-after interval (`GAP-BE-724`).
- **Idempotency.** `GET` is safe and idempotent; no idempotency key is needed. [INV-BE-110]
<!-- /SECTION: backend.search.jobs -->
<!-- SECTION: backend.search.test-plan | status: DRAFT-UNVERIFIED -->
## 9. Test plan

**Unit.**
- Parameter parsing and validation for `q`, `tags`, `include_archived`, `page`, `limit` (FR-003, 005, 007, 010, 014).
- Query construction: pinned-first then relevance ordering; default `status=active` filter.

**Integration (PostgreSQL 16, seeded data).**
- All FR-001 to FR-014 and FR-017 tests named in `backend.search.requirements`.
- A note with several matching tags appears once (`INV-BE-113`).
- A simulated index failure returns 503 with retry-after (FR-016).
- No write occurs during a search (`INV-BE-110`).

**Load.**
- `test-search-load-p95`: 50 requests per second, page size up to 100, dataset of 100k notes; assert p95 < 300 ms. [NFR-006, NFR-007; US-003-03]

**Contract.**
- The response and error shapes match the published OpenAPI document for `API-SEARCH-580505`, including 400, 401, 403 and 503, and example payloads include an empty result. [NFR-008, NFR-009; US-004-05]
- Authorization tests are `BLOCKED` with section 7.
<!-- /SECTION: backend.search.test-plan -->
<!-- SECTION: backend.search.gaps | status: DRAFT-UNVERIFIED -->
## 10. Gaps, contradictions and assumptions

**Open questions (this module).**

| ID | Class | Question |
|---|---|---|
| `Q-BE-401` | degradable | Is a search with `tags` and no `q` allowed? US-003-01 requires `q`; US-003-02 supplies `tags` alone. Default: allowed when at least one of `q` or `tags` is present, 400 when neither is (`ASM-BE-505`). |

**Gaps.**

| ID | Statement | Closing artefact |
|---|---|---|
| `GAP-BE-717` | Text-search configuration (language, stemming, ranking) is not stated. | a decision on language and ranking |
| `GAP-BE-718` | Which module creates the search indexes is not stated. | a decision on ownership |
| `GAP-BE-719` | With several tags, whether a note needs all or any is not stated. | a rule from the product owner |
| `GAP-BE-720` | The separator for several tags in `tags` is not stated. | a parameter format decision |
| `GAP-BE-721` | "Malformed filter combination" (US-003-04) is undefined. | a list of invalid combinations |
| `GAP-BE-722` | Page base, response field names and total-count field name are not stated. | response shape decision |
| `GAP-BE-723` | Accepted literals for `include_archived` are not stated. | a parameter format decision |
| `GAP-BE-724` | Retry-after value and the timeout before 503 are not stated. | a resilience rule |
| `GAP-BE-725` | Whether any index refresh job is needed is not stated. | the index technology decision |

**Open items cited from the project spec.** `GAP-BE-001`, `GAP-BE-017`, `GAP-BE-035`, `GAP-BE-085`, `GAP-BE-303`, `GAP-BE-304`, `GAP-BE-310`, `GAP-BE-500`; `CON-BE-003` (Critical); `Q-BE-001`, `Q-BE-003`, `Q-BE-012`; `ASM-BE-302`.

**Assumptions.**

| ID | Assumption |
|---|---|
| `ASM-BE-501` | Search is implemented with PostgreSQL 16 full-text and btree indexes. PostgreSQL is the platform [platform.md]; the specific index types are proposals. Confirm with the platform decision. |
| `ASM-BE-502` | Results are de-duplicated per note when several tags match. Stories imply a list of notes, not note-tag rows. |
| `ASM-BE-503` | Validation precedes query execution. Standard behaviour implied by the 400 criteria. |
| `ASM-BE-504` | The total count honours the same visibility rules as the results. Depends on the access rule. |
| `ASM-BE-505` | Default for `Q-BE-401`: at least one of `q` or `tags` is required. |
<!-- /SECTION: backend.search.gaps -->
