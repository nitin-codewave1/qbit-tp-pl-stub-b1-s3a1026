---
id: agent-developer/notes_api/backend/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/search-SPEC.md
depends_on: [notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06, notes_api:US-006-03]
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:backend/modules/search-SPEC.md
---

<!-- SECTION: backend.search.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: search
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-002, Q-BE-003]
scope: [API-SEARCH-580505]
```

`blocked_by` blocks only `backend.search.authorization`. Every other section is written. The scope id is the operation id supplied to this run. Q-BE-002 and Q-BE-003 are defined in `backend.coverage` of the project spec.
<!-- /SECTION: backend.search.frontmatter -->
<!-- SECTION: backend.search.purpose | status: DERIVED -->
## Purpose

**Responsibility.** The `search` module answers one read-only question: which notes match a keyword, a tag filter or both. It returns them paginated, active-only by default, with pinned notes first. Its boundary is stated in `backend.module-map`: it covers US-003-01 to US-003-06, BR-009 to BR-011 and BR-019. It also serves the backend half of US-006-03. The operation is `API-SEARCH-580505`. The path `GET /notes/search` is also given by US-003-01 (`GET /notes/search?q=keyword`).

It owns:

- keyword matching over note title and body;
- tag filtering and the combination of the two filters;
- the archived-exclusion default and the `include_archived` override;
- pinned-first ordering across the whole result set;
- pagination with a total count;
- the 503 response with a `Retry-After` header when the search index is degraded;
- the search indexes (migration 003 in `backend.migration-plan`).

**Not responsible for:**

- Note storage, note representation, tag add and remove, and archive state. These belong to `notes`. `search` reads `notes` and `note_tags` and never writes them (`search → notes`, `backend.module-map`).
- Listing notes without a keyword or tag, and listing archived notes only. No operation does this (`GAP-BE-005`). `include_archived=true` returns active and archived notes mixed. It does not isolate archived notes.
- Login, credentials and note ownership. These belong to `auth` and to the decisions in `Q-BE-002` and `Q-BE-003`.
- The published OpenAPI document, which `contract-docs` owns. The request and response shapes, 4xx/5xx tables and examples for this operation are defined once, in `api-contract` below, and nowhere else.
- The web_app search box (US-006-03 UI behaviour). It is a separate surface.
- Fuzzy matching, synonyms, stemming and highlighting. No input asks for them.

**Design input.** `design-brief.md` carries acceptance criteria only (AC-001-01-01 to AC-002-01-03), none about search. No design claim is made.
<!-- /SECTION: backend.search.purpose -->
<!-- SECTION: backend.search.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Tables owned: none.** `search` adds no table, column, default or constraint. It reads two structures owned by `notes` (`backend.domain-model`):

| Structure | Columns read | Source |
|---|---|---|
| `notes` | `id`, `title`, `body`, `pinned`, `status` (active or archived), `updated_at` | `backend.domain-model`; US-002-01, BR-007, BR-018 |
| `note_tags` | `note_id`, `tag_name` (unique per note and tag name) | US-002-03 |

Nullability, types and defaults of these columns are defined in `modules/notes-SPEC.md`, not here (core rule 6).

**Objects owned: indexes only.** They are created by migration 003. PostgreSQL 16 holds them in the same database as the notes (`platform.md`, `ASM-BE-010`). An index is stored with its data and moves with it.

| Index | On | Kind | Query it serves |
|---|---|---|---|
| `idx_notes_fulltext` | the combined `title` and `body` text of `notes`, using the `simple` text-search configuration (`ASM-BE-067`) | inverted full-text (GIN) | keyword match, US-003-01, and the 100k-note latency requirement of US-003-03 |
| `idx_note_tags_lookup` | `note_tags (tag_name, note_id)` | B-tree | tag filter, US-003-02, US-003-04 |
| `idx_notes_order` | `notes (status, pinned DESC, updated_at DESC, id)` | B-tree | the ordering and archive filter of a tags-only search (`ASM-BE-072`, `ASM-BE-078`) |

**Reversibility.** All three indexes can be dropped with no data loss (`backend.migration-plan`, 003). Dropping the first leaves keyword search without an index and is not acceptable for NFR-006. Dropping the third is acceptable if load tests show it is unneeded (`ASM-BE-078`).

**PII.** Note content may contain anything the user typed, and no input classifies it as PII. The indexes are derived copies of title and body. No input states encryption at rest (`GAP-BE-006`), so none is claimed.

**Consistency.** The indexes live in the notes database and are updated with the note change. A note created, edited, archived, unarchived or re-tagged is visible to the next search (`ASM-BE-074`).

**Read-only.** No write path exists in this module.
<!-- /SECTION: backend.search.data-model -->
<!-- SECTION: backend.search.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**No entity state.** `search` owns no entity and no status column. The note `status` (active or archived) belongs to `notes`, and search only filters on it. No persistent state exists, so there are no entity states and no illegal transition. Index health is judged per request with no circuit breaker (`ASM-BE-073`). No state is remembered between requests.

The one lifecycle is that of a single request:

| From | To | Trigger | Guard |
|---|---|---|---|
| RECEIVED | REJECTED | validation fails | any rule in `requirements` FR-003, 006, 009, 012, 016 | 
| RECEIVED | QUERYING | validation passes | authentication, if any, runs before validation. It is not specified (`Q-BE-003`). |
| QUERYING | RESPONDED | index query returns | including an empty result or a page beyond the end |
| QUERYING | UNAVAILABLE | index query errors or exceeds the request deadline | `ASM-BE-073`; the deadline value is unstated (`GAP-BE-070`) |
| QUERYING | FAILED | any other unhandled error | |

**Terminal states** (the request ends): REJECTED (400 `VALIDATION_ERROR`), RESPONDED (200), UNAVAILABLE (503 `SEARCH_UNAVAILABLE`, `Retry-After` header) and FAILED (500 `INTERNAL_ERROR`). The status and registry codes are those of the project `backend.error-model`, whose code names are proposals (`ASM-BE-001`). Every state is terminal for its request, and no state is re-entered.
<!-- /SECTION: backend.search.state-machine -->
<!-- SECTION: backend.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements implement `API-SEARCH-580505`. Query parameter names (`q`, `tags`, `page`, `limit`, `include_archived`) come from the stories. Their types and constraints are carried by the rendered contract, not restated here.

### Keyword

**BE-SEARCH-FR-001 Keyword match.**
Given notes whose title or body contain the keyword, when `GET /notes/search?q=keyword` is called, then the response is 200 with the matching notes, pinned first, then by relevance.
- **Traces to.** US-003-01 (AC1), BR-019, US-006-03 (AC1)
- **Verification.** `search.keyword.matches_title_and_body` (planned)
- **API.** `API-SEARCH-580505`
- Match semantics: `ASM-BE-067`. The relevance function is unspecified (`GAP-BE-072`).

**BE-SEARCH-FR-002 Keyword with no match.**
Given no note matches the keyword, when the search runs, then the response is 200 with an empty result set, not an error.
- **Traces to.** US-003-01 (AC2), US-006-03 (AC3)
- **Verification.** `search.keyword.no_match_returns_empty_200` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-003 No usable filter.**
Given `q` is missing, empty or whitespace-only, and no `tags` value is supplied, when the search runs, then the response is 400 `VALIDATION_ERROR` and the index is not queried.
- **Traces to.** US-003-01 (AC3), `CON-BE-003`, `ASM-BE-015`, `ASM-BE-076`
- **Verification.** `search.validation.no_q_and_no_tags_is_400` (planned)
- **API.** `API-SEARCH-580505`

### Tags

**BE-SEARCH-FR-004 Tag filter.**
Given notes tagged `project-x` exist, when `GET /notes/search?tags=project-x` is called, then only notes carrying that tag are returned.
- **Traces to.** US-003-02 (AC1), US-006-03 (AC2)
- **Verification.** `search.tags.single_tag_filters` (planned)
- **API.** `API-SEARCH-580505`
- A tags-only search is allowed (`ASM-BE-015`).
- Several tags use the `tags` format and the all-of rule of `ASM-BE-070` and `ASM-BE-071` (`Q-BE-055`).
- Tag names are compared exactly as stored (`ASM-BE-077`).

**BE-SEARCH-FR-005 Tag with no match.**
Given no note carries the tag, when the search runs, then the response is 200 with an empty result set.
- **Traces to.** US-003-02 (AC2)
- **Verification.** `search.tags.no_match_returns_empty_200` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-006 Invalid tag characters.**
Given the `tags` parameter contains invalid characters, when the search is made, then the response is 400 `VALIDATION_ERROR` with error detail.
- **Traces to.** US-003-02 (AC3)
- **Verification.** `search.tags.invalid_characters_is_400` (planned)
- **API.** `API-SEARCH-580505`
- The valid character set is not defined by any input (`GAP-BE-010`).

### Combination

**BE-SEARCH-FR-007 Combined filters.**
Given `q=budget` and `tags=finance`, when the search runs, then only notes matching both are returned.
- **Traces to.** US-003-04 (AC1), BR-010
- **Verification.** `search.combined.keyword_and_tag_intersect` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-008 Combination that excludes everything.**
Given the keyword matches but the tag filter excludes all matching notes, when the search runs, then the response is 200 with an empty result set.
- **Traces to.** US-003-04 (AC2)
- **Verification.** `search.combined.tag_excludes_all_returns_empty_200` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-009 Malformed combination.**
Given a malformed filter combination, when the search is made, then the response is 400 `VALIDATION_ERROR`. A request is malformed if either of these holds (`ASM-BE-079`):
- a parameter is supplied more than once;
- a parameter is not one of the five named above.
- **Traces to.** US-003-04 (AC3)
- **Verification.** `search.validation.malformed_combination_is_400` (planned)
- **API.** `API-SEARCH-580505`

### Pagination

**BE-SEARCH-FR-010 Pages and total.**
Given more matching notes than the page size, when `page` and `limit` are supplied, then the requested subset is returned together with the total count of all matches.
- **Traces to.** US-003-05 (AC1), BR-011
- **Verification.** `search.pagination.subset_and_total` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-011 Page beyond the end.**
Given a `page` beyond the available results, when the search runs, then the response is 200 with an empty result set.
- **Traces to.** US-003-05 (AC2)
- **Verification.** `search.pagination.page_beyond_end_returns_empty_200` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-012 Invalid paging values.**
Given a negative or non-numeric `page` or `limit`, when the search is made, then the response is 400 `VALIDATION_ERROR`. Under `ASM-BE-068` this also covers `page` = 0 and `limit` = 0.
- **Traces to.** US-003-05 (AC3)
- **Verification.** `search.pagination.invalid_values_is_400` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-013 Bounded result set.**
Given no `page` or `limit`, when the search runs, then a default page and page size apply. A `limit` above the maximum is rejected with 400 (`Q-BE-054`). A result set is never unbounded.
- **Traces to.** BR-011, NFR-007, `ASM-BE-009`, `ASM-BE-068`
- **Verification.** `search.pagination.defaults_and_max_enforced` (planned)
- **API.** `API-SEARCH-580505`

### Archived and pinned

**BE-SEARCH-FR-014 Archived excluded by default.**
Given both archived and active notes match, when a default search runs, then only active notes are returned.
- **Traces to.** US-003-06 (AC1), BR-009
- **Verification.** `search.archived.excluded_by_default` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-015 Archived on request.**
Given `include_archived=true`, when the search runs, then archived notes are included alongside active notes.
- **Traces to.** US-003-06 (AC2), BR-009
- **Verification.** `search.archived.included_when_requested` (planned)
- **API.** `API-SEARCH-580505`
- The operation record says archived notes are excluded and does not mention this parameter (`CON-BE-054`).

**BE-SEARCH-FR-016 Invalid `include_archived`.**
Given an `include_archived` value other than `true` or `false`, when the search is made, then the response is 400 `VALIDATION_ERROR`.
- **Traces to.** US-003-06 (AC3), `ASM-BE-069`
- **Verification.** `search.archived.invalid_value_is_400` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-017 Pinned first across the whole result set.**
Given pinned and unpinned notes match, when the search runs, then every pinned match precedes every unpinned match across all pages. The ordering is applied before pagination, not within each page.
- **Traces to.** BR-019, US-003-01 (AC1)
- **Verification.** `search.ordering.pinned_before_unpinned_across_pages` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-018 Stable order within a group.**
Given several matches in the same pinned group, when pages are requested in sequence, then the order is the same on every request. It is relevance (descending) when `q` is present, otherwise latest update first, with the note id as the final tie-break (`ASM-BE-072`). No note appears on two pages and none is skipped between pages.
- **Traces to.** BR-019 ("ordering … unchanged"), US-003-05
- **Verification.** `search.ordering.deterministic_across_pages` (planned)
- **API.** `API-SEARCH-580505`
- The prior ordering is not defined by any input (`ASM-BE-072`).

### Non-functional

**BE-SEARCH-FR-019 Latency.**
Given 50 requests per second, when searches run against about 100k notes, then p95 latency is under 300 ms. This holds for page sizes up to 100.
- **Traces to.** NFR-006, NFR-007, US-003-03 (AC1, AC2)
- **Verification.** `search.perf.p95_under_300ms_at_50rps_100k_notes` (planned)
- **API.** `API-SEARCH-580505`
- The environment is not defined (`GAP-BE-073`).

**BE-SEARCH-FR-020 Degraded index.**
Given the search index is degraded or unavailable, when a search is requested, then the response is 503 `SEARCH_UNAVAILABLE` with a `Retry-After` header. The request does not hang and no partial results are returned.
- **Traces to.** US-003-03 (AC3)
- **Verification.** `search.availability.degraded_index_returns_503_retry_after` (planned)
- **API.** `API-SEARCH-580505`
- The `Retry-After` value and the request deadline are unstated (`GAP-BE-070`).

**BE-SEARCH-FR-021 Input is data.**
Given a keyword or tag containing SQL metacharacters or search-syntax operators, when the search runs, then the value is treated as plain data. It causes no query error and no injection.
- **Traces to.** NFR-012
- **Verification.** `search.security.injection_inputs_treated_as_data` (planned)
- **API.** `API-SEARCH-580505`

**BE-SEARCH-FR-022 Route precedence.**
Given `GET /notes/search` and `GET /notes/{id}` both exist, when `/notes/search` is called, then the search handler answers it. It is never read as a note id.
- **Traces to.** `backend.api-surface`: both operations are in the project api-surface. `platform.md` (Express) is the router.
- **Verification.** `search.routing.search_path_not_captured_by_get_note` (planned)
- **API.** `API-SEARCH-580505`

### Story coverage owned by this module

| Story | Requirements |
|---|---|
| US-003-01 | FR-001, 002, 003 |
| US-003-02 | FR-004, 005, 006 |
| US-003-03 | FR-019, 020 |
| US-003-04 | FR-007, 008, 009 |
| US-003-05 | FR-010, 011, 012, 013 |
| US-003-06 | FR-014, 015, 016 |
| US-006-03 | FR-001, 002, 004, 005. These cover the backend half. The search box UI is the web_app's. |
<!-- /SECTION: backend.search.requirements -->
<!-- SECTION: backend.search.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-SEARCH-580505` | GET | `/notes/search` | `search` | 200 | 400, 500, 503 | Search active notes by keyword, tag or both, with pagination. Archived notes are excluded and pinned notes are ordered first. Returns 503 with Retry-After when the search index is degraded. Path is a proposal; no input names it. |

