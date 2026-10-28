---
id: agent-developer/notes_api/backend/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/search-SPEC.md
depends_on: [notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06, notes_api:US-006-03]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: notes_api:backend/modules/search-SPEC.md
---

<!-- SECTION: backend.search.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: search
surface: backend
spec_mode: NOT-APPLICABLE
doc_status: DRAFT
blocked_by: [Q-BE-001, Q-BE-002, Q-BE-003]
scope: [API-SEARCH-580505]
```

`blocked_by` lists the project-level blocking questions. They block only `backend.search.authorization`. The rest of the module is written and carries its own `ASM-` and `GAP-` entries.
<!-- /SECTION: backend.search.frontmatter -->
<!-- SECTION: backend.search.purpose | status: DERIVED -->
## Purpose

**Responsible for** the single read operation `API-SEARCH-580505` (GET `/notes/search`). It provides:

- Keyword search over notes (US-003-01).
- Tag filtering (US-003-02).
- Keyword and tag filters combined in one request (US-003-04, BR-010).
- Pagination with a total count (US-003-05, BR-011).
- Exclusion of archived notes by default, with `include_archived` as the opt-in (US-003-06, BR-009).
- Pinned-first ordering (US-003-01, BR-019).
- A search latency of p95 < 300 ms (US-003-03, NFR-006, NFR-007).
- A 503 response with a `Retry-After` header when the search index is degraded or unavailable (US-003-03).

For US-006-03 the backend's part is this endpoint. A search that finds nothing returns 200 with an empty array, so the web app can show a "no results" message instead of an error. The search box, the message and the form are web-app concerns.

**Not responsible for** (owned elsewhere per the project module map, or by no module):

- Creating, editing, getting, tagging, archiving or unarchiving notes, and the definition of a note, its tags and its archive state. These belong to `notes`. Search only reads them.
- Listing all notes, or listing archived notes only. No input defines either operation (`GAP-BE-005`, `GAP-BE-006`). Search is not a substitute: it needs `q` or `tags`, and `include_archived=true` adds archived notes to the results instead of replacing the active ones.
- Login codes, credentials and the access rule. These belong to `auth` and are pending `Q-BE-001`, `Q-BE-002` and `Q-BE-003`.
- The published OpenAPI document, its example payloads (including the empty-result example, US-004-05) and the spec-versus-route coverage check. These belong to `contract-docs`.
- Refusal of attachment, real-time and mobile calls. This belongs to `scope-guard`.
- The residency checks, scans and report. These belong to `residency`.
<!-- /SECTION: backend.search.purpose -->
<!-- SECTION: backend.search.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Tables owned by this module: none.** Search reads the note and tag-entry structures defined in `backend.notes.data-model`. They are created by migrations 001 and 002 of the project migration plan. They are not restated here (core rule 6). The note fields search relies on are `title`, `body`, `pinned`, the archive state, the timestamps and the note's tags (US-002-01, US-002-06, BR-009, BR-018).

**Objects owned: index artefacts only**, created by migration 003 of the project migration plan ("Search index on title, body and tags").

| Index | Definition | Query it serves | Source |
|---|---|---|---|
| Full-text index over note `title` and `body` | PostgreSQL full-text index, built on an expression over the two columns. It adds no column to the notes table. | The `q` match (BE-SEARCH-FR-001) | US-003-01, `ASM-BE-063` |
| Tag lookup index, by tag name then note reference | Secondary index on the notes-owned tag entries | The `tags` filter (BE-SEARCH-FR-004, BE-SEARCH-FR-007) | US-003-02, `ASM-BE-063` |
| Index supporting archive-state and `pinned` ordering | **Not specified.** Added only if the load test fails the p95 target. | Default archived exclusion and pinned-first ordering | NFR-006. No figure is invented. |

**Constraints.**

- The module owns no foreign keys, so there is no `ON DELETE` behaviour to state.
- The module owns no money columns and no timestamp columns.
- The full-text index is part of the same PostgreSQL 16 database as the notes (`platform.md`). It is therefore subject to the same EU-region provisioning as the database (BR-001). No external search service is introduced (`ASM-BE-063`).
- The index is derived from `title` and `body`, so its content follows the note and is removed when the note's data is removed (derived).
- Encryption at rest of the index is unstated (`GAP-BE-010`).
- The migration is reversible. Dropping an index loses no data (project `backend.migration-plan`, row 003).
- Query values are bound parameters. They are never concatenated into query text (NFR-012; BE-SEARCH-FR-023).
<!-- /SECTION: backend.search.data-model -->
<!-- SECTION: backend.search.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**Persisted entity states: none.** Search owns no entity with a status column. The note's active or archived state belongs to `notes` (BR-009 filters on `status=active`). A search never changes it (`INV-BE-054`).

**Observed index availability.** Stories describe one externally visible failure mode: "index degraded or unavailable" returns 503 (US-003-03). It is modelled as two observed states and is not persisted.

| From | To | Trigger | Guard |
|---|---|---|---|
| AVAILABLE | UNAVAILABLE | An index query fails, or exceeds the request deadline | The deadline value is unstated (`GAP-BE-082`) |
| UNAVAILABLE | AVAILABLE | A later request's index query succeeds | None |

- **Terminal states:** none.
- **Per-request evaluation.** Each request is evaluated independently. No circuit breaker or cached health flag is specified (`ASM-BE-070`).
- **Illegal transitions:** none exist. The only error outcome is 503 with `Retry-After`, with the proposed error code `SEARCH_UNAVAILABLE` from `backend.error-model`.
- **Triggers.** How "degraded" is detected is a gap (`GAP-BE-082`).
<!-- /SECTION: backend.search.state-machine -->
<!-- SECTION: backend.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements implement `API-SEARCH-580505`. Their error statuses and proposed error codes are listed in `backend.error-model`. This section does not restate them.

**BE-SEARCH-FR-001 — Keyword match**
- Given active notes whose title or body contains the keyword,
- When `GET /notes/search` is called with `q`,
- Then the response is 200 and contains the matching notes. The match covers `title` and `body` only, not tag names (`ASM-BE-072`). Word matching and case follow `ASM-BE-064`.
- Traces to: US-003-01, US-006-03, BR-019
- Verification: (planned) `search_q_matches_title_or_body`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-002 — No keyword match**
- Given no note matches the keyword,
- When the search is executed,
- Then the response is 200 with an empty array and is not an error.
- Traces to: US-003-01, US-006-03
- Verification: (planned) `search_q_no_match_returns_200_empty`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-003 — No usable filter**
- Given `q` is supplied but empty, or neither `q` nor `tags` is supplied,
- When the search request is made,
- Then the response is 400 with a validation error and no data. A request with `tags` and no `q` is valid (`CON-BE-053`, `Q-BE-058`).
- Traces to: US-003-01
- Verification: (planned) `search_without_q_or_tags_returns_400`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-004 — Tag filter**
- Given notes tagged `project-x` exist,
- When `GET /notes/search?tags=project-x` is called,
- Then only notes carrying that tag are returned.
- Traces to: US-003-02, US-006-03
- Verification: (planned) `search_tags_returns_only_tagged`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-005 — Tag without matches**
- Given a tag that no note carries,
- When the search is executed,
- Then the response is 200 with an empty result set.
- Traces to: US-003-02
- Verification: (planned) `search_tags_no_match_returns_200_empty`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-006 — Invalid tag characters**
- Given the `tags` parameter contains invalid characters,
- When the search request is made,
- Then the response is 400 with error detail. The allowed set is unstated (`GAP-BE-079`, `GAP-BE-008`).
- Traces to: US-003-02
- Verification: (planned) `search_tags_invalid_chars_returns_400`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-007 — Several tags**
- Given more than one tag is supplied,
- When the search is executed,
- Then only notes carrying all supplied tags are returned (`ASM-BE-009`, `Q-BE-004`). The parameter encoding is `ASM-BE-068`.
- Traces to: US-003-02, BR-010
- Verification: (planned) `search_multiple_tags_all_of`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-008 — Combined filters**
- Given `q=budget` and `tags=finance` are both supplied,
- When the search is executed,
- Then only notes matching both are returned.
- Traces to: US-003-04, BR-010
- Verification: (planned) `search_q_and_tags_intersection`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-009 — Combined filters with no overlap**
- Given the keyword matches but the tag filter excludes every matching note,
- When the search is executed,
- Then the response is 200 with an empty result set.
- Traces to: US-003-04
- Verification: (planned) `search_combined_no_overlap_returns_empty`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-010 — Malformed filter combination**
- Given a malformed filter combination is supplied,
- When the search request is made,
- Then the response is 400 with error details. "Malformed" is not defined beyond the cases in FR-003, FR-006, FR-014 and FR-018 (`GAP-BE-080`).
- Traces to: US-003-04
- Verification: (planned) `search_malformed_combination_returns_400`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-011 — Ordering**
- Given matching notes of which some are pinned,
- When the results are returned,
- Then:
  - every pinned note precedes every unpinned note across the whole ordered result set, not only within a page;
  - within each group, notes are ordered by relevance when `q` is present, otherwise by the proposed order in `ASM-BE-069`;
  - ties are broken by note id so that pages never overlap or skip.
- Traces to: US-003-01, BR-019, US-003-05
- Verification: (planned) `search_pinned_first_stable_across_pages`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-012 — Pagination and total**
- Given more matches exist than the page size,
- When `page` and `limit` are supplied,
- Then the correct subset is returned together with the total count of all matches, regardless of page.
- Traces to: US-003-05, BR-011
- Verification: (planned) `search_pagination_subset_and_total`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-013 — Page beyond results**
- Given a page number beyond the available results,
- When the search is executed,
- Then the response is 200 with an empty array. The total count still reports all matches (`ASM-BE-073`).
- Traces to: US-003-05
- Verification: (planned) `search_page_beyond_returns_200_empty`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-014 — Invalid paging values**
- Given `page` or `limit` is negative or non-numeric,
- When the search request is made,
- Then the response is 400 with a validation error. The module also rejects `page` = 0 (`ASM-BE-065`) and `limit` above the maximum (`ASM-BE-066`, `Q-BE-059`).
- Traces to: US-003-05, BR-011
- Verification: (planned) `search_invalid_paging_returns_400`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-015 — Default and maximum page size**
- Given `limit` is omitted,
- When the search is executed,
- Then the default page size applies. The module enforces a maximum page size. The proposed values are 20 and 100 (`ASM-BE-011`, `Q-BE-005`, `GAP-BE-009`).
- Traces to: BR-011, NFR-007
- Verification: (planned) `search_default_and_max_page_size`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-016 — Archived excluded by default**
- Given archived and active notes both match,
- When a default search is executed,
- Then only active notes are returned.
- Traces to: US-003-06, BR-009
- Verification: (planned) `search_default_excludes_archived`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-017 — Archived included on request**
- Given `include_archived=true`,
- When the search is executed,
- Then archived notes are returned together with active notes, in the single order of FR-011.
- Traces to: US-003-06, BR-009
- Verification: (planned) `search_include_archived_true_adds_archived`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-018 — Invalid `include_archived`**
- Given an invalid `include_archived` value,
- When the search request is made,
- Then the response is 400 with an error message. Only `true` and `false` are accepted (`ASM-BE-067`).
- Traces to: US-003-06
- Verification: (planned) `search_include_archived_invalid_returns_400`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-019 — Degraded index**
- Given the search index is degraded or unavailable,
- When a search is requested,
- Then the response is 503 with a `Retry-After` header and does not hang. The header value and the deadline are unstated (`GAP-BE-082`).
- Traces to: US-003-03
- Verification: (planned) `search_index_failure_returns_503_retry_after`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-020 — Latency under load**
- Given a request rate of 50 requests per second,
- When searches are executed,
- Then p95 latency is under 300 ms.
- Traces to: US-003-03, NFR-006
- Verification: (planned) `search_load_p95_under_300ms_50rps`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-021 — Large dataset**
- Given a dataset of about 100k notes,
- When a search is executed,
- Then the p95 SLA is still met via indexed lookup.
- Traces to: US-003-03
- Verification: (planned) `search_load_100k_notes_p95_under_300ms`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-022 — Latency at maximum page size**
- Given a page size of 100,
- When searches are executed,
- Then p95 latency stays under 300 ms.
- Traces to: NFR-007
- Verification: (planned) `search_limit_100_p95_under_300ms`
- API: → API-SEARCH-580505

**BE-SEARCH-FR-023 — Injection safety**
- Given any `q`, `tags`, `page`, `limit` or `include_archived` value,
- When the request is processed,
- Then every value is validated and bound as a query parameter. No security scan finds a critical injection vulnerability on this endpoint.
- Traces to: NFR-012
- Verification: (planned) `search_injection_payloads_are_inert`
- API: → API-SEARCH-580505

**Story map for this module.**

| Story | Requirements |
|---|---|
| US-003-01 | FR-001, FR-002, FR-003, FR-011 |
| US-003-02 | FR-004, FR-005, FR-006, FR-007 |
| US-003-03 | FR-019, FR-020, FR-021 |
| US-003-04 | FR-008, FR-009, FR-010 |
| US-003-05 | FR-012, FR-013, FR-014, FR-015, FR-022 |
| US-003-06 | FR-016, FR-017, FR-018 |
| US-006-03 | FR-001, FR-002, FR-004 (API part only; the UI belongs to web-app) |
<!-- /SECTION: backend.search.requirements -->
<!-- SECTION: backend.search.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-SEARCH-580505` | GET | `/notes/search` | `search` | 200 | 400, 503 | Search notes by keyword, tag, or both combined. Excludes archived notes unless requested. Pinned notes come first. Results are paginated. Returns 503 with Retry-After when the search index is degraded. |

