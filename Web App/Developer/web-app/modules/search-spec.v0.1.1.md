---
id: agent-developer/web_app/web-app/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/search-SPEC.md
depends_on: [web_app:US-006-01, web_app:US-006-04, web_app:US-001-04, web_app:US-002-03, web_app:US-002-04, web_app:US-002-05, web_app:US-002-06, web_app:US-003-03, web_app:US-003-04]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/modules/search-SPEC.md
---

<!-- SECTION: web-app.search.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: search
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-002, GAP-WEB-003, GAP-WEB-004]
contract_pin: "sha256:8e424bb2b40a"
scope: [/notes, /tags]
```

- `scope` lists the module's routes, as proposed in `web-app.routing` (`ASM-WEB-001`). Route paths are proposals, so this section is `DRAFT-UNVERIFIED`.
- `blocked_by` holds foreign ids defined in the project spec (`web-app.coverage`), resolved under core rule 5.3.
<!-- /SECTION: web-app.search.frontmatter -->
<!-- SECTION: web-app.search.purpose | status: DERIVED -->
## Purpose

**Responsible for** (module map `web-app.module-map`, row `search`; design label M2):
- The notes list and search screen. It holds the sort control, the pinned-first ordering, the live result count, the keyword search box, the tag filter, and the paging of search results (US-006-01, US-006-04, US-002-03, US-002-04, US-002-06, BR-019 to BR-021).
- The pin toggle on a list row (the pin part of US-006-06, assigned to this module by the module map).
- The tags list screen (US-002-05).
- Consuming the notes list, archived-only list and active-only list stories (US-001-04, US-003-03, US-003-04). These are `BLOCKED` by the missing contract operations (`GAP-WEB-002`).

**Not responsible for:**
- Creating or editing a note. `editor` owns that.
- Opening a note, archiving or restoring it, and the detail view. `note-detail` owns those.
- Login and session mechanics. `login` owns login; the session decision is `GAP-WEB-001`.
- Residency and scope documentation screens (`Q-WEB-001`).
- Tag add and remove controls (`GAP-WEB-015`).
- Server-side sort. The web app sends no sort parameter (`INV-WEB-003`, BR-020).
- Backend behaviour of any kind. Needs from the backend are `GAP-WEB-002`, `GAP-WEB-003` and `GAP-WEB-009`.
<!-- /SECTION: web-app.search.purpose -->
<!-- SECTION: web-app.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Requirements marked **BLOCKED** are recorded so that they are not lost. They are not buildable until the named gap closes.

### WEB-SEARCH-FR-001: Sort control
- **Given** the notes list or search results are shown,
- **When** the screen renders,
- **Then** a sort control above the list offers Newest first, Oldest first and Title A-Z, with Newest first selected by default. Sorting is done in the browser and adds no parameter to any API call.
- **Traces to.** US-006-01, BR-020, `INV-WEB-003`
- **Verification.** (planned) `search_sort_default_newest_first`, `search_sort_sends_no_sort_param`
- **API.** none
- Which timestamp "Newest" uses and how Title A-Z collates are `ASM-WEB-101`, `ASM-WEB-105`, `GAP-WEB-102`.

### WEB-SEARCH-FR-002: Sort selection survives searching
- **Given** a sort option is selected,
- **When** the user submits a keyword or applies a tag filter,
- **Then** results render in that sort order and the selection does not change.
- **Traces to.** US-006-04, BR-020
- **Verification.** (planned) `search_keeps_selected_sort`
- **API.** API-SEARCH-580505

### WEB-SEARCH-FR-003: Pinned notes first
- **Given** any rendered set of notes, whether the plain list, filtered results or search results, under any sort,
- **When** it renders,
- **Then** pinned notes appear above unpinned notes, each pinned row shows a pin icon, and the selected sort applies within each group.
- **Traces to.** US-006-01, BR-021, `INV-WEB-001`
- **Verification.** (planned) `search_pinned_above_unpinned_all_sorts`
- **API.** API-SEARCH-580505
- Relevance order conflicts with this requirement: `CON-WEB-101`.

### WEB-SEARCH-FR-004: Live result count
- **Given** notes are rendered,
- **When** the list, search results or filters change,
- **Then** a count above the list equals the number of notes currently rendered. It is 0 when nothing is shown. It is not the API's `total` across pages.
- **Traces to.** US-006-01, US-006-04, BR-019, `INV-WEB-002`
- **Verification.** (planned) `search_count_equals_rendered_rows`
- **API.** API-SEARCH-580505
- The design has no count component (`GAP-WEB-004`).

### WEB-SEARCH-FR-005: Load the plain notes list (BLOCKED)
- **Given** notes exist,
- **When** the notes list page loads with no search or filter,
- **Then** each note's title and status render, pinned first, with the count of FR-004.
- **Traces to.** US-006-01, US-001-04
- **Verification.** (planned) `notes_list_loads_and_renders`
- **API.** none in the contract. US-006-01 names `GET /notes`, which the pinned contract does not contain (`GAP-WEB-002`). No endpoint is assumed.
- **Blocked by** `GAP-WEB-002`. Until it closes, the plain list is not built and `/notes` offers search only (`ASM-WEB-107`).

### WEB-SEARCH-FR-006: Empty states
- **Given** the notes list has no notes, or a search or filter matches none,
- **When** the result loads,
- **Then** an empty-state message renders with a count of 0, and no error is shown. A no-matches search shows an empty-results message.
- **Traces to.** US-006-01, US-006-04, US-001-04, US-002-03, US-002-04
- **Verification.** (planned) `search_no_matches_shows_empty_results_zero`
- **API.** API-SEARCH-580505. The plain-list empty case is `BLOCKED` by `GAP-WEB-002`.

### WEB-SEARCH-FR-007: Error state
- **Given** the notes service returns an error,
- **When** the screen loads or a search is submitted,
- **Then** a user-facing error state renders instead of a blank screen, with a retry action.
- **Traces to.** US-006-01
- **Verification.** (planned) `search_error_shows_error_state_not_blank`
- **API.** API-SEARCH-580505

### WEB-SEARCH-FR-008: Keyword search
- **Given** the user enters a keyword,
- **When** they submit,
- **Then** the web app calls search with the keyword, shows the first page and updates the count.
- **Traces to.** US-006-04, US-002-03
- **Verification.** (planned) `search_keyword_submit_calls_search`
- **API.** API-SEARCH-580505

### WEB-SEARCH-FR-009: Search guard
- **Given** the search box is empty or whitespace and no tag is selected,
- **When** the user submits,
- **Then** the web app does not call search. It shows inline guidance on the search field.
- **Traces to.** US-002-03, BR-007, `INV-WEB-101`
- **Verification.** (planned) `search_blank_submit_makes_no_request`
- **API.** API-SEARCH-580505 (the server's 400 is the backstop)

### WEB-SEARCH-FR-010: Tag filter and combined filter
- **Given** the user selects one or more tags, with or without a keyword,
- **When** they apply the filter,
- **Then** the tags are included in the search request, only matching notes render in the selected sort, and the count updates. With several tags, a note must carry all of them (`ASM-BE-009`, pending `Q-BE-004`).
- **Traces to.** US-006-04, US-002-04
- **Verification.** (planned) `search_tag_filter_includes_tags_param`, `search_keyword_plus_tag_combined`
- **API.** API-SEARCH-580505
- The source of tag choices is `GAP-WEB-003`. The control is undesigned (`GAP-WEB-103`, `ASM-WEB-102`).

### WEB-SEARCH-FR-011: Pagination
- **Given** a search matches more notes than one page holds,
- **When** the user moves to another page,
- **Then** the web app requests that page. The default page size is 20 and the maximum is 100 (NFR-003). A page beyond the results renders the empty state, and page and limit sent are always positive integers (`INV-WEB-102`).
- **Traces to.** US-002-06, NFR-003
- **Verification.** (planned) `search_paging_requests_page_and_limit`, `search_page_beyond_range_shows_empty`
- **API.** API-SEARCH-580505
- The paging control is undesigned (`GAP-WEB-014`, `ASM-WEB-104`).

### WEB-SEARCH-FR-012: Pin and unpin from a row
- **Given** a row in the list,
- **When** the user clicks its pin toggle,
- **Then** the web app sets the note's pinned flag, the row moves to the top with a pin icon (or returns to its sort position when unpinned), and cached search results are invalidated. If the request fails, the row and cache are unchanged and an error is shown (`INV-WEB-103`).
- **Traces to.** US-006-06 (pin part), BR-021
- **Verification.** (planned) `pin_toggle_moves_row_to_top`, `pin_toggle_failure_leaves_list_unchanged`
- **API.** API-NOTES-847551
- The toggle is not designed (`GAP-WEB-004`).

### WEB-SEARCH-FR-013: Search unavailable
- **Given** search answers 503 with a retry delay,
- **When** the response arrives,
- **Then** the error state of FR-007 shows, and retry is offered no earlier than the supplied delay. The request does not hang.
- **Traces to.** US-006-01, `web-app.state-management` rule 5
- **Verification.** (planned) `search_503_honours_retry_after`
- **API.** API-SEARCH-580505

### WEB-SEARCH-FR-014: Tags list (BLOCKED)
- **Given** notes with tags exist,
- **When** the tags list loads,
- **Then** the distinct tags in use render on `Card/Item`, and an empty list shows an empty state.
- **Traces to.** US-002-05, BR-008
- **Verification.** (planned) `tags_list_renders_distinct_tags`
- **API.** none in the contract. **Blocked by** `GAP-WEB-003`.

### WEB-SEARCH-FR-015: Archived-only list (BLOCKED)
- **Given** some notes are archived,
- **When** the user requests the archived view,
- **Then** only archived notes render, and an empty archived list is an empty state, not an error.
- **Traces to.** US-003-03
- **Verification.** (planned) `archived_list_shows_only_archived`
- **API.** none. Search cannot substitute: it requires a keyword or tag (BR-007) and `include_archived` mixes both states. **Blocked by** `GAP-WEB-002`.

### WEB-SEARCH-FR-016: Active-only results
- **Given** archived and active notes exist,
- **When** the user searches without requesting archived notes,
- **Then** only active notes render, and an all-archived outcome renders the empty state.
- **Traces to.** US-003-04, BR-011
- **Verification.** (planned) `search_default_excludes_archived`
- **API.** API-SEARCH-580505. The plain active list is `BLOCKED` by `GAP-WEB-002`.

**Invariants** (defined in `web-app.state-management`, cited here): `INV-WEB-001`, `INV-WEB-002`, `INV-WEB-003`.
**Module invariants:**
- `INV-WEB-101`: The web app never sends a search with neither a keyword nor a tag (BR-007).
- `INV-WEB-102`: Page and limit are sent only as positive integers, with limit at most 100 (NFR-003, US-002-06).
- `INV-WEB-103`: A failed pin request never changes displayed or cached data (`web-app.state-management` rule 4).
<!-- /SECTION: web-app.search.requirements -->
<!-- SECTION: web-app.search.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes` | `SearchScreen` (list, search, filter, sort, paging) | app | `BLOCKED`: whether notes and search need a session is undecided (`GAP-WEB-001`) |
| `/tags` | `TagsListScreen` | app | `BLOCKED`: same, and no list-tags operation exists (`GAP-WEB-003`) |