### `API-SEARCH-580505` — GET `/notes/search`

Search active notes by keyword, tag or both, with pagination. Archived notes are excluded and pinned notes are ordered first. Returns 503 with Retry-After when the search index is degraded. Path is a proposal; no input names it.

**Request:** 
- `q` (string): Keyword matched against note title and body. At least one of q or tags is required (ASM-BE-015); a missing or empty q with no tags is a 400.
- `tags` (array): Tag names to filter by, combinable with q (combined filter). Sent as a repeated or comma-separated query parameter.
- `page` (integer): 1-based page number. Invalid or out-of-range values return 400.
- `page_size` (integer): Results per page; default 20, maximum 100 (ASM-BE-009). Values over the maximum or below 1 return 400.
**Response:** 
- `items` (array, required): Matching non-archived notes for the requested page, pinned notes first. Each item has id, title, body, pinned, tags, status, created_at, updated_at.
- `page` (integer, required): Current page number.
- `page_size` (integer, required): Page size applied to this response.
- `total` (integer, required): Total number of matching non-archived notes across all pages.
<!-- /SECTION: backend.search.api-contract -->
<!-- SECTION: backend.search.invariants | status: DERIVED -->
## Invariants

**INV-BE-051.** Every returned note satisfies every filter in the request:
- it carries the keyword match, if `q` was supplied;
- it carries all supplied tags, if `tags` was supplied;
- it is active, unless `include_archived=true`.
(US-003-01, 02, 04, 06; BR-009, BR-010)