### `API-SEARCH-580505` — GET `/notes/search`

Search notes by keyword, tag, or both combined. Excludes archived notes unless requested. Pinned notes come first. Results are paginated. Returns 503 with Retry-After when the search index is degraded.

**Request:** 
- `q` (string): Keyword matched against note title and body. Malformed values return 400 VALIDATION_ERROR.
- `tags` (array): Tag filter. Combined with q when both are given. A note must carry all supplied tags (ASM-BE-009, pending Q-BE-004). Sent as a query parameter.
- `page` (integer): Page number for pagination. Invalid values return 400 VALIDATION_ERROR.
- `limit` (integer): Page size. Default 20 and maximum 100 are assumed (ASM-BE-011, pending Q-BE-005). Invalid values return 400 VALIDATION_ERROR.
- `include_archived` (boolean): When true, archived notes are included in results. Default false, so archived notes are excluded.
**Response:** 
- `items` (array, required): Matching notes on the requested page, pinned notes first. Each item shows the note id, title, body, tags, pinned flag, archived state and timestamps.
- `page` (integer, required): Page number of this result set.
- `limit` (integer, required): Page size applied to this result set.
- `total` (integer, required): Total number of notes matching the filters across all pages (proposed; the total count is not stated in the inputs).
<!-- /SECTION: backend.search.api-contract -->
<!-- SECTION: backend.search.invariants | status: DERIVED -->
## Invariants

