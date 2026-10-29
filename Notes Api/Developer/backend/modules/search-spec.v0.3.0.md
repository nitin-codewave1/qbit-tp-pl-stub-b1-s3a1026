---
id: agent-developer/notes_api/backend/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/modules/search-SPEC.md
depends_on: [notes_api:US-003-01, notes_api:US-003-02, notes_api:US-003-03, notes_api:US-003-04, notes_api:US-003-05, notes_api:US-003-06, notes_api:US-006-03]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
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
<!-- /SECTION: backend.search.frontmatter -->
<!-- SECTION: backend.search.purpose | status: DERIVED -->
## Purpose

**Responsible for** (module map in `backend.module-map`; US-003-01 to US-003-06, US-006-03; BR-009 to BR-011, BR-019; NFR-006, NFR-007):

- Keyword search across note title and body, tag filtering, and the two combined, through `API-SEARCH-580505`.
- Pagination with a total count, with no unbounded result sets.
- Exclusion of archived notes by default and their inclusion on request.
- Pinned-first ordering of results.
- A 503 response when the search index is degraded.
- Meeting the search latency targets in `backend.nfr`.

**Serves the web_app search box (US-006-03) through the same operation.** The screens themselves belong to the web_app surface. The backend adds no client-specific behaviour.

**Not responsible for:**

- Creating, editing, archiving, unarchiving or tagging notes, and the note and tag tables. These belong to the `notes` module. Search only reads them (`backend.module-map`).
- Listing active notes, archived-only notes or distinct tags. No story or decision defines these (`GAP-BE-005`, `GAP-BE-006`). Search cannot substitute for them because it requires `q` or a tag (`ASM-BE-009`).
- Authentication, login codes and sessions. These belong to the `auth` module.
- Refusing out-of-scope calls. This belongs to `scope-guard`.
- Documenting the operation in the published OpenAPI document. This belongs to `contract-docs`.
- The error envelope. It is defined once in `backend.error-model` and only referenced here.

**Inbound handoffs relevant to this module.** `HANDOFF-WEBAPP-732181` asks for operations that list active and archived-only notes. `HANDOFF-WEBAPP-263975` asks for an operation that lists distinct tags. The handoff file bodies were not supplied to this run, so only their summaries in the prior approved `HANDOFFS.md` were available (`GAP-BE-001`). This module cannot answer either one without inventing an operation no input defines. The reasons are `GAP-BE-005` and `GAP-BE-006`.
<!-- /SECTION: backend.search.purpose -->
<!-- SECTION: backend.search.data-model | status: DRAFT-UNVERIFIED -->
## Data model

**Tables owned by this module: none.** Search is read-only. It reads the note and note-tag tables, which are defined once in `backend.notes.data-model` and are not restated here. The fields it reads are title, body, `pinned`, archive status and the tag names on a note (US-003-01, US-003-02, US-003-06, BR-018, BR-019).

**Index structure (migration 006 in `backend.migration-plan`).** The index serves these queries:

| Query served | Source |
|---|---|
| Keyword match against title and body | US-003-01 |
| Match by one or more tag names | US-003-02 |
| Keyword and tag conditions applied together | US-003-04, BR-010 |
| Filter on archive status, defaulting to active only | US-003-06, BR-009 |
| Ordering pinned notes before unpinned notes | BR-019 |
| A stable page of results plus a total count | US-003-05 |

The index must support a dataset of about 100k notes within the latency target (US-003-03). The mechanism is not named by any input. `platform.md` names PostgreSQL 16, but no input names a search feature (`GAP-BE-012`). Column types, index type and any search-vector column are therefore not stated. The migration is reversible by dropping the index, and no note data is lost (`backend.migration-plan`).

**Timestamps, money and PII.** This module stores none. Note content returned in results is defined by `backend.notes.data-model`.
<!-- /SECTION: backend.search.data-model -->
<!-- SECTION: backend.search.state-machine | status: DRAFT-UNVERIFIED -->
## State machine

**No entity owned by this module has a status column.** The note archive status that search filters on belongs to the `notes` state machine.

