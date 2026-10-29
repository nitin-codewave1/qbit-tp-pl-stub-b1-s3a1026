---
id: agent-developer/notes_api/backend/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/search-SPEC.md
depends_on: [notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06, notes_api:US-006-03]
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
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
scope: [API-SEARCH-842161]
```

`blocked_by` lists the three blocking questions defined in `backend.auth-model` of the project spec. They block only `backend.search.authorization`. Every other section is written.
<!-- /SECTION: backend.search.frontmatter -->
<!-- SECTION: backend.search.purpose | status: DERIVED -->
## Purpose

**Responsible for** (derivation: the `search` row of `backend.module-map`, US-003-01 to US-003-06, BR-009 to BR-011, BR-019):
- Keyword search over note title and body.
- Tag filtering, and the combination of keyword and tag filters in one request (BR-010).
- Pagination with a total count, with bounded page size (BR-011).
- Excluding archived notes by default, with an opt-in to include them (BR-009).
- Putting pinned notes before unpinned notes (BR-019).
- Answering 503 with a retry-after header when the search index is degraded or unavailable (US-003-03).
- Meeting the latency target stated in NFR-006 and NFR-007.
- The backend side of US-006-03: the web app's search box calls this operation.

**Not responsible for:**
- Creating, editing, archiving or unarchiving notes, and adding tags. The `notes` module owns these, and `search` only reads their results.
- The definition of the `active` / `archived` status and the `pinned` flag. `notes` owns both.
- Authentication, one-time codes and sessions. The `auth` module owns them.
- The published OpenAPI document. The `contract-docs` module owns it.
- Refusal of out-of-scope calls. The `scope-guard` module owns it.
- The web app's search box and result screens (US-006-03). They belong to the web surface.
- Listing all active notes without a filter (US-006-01), and listing archived-only notes or distinct tags. No operation for any of these exists in this module (`GAP-BE-005`, `GAP-BE-006` in the project spec).
- Logging and classifying stakeholder requests (`GAP-BE-009` in the project spec).

**Unchanged by CR-132.** The email one-time-code change does not alter any search behaviour. The only link is the pending access-check edge `search → auth` in the module map, which is not committed (see `backend.search.authorization`).
<!-- /SECTION: backend.search.purpose -->
<!-- SECTION: backend.search.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Tables owned by this module: none.** `search` is read-only and owns no table. It reads the note and note-tag records defined in `backend.domain-model` of the project spec (note: `id`, `title`, `body`, `pinned`, `status`; note-tag: `note_id`, `tag_name`). Their types, nullability, defaults and constraints are defined once, in `backend.notes.data-model`, and are not repeated here.

**The index.** US-003-03 requires "indexed lookup" at 100k notes, and migration step 2 of the project spec places "search indexing over note title, body and tags" after the note tables. The index lives in PostgreSQL 16 (`platform.md`; `ASM-BE-051`). Its data-definition statements belong to that migration step, not to a table owned here.

| Index need | Query it serves | Source |
|---|---|---|
| Text lookup over note title and body | The keyword predicate | US-003-01, US-003-03 |
| Lookup of notes by tag name | The tag predicate and the combined predicate | US-003-02, US-003-04 |
| Support for the status filter and for pinned-first ordering | Default archived exclusion, `include_archived`, and ordering | BR-009, BR-019 |

The index kind, the column order and the exact definitions are not named by any input. They are decided at implementation. The choice must satisfy NFR-006 and NFR-007 (`ASM-BE-051`, `GAP-BE-056`).

**Constraints on the data this module reads.** Search relies on, and does not enforce, the rule that a tag appears at most once per note (US-002-03). Tag character rules are open (`GAP-BE-010` in the project spec).

**PII.** Note content may contain personal data, but no input classifies it. Search returns note content only to the caller of the operation, and logs none of it. No input states how search logs are treated (`GAP-BE-059`).
<!-- /SECTION: backend.search.data-model -->
<!-- SECTION: backend.search.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

Search has no entity of its own, so it has no entity lifecycle. The note's `active` / `archived` status belongs to `notes`. Search only filters on it.

The one stateful thing search depends on is the availability of the search index. US-003-03 distinguishes a degraded or unavailable index from a healthy one.

| State | Meaning | Behaviour of `API-SEARCH-842161` |
|---|---|---|
| `AVAILABLE` | The index can answer queries | Answers 200 with results, or 400 for an invalid request |
| `DEGRADED` | The index is degraded or unavailable | Answers 503 with a retry-after header and does not hang (US-003-03) |

| Transition | Trigger | Guard |
|---|---|---|
| `AVAILABLE → DEGRADED` | Index failure or degradation is detected | Detection mechanism unstated (`GAP-BE-056`) |
| `DEGRADED → AVAILABLE` | The index recovers | Recovery mechanism unstated (`GAP-BE-056`) |

**Terminal states:** none. Both states can be left.

**Illegal transitions:** none exist. No client request moves the index between states, so no status or error code is defined for an illegal transition.

**Validation versus availability.** A request that is invalid answers 400 whatever the index state. Whether an invalid request is checked before the index is consulted is not stated by any input. The default is that validation runs first, because it needs no index (`ASM-BE-052`).
<!-- /SECTION: backend.search.state-machine -->
<!-- SECTION: backend.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements use the operation `API-SEARCH-842161`. Parameter names `q`, `tags`, `include_archived`, `page` and `limit` are taken from the stories. The rendered operation record governs their final form. Types and constraints are not restated here (core rule 6).

**BE-SEARCH-FR-001 - Keyword search over title and body**
- Given notes exist whose title or body matches the keyword in `q`,
- When `API-SEARCH-842161` is called with that `q`,
- Then it answers 200 with the matching notes, pinned notes first, then ordered by relevance.
- Traces to. US-003-01 (criterion 1), BR-009, BR-019
- Verification. `search_keyword_returns_matches_pinned_first` (planned)
- API. API-SEARCH-842161
- Note. Keyword matching is case-insensitive (`ASM-BE-053`). The relevance measure is unstated (`GAP-BE-054`).

**BE-SEARCH-FR-002 - No keyword match**
- Given no note matches the keyword,
- When the search is executed,
- Then it answers 200 with an empty array, not an error.
- Traces to. US-003-01 (criterion 2)
- Verification. `search_no_match_returns_empty_200` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-003 - Missing or empty keyword**
- Given a request with no usable `q` and no `tags`,
- When the search request is made,
- Then it answers 400 with a validation error message.
- Traces to. US-003-01 (criterion 3)
- Verification. `search_without_q_or_tags_returns_400` (planned)
- API. API-SEARCH-842161
- Note. US-003-02 permits a search by tag alone, so US-003-01 criterion 3 cannot mean "q is always required" (`CON-BE-051`). The adopted reading is that at least one of `q` or `tags` is required. A `q` that is present but empty, or only whitespace, counts as absent (`ASM-BE-054`, `Q-BE-053`).

**BE-SEARCH-FR-004 - Tag filter**
- Given notes tagged `project-x` exist,
- When the search is called with `tags=project-x`,
- Then only notes carrying that tag are returned.
- Traces to. US-003-02 (criterion 1), BR-010
- Verification. `search_tag_filter_returns_only_tagged` (planned)
- API. API-SEARCH-842161
- Note. Tag matching is exact string equality against stored tags (`ASM-BE-055`). With several tags, a note must carry all of them (`ASM-BE-056`, `Q-BE-052`).

**BE-SEARCH-FR-005 - Tag with no match**
- Given a tag that no note carries,
- When the search is executed,
- Then it answers 200 with an empty result set.
- Traces to. US-003-02 (criterion 2)
- Verification. `search_unknown_tag_returns_empty_200` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-006 - Invalid tag characters**
- Given the `tags` parameter contains invalid characters,
- When the search request is made,
- Then it answers 400 with an error detail.
- Traces to. US-003-02 (criterion 3)
- Verification. `search_invalid_tag_chars_returns_400` (planned)
- API. API-SEARCH-842161
- Note. The valid character set is not defined by any input (`GAP-BE-010` in the project spec). The requirement is testable only once it is.

**BE-SEARCH-FR-007 - Combined keyword and tag filters**
- Given `q=budget` and `tags=finance` are both supplied,
- When the search is executed,
- Then only notes matching both criteria are returned.
- Traces to. US-003-04 (criterion 1), BR-010
- Verification. `search_combined_filters_match_both` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-008 - Combined filters excluding everything**
- Given the keyword matches but the tag filter excludes all matching notes,
- When the search is executed,
- Then it answers with an empty result set.
- Traces to. US-003-04 (criterion 2)
- Verification. `search_combined_filters_empty_result` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-009 - Malformed filter combination**
- Given a malformed filter combination is supplied,
- When the search request is made,
- Then it answers 400 with error details.
- Traces to. US-003-04 (criterion 3)
- Verification. `search_malformed_combination_returns_400` (planned)
- API. API-SEARCH-842161
- Note. "Malformed filter combination" is not defined by any input (`GAP-BE-057`). Until it is, this requirement covers only the cases other requirements already name as invalid (FR-003, FR-006, FR-012, FR-016).

**BE-SEARCH-FR-010 - Pagination with total count**
- Given more results exist than the page size,
- When `page` and `limit` are supplied,
- Then the correct subset is returned along with a total count of the filtered set.
- Traces to. US-003-05 (criterion 1), BR-011
- Verification. `search_pagination_returns_subset_and_total` (planned)
- API. API-SEARCH-842161
- Note. `page` is 1-based and defaults to 1 (`ASM-BE-057`).

**BE-SEARCH-FR-011 - Page beyond available results**
- Given a page number beyond the available results,
- When the search is executed,
- Then it answers 200 with an empty array.
- Traces to. US-003-05 (criterion 2)
- Verification. `search_page_beyond_end_returns_empty_200` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-012 - Invalid page or limit**
- Given a negative or non-numeric `page` or `limit`,
- When the search request is made,
- Then it answers 400 with a validation error.
- Traces to. US-003-05 (criterion 3)
- Verification. `search_invalid_page_or_limit_returns_400` (planned)
- API. API-SEARCH-842161
- Note. Zero for `page` or `limit` is not named by the story and is also rejected (`ASM-BE-057`).

**BE-SEARCH-FR-013 - Bounded page size**
- Given any search request,
- When results are returned,
- Then the number of results never exceeds the maximum page size, and unbounded result sets are never returned.
- Traces to. BR-011, NFR-007
- Verification. `search_page_size_never_exceeds_maximum` (planned)
- API. API-SEARCH-842161
- Note. NFR-007 states performance "up to page sizes of 100 results", so 100 is taken as the maximum (`ASM-BE-058`). A `limit` above it is rejected with 400 (`ASM-BE-058`, `Q-BE-051`). The default page size is unstated (`GAP-BE-053`).

**BE-SEARCH-FR-014 - Archived notes excluded by default**
- Given both archived and active notes exist,
- When a default search is executed,
- Then only active notes are returned.
- Traces to. US-003-06 (criterion 1), BR-009
- Verification. `search_default_excludes_archived` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-015 - Include archived on request**
- Given `include_archived=true` is passed,
- When the search is executed,
- Then archived notes are included in the results.
- Traces to. US-003-06 (criterion 2), BR-009
- Verification. `search_include_archived_true_includes_archived` (planned)
- API. API-SEARCH-842161
- Note. Pinned-first ordering applies to the combined set (`ASM-BE-059`).

**BE-SEARCH-FR-016 - Invalid include_archived value**
- Given an `include_archived` value other than `true` or `false`,
- When the search request is made,
- Then it answers 400 with an error message.
- Traces to. US-003-06 (criterion 3)
- Verification. `search_invalid_include_archived_returns_400` (planned)
- API. API-SEARCH-842161
- Note. Accepted values are exactly `true` and `false` (`ASM-BE-060`).

**BE-SEARCH-FR-017 - Latency target**
- Given a search request under a load of 50 requests per second, including a dataset of 100k notes,
- When the request is executed,
- Then p95 response latency is under 300 ms, served by indexed lookup.
- Traces to. US-003-03 (criteria 1 and 2), NFR-006, NFR-007
- Verification. `search_p95_under_300ms_at_50rps_100k_notes` (planned)
- API. API-SEARCH-842161
- Note. The load-test environment is unstated (`GAP-BE-059`).

**BE-SEARCH-FR-018 - Degraded index**
- Given the search index is degraded or unavailable,
- When a valid search is requested,
- Then it answers 503 with a retry-after header instead of hanging.
- Traces to. US-003-03 (criterion 3)
- Verification. `search_degraded_index_returns_503_with_retry_after` (planned)
- API. API-SEARCH-842161
- Note. The header value and the definition of "degraded" are unstated (`GAP-BE-055`, `GAP-BE-056`).

**BE-SEARCH-FR-019 - Deterministic ordering**
- Given any search that returns more than one note,
- When results are ordered,
- Then pinned notes come first, then notes ordered by relevance when `q` is present, and equal notes are ordered by note id so that pages do not shift between calls.
- Traces to. US-003-01 (criterion 1), BR-019, US-003-05 (criterion 1)
- Verification. `search_ordering_is_stable_across_pages` (planned)
- API. API-SEARCH-842161
- Note. The tie-break by note id is an assumption (`ASM-BE-061`). The ordering of tag-only results after pinned notes is not stated by any input (`ASM-BE-061`, `Q-BE-053`).

**BE-SEARCH-FR-020 - Input safety**
- Given a `q` or `tags` value containing characters meant to alter a query,
- When the search is executed,
- Then the value is validated and treated only as data, and the query's structure is never changed by it.
- Traces to. NFR-012
- Verification. `search_inputs_cannot_alter_query` (planned)
- API. API-SEARCH-842161

**BE-SEARCH-FR-021 - Serving the web app's search box**
- Given the web app submits a keyword, a tag, or both,
- When the request reaches the backend,
- Then the same operation answers as for any other caller, with the behaviour in FR-001 to FR-019.
- Traces to. US-006-03
- Verification. `search_contract_serves_web_app_keyword_and_tag` (planned)
- API. API-SEARCH-842161
- Note. Only the backend side is specified. The "no results" message and the box itself belong to the web surface.

**Story coverage for this module**

| Story | Covered by |
|---|---|
| US-003-01 | BE-SEARCH-FR-001, 002, 003, 019 |
| US-003-02 | BE-SEARCH-FR-004, 005, 006 |
| US-003-03 | BE-SEARCH-FR-017, 018 |
| US-003-04 | BE-SEARCH-FR-007, 008, 009 |
| US-003-05 | BE-SEARCH-FR-010, 011, 012, 013, 019 |
| US-003-06 | BE-SEARCH-FR-014, 015, 016 |
| US-006-03 | BE-SEARCH-FR-021 |
<!-- /SECTION: backend.search.requirements -->
<!-- SECTION: backend.search.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-SEARCH-842161` | GET | `/search` | `search` | 200 | 400, 503 | Search notes by keyword, tag or both, with pagination, archived exclusion by default and pinned-first ordering; returns 503 with a retry-after header when the search index is degraded. |