- **`INV-BE-054`** — No search request creates, modifies or deletes any data. Derived from the read-only nature of the operation and BR-006, BR-007.
- **`INV-BE-055`** — Unless `include_archived=true` is supplied, no returned note is archived (BR-009).
- **`INV-BE-056`** — In the ordered result set, no unpinned note precedes a pinned note, across pages (BR-019).
- **`INV-BE-057`** — Every returned note satisfies every supplied filter (`q`, `tags`, archive rule). No note outside the intersection is returned (BR-010, US-003-04).
- **`INV-BE-058`** — The number of items on a page never exceeds the effective `limit`, and the effective `limit` never exceeds the maximum page size. The total count equals the number of matching notes regardless of page (BR-011, US-003-05).
- **`INV-BE-059`** — With unchanged data and identical parameters, the order is identical, so consecutive pages neither overlap nor skip (`ASM-BE-069`).
- **`INV-BE-060`** — "No matches" is always 200 with an empty array, and index failure is always 503. Neither is ever returned as the other, and no 200 carries partial results from a failed index (US-003-01, US-003-03).
- **`INV-BE-061`** — A request with an invalid parameter returns 400 and no result data (US-003-01 to US-003-06).
<!-- /SECTION: backend.search.invariants -->
<!-- SECTION: backend.search.authorization | status: BLOCKED -->
## Authorization