**INV-BE-052.** In the full ordered result set, no unpinned note precedes a pinned note, on any page. (BR-019)

**INV-BE-053.** Search changes no data. A search leaves `notes` and `note_tags` byte-identical, and the same request may be repeated freely. (derived: no write path, `data-model`)

**INV-BE-054.** For a fixed data set, the pages of one query are disjoint and together form the full ordered result set. The reported total equals the size of that set. (US-003-05; FR-018)

**INV-BE-055.** A 503 response carries no result items. A 400 response means no index query ran. (US-003-03 AC3; FR-003, FR-020)

**INV-BE-056.** A result is never unbounded: the number of returned items never exceeds the maximum page size. (BR-011, `ASM-BE-009`)
<!-- /SECTION: backend.search.invariants -->
<!-- SECTION: backend.search.authorization | status: BLOCKED -->
## Authorization

**BLOCKED.** The operation × role matrix and the ownership predicate cannot be written without inventing an authorisation rule. The project `backend.auth-model` is itself BLOCKED, for the same reasons.

| Missing decision | What it decides here | What would unblock it |
|---|---|---|
| `Q-BE-003`: what a successful verify issues, and whether note and search endpoints require it | whether `API-SEARCH-580505` needs a credential, and whether it documents 401 or 403 (US-004-03 expects them for authenticated endpoints) | the post-verify credential decision |
| `Q-BE-002`: whether notes are owned per user or shared by the team workspace (BR-031) | whether results are restricted to a caller's own notes. This is the ownership predicate and it would sit in the query's filter. | the ownership decision and the access rule per operation |