- Paths are proposals (`ASM-WEB-001`). Hosting both the plain list and search on `/notes` is `ASM-WEB-003`.
- No role differentiation exists on this surface (`web-app.authorization-ui`, `ASM-WEB-006`).
- Opening a result goes to `note-detail`. That route is not specified here.
<!-- /SECTION: web-app.search.routes -->
<!-- SECTION: web-app.search.components | status: UNKNOWN -->
## Components

| Element | Design component | Variants / states | Token bindings |
|---|---|---|---|
| Search box, tag entry | `Input/Text` | Default, Focused, Filled, Error, Disabled (5 variants in the brief) | not supplied |
| Empty list, no matches | `Empty/State` | 1 variant (Default) | not supplied |
| Tag row on `/tags` | `Card/Item` | 1 variant (Default) | not supplied |
| Sort control | none in design | n/a | n/a |
| Result count | none in design | n/a | n/a |
| Pin toggle and pin icon | none in design | n/a | n/a |
| Pagination control | none in design | n/a | n/a |
| Tag filter chips or picker | none in design | n/a | n/a |
| Loading indicator | none in design | n/a | n/a |

- **Component names and states** come from `components-inventory.md` and `screens-inventory.md` (`SearchScreen` uses `Input/Text` and `Empty/State`; `TagsListScreen` uses `Card/Item`).
- **Token bindings are not written.** The brief lists 71 token ids but omits component bodies, so no component-to-token mapping is supplied and none is asserted. Token files are absent (`GAP-WEB-005`).
- **Undesigned elements:** the elements marked "none in design" are required by stories but have no design component (`GAP-WEB-004`, `GAP-WEB-014`, `GAP-WEB-103`). No component is invented here.
- **Closing artefact:** the component bodies, or a connected Figma MCP.
<!-- /SECTION: web-app.search.components -->
<!-- SECTION: web-app.search.states | status: DRAFT-UNVERIFIED -->
## States