The only state this module has is the operational condition of the search index (US-003-03):

| State | Meaning | Terminal |
|---|---|---|
| `AVAILABLE` | Searches are answered. | no |
| `DEGRADED` | The index is degraded or unavailable. | no |

| Transition | Trigger | Guard | Effect |
|---|---|---|---|
| `AVAILABLE` → `DEGRADED` | Index becomes degraded or unavailable | Detection mechanism unstated (`GAP-BE-071`) | Every search returns 503 with a retry-after header instead of hanging |
| `DEGRADED` → `AVAILABLE` | Index recovers | Unstated (`GAP-BE-071`) | Searches are answered again |

No state is terminal. An illegal transition does not arise, because the states are an observed condition and not a caller-driven workflow. The 503 error code is proposed in `backend.error-model` (`ASM-BE-007`).
<!-- /SECTION: backend.search.state-machine -->
<!-- SECTION: backend.search.requirements | status: UNKNOWN -->
<!-- (status set below) -->
<!-- /SECTION: backend.search.requirements -->
<!-- SECTION: backend.search.api-contract | status: VERIFIED -->
## Api contract

_Rendered by DevAgent from the operation records._

| id | method | path | module | success | errors | summary |
|---|---|---|---|---|---|---|
| `API-SEARCH-580505` | GET | `/notes/search` | `search` | 200 | 400, 503, 500 | Search notes by keyword, by tag, or by both combined. Results are paginated, exclude archived notes unless include_archived is set, and list pinned notes first. Returns 503 when the search index is degraded. At least one of q or tags is required (ASM-BE-009, CON-BE-003). |

### `API-SEARCH-580505` — GET `/notes/search`

Search notes by keyword, by tag, or by both combined. Results are paginated, exclude archived notes unless include_archived is set, and list pinned notes first. Returns 503 when the search index is degraded. At least one of q or tags is required (ASM-BE-009, CON-BE-003).

**Request:** 
- `q` (string): Keyword matched against note title and body. At least one of q or tags must be supplied. A present but empty q returns 400 (US-003-01).
- `tags` (array): One or more tag names to filter by. When combined with q, both filters apply (US-003-02, US-003-04). Tag rules are open (GAP-BE-010).
- `include_archived` (boolean): When true, archived notes are included. The default is false, so archived notes are excluded (BR-009). A non-boolean value returns 400.
- `page` (integer): 1-based page number. An invalid or non-numeric value returns 400 (US-003-05).
- `page_size` (integer): Results per page, maximum 100 (NFR-007). The default of 20 is an assumption (ASM-BE-009). A value over the maximum or a non-numeric value returns 400.
**Response:** 
- `items` (array, required): Matching notes for the requested page, with pinned notes before unpinned ones (BR-010). Each item shows the note's id, title, body, pinned flag, status (active or archived), tags and timestamps.
- `page` (integer, required): The page number returned.
- `page_size` (integer, required): The page size applied.
- `total` (integer, required): Total number of notes matching the filters across all pages.
<!-- /SECTION: backend.search.api-contract -->
<!-- SECTION: backend.search.invariants | status: DRAFT-UNVERIFIED -->
## Invariants

| ID | Invariant | Source |
|---|---|---|
| `INV-BE-051` | After any search, no data has changed. Search is read-only. | US-003-01 to US-003-06 |
| `INV-BE-052` | An archived note appears in a response only when `include_archived` is `true`. | BR-009, US-003-06 |
| `INV-BE-053` | Across the whole ordered result set, every matching pinned note precedes every matching unpinned note. | BR-019, US-003-01 |
| `INV-BE-054` | A 200 response never carries more items than the effective page size, and the effective page size never exceeds the maximum. | BR-011, NFR-007, `ASM-BE-009` |
| `INV-BE-055` | A response is never an unbounded result set. | BR-011 |
| `INV-BE-056` | A request with neither a usable `q` nor any `tags` never returns 200. | US-003-01, `CON-BE-003`, `ASM-BE-009`, `ASM-BE-067` |
| `INV-BE-057` | When the index is degraded, the caller receives 503 and not a hung request. | US-003-03 |
<!-- /SECTION: backend.search.invariants -->
<!-- SECTION: backend.search.authorization | status: BLOCKED -->
## Authorization