Once unblocked, the section will hold the role matrix for this one read operation and the ownership predicate applied to the result set. Until then no role, no credential requirement and no ownership scoping is assumed.
<!-- /SECTION: backend.search.authorization -->
<!-- SECTION: backend.search.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**Scheduled work: none.** The indexes live in the notes database and are updated with each note change (`ASM-BE-074`, `ASM-BE-010`). No sweep, reindex or backfill job is defined by any input, so none is specified. If the index were external or asynchronous, that assumption would need to change.

**Retries: none on the server.** A degraded index returns 503 with `Retry-After`, and the client decides whether to retry (`ASM-BE-073`). There is no server-side retry and no circuit breaker.

**Idempotency.** `GET` is safe and idempotent (`INV-BE-053`). No idempotency key applies.

Retention deletion for login data is owned by `auth` (NFR-019) and is not a search concern.
<!-- /SECTION: backend.search.jobs -->
<!-- SECTION: backend.search.test-plan | status: DERIVED -->
## Test plan

Test names are planned. Requirement ids are the `BE-SEARCH-FR-` ids above.

**Unit**
- Parameter parsing and validation: `search.validation.*`, `search.tags.invalid_characters_is_400`, `search.pagination.invalid_values_is_400`, `search.archived.invalid_value_is_400` (FR-003, 006, 009, 012, 016).
- Keyword tokenisation and case handling: `search.keyword.words_all_must_match_case_insensitive` (FR-001, `ASM-BE-067`).
- Ordering comparator for pinned, relevance, update time and id tie-break: `search.ordering.*` (FR-017, 018).