### `/notes` (`SearchScreen`)

| State | Behaviour | Source |
|---|---|---|
| Loading | Required, but no design variant exists (`SearchScreen` variants are Default, Empty, Error). Sort control and count stay visible. Rows are replaced by a loading indicator, and the count is not shown as 0 while loading. | `GAP-WEB-104` |
| Empty | `Empty/State` with the empty-results message and a count of 0. For the no-notes case, see FR-005 (`BLOCKED`). | US-006-01, US-006-04 |
| Populated | Pinned rows first, the sort applied within groups, the count equal to the rendered rows, and paging controls when more pages exist. No `Filled` variant exists for this screen. | FR-001 to FR-004, FR-011 |
| Error | User-facing error state with retry. A 503 shows the same state with retry gated by the supplied delay. | US-006-01, FR-013 |
| Permission-denied | Not specified. It is omitted until the access model is decided (`Q-BE-002`, `GAP-WEB-011`). | `web-app.authorization-ui` |

### `/tags` (`TagsListScreen`)

| State | Behaviour |
|---|---|
| Loading, Empty, Populated, Error | `BLOCKED` by `GAP-WEB-003`. Design variants Default and Filled exist, but there is no contract operation to drive them. FR-014 gives the behaviour once unblocked. |

### Partial failure
Not applicable. This module has no multi-item operation: pinning acts on one note, and no bulk selection exists in any input. No `GAP-` is raised for it.
<!-- /SECTION: web-app.search.states -->
<!-- SECTION: web-app.search.api-integration | status: VERIFIED -->
## API integration