### `API-SEARCH-842161` — GET `/search`

Search notes by keyword, tag or both, with pagination, archived exclusion by default and pinned-first ordering; returns 503 with a retry-after header when the search index is degraded.

**Request:** 
- `q` (string): Keyword matched against note title and body (US-003-01). At least one of q or tag is required.
- `tag` (string): Tag filter (US-003-02). When combined with q, both filters apply together (US-003-04). At least one of q or tag is required.
- `include_archived` (boolean): When absent or false, archived notes are excluded from results (US-003-06, BR-009). Invalid values return 400.
- `page` (integer): Page number for pagination (US-003-05, BR-011). Invalid values return 400.
- `limit` (integer): Page size, up to 100 (NFR-007, US-003-05). Invalid values return 400.
**Response:** 
- `items` (array, required): Matching notes for the requested page, pinned notes ordered first (BR-019, US-003-01). Each item shows the note's id, title, body, tags, pinned flag and status, as defined once by the notes module.
- `page` (integer, required): The page number returned.
- `limit` (integer, required): The page size applied.
- `total` (integer, required): Total number of notes matching the filters, so clients can paginate.
<!-- /SECTION: backend.search.api-contract -->
<!-- SECTION: backend.search.invariants | status: DERIVED -->
## Invariants

Each is derived from the cited input and must hold after every call to `API-SEARCH-842161`.