**Integration** (PostgreSQL 16, seeded notes and tags)
- Filters: `search.keyword.*`, `search.tags.*`, `search.combined.*`, `search.archived.*` (FR-001 to 008, 014 to 016).
- Pagination: `search.pagination.*` (FR-010 to 013).
- Ordering across pages: `search.ordering.pinned_before_unpinned_across_pages` and `search.ordering.deterministic_across_pages` (FR-017, 018, INV-BE-052, 054).
- Read-your-writes: `search.consistency.new_edited_archived_note_visible_to_next_search` (`ASM-BE-074`).
- Degraded index: `search.availability.degraded_index_returns_503_retry_after`, using a forced index failure (FR-020).
- Injection inputs: `search.security.injection_inputs_treated_as_data` (FR-021).
- Routing: `search.routing.search_path_not_captured_by_get_note` (FR-022).

**Contract**
- `GET /notes/search` is present in the published spec with its 400 and 503 responses and its example payloads for search (BR-012, NFR-008, NFR-009, US-004-03, US-004-05).
- The contract documents `include_archived` (`CON-BE-054`).
- The contract-docs coverage check reports this operation as covered (US-004-04).
- The scope-guard contract suite finds no overlap between `/notes/search` and an excluded category (NFR-010).

**Non-functional**
- `search.perf.p95_under_300ms_at_50rps_100k_notes` (FR-019), and again at page size 100 (NFR-007). The environment is `GAP-BE-073`.
<!-- /SECTION: backend.search.test-plan -->
<!-- SECTION: backend.search.gaps | status: VERIFIED -->
## Gaps