**BLOCKED.** No role matrix or ownership predicate can be written without inventing an authorisation rule.

| Missing decision | Why it blocks this module | What would unblock it |
|---|---|---|
| Whether notes are owned per user or shared in the team workspace, and the access rule per operation (BR-031, a placeholder). | It decides which notes a search may return to a caller. | `Q-BE-002` |
| What a successful login verify issues, and whether the note and search endpoints require it. | It decides whether `API-SEARCH-580505` needs authentication and whether 401/403 apply (US-004-03). | `Q-BE-003` |
| Who may obtain a login code at all (BR-030, garbled). | It defines the actors. `backend.auth-model` is itself blocked on it. | `Q-BE-001` |

Once decided, the access rule from `Q-BE-002` must apply to search results. This module defines no rule until then.
<!-- /SECTION: backend.search.authorization -->
<!-- SECTION: backend.search.jobs | status: DRAFT-UNVERIFIED -->
## Jobs

- **Scheduled work:** no input specifies any for this module.
- **Index maintenance:** whether the index is updated synchronously with note writes or by a background process, and whether any rebuild or refresh job exists, is unstated. It depends on the unnamed mechanism (`GAP-BE-012`, `GAP-BE-071`). None is invented.
- **Retries:** none specified. A caller that receives 503 retries after the retry-after header. The header value is open (`GAP-BE-011`).
- **Idempotency keys:** none needed. `API-SEARCH-580505` is a read-only GET (`INV-BE-051`), so repeating it has no side effects.
<!-- /SECTION: backend.search.jobs -->
<!-- SECTION: backend.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

The test framework is unnamed by any input (`GAP-BE-012`). Test names are planned.

**Unit**

- `parse_search_params_accepts_q_only`, `..._tags_only`, `..._both` (FR-001, FR-005, FR-008)
- `parse_search_params_rejects_missing_q_and_tags` and `..._whitespace_q` (FR-004)
- `parse_search_params_rejects_non_numeric_negative_zero_page_and_limit` (FR-013)
- `parse_search_params_rejects_limit_over_max` (FR-014)
- `parse_include_archived_accepts_true_false_rejects_other` (FR-017)
- `order_results_places_pinned_first` and `order_is_deterministic_across_pages` (FR-002)

**Integration**

- Keyword matches title and body; no match returns 200 with an empty array (FR-001, FR-003).
- Tag filter, with and without matches; combined filter; combined filter excluding every keyword match (FR-005, FR-006, FR-008, FR-009).
- Pagination: correct subset, `total` equals all matches, page beyond the end returns an empty array (FR-011, FR-012).
- Archived excluded by default and included with `include_archived=true`. Pinned archived notes follow `ASM-BE-073` (FR-015, FR-016).
- Degraded index returns 503 with a retry-after header (FR-018).
- Injection strings in `q` and `tags` do not alter the query (FR-020).

**Contract**

- `contract_search_response_matches_API-SEARCH-580505`, which checks the live response against the published operation. The schema is not restated here.
- `contract_search_documented_in_openapi`, which checks that the route registry and the published document agree (NFR-008).

**Load**

- 50 requests per second against a dataset of about 100k notes, p95 under 300 ms, and the same p95 at a page size of 100 (FR-019; US-003-03; NFR-006, NFR-007). Measurement point and environment are unstated (`GAP-BE-072`).
<!-- /SECTION: backend.search.test-plan -->
<!-- SECTION: backend.search.gaps | status: VERIFIED -->
## Gaps, contradictions and assumptions

### Questions

No question was asked. The run is unattended, so each takes its stated default (core 10). None changes a data shape, state machine, auth rule, or money or PII path, so none is blocking.

