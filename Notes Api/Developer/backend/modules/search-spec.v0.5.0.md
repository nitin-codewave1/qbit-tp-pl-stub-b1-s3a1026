---
id: agent-developer/notes_api/backend/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/search-SPEC.md
depends_on: [notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
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

- `blocked_by` names two blocking questions raised in `BACKEND-SPEC.md` (`backend.auth-model`, `backend.coverage`).
- They block only `backend.search.authorization`. Every other section is written.
- `contract_pin` is omitted: the backend is the source of the contract.
<!-- /SECTION: backend.search.frontmatter -->
<!-- SECTION: backend.search.purpose | status: DERIVED -->
## Purpose

**Responsible for.** One read operation, `API-SEARCH-580505` (GET `/notes/search`).
- It finds notes by keyword across title and body, by tag, or by both combined (US-003-01, US-003-02, US-003-04).
- It excludes archived notes unless `include_archived=true` (US-003-06, BR-009).
- It paginates results with a total count (US-003-05, BR-011).
- It returns pinned notes before unpinned ones (US-003-01, BR-019).
- It returns 503 with a retry-after header when the search index is degraded (US-003-03).
- It meets p95 < 300 ms at 50 requests per second (NFR-006, NFR-007, US-003-03).

**Derivation.** The module boundary is `search` in `backend.module-map`. The dependency `search → notes` is read-only.

**Not responsible for.**
- Creating, editing, archiving or unarchiving notes, or the `pinned` flag. These belong to `notes` (`backend.module-map`).
- Adding or removing tags. They belong to `notes`.
- Tag removal and distinct-tag listing (`GAP-BE-003`, `Q-BE-006`).
- Listing notes without a filter, such as an active-notes list or an archived-only list. No such operation is defined (`GAP-BE-002`, `Q-BE-005`).
- Authentication, sessions and the access rule. They belong to `auth` and are blocked (`Q-BE-002`, `Q-BE-003`).
- The published OpenAPI document and its examples (`contract-docs`).
- Refusing out-of-scope calls (`scope-guard`).
- The error envelope and the error-code registry (`backend.error-model`). Retry-after values are open there too (`Q-BE-004`).
<!-- /SECTION: backend.search.purpose -->
<!-- SECTION: backend.search.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Ownership.**
- `search` owns no table and persists nothing. No input requires a search history, search log or saved query.
- Notes and note-tag rows are owned by `notes`. Their columns, types, nullability and constraints are defined once in `backend.notes.data-model` and are not restated here.
- `search` owns only the index structures. They are migration 4 in `backend.migration-plan` ("Add the search index structures").

**Fields read by a search** (names from `backend.domain-model`):

| Entity | Fields read | Used for |
|---|---|---|
| NOTE | title, body | keyword match (US-003-01) |
| NOTE | status | archived exclusion (BR-009) |
| NOTE | pinned | pinned-first ordering (BR-019) |
| NOTE_TAG | tag_name, note_id | tag filter (US-003-02) |

**Index structures.** The labels are local to this document, not identifiers.

| Structure | Over | Query it serves | Source |
|---|---|---|---|
| Text index | NOTE title and body | `q` match, under the p95 target at about 100k notes | US-003-01, US-003-03, NFR-006 |
| Tag index | NOTE_TAG (tag_name, note_id) | `tags` filter. Note-tag rows are unique per note and tag (migration 2, `notes`). | US-003-02, `backend.migration-plan` |
| Ordering and archive filter | NOTE (pinned, status) | pinned-first order and the archived exclusion | BR-009, BR-019 |

**Open points.**
- The index technology, type and update mechanism are unstated. `ASM-BE-068` assumes the index lives in the PostgreSQL 16 database (`platform.md` names no other store). `GAP-BE-075` records the rest.
- Whether a dedicated index on (pinned, status) is needed cannot be decided without data volumes (`GAP-BE-075`).
- "Relevance" has no stated scoring (`GAP-BE-073`).
- Tag rules are unstated (`GAP-BE-003`).
<!-- /SECTION: backend.search.data-model -->
<!-- SECTION: backend.search.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**Entity state.**
- `search` owns no entity with a status column, so it owns no entity state machine.
- It reads the note `status` (active or archived), which is owned by `notes`. Its transitions are in `backend.notes.state-machine` and are not restated here.
- Search never changes a status (`INV-BE-051`).

**Index availability.** The only state this module can distinguish is whether the index can answer. US-003-03 distinguishes "degraded or unavailable" from normal operation.

| State | Meaning | Source |
|---|---|---|
| AVAILABLE | the index answers queries | derived from US-003-03 |
| DEGRADED | the index is degraded or unavailable | US-003-03 |

| Transition | Trigger | Guard | Source |
|---|---|---|---|
| AVAILABLE → DEGRADED | not specified | not specified | `GAP-BE-075` |
| DEGRADED → AVAILABLE | not specified | not specified | `GAP-BE-075` |

**Behaviour in each state.**
- AVAILABLE: validate the parameters, then answer. Invalid parameters give 400.
- DEGRADED: a valid request gets 503 with a retry-after header (BE-SEARCH-FR-017).
- An invalid request in DEGRADED gets 400, because validation runs first (`ASM-BE-072`).

**Terminal states.** None. Both states are recoverable, and the recovery mechanism is unspecified.
<!-- /SECTION: backend.search.state-machine -->
<!-- SECTION: backend.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All entries depend on `→ API-SEARCH-580505`. Verification names are proposed (planned). Error codes follow `backend.error-model`, whose names are themselves proposed (`ASM-BE-006`).

**BE-SEARCH-FR-001: keyword match**
- Given notes whose title or body matches a keyword,
- When GET `/notes/search?q=keyword` is called,
- Then 200 returns the matching notes, pinned notes first, then ordered by relevance.
- Traces to. US-003-01, BR-019.
- Verification. `search_keyword_matches_title_or_body` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-002: no keyword match**
- Given no note matches the keyword,
- When the search runs,
- Then 200 returns an empty array, not an error.
- Traces to. US-003-01.
- Verification. `search_no_match_returns_empty_200` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-003: no usable filter**
- Given `q` is missing, empty or whitespace-only, and no `tags` value is supplied,
- When the search request is made,
- Then 400 returns a validation error.
- A request with `tags` and no `q` is valid (`CON-BE-002`, `ASM-BE-004`, `ASM-BE-063`).
- Traces to. US-003-01, US-003-02, BR-010.
- Verification. `search_without_q_or_tags_400`; `search_tags_only_is_valid` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-004: tag filter**
- Given notes tagged `project-x` exist,
- When GET `/notes/search?tags=project-x` is called,
- Then only notes carrying that tag are returned.
- Traces to. US-003-02.
- Verification. `search_by_tag_returns_only_tagged` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-005: tag with no matching notes**
- Given a tag with no matching notes,
- When the search runs,
- Then 200 returns an empty result set.
- Traces to. US-003-02.
- Verification. `search_unused_tag_returns_empty_200` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-006: invalid tag characters**
- Given the `tags` parameter contains invalid characters,
- When the search request is made,
- Then 400 returns an error detail.
- The valid character set is unstated (`GAP-BE-003`).
- Traces to. US-003-02.
- Verification. `search_tags_with_invalid_chars_400` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-007: combined filters**
- Given `q=budget` and `tags=finance` are both supplied,
- When the search runs,
- Then only notes matching both criteria are returned.
- Several tags in `tags` follow `ASM-BE-061` and `ASM-BE-062`.
- Traces to. US-003-04, BR-010.
- Verification. `search_q_and_tags_requires_both` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-008: combined filters exclude everything**
- Given the keyword matches but the tag filter excludes all matches,
- When the search runs,
- Then an empty result set is returned.
- Traces to. US-003-04.
- Verification. `search_combined_no_overlap_returns_empty` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-009: malformed filter combination**
- Given a malformed filter combination,
- When the search request is made,
- Then 400 returns error details.
- "Malformed" is not defined by any input beyond FR-003, FR-006, FR-012 and FR-016 (`GAP-BE-077`).
- Traces to. US-003-04.
- Verification. `search_malformed_combination_400` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-010: pagination**
- Given more results exist than the page size,
- When `page` and `limit` are supplied,
- Then the correct subset is returned along with a total count.
- The count is the total across all pages (`ASM-BE-067`).
- Traces to. US-003-05, BR-011.
- Verification. `search_pagination_returns_subset_and_total` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-011: page beyond the results**
- Given a page number beyond the available results,
- When the search runs,
- Then 200 returns an empty array.
- Traces to. US-003-05.
- Verification. `search_page_beyond_results_returns_empty_200` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-012: invalid paging values**
- Given `page` or `limit` is negative or non-numeric,
- When the search request is made,
- Then 400 returns a validation error.
- By assumption the same applies to zero, and to a `limit` above the maximum (`ASM-BE-065`).
- Traces to. US-003-05.
- Verification. `search_invalid_page_or_limit_400` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-013: bounded result sets**
- Given a request with or without `limit`,
- When the search runs,
- Then the page never exceeds the maximum page size, and a default page size applies when `limit` is absent.
- Both values follow `ASM-BE-065`.
- Traces to. BR-011, NFR-007.
- Verification. `search_default_and_max_page_size_enforced` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-014: archived excluded by default**
- Given archived and active notes both match,
- When a default search runs,
- Then only active notes are returned.
- Traces to. US-003-06, BR-009.
- Verification. `search_default_excludes_archived` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-015: archived included on request**
- Given `include_archived=true`,
- When the search runs,
- Then archived notes are included in the results.
- Traces to. US-003-06, BR-009.
- Verification. `search_include_archived_true_includes_archived` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-016: invalid `include_archived`**
- Given an `include_archived` value other than the accepted values (`ASM-BE-066`),
- When the search request is made,
- Then 400 returns an error message.
- Traces to. US-003-06.
- Verification. `search_invalid_include_archived_400` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-017: degraded index**
- Given the search index is degraded or unavailable,
- When a valid search is requested,
- Then 503 is returned with a retry-after header instead of hanging.
- The header value is unstated (`Q-BE-004`, `GAP-BE-004`).
- Traces to. US-003-03.
- Verification. `search_index_degraded_returns_503_with_retry_after` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-018: latency**
- Given 50 requests per second, and a dataset of about 100k notes,
- When searches run,
- Then p95 latency is under 300 ms, including page sizes up to 100.
- Traces to. US-003-03, NFR-006, NFR-007.
- Verification. `search_p95_under_300ms_at_50rps_100k_notes` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-019: ordering**
- Given any result set, including one with archived notes included,
- When the results are ordered,
- Then all pinned notes precede unpinned notes across the whole result set.
- Within each group, ordering is relevance first (`GAP-BE-073`), then note id (`ASM-BE-064`), so pages are stable.
- Traces to. US-003-01, BR-019.
- Verification. `search_pinned_first_across_pages`; `search_order_is_stable_between_pages` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-020: route precedence**
- Given a parameterised note-get route exists (`backend.notes`),
- When GET `/notes/search` is called,
- Then it is handled by search, not as a note id (`ASM-BE-070`).
- Traces to. US-003-01.
- Verification. `search_route_not_shadowed_by_note_get` (planned).
- API. → API-SEARCH-580505

**BE-SEARCH-FR-021: input handled as data**
- Given `q`, `tags` or other parameters containing injection payloads,
- When the search runs,
- Then they are treated as data, never as query syntax.
- Traces to. NFR-012.
- Verification. `search_injection_payloads_are_inert`; the release security scan (planned).
- API. → API-SEARCH-580505

**Story coverage in this module**

| Story | Requirements |
|---|---|
| US-003-01 | FR-001, FR-002, FR-003, FR-019, FR-020 |
| US-003-02 | FR-003, FR-004, FR-005, FR-006 |
| US-003-03 | FR-017, FR-018 |
| US-003-04 | FR-007, FR-008, FR-009 |
| US-003-05 | FR-010, FR-011, FR-012, FR-013 |
| US-003-06 | FR-014, FR-015, FR-016 |

All six owned stories are covered. Several rest on assumptions listed in `backend.search.gaps`.
<!-- /SECTION: backend.search.requirements -->
<!-- SECTION: backend.search.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-SEARCH-580505` | GET | `/notes/search` | `search` | 200 | 400, 503 | Search notes by keyword, by tag, or both combined. Archived notes are excluded unless include_archived is set. Results are paginated with pinned notes first. Returns 503 when the search index is degraded. |

### `API-SEARCH-580505` — GET `/notes/search`

Search notes by keyword, by tag, or both combined. Archived notes are excluded unless include_archived is set. Results are paginated with pinned notes first. Returns 503 when the search index is degraded.

**Request:** 
- `q` (string): Keyword to match against notes. Optional when at least one tag is given (ASM-BE-004, CON-BE-002). A request with neither q nor tags is rejected with 400 (US-003-01).
- `tags` (array): One or more tags to filter by. When given together with q, both filters apply (US-003-02, BR-010). Tag rules are unstated (GAP-BE-003).
- `include_archived` (boolean): When true, archived notes are included. Default is false, so archived notes are excluded (BR-009, BR-019). A non-boolean value returns 400.
- `page` (integer): 1-based page number for pagination (US-003-05, BR-011). The name and base are not stated by an input.
- `page_size` (integer): Number of results per page, up to 100 (NFR-007). Larger or invalid values return 400. The name and default are not stated by an input.
**Response:** 
- `items` (array, required): Matching notes for the requested page. Pinned notes come first, then the remaining notes (BR-019). Each item shows the note id, title, body, pinned flag, status, tags and timestamps.
- `page` (integer, required): The page number returned.
- `page_size` (integer, required): The page size applied.
- `total` (integer, required): Total number of notes matching the filters across all pages.
<!-- /SECTION: backend.search.api-contract -->
<!-- SECTION: backend.search.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

- **INV-BE-051.** After any search, nothing is changed. No note, tag, status, `pinned` value or login record differs from before. Derived: US-003-01 to US-003-06 describe retrieval only.
- **INV-BE-052.** Without `include_archived=true`, no returned note has archived status (BR-009).
- **INV-BE-053.** In any result set, every pinned note precedes every unpinned note (BR-019). This holds across pages.
- **INV-BE-054.** Every returned note satisfies every supplied filter. A note must match `q` if given, and the tag filter if given (US-003-04, BR-010).
- **INV-BE-055.** No response contains more notes than the maximum page size. Unbounded result sets are never returned (BR-011, `ASM-BE-065`).
- **INV-BE-056.** A request with an invalid parameter never gets 200, and a request against a degraded index never gets 200. They get 400 and 503 respectively (US-003-01 to US-003-06; US-003-03).
- **INV-BE-057.** Paging is deterministic. For an unchanged data set, the same request returns the same page (`ASM-BE-064`, FR-019).
<!-- /SECTION: backend.search.invariants -->
<!-- SECTION: backend.search.authorization | status: BLOCKED -->
## Authorization

**Blocked.** Neither roles, nor an ownership predicate, nor 401/403 behaviour for `API-SEARCH-580505` can be written from the inputs.

**What would unblock it:**
- `Q-BE-002`: note ownership (per user, or shared team workspace) and the access rule per note and search operation. BR-031 is a placeholder.
- `Q-BE-003`: the session model, including whether search requires a session.
- `Q-BE-001`: which addresses may obtain a login code (BR-030, garbled).

**What is not asserted.**
- Whether a caller sees all notes or only their own.
- Whether an unauthenticated call is refused.
- Any role or permission matrix entry.

US-004-03 requires 401 and 403 to be documented for endpoints that require authentication. Whether this endpoint is one of them is part of the same open question.

The working premise is `ASM-BE-071`, nothing more.
<!-- /SECTION: backend.search.authorization -->
<!-- SECTION: backend.search.jobs | status: UNKNOWN -->
## Jobs

**No scheduled job, sweep or retry job is derived from the inputs for this module.**
- Retention purge belongs to `auth` (NFR-019).
- Residency scans belong to `residency`.

**Idempotency.** `API-SEARCH-580505` is a read-only GET (`INV-BE-051`), so it is idempotent and needs no idempotency key.

**Retry.** After a 503 the client retries per the retry-after header (FR-017). The server runs no retry.

**Index maintenance.** How the index is built, rebuilt and kept current is unstated (`GAP-BE-075`). `ASM-BE-068` assumes it is updated with note writes, so no job is needed.
<!-- /SECTION: backend.search.jobs -->
<!-- SECTION: backend.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All test names are proposed (planned).

**Unit**
- Parameter parsing and validation, covering `q`, `tags`, `include_archived`, `page` and `limit`.
  - Tests: `parse_q_trims_and_treats_blank_as_absent`, `parse_tags_splits_and_rejects_invalid`, `parse_include_archived_accepts_only_true_false`, `parse_paging_rejects_negative_zero_nonnumeric_and_over_max`.
  - Covers FR-003, 006, 009, 012, 016.
- Ordering comparator.
  - Test: `order_pinned_then_relevance_then_id`.
  - Covers FR-019.

**Integration** (database and index in place)
- Keyword match across title and body, and no match.
  - Tests: `search_keyword_matches_title_or_body`, `search_no_match_returns_empty_200`.
  - Covers FR-001, 002.
- Tag filter and combined filters, including the empty cases.
  - Tests: `search_by_tag_returns_only_tagged`, `search_unused_tag_returns_empty_200`, `search_q_and_tags_requires_both`, `search_combined_no_overlap_returns_empty`.
  - Covers FR-004, 005, 007, 008.
- Archived exclusion and `include_archived=true`.
  - Tests: `search_default_excludes_archived`, `search_include_archived_true_includes_archived`.
  - Covers FR-014, 015.
- Pagination, total count, and a page beyond the results.
  - Tests: `search_pagination_returns_subset_and_total`, `search_page_beyond_results_returns_empty_200`, `search_default_and_max_page_size_enforced`.
  - Covers FR-010, 011, 013.
- Pinned-first ordering across pages and stable order.
  - Tests: `search_pinned_first_across_pages`, `search_order_is_stable_between_pages`.
  - Covers FR-019 and INV-BE-053, INV-BE-057.
- Route precedence.
  - Test: `search_route_not_shadowed_by_note_get`.
  - Covers FR-020.
- Read-only behaviour.
  - Test: `search_leaves_all_tables_unchanged`.
  - Covers INV-BE-051.
- Degraded index.
  - Tests: `search_index_degraded_returns_503_with_retry_after`, `search_invalid_params_win_over_degraded_503`.
  - Covers FR-017, `ASM-BE-072`.

**Contract**
- `API-SEARCH-580505` appears in the published OpenAPI document with 200, 400 and 503 documented (BR-012, NFR-008).
  - Test: `openapi_documents_search_operation_with_200_400_503`.
- Example payloads exist for search, including an empty-result example (NFR-009, US-004-05).
  - Test: `openapi_search_has_examples_including_empty_result`.
- The spec-versus-route coverage check reports no undocumented search route.
  - Test: `coverage_check_includes_search_route`.
- 401/403 documentation is deferred until `Q-BE-002` and `Q-BE-003` are answered.

**Non-functional**
- Load test: p95 under 300 ms at 50 requests per second on about 100k notes, including `limit=100`.
  - Test: `search_p95_under_300ms_at_50rps_100k_notes`.
  - Covers FR-018, NFR-006, NFR-007.
- Injection suite and the per-release security scan (FR-021, NFR-012).
<!-- /SECTION: backend.search.test-plan -->
<!-- SECTION: backend.search.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, questions and assumptions

### Gaps minted by this module

- **`GAP-BE-073`: "relevance" is undefined.**
  - No input defines the scoring or the order for a tag-only search after pinned notes.
  - Degrades FR-001 and FR-019.
  - Fallback: `ASM-BE-064`.
  - Closing artefact: a ranking rule.
- **`GAP-BE-074`: keyword matching semantics are unstated.**
  - Missing: case, partial versus whole-word, stemming, language, multi-word handling and maximum `q` length.
  - Degrades FR-001.
  - Fallback: `ASM-BE-063`.
  - Closing artefact: a matching rule.
- **`GAP-BE-075`: index behaviour is unstated.**
  - Missing: technology, type, how it is kept current, what "degraded" means, how degradation is detected and cleared, and whether a dedicated (pinned, status) index is needed.
  - Degrades `backend.search.data-model`, `backend.search.state-machine` and FR-017.
  - Fallback: `ASM-BE-068`, `ASM-BE-069`.
  - Closing artefact: an index design statement.
- **`GAP-BE-076`: multi-tag semantics and the `tags` encoding are unstated.**
  - Missing: all-of versus any-of, and comma-separated versus repeated parameter.
  - Degrades FR-004 and FR-007.
  - Fallback: `ASM-BE-061`, `ASM-BE-062`.
  - Closing artefact: a filter rule.
- **`GAP-BE-077`: "malformed filter combination" is undefined.**
  - US-003-04 requires a 400 but no input says what is malformed beyond the other validation rules.
  - Degrades FR-009.
  - Closing artefact: a list of invalid combinations.
- **`GAP-BE-078`: no default page size is stated.** The maximum is only implied by "up to page sizes of 100" (NFR-007).
  - Degrades FR-013.
  - Fallback: `ASM-BE-065`.
  - Closing artefact: a paging statement.
- **`GAP-BE-079`: handling of search terms in logs is unstated.**
  - NFR-018 covers emails and login codes only. Whether note content in `q` is logged or redacted is not stated, and no redaction is asserted.
  - Closing artefact: a logging statement.
- **`GAP-BE-080`: the rate-limit class for search is unstated.**
  - Closing artefact: an NFR statement.

### Referenced, not minted

- `GAP-BE-002`: no list-notes operation, including an archived-only view.
- `GAP-BE-003`: tag rules.
- `GAP-BE-004`: error envelope, code names and retry values.
- `CON-BE-002`: US-003-01 returns 400 when `q` is missing, but US-003-02 and BR-010 allow tags-only searches. The tags-only reading is used (`ASM-BE-004`).
- `ASM-BE-004`, `ASM-BE-006`.
- Blocking questions `Q-BE-002`, `Q-BE-003`, plus `Q-BE-001`.

No `CON-` is minted here.

Directions checked:
- story ↔ design: no design content (`design-brief.md` repeats the acceptance criteria).
- story ↔ requirement: consistent apart from `CON-BE-002`.
- requirement ↔ requirement: consistent within this module.
- design ↔ design: not applicable.

### Questions (all degradable)

Unattended defaults apply.

- **`Q-BE-061`:** how should keywords match, and how are results ranked? Default: `ASM-BE-063`, `ASM-BE-064`.
- **`Q-BE-062`:** with several tags, must a note carry all of them or any? How is the `tags` parameter encoded? Default: `ASM-BE-061`, `ASM-BE-062`.
- **`Q-BE-063`:** what are the default and maximum page sizes, and is an oversize `limit` rejected or clamped? Default: `ASM-BE-065`.
- **`Q-BE-064`:** what does "degraded" mean, and must a note be searchable as soon as its write returns? Default: `ASM-BE-068`, `ASM-BE-069`.

### Assumptions

Each is confirmed by the answer to the named question or artefact.

- **`ASM-BE-061`:** the `tags` parameter is one comma-separated value, such as `tags=a,b`.
  - Reasoning: US-003-02 shows the single form `tags=project-x` and speaks of "one or more tags".
  - Confirmed by `Q-BE-062`.
- **`ASM-BE-062`:** with several tags, a note must carry all of them. This is consistent with the combined-filter wording in US-004-04.
  - Confirmed by `Q-BE-062`.
- **`ASM-BE-063`:** `q` is trimmed. A whitespace-only `q` counts as absent. The match is case-insensitive over title and body.
  - Whether it is partial or whole-word, and whether stemming applies, is left to `Q-BE-061`.
- **`ASM-BE-064`:** within pinned and unpinned groups, results are ordered by relevance when `q` is given, then by note id. For a tag-only search, order is by note id after pinned notes.
  - Reasoning: deterministic paging requires a total order. BR-019 only says same-pinned ordering is "unchanged".
  - Confirmed by `Q-BE-061`.
- **`ASM-BE-065`:** `page` is 1-based and defaults to 1. `page=0` and `limit=0` return 400. The maximum `limit` is 100, from the NFR-007 wording. A larger `limit` returns 400 and is not clamped.
  - **The default `limit` is 20. This figure has no source** and is a placeholder.
  - Confirmed by `Q-BE-063`.
- **`ASM-BE-066`:** `include_archived` accepts exactly `true` and `false`, in lowercase. Absent means `false`.
  - Reasoning: US-003-06 names only `true` and "invalid".
  - Confirmed by a statement of accepted values.
- **`ASM-BE-067`:** the total count is the number of notes matching the request across all pages. It is still reported on a page beyond the results.
  - Confirmed by the response schema decision.
- **`ASM-BE-068`:** the search index lives in the PostgreSQL 16 database (the only store `platform.md` names). It is updated with note writes, so a note is searchable once its write returns.
  - Reasoning: web_app saves and then searches (US-006-02, US-006-03).
  - Confirmed by `Q-BE-064`.
- **`ASM-BE-069`:** "degraded" means the index cannot answer the query. 503 carries a `Retry-After` header, whose value is `Q-BE-004`.
- **`ASM-BE-070`:** `/notes/search` is registered before the parameterised note-get route and takes precedence over it.
  - Reasoning: Express matches routes in registration order (`platform.md` names Express). Whether a note id can equal `search` is unstated.
  - Confirmed by the note id format.
- **`ASM-BE-071`:** until `Q-BE-002` is answered, search is specified without any per-user scoping.
  - Reasoning: BR-031's rationale calls the shared-workspace default only a placeholder.
  - The authorization section remains BLOCKED.
- **`ASM-BE-072`:** all parameters are validated before the index is consulted. An invalid request gets 400 even when the index is degraded.
  - Several validation errors are reported in one 400. Their shape is `GAP-BE-004`.
<!-- /SECTION: backend.search.gaps -->