### Contradiction checks run

- **Story ↔ design.** `design-brief.md` carries acceptance criteria only (AC-001-01-01 to AC-002-01-03), none about search. Nothing found.
- **Story ↔ requirement.** `CON-BE-003` (cited, defined in the project spec) concerns a missing `q`. `CON-BE-054` is raised here.
- **Requirement ↔ requirement.** BR-019 says ordering among notes with equal pinned value is "unchanged", but no prior ordering is defined (`ASM-BE-072`). BR-011 and NFR-007 agree. No other conflict found.
- **Design ↔ design.** Not applicable.

### Contradictions

**CON-BE-054 (non-critical).** The operation record for `API-SEARCH-580505` says archived notes are excluded and gives no override. BR-009 and US-003-06 (AC2) say the default excludes archived notes and a caller may pass `include_archived=true`.
- Reading used: BR-009 and US-003-06, the explicit requirements (FR-014, 015, 016).
- If the record wins: the override is not offered, US-003-06 AC2 fails, and no client can reach archived notes through search.
- If BR-009 wins: the record text is incomplete and the rendered contract must carry `include_archived`.
- Affected sections: `requirements` (FR-014 to 016).

### Questions

All three are `degradable`. This run is unattended, so each takes its stated default.

| ID | Question | Class | Default taken |
|---|---|---|---|
| `Q-BE-054` | Is a `limit` above the maximum rejected with 400 or clamped to the maximum? | degradable | rejected with 400 (`ASM-BE-068`) |
| `Q-BE-055` | With several tags, must a note carry all of them or any of them? | degradable | all of them (`ASM-BE-071`) |
| `Q-BE-056` | Is a whitespace-only `q` treated as empty? | degradable | treated as empty, so 400 when no tags (`ASM-BE-076`) |

### Assumptions