| Web operation | Contract operation |
|---|---|
| Keyword, tag, combined search and paging; default exclusion of archived notes | → API-SEARCH-580505 |
| Pin or unpin a note from a row | → API-NOTES-847551 |
| Load the plain list of active notes | none (`GAP-WEB-002`) |
| Load the archived-only list | none (`GAP-WEB-002`) |
| Load distinct tags | none (`GAP-WEB-003`) |

Request and response shapes, the error envelope and status codes are defined only in the pinned contract (`backend.api-surface`, `backend.error-model`) and are not restated here. The field this module displays from the search response is the `total` count. That field is marked "proposed" in the contract (`GAP-WEB-105`). The web count is the number of rendered rows (`INV-WEB-002`).
<!-- /SECTION: web-app.search.api-integration -->
<!-- SECTION: web-app.search.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side | Source |
|---|---|---|---|
| At least one of keyword or tag is present | Yes. Blank or whitespace-only input makes no request (FR-009). | Yes, a validation error is returned for malformed input | BR-007, US-002-03, `INV-WEB-101` |
| Page is a positive integer | Yes, the control only produces valid values | Yes, invalid values return a validation error | US-002-06, `INV-WEB-102` |
| Limit is a positive integer, at most 100 | Yes | Yes for invalid values. The limit maximum is a pending assumption (`ASM-BE-011`, `Q-BE-005`). | US-002-06, NFR-003 |
| Tag text is trimmed and lowercased before sending | Yes (`ASM-WEB-102`) | Tag case rules are unstated for search (`GAP-BE-008`) | BR-005 |
| Whitespace-only keyword counts as empty | Yes (`ASM-WEB-103`) | Not stated | US-002-03 |