**Blocked.** Which role may call `API-SEARCH-580505`, and the ownership predicate that limits which notes a caller sees, depend on project decisions that are still open:

- `Q-BE-001`: who may obtain a login code.
- `Q-BE-002`: whether notes are owned per user or shared by the team workspace, and the access rule (BR-031).
- `Q-BE-003`: what credential a verify issues, and whether note and search endpoints require it.

**What would unblock it:**

- Answers to those three questions (see also `GAP-BE-004` and `GAP-BE-085`).
- Clean source text for BR-030 and BR-031 (`GAP-BE-003`).
- The contents of the four inbound handoff files (`GAP-BE-001`), if they answer any of these questions.
<!-- /SECTION: backend.search.authorization -->
<!-- SECTION: backend.search.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

- **Scheduled work:** none is specified or derivable. With the index inside PostgreSQL, it is maintained with the note data (`ASM-BE-063`), so no sync or rebuild job exists. NFR-019 retention does not delete notes, so no sweep touches the index.
- **Retries:** server-side retries are not specified. A client that receives 503 retries after the `Retry-After` interval (FR-019).
- **Idempotency keys:** not applicable. The operation is a GET and has no side effects (`INV-BE-054`).
- **Index health detection:** no input defines a health check or its schedule (`GAP-BE-082`).
<!-- /SECTION: backend.search.jobs -->
<!-- SECTION: backend.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