- **INV-BE-051.** Archived notes never appear in results unless `include_archived=true` was supplied and valid. (BR-009, US-003-06)
- **INV-BE-052.** Within the full ordered result set, every pinned note precedes every unpinned note, on every page. (BR-019)
- **INV-BE-053.** A response never carries more notes than the maximum page size. (BR-011)
- **INV-BE-054.** Search changes nothing. No note, tag or any other record is created, modified or deleted by a call. (Derivation: the operation is a read, and the module owns no table.)
- **INV-BE-055.** When the index is degraded or unavailable, the answer is 503. It is never a 200 that presents results from the degraded index as complete. (US-003-03)
- **INV-BE-056.** A search that matches nothing, or a page beyond the end, answers 200 with an empty array and never an error. (US-003-01 criterion 2, US-003-02 criterion 2, US-003-04 criterion 2, US-003-05 criterion 2)
- **INV-BE-057.** The total count describes the filtered set as a whole, not the current page. (US-003-05 criterion 1)
<!-- /SECTION: backend.search.invariants -->
<!-- SECTION: backend.search.authorization | status: BLOCKED -->
## Authorization

**Blocked.** Which actor may call `API-SEARCH-842161`, and which notes a caller may see, cannot be written. The project spec's `backend.auth-model` is itself `BLOCKED` on three decisions that no input answers:

| Open decision | Effect on this module |
|---|---|
| `Q-BE-001` Who may request, verify or receive a login code | Sets who can hold a credential at all |
| `Q-BE-002` Note ownership and the access rule (BR-031 is a placeholder) | Decides whether search must restrict results to a caller's own notes or to a shared workspace |
| `Q-BE-003` What a successful verification issues, and whether note and search operations require it | Decides whether this operation requires a credential, and what it answers when none is presented |

**What the answer will change here.** If notes are owned per user, the ownership predicate must be applied inside the query, so that results, the total count (INV-BE-057) and the 200-versus-empty behaviour (INV-BE-056) all respect it. If notes are shared, no predicate is needed. The `search → auth` edge in the module map stays uncommitted until then.

**Row needed once unblocked:** one row for `API-SEARCH-842161`, naming the permitted role or roles and the ownership predicate.

**What would unblock it:** the answers to `Q-BE-001`, `Q-BE-002` and `Q-BE-003`. No role is named here because no input defines one.
<!-- /SECTION: backend.search.authorization -->
<!-- SECTION: backend.search.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

**Scheduled work, sweeps, retries:** no input requires any for this module.

**Index maintenance.** If the search index is updated asynchronously rather than within the note write, a job would be needed. How quickly a new, edited, archived or unarchived note must appear in or leave search results is not stated by any input (`GAP-BE-058`). Nothing is specified until it is.