| ID | Class | Question | Default taken |
|---|---|---|---|
| `Q-BE-054` | degradable | Is a `limit` above the maximum rejected with 400 or clamped to the maximum? | Rejected with 400 (`ASM-BE-065`) |
| `Q-BE-055` | degradable | With several tags, must a note carry all of them or any of them? | All of them (`ASM-BE-066`) |
| `Q-BE-056` | degradable | Is a whitespace-only `q` treated as empty? | Treated as empty (`ASM-BE-067`) |

### Contradictions

No new contradiction was found in this module. `CON-BE-003` already covers US-003-01 against US-003-02: a missing or empty `q` returns 400, while a tags-only search is valid. US-006-03 adds a second tags-only case, "filter by a tag in the search box", which supports the same reading. Reading adopted: at least one of `q` or `tags` is required (`ASM-BE-009`).

Checks performed:

- story ↔ story within EP-003, EP-006 and BR-019;
- requirement ↔ requirement across BR-009 to BR-011 and BR-019;
- story ↔ design: the brief has no search content.

### Assumptions

| ID | Assumption | Confirms with |
|---|---|---|
| `ASM-BE-064` | The query parameters are `q`, `tags`, `include_archived`, `page` and `limit`, as named in US-003-01, US-003-02, US-003-05 and US-003-06. The rendered operation record for `API-SEARCH-580505` governs the final names. | The rendered contract |
| `ASM-BE-065` | A `limit` above the maximum (100, `ASM-BE-009`) returns 400. | `Q-BE-054` |
| `ASM-BE-066` | With several tags, a note must carry all of them. | `Q-BE-055` |
| `ASM-BE-067` | A `q` that is empty or only whitespace counts as absent. | `Q-BE-056` |
| `ASM-BE-068` | Keyword matching is case-insensitive over title and body. | A search-behaviour decision (`GAP-BE-070`) |
| `ASM-BE-069` | Several tags are sent as repeated `tags` query parameters. A comma-separated form is not accepted. | A decision on tag encoding, with tag rules (`GAP-BE-010`) |
| `ASM-BE-070` | `page` is 1-based and defaults to 1. `page` below 1 and `limit` below 1 return 400. Zero is not named by US-003-05, which names only negative and non-numeric values. | A pagination decision |
| `ASM-BE-071` | Equal results are ordered by note id after pinned status and relevance, so pages are stable. | `GAP-BE-067` |
| `ASM-BE-072` | `include_archived` accepts only `true` or `false`. Any other value returns 400. | A decision on accepted values |
| `ASM-BE-073` | When archived notes are included, pinned-first applies to them as well. | `GAP-BE-069` |

### Gaps

| ID | Gap | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-BE-067` | How relevance is computed, and how ties are broken. US-003-01 says "ordered by relevance" and BR-019 says ordering among equal pinned values is "unchanged", without saying from what. | FR-002 | A relevance and ordering rule from product |
| `GAP-BE-068` | What a "malformed filter combination" is (US-003-04). No example is given. | FR-010 | A definition of the invalid combinations |
| `GAP-BE-069` | Where a pinned archived note sits when archived notes are included. | FR-002, FR-016 | A decision on ordering across archive status |
| `GAP-BE-070` | Keyword semantics: whole word or substring, multi-word queries as all or any terms, stemming. | FR-001, `ASM-BE-068` | A keyword-search behaviour decision |
| `GAP-BE-071` | How index degradation is detected and cleared, and whether index maintenance runs as a job. | State machine, FR-018, jobs | A platform and search-mechanism decision |
| `GAP-BE-072` | The environment and measurement point for the p95 target (NFR-006), and what "typical load" excludes. | FR-019, load test | A performance-test specification |

Open items from the project spec that touch this module:

- `GAP-BE-005` and `GAP-BE-006`: no list operations (see `backend.search.purpose`).
- `GAP-BE-010`: tag rules (case, characters, length). They block the exact condition for "invalid characters" in FR-007.
- `GAP-BE-011`: error envelope details and the retry-after value.
- `GAP-BE-012`: search mechanism, encryption and platform details.
- `GAP-BE-001`: inbound handoff bodies not supplied.
- `Q-BE-001`, `Q-BE-002` and `Q-BE-003` block `backend.search.authorization`.
<!-- /SECTION: backend.search.gaps -->