**Unit**
- Parameter parsing and validation, table-driven: `q`, `tags`, `page`, `limit`, `include_archived` (FR-003, FR-006, FR-010, FR-014, FR-018).
- The ordering comparator: pinned first, then relevance or the proposed order, then note id (FR-011).
- Pagination arithmetic: offset, total count, page beyond the last (FR-012, FR-013).

**Integration** (real PostgreSQL 16, per `platform.md`)
- Seeded active, archived, pinned and tagged notes, covering FR-001, FR-002, FR-004, FR-005, FR-007, FR-008, FR-009, FR-011, FR-016 and FR-017.
- Failure injection on the index query: a 503 response with `Retry-After` and no hang (FR-019).
- Page stability: with pinned notes straddling a page boundary, concatenating pages 1..n equals the full ordered set with no duplicates (`INV-BE-056`, `INV-BE-059`).
- Read-only check: a before-and-after row comparison around a search (`INV-BE-054`).
- Route-collision check, if the notes module exposes `/notes/{id}`: `/notes/search` must not be handled as a note id. This depends on the notes module path, which is not visible to this module.

**Load and security**
- 100k seeded notes, 50 requests per second, p95 < 300 ms; also at `limit` = 100 (FR-020, FR-021, FR-022).
- Injection payloads in `q` and `tags`, plus the security scan (FR-023, NFR-012).