**Idempotency.** `API-SEARCH-842161` is a read. Repeating a call with the same inputs and unchanged data returns the same result (FR-019), so no idempotency key applies.

**Retry.** After a 503, a caller waits for the interval in the retry-after header before retrying (US-003-03). Automatic retry by the service is not required.
<!-- /SECTION: backend.search.jobs -->
<!-- SECTION: backend.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are planned. The set is fixed by requirement ids and gets exact names at implementation.

**Unit**
- Validation of `q`, `tags`, `include_archived`, `page` and `limit`: missing, empty, whitespace-only, negative, non-numeric, zero, above maximum, invalid characters (FR-003, 006, 009, 012, 013, 016).
- The ordering comparator: pinned first, relevance, then note id (FR-019, INV-BE-052).
- Page and total-count arithmetic, including a page beyond the end (FR-010, 011, INV-BE-057).
- Rejection of inputs that could alter query structure (FR-020).

**Integration** (PostgreSQL 16, per `platform.md`)
- Seed notes that are active, archived, pinned and unpinned, with and without tags. Cover FR-001, 002, 004, 005, 007, 008, 014, 015 and INV-BE-051, 052, 056.
- Assert that no row changes across any search call (INV-BE-054).
- Simulate a degraded index and assert 503 with a retry-after header (FR-018, INV-BE-055).
- Load test at 50 requests per second against 100k notes and assert p95 under 300 ms (FR-017). The test environment is open (`GAP-BE-059`).
- Assert that stable ordering holds across consecutive pages (FR-019).