- Tag format and length constraints are not stated anywhere (`GAP-BE-008`), so none are validated on the client.
- Server validation errors for these fields are not expected from a correct client. If one arrives, FR-007 applies.
<!-- /SECTION: web-app.search.validation -->
<!-- SECTION: web-app.search.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** The project spec has no accessibility target or verification method (`GAP-WEB-007`, `web-app.accessibility`). It defers focus management, keyboard paths and live regions to module specs "once a target exists". None is written here.

**Elements this module will need to cover once a target exists:**
- The search box, its inline guidance and the tag entry (`Input/Text` has Focused and Error states).
- The sort control and the pin toggle.
- The paging control.
- The announcement of result count changes and error states.

**Unblocked by:** a requirement naming the target standard, its level and the verification method.
<!-- /SECTION: web-app.search.accessibility -->
<!-- SECTION: web-app.search.i18n | status: BLOCKED -->
## i18n

**Blocked.** No input supplies a locale list, copy strings or copy keys (`GAP-WEB-016`).

**Messages this module needs:**
- Sort labels. "Newest first", "Oldest first" and "Title A-Z" are named in US-006-01 and BR-020.
- The result count label.
- The empty-state message and the empty-results message.
- The error state and its retry action.
- The search-unavailable message.
- The inline guidance for a blank search.
- The pin and unpin labels.
- The empty tags message.