- **ASM-BE-067.** `q` is split into words. A note matches when every word appears in its title or body, case-insensitively. The `simple` text-search configuration is used: no stemming, no synonyms. No input names a language configuration. Confirm with the product owner. Used by FR-001 and `data-model`.
- **ASM-BE-068.** `page` is 1-based with default 1, and the default and maximum `limit` are those of `ASM-BE-009`. `page` below 1, `limit` below 1 or above the maximum, and non-integers are rejected with 400 (`Q-BE-054`).
- **ASM-BE-069.** `include_archived` defaults to false and accepts only `true` and `false`.
- **ASM-BE-070.** `tags` is one comma-separated parameter. An empty element, as in `a,,b`, is rejected with 400.
- **ASM-BE-071.** Multiple tags are all-of: a note must carry every supplied tag (`Q-BE-055`). US-003-02 says "one or more tags" and does not say which. US-003-04 says "matching both" for keyword and tag.
- **ASM-BE-072.** Within a pinned group, order is relevance (descending) when `q` is present. Otherwise it is the note's updated timestamp (descending). Ties are broken by note id (ascending). This definition is mine; BR-019 gives none.
- **ASM-BE-073.** An index query error, or exceeding a request deadline, yields 503. It is judged per request, with no circuit breaker and no stored index-health state.
- **ASM-BE-074.** The index is maintained in the notes database with each note change, so the next search sees the change. No asynchronous lag exists.
- **ASM-BE-075.** Each result item uses the same note representation as the get-note response (`notes` owns it), plus the pagination envelope carried by the rendered contract.
- **ASM-BE-076.** A whitespace-only `q` is treated as empty (`Q-BE-056`).
- **ASM-BE-077.** Tag names are compared exactly as stored: case-sensitive, no normalisation (`GAP-BE-010`).
- **ASM-BE-078.** The ordering index `idx_notes_order` is a performance proposal for tags-only searches. It may be dropped if load tests show it is unnecessary.
- **ASM-BE-079.** A malformed filter combination is a repeated parameter, or a parameter outside `q`, `tags`, `page`, `limit` and `include_archived`. The latter is an assumption, not stated by any input. It means a misspelt parameter fails loudly. Confirm that web_app does not send extra parameters, such as cache-busters.

### Gaps

**GAP-BE-070.** The `Retry-After` value and format on 503, and the request deadline after which a search counts as hanging, are unstated.
- Degrades: FR-020, `state-machine` (UNAVAILABLE).
- Instead: the header is required, and the value is left to the error-handling requirement.
- Closing artefact: an error-handling requirement. This refines `GAP-BE-011`.

**GAP-BE-071.** Not stated: minimum or maximum length of `q`, maximum number of tags in one request, maximum page depth.
- Degrades: FR-003, 006, 013, FR-019.
- Instead: no bound is imposed beyond the page size.
- Closing artefact: validation requirements.

**GAP-BE-072.** The relevance ranking function is unspecified. US-003-01 says only "ordered by relevance".
- Degrades: FR-001, FR-018.
- Instead: relevance means the ranking of the index match, and ties are broken by `ASM-BE-072`.
- Closing artefact: a ranking requirement, or a decision that the index default is accepted.

**GAP-BE-073.** The environment for verifying NFR-006 and NFR-007 is unstated: hardware, connection pool, data distribution and tag cardinality.
- Degrades: FR-019, and the `test-plan` performance test.
- Instead: the test is planned against 50 requests per second and about 100k notes only.
- Closing artefact: a performance-test environment definition.

### Gaps defined in the project spec and relevant here

- `GAP-BE-001`: the contents of the four inbound handoffs (`HANDOFF-WEBAPP-263975`, `-452996`, `-500592`, `-732181`) were not supplied. This module cannot say which of them it answers.
- `GAP-BE-005`: no operation lists active notes or archived notes only. `include_archived=true` mixes both, so it is not an archived-only view (US-006-05). Plainly stated, the web_app needs an archived-only list or filter and this module does not provide one.
- `GAP-BE-006`: encryption at rest is not stated.
- `GAP-BE-010`: tag rules are not stated.
- `GAP-BE-011`: the `Retry-After` value on 503 is not stated; `GAP-BE-070` narrows it.
<!-- /SECTION: backend.search.gaps -->