**Contract**
- The rendered operation record for `API-SEARCH-842161` matches the published OpenAPI document, with its 400 and 503 responses documented (NFR-008, US-004-03).
- The OpenAPI document carries example request and response payloads for search, including an empty result (NFR-009, US-004-05).
- The web app's keyword and tag calls are accepted by the operation as documented (FR-021).
- A guard that no search path or parameter matches an excluded category (scope-guard tests, NFR-010). That suite is owned by `scope-guard`.

**Not testable yet:** the access predicate (blocked by `Q-BE-002`, `Q-BE-003`), the definition of relevance (`GAP-BE-054`), and the "malformed combination" cases (`GAP-BE-057`).
<!-- /SECTION: backend.search.test-plan -->
<!-- SECTION: backend.search.gaps | status: VERIFIED -->
## Gaps, contradictions, assumptions and questions

Ids carry this run's surface code `BE`. Ids from the project spec are cited by reference and not redefined. The project spec's register also cites `Q-BE-054` to `Q-BE-056`, `ASM-BE-065` to `ASM-BE-067` and `CON-BE-003` from an earlier draft of this module. This document restates those points under its own reserved numbers (see `GAP-BE-052`).

### Contradictions

**CON-BE-051 - Is `q` required?**
- Claim A: US-003-01 criterion 3 says a missing or empty `q` returns 400.
- Claim B: US-003-02 criteria 1 and 2 search with `tags` alone and expect results, with no `q`.
- Consequence of A: tag-only search returns 400, and the tag filter can only narrow a keyword search.
- Consequence of B: `q` is optional when `tags` is present, and 400 applies only when neither is usable.
- Severity: non-critical. It changes request validation, not a stored data shape, a state machine, an authorisation rule or a money/PII path.
- Adopted reading: at least one of `q` or `tags` is required (cited most strongly because both stories are Must-have behaviour and B is the only reading under which both hold). Sections affected are `DRAFT-UNVERIFIED`.
- This is the contradiction the project spec's register carries as `CON-BE-003`.

**CON-BE-052 - Path of the search operation**
- Claim A: US-003-01 criterion 1 names the call `GET /notes/search?q=keyword`.
- Claim B: the operation record supplied for this run is `GET /search` (`API-SEARCH-842161`).
- Consequence of A: clients written from the story call a path the record does not define.
- Consequence of B: the story's path is only illustrative, and the record governs.
- Severity: non-critical.
- Adopted reading: the operation record governs. Requirements here refer to the operation by id and state no path. Confirm which path is intended.

### Gaps

**GAP-BE-052 - Earlier-draft ids cited by the project spec**
- Missing: definitions of `Q-BE-054` to `Q-BE-056`, `ASM-BE-065` to `ASM-BE-067` and `CON-BE-003`, cited in `backend.coverage` of the project spec but not defined in this document.
- Degrades: traceability of the register to this module.
- Did instead: restated the same points as `Q-BE-051` to `Q-BE-053`, `ASM-BE-056`, `ASM-BE-058`, `ASM-BE-054` and `CON-BE-051`.
- Closing artefact: the orchestrator reconciling the register with this document.

**GAP-BE-053 - Default page size**
- Missing: the default page size. BR-011 requires a default and maximum, and no input gives the default.
- Degrades: `BE-SEARCH-FR-010` and `BE-SEARCH-FR-013`.
- Did instead: no default value written.
- Closing artefact: a stated default page size.

**GAP-BE-054 - Relevance**
- Missing: how relevance is measured, and the ordering after pinned notes when `q` is absent.
- Degrades: `BE-SEARCH-FR-001` and `BE-SEARCH-FR-019`.
- Did instead: stated only the pinned-first rule and a deterministic tie-break (`ASM-BE-061`).
- Closing artefact: a relevance definition from the product owner.

**GAP-BE-055 - Retry-after value**
- Missing: the value and form of the retry-after header on a 503.
- Degrades: `BE-SEARCH-FR-018`.
- Did instead: required the header and did not state a value.
- Closing artefact: a stated retry interval.