**Contract**
- The route registry contains `API-SEARCH-580505` and the published OpenAPI document lists it, including its 400 and 503 responses (BR-012; checked by `contract-docs`).
- Responses validate against the documented schema.
- `scope-guard` contract tests find no real-time upgrade accepted on this path (BR-015).
<!-- /SECTION: backend.search.test-plan -->
<!-- SECTION: backend.search.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Contradictions**
- **`CON-BE-053`** (non-critical)
  - Claims: US-003-01 requires 400 when `q` is missing or empty. US-003-02 and BR-010 allow `tags=project-x` with no `q`.
  - Consequence of each reading: requiring `q` makes tag-only search impossible. Allowing tag-only search relaxes US-003-01's "missing".
  - Reading taken: `q` may be absent when `tags` is present. A supplied-but-empty `q` and a request with neither parameter return 400.
  - Status: FR-003 and FR-004 are `DRAFT-UNVERIFIED`. See `Q-BE-058`.
- **Sweep result.** No other contradiction was found in the four directions. No design content was supplied for this module.

**Questions** (degradable; defaults taken because the run is unattended)
- **`Q-BE-058`**: Is a tags-only search (no `q`) permitted? Default: yes.
- **`Q-BE-059`**: Is `limit` above the maximum rejected with 400, or clamped? Default: rejected with 400.

**Assumptions**
- **`ASM-BE-063`**: Search uses PostgreSQL's own full-text index inside the notes database. No external engine is named by any input. Confirm with the platform owner.
- **`ASM-BE-064`**: `q` is split into words. A note matches when every word appears in its title or body, case-insensitively.
- **`ASM-BE-065`**: `page` is 1-based, so `page` = 0 is rejected with 400.
- **`ASM-BE-066`**: `limit` above the maximum is rejected with 400 and not clamped. See `Q-BE-059`.
- **`ASM-BE-067`**: `include_archived` accepts only `true` and `false`.
- **`ASM-BE-068`**: `tags` is a single comma-separated parameter.
- **`ASM-BE-069`**: Within a pinned group, results are ordered by relevance (descending) when `q` is present. Otherwise they are ordered by the note's updated timestamp (descending). Ties are broken by note id. BR-019 says ordering "is unchanged", but no prior ordering is defined (`GAP-BE-005`).
- **`ASM-BE-070`**: An index query failure, or exceeding a request deadline, yields 503. The failure is judged per request, with no circuit breaker.
- **`ASM-BE-071`**: Each result item uses the same note representation as the get-note response (title, body, tags, archive status, pinned flag), owned by `notes`.
- **`ASM-BE-072`**: `q` matches `title` and `body` only, and "content" in US-003-01 means `body`. Tag names are matched only through `tags`.
- **`ASM-BE-073`**: A page beyond the available results still reports the full total count.

**Gaps**
- **`GAP-BE-078`**: Keyword semantics are unstated: word versus substring matching, multi-word handling, case, language and stemming, and how relevance is computed. Closing artefact: acceptance criteria or a decision for the `q` match.
- **`GAP-BE-079`**: The `tags` encoding for several tags, the allowed characters, and case sensitivity are unstated (related: `GAP-BE-008`). Closing artefact: tag field constraints.
- **`GAP-BE-080`**: "Malformed filter combination" (US-003-04) is not defined. Closing artefact: an acceptance criterion listing the malformed cases.
- **`GAP-BE-081`**: The behaviour of `limit` above the maximum is unstated. Closing artefact: the answer to `Q-BE-059`.
- **`GAP-BE-082`**: The definition and detection of "degraded", the request deadline and the `Retry-After` value are unstated. Closing artefact: an operations NFR.
- **`GAP-BE-083`**: The rate-limit class for the endpoint, and any per-caller limit, are unstated. 50 requests per second is a test condition (NFR-006) and not a limit. Closing artefact: an NFR.
- **`GAP-BE-084`**: The delay between a note change and its appearance in search is unstated. This matters after create, tag and archive operations. Closing artefact: an NFR on search freshness.
- **`GAP-BE-085`**: Once the access rule is decided, the total count must count only notes the caller may read. Closing artefact: the answers to `Q-BE-002` and `Q-BE-003`.
- **`GAP-BE-086`**: Search depends on definitions that `notes` must supply: the note representation, the archive-state column, and the tag-entry structure. Closing artefact: `backend.notes.data-model`.
<!-- /SECTION: backend.search.gaps -->