**Unblocked by:** a copy deck with locales.
<!-- /SECTION: web-app.search.i18n -->
<!-- SECTION: web-app.search.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode: FULL-FROM-BRIEF`. Figma MCP is not connected, so every name below is an unverified claim from `design-brief.md` and its inventories. No frame ids are supplied anywhere.

| Screen | Module label | Variants | Components | Frame id | Verification |
|---|---|---|---|---|---|
| `SearchScreen` | M2 | Default, Empty, Error | `Empty/State`, `Input/Text` | not supplied | unverified |
| `TagsListScreen` | M2 | Default, Filled | `Card/Item` | not supplied | unverified |

**Story to screen** (`stories-map.md`):
- US-002-03, US-002-04, US-002-06 and US-006-04 map to `SearchScreen`.
- US-002-05 maps to `TagsListScreen`.
- US-006-01, US-001-04, US-003-03, US-003-04 and US-006-06 map to no screen (`GAP-WEB-004`).

`NoteDetailScreen` links to `SearchScreen` (`screens-inventory.md`). That link is owned by `note-detail`.
<!-- /SECTION: web-app.search.design-refs -->
<!-- SECTION: web-app.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests below are (planned).

**Component**
- `search_sort_default_newest_first`
- `search_pinned_above_unpinned_all_sorts`
- `search_count_equals_rendered_rows`
- `search_blank_submit_makes_no_request`
- `pin_toggle_failure_leaves_list_unchanged`
- `search_error_shows_error_state_not_blank`
- `search_503_honours_retry_after`

**E2E**
- `e2e_search_keyword_keeps_sort`
- `e2e_tag_filter_updates_count`
- `e2e_pin_moves_row_top`
- `e2e_paging_to_empty_page`

**Contract**
- `contract_search_request_params_match_pinned_contract`
- `contract_search_response_fields_displayed_present`
- `contract_pin_via_patch_returns_full_note`
- `contract_no_sort_param_sent`
- **Re-run against the new pin.** Any contract change to `total` (`GAP-WEB-105`) triggers a re-run.

**Accessibility**
- `BLOCKED` until a target and method exist (`GAP-WEB-007`).

**Blocked tests.** Plain-list load, tags list and archived-only list have no tests until `GAP-WEB-002` and `GAP-WEB-003` close.
<!-- /SECTION: web-app.search.test-plan -->
<!-- SECTION: web-app.search.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, assumptions and questions

### Contradictions (non-critical; neither side resolved)
- `CON-WEB-101`: US-002-03 ranks search results by relevance, while BR-020 and BR-021 require the selected client-side sort with pinned notes first, and no sort option is "Relevance".
  - Consequence of keeping relevance: it breaks pinned-first and the sort the user chose.
  - Consequence of keeping the sort: relevance ranking is lost to the user.
  - Default: the selected sort and pinned-first apply. Relevance order is never shown.
- `CON-WEB-001` (project spec, cited): client-side sort versus paginated results. FR-001 and FR-011 sort only the loaded page (`ASM-WEB-106`).
- `CON-WEB-002` (project spec, cited): not owned by this module; archive is in `note-detail`.

### Questions (degradable; defaults taken, unattended)
- `Q-WEB-101`: How does the user choose tags to filter by? Default: typed tag names, because the list of tags is unavailable (`ASM-WEB-102`).
- `Q-WEB-102`: When the search box is cleared and submitted, does the screen return to the plain list? Default: yes once a list operation exists; until then the screen shows its initial state (`ASM-WEB-107`).

### Assumptions
- `ASM-WEB-101`: "Newest first" and "Oldest first" sort on `created_at`. The search items carry timestamps, but no input names the sort key.
- `ASM-WEB-102`: Tag entries are typed free text, trimmed and lowercased before sending (BR-005 normalises tags to lowercase on write).
- `ASM-WEB-103`: A whitespace-only keyword counts as empty.
- `ASM-WEB-104`: Paging is by page number using the default page size; no page-size selector exists because none is designed. The control form is undecided.
- `ASM-WEB-105`: Title A-Z compares case-insensitively with locale-aware collation.
- `ASM-WEB-106`: Sort and pinned-first apply to the loaded page only (default of `CON-WEB-001`).
- `ASM-WEB-107`: Until `GAP-WEB-002` closes, `/notes` offers search only and builds no plain-list load.

### Gaps (each names its closing artefact)
- `GAP-WEB-102`: The timestamp used by Newest/Oldest and the Title A-Z collation are unstated. Closing artefact: a rule in the list story.
- `GAP-WEB-103`: No design for the tag filter control (picker, chips, typed entry) and no web story for it. Closing artefact: design frames.
- `GAP-WEB-104`: `SearchScreen` has no loading variant, no populated variant, and no frames for the sort control, count, pin toggle or paging. Closing artefact: design frames. This extends `GAP-WEB-004`.
- `GAP-WEB-105`: The contract marks the search `total` field as "proposed", because the inputs never state a total count, though US-002-06 expects one. Closing artefact: a confirmed contract field.

**Project-level gaps this module waits on** (defined in `web-app.coverage`, not re-minted): `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003`, `GAP-WEB-004`, `GAP-WEB-005`, `GAP-WEB-007`, `GAP-WEB-009`, `GAP-WEB-011`, `GAP-WEB-014`, `GAP-WEB-015`, `GAP-WEB-016`.

**Backend needs** (sent through the lane-needs step, not written here):
- A list-active and list-archived operation (`GAP-WEB-002`).
- A list-distinct-tags operation (`GAP-WEB-003`).
- Confirmation of the search `total` field (`GAP-WEB-105`).
<!-- /SECTION: web-app.search.gaps -->