**GAP-BE-056 - Index health and technology**
- Missing: what counts as degraded or unavailable, how it is detected and recovered, and the index kind.
- Degrades: `backend.search.state-machine`, `backend.search.data-model` and `BE-SEARCH-FR-018`.
- Did instead: described two states and left triggers unstated.
- Closing artefact: a platform decision on the search index.

**GAP-BE-057 - Malformed filter combination**
- Missing: a definition of "malformed filter combination" (US-003-04 criterion 3).
- Degrades: `BE-SEARCH-FR-009`.
- Did instead: limited the requirement to cases other requirements already name as invalid.
- Closing artefact: a list of invalid combinations.

**GAP-BE-058 - Index freshness**
- Missing: how soon a created, edited, archived or unarchived note must show in or leave search results.
- Degrades: `backend.search.jobs`, and the consistency of search with the notes module.
- Did instead: specified no maintenance job.
- Closing artefact: a freshness requirement.

**GAP-BE-059 - Test environment, search logging, PII**
- Missing: the load-test environment for "typical load"; whether `q` and `tags` values may be logged; whether note content is classed as personal data.
- Degrades: `BE-SEARCH-FR-017` and `backend.search.data-model`.
- Did instead: stated no figure beyond NFR-006 and NFR-007, and no logging rule.
- Closing artefact: a test-environment definition and a data-classification statement.

**Also open and defined in the project spec, cited not restated:** `GAP-BE-010` (tag rules), `GAP-BE-011` (error envelope and code names), `GAP-BE-005` (no list-all or archived-only operation), `GAP-BE-006` (no distinct-tags operation), `GAP-BE-001` (inbound handoff files not supplied). The four inbound web-app handoffs listed in `RUN-CONTEXT.upstream.handoffs` were not supplied to this run, so none is answered by this module.

### Assumptions

| Id | Assumption | Would be confirmed by |
|---|---|---|
| `ASM-BE-051` | The search index is held in PostgreSQL 16 (`platform.md`), not a separate search service. No input names one. | Platform owner |
| `ASM-BE-052` | Request validation runs before the index is consulted, so an invalid request answers 400 even when the index is degraded. | Search-behaviour decision |
| `ASM-BE-053` | Keyword matching is case-insensitive over title and body. | Search-behaviour decision |
| `ASM-BE-054` | A `q` that is empty or only whitespace counts as absent. | `Q-BE-053` |
| `ASM-BE-055` | Tag matching is exact equality against stored tags, with no normalisation. | Tag rules (`GAP-BE-010`) |
| `ASM-BE-056` | With several tags, a note must carry all of them. | `Q-BE-052` |
| `ASM-BE-057` | `page` is 1-based and defaults to 1. Zero for `page` or `limit` returns 400, because US-003-05 names only negative and non-numeric values. | Pagination decision |
| `ASM-BE-058` | The maximum page size is 100 (NFR-007 wording: "up to page sizes of 100 results"). A `limit` above it returns 400 rather than being clamped. | `Q-BE-051` |
| `ASM-BE-059` | When archived notes are included, pinned-first ordering applies to the combined set. | Ordering decision |
| `ASM-BE-060` | `include_archived` accepts only `true` and `false`. | Decision on accepted values |
| `ASM-BE-061` | Equal results are ordered by note id after pinned status and relevance. For a tag-only search with no `q`, the order after pinned notes is by note id. | `Q-BE-053` |

### Questions

All questions are degradable. The default is stated and the run continues; none halts it. Budget used: 3 of 12.

| Id | Class | Question | Default taken |
|---|---|---|---|
| `Q-BE-051` | degradable | Is a `limit` above the maximum rejected with 400 or clamped to the maximum? | Rejected with 400 (`ASM-BE-058`) |
| `Q-BE-052` | degradable | With several tags, must a note carry all of them or any of them? | All of them (`ASM-BE-056`) |
| `Q-BE-053` | degradable | Is a whitespace-only `q` treated as absent, and how are tag-only results ordered after pinned notes? | Treated as absent; ordered by note id (`ASM-BE-054`, `ASM-BE-061`) |
<!-- /SECTION: backend.search.gaps -->
