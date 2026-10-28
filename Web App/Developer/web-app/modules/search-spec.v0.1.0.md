---
id: agent-developer/web_app/web-app/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/search-SPEC.md
depends_on: [web_app:US-002-03, web_app:US-002-04, web_app:US-002-06, web_app:US-006-04]
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:web-app/modules/search-SPEC.md
---

<!-- SECTION: web-app.search.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: search
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-002, GAP-WEB-011]
contract_pin: "sha256:a6f2ca3343de"
scope: [/notes/search]
```

- **`spec_mode`.** The design brief is present and Figma is not connected, so every screen, component and token name here is an unverified claim from design-brief.md. [RUN-CONTEXT `inputs.figma_mcp`; design-brief.md]
- **`blocked_by`.** The sort control, tag filter control, result row, live count and pagination controls have no design. They degrade the components and states sections but do not stop the behavioural requirements. [project spec `GAP-WEB-002`, `GAP-WEB-011`]
- **`scope`.** The single route the project module map assigns to `search`. [project spec `web-app.module-map`, `web-app.routing`]
<!-- /SECTION: web-app.search.frontmatter -->
<!-- SECTION: web-app.search.purpose | status: DERIVED -->
## Purpose

**Responsible for.** Keyword and single-tag search of notes from the browser, with the sort selection and live result count applied to the displayed results, paginated loading, and the empty and error presentation of a search. [stories.md US-002-03, US-002-04, US-002-06, US-006-04; project spec `web-app.module-map`]

**Not responsible for:**
- **Sign-in, session expiry and the global `401` redirect.** Owned by `auth` and `web-app.auth-flows`. This module only states that a `401` from search follows that rule.
- **The notes list, row pin toggle, create, view and edit.** Owned by `notes`. Pin affects only the order of search results (`INV-WEB-006`).
- **Archive, restore and the archived view.** Owned by `archive`.
- **Tag add and remove and distinct-tag discovery.** No web story or screen asks for them. [project spec `GAP-WEB-003`]
- **Search ranking, indexing, latency and result limits.** Server behaviour. The 300 ms target is the API's, and the web sets no target of its own. [project spec `web-app.performance`]
- **Defining the response, parameter or error schemas.** Referenced by pinned ID only (`web-app.search.api-integration`).
<!-- /SECTION: web-app.search.purpose -->
<!-- SECTION: web-app.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements depend on `API-SEARCH-580505`. Test names are proposed, hence `(planned)`.

**`WEB-SEARCH-FR-001` — Keyword search.**
- *Given* an authenticated user on `/notes/search` who enters a keyword, *when* they submit, *then* the web app calls the search operation with that keyword and renders the matching notes. [stories.md US-002-03, US-006-04]
- **Traces to.** US-002-03, US-006-04.
- **Verification.** `search.keyword.renders-matches` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-002` — Tag filter.**
- *Given* the user selects a tag filter, *when* they apply it, *then* the web app includes that tag in the search request and renders only the matching notes. [stories.md US-002-04, US-006-04]
- The control offers one tag, because the contract takes a single tag (`GAP-WEB-024`).
- **Traces to.** US-002-04, US-006-04.
- **Verification.** `search.tag.filters-results` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-003` — Keyword and tag combined.**
- *Given* a keyword and a tag are both set, *when* the user submits, *then* one request carries both and the rendered notes are those the server returns. [stories.md US-002-04]
- **Traces to.** US-002-04.
- **Verification.** `search.combined.sends-both` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-004` — Search precondition.**
- *Given* neither a keyword nor a tag is present after trimming, *when* the user submits, *then* no request is sent and the keyword field shows an inline "enter a keyword or choose a tag" error. [requirements.md BR-007]
- See `CON-WEB-120` and `INV-WEB-112`.
- **Traces to.** US-002-03, BR-007.
- **Verification.** `search.precondition.blocks-empty-submit` (planned).
- **API.** none (no request is issued).

**`WEB-SEARCH-FR-005` — No matches.**
- *Given* the server returns a successful response with no results, *when* results load, *then* the app shows `Empty/State` and a count of `0`, with no error. [stories.md US-002-03, US-002-04, US-006-04; design-brief.md components-inventory]
- This includes a tag that exists on no note, which the server reports as an empty result. [stories.md US-002-04]
- **Traces to.** US-002-03, US-002-04, US-006-04.
- **Verification.** `search.empty.shows-empty-state-and-zero` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-006` — Pagination.**
- *Given* more matches exist than one page, *when* the user requests the next page, *then* the app requests the next page and appends its notes to the loaded set. [stories.md US-002-06; requirements.md NFR-003; `ASM-WEB-012`, `ASM-WEB-502`]
- The first request uses page size 20, and the page size never exceeds 100.
- The next page is offered while the loaded count is below the server-reported total. [project spec `web-app.state-management`]
- *Given* a requested page is empty, *when* it loads, *then* pagination ends with no error. [stories.md US-002-06; `ASM-WEB-503`]
- **Traces to.** US-002-06.
- **Verification.** `search.pagination.appends-next-page` and `search.pagination.empty-page-ends` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-007` — Client-side sort, pinned first.**
- *Given* a sort selection (Newest first, Oldest first or Title A-Z), *when* results render, *then* they are ordered by that selection with pinned notes above unpinned ones, and running a search does not change the selection. [stories.md US-006-04; requirements.md BR-020, BR-021]
- Sort is never sent to the API (`INV-WEB-005`, `INV-WEB-006`).
- Sort orders only the loaded set and overrides server relevance order (`CON-WEB-003`).
- **Traces to.** US-006-04, BR-020, BR-021.
- **Verification.** `search.sort.client-side-pinned-first` and `search.sort.selection-survives-search` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-008` — Live result count.**
- *Given* results are displayed, *when* the filter, sort or result set changes, *then* the count above the list equals the number of notes rendered, and is hidden while results load. [requirements.md BR-019; stories.md US-006-04; `INV-WEB-004`]
- See `CON-WEB-002` and `ASM-WEB-010`.
- **Traces to.** US-006-04, BR-019.
- **Verification.** `search.count.equals-rendered` and `search.count.hidden-while-loading` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-009` — Failure presentation.**
- *Given* a search fails with a validation failure, an unavailable-search response, or a network or server failure, *when* the response arrives, *then* the app shows a user-facing error state, never a blank screen. [stories.md US-006-01 (pattern for list screens); backend-api.md `backend.error-model`]
- A rejected parameter is shown on the field it names (the response carries the detail).
- An unavailable-search response (`503`) shows a search-unavailable error with a manual retry (`ASM-WEB-505`).
- **Traces to.** US-002-06, US-006-04.
- **Verification.** `search.error.validation`, `search.error.unavailable` and `search.error.network` (planned).
- **API.** `API-SEARCH-580505`.

**`WEB-SEARCH-FR-010` — Result activation.**
- *Given* a result row, *when* the user activates it, *then* the app navigates to `/notes/:id`. [`ASM-WEB-506`]
- The route is owned by `notes`.
- **Traces to.** US-006-04 (inferred; see `ASM-WEB-506`).
- **Verification.** `search.row.navigates-to-note` (planned).
- **API.** none.

**Invariants:**
- **`INV-WEB-112`.** The web app never sends a search request that has neither a keyword nor a tag. [requirements.md BR-007]
- **`INV-WEB-113`.** A response to a superseded search (an older filter or page) never renders and never changes the count. [derived from `INV-WEB-004`: a count from a previous filter never persists]
<!-- /SECTION: web-app.search.requirements -->
<!-- SECTION: web-app.search.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes/search` | `SearchScreen` (M2) | Authenticated shell | Authenticated Team member session |

- **Paths.** The path is proposed. [project spec `ASM-WEB-001`]
- **Matching.** The static segment `search` must match before `/notes/:id`. [project spec `INV-WEB-001`]
- **Permission.** No role differentiation exists on this surface: the Team member is the only role. A `401` ends the session and redirects to `/login` with the return target. A `403` renders the permission-denied state. [project spec `web-app.authorization-ui`, `web-app.auth-flows`]
- **Layout.** The authenticated shell has no design. [project spec `GAP-WEB-002`]
- **Search state in the URL.** No input says whether the keyword, tag, page or sort is reflected in the URL (`ASM-WEB-507`).
<!-- /SECTION: web-app.search.routes -->
<!-- SECTION: web-app.search.components | status: DRAFT-UNVERIFIED -->
## Components

Names, variants and states are from the design brief. Per-component token bindings are not supplied (`GAP-WEB-705`), so the binding column is empty rather than guessed. Token IDs exist in the brief (`web-app.design-system`), but no input maps any of them to a component.

| Component | Role in this module | Variants and states (brief) | Token bindings |
|---|---|---|---|
| `Input/Text` | Keyword field. [`ASM-WEB-504`] | 5 variants; states Default, Focused, Filled, Error, Disabled | not supplied (`GAP-WEB-705`) |
| `Empty/State` | No-matches presentation (`WEB-SEARCH-FR-005`) | 1 variant; state Default | not supplied (`GAP-WEB-705`) |
| `Button/Primary` | Submit search. Not shown on `SearchScreen` in the brief inventory, so use is an assumption (`ASM-WEB-504`). | 12 variants; states Default, Pressed, Loading, Disabled | not supplied (`GAP-WEB-705`) |

**Needed, with no design:**
- tag filter control;
- sort control (shared with `notes`);
- live result count;
- result row;
- pagination or load-more control;
- search-unavailable retry control;
- permission-denied state.

[project spec `GAP-WEB-002`, `GAP-WEB-011`; `GAP-WEB-706`]
<!-- /SECTION: web-app.search.components -->
<!-- SECTION: web-app.search.states | status: DRAFT-UNVERIFIED -->
## States

The brief gives `SearchScreen` three variants: Default, Empty, Error. [design-brief.md screens-inventory] The mapping below is assumed (`ASM-WEB-501`).

**Route `/notes/search`:**

| State | Presentation | Basis |
|---|---|---|
| Idle (nothing searched yet) | `SearchScreen` Default; no count shown (`ASM-WEB-508`) | `ASM-WEB-501` |
| Loading | A search is in flight. The count is hidden and stale results do not render (`INV-WEB-004`, `INV-WEB-113`). The loading presentation is not designed. | project spec `GAP-WEB-010` |
| Populated | Results sorted per `WEB-SEARCH-FR-007`, with the count per `WEB-SEARCH-FR-008`. A next-page affordance while loaded count is below the total. | stories.md US-006-04, US-002-06 |
| Empty | `Empty/State`, count `0`, no error | stories.md US-002-03, US-006-04 |
| Error | `SearchScreen` Error variant. Validation failure shows on the field. Search-unavailable (`503`) shows an error with a manual retry. Any other failure shows a generic try-again error. | `WEB-SEARCH-FR-009`; `ASM-WEB-505` |
| Permission-denied | A `403` shows the permission-denied state, distinct from a not-found state. It has no design. | project spec `web-app.authorization-ui`; `GAP-WEB-002` |
| Unauthenticated | A `401` ends the session and redirects to `/login`. Not rendered here. | project spec `web-app.auth-flows` |

**Pagination edge.** A page-load failure after the first page keeps the already loaded results visible and shows the error beside the next-page affordance (`ASM-WEB-509`).

**Partial-failure.** Not applicable. This module has no multi-item operation: search is a single read.

Copy for every state is not supplied (project spec `GAP-WEB-010`).
<!-- /SECTION: web-app.search.states -->
<!-- SECTION: web-app.search.api-integration | status: VERIFIED -->
## API integration

| Operation | Pinned ID | Used by |
|---|---|---|
| Search notes (keyword, tag, pagination) | `API-SEARCH-580505` | `WEB-SEARCH-FR-001` to `WEB-SEARCH-FR-009` |

- **Contract.** Pin `sha256:a6f2ca3343de`. [RUN-CONTEXT `upstream.contract_pin`] The ID carries no `@version` suffix in the contract. [project spec `GAP-WEB-015`]
- **Parameters, response and errors.** Defined in the contract at `API-SEARCH-580505` and not restated here.
- **Not used.** The archived-inclusion parameter of the operation is not set by any requirement here (`GAP-WEB-705`).
- **Not available.** A notes list operation and a distinct-tag list operation do not exist in the contract. [project spec `GAP-WEB-001`, `GAP-WEB-003`]
<!-- /SECTION: web-app.search.api-integration -->
<!-- SECTION: web-app.search.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side |
|---|---|---|
| At least one of keyword or tag present | Yes. No request is sent otherwise (`INV-WEB-112`). | Yes: a request with neither is rejected as a validation failure. [requirements.md BR-007; stories.md US-002-03] |
| Keyword and tag values are trimmed; a whitespace-only keyword counts as absent | Yes (`ASM-WEB-504`) | Not stated |
| Page size never above 100; first request uses 20 | Yes [requirements.md NFR-003; `ASM-WEB-012`] | Invalid page values are rejected as a validation failure. [stories.md US-002-06; backend-api.md `API-SEARCH-580505`] |
| Page value is a positive integer | Yes, by construction: the client generates page values from its own pagination state | Same as above |
| Keyword length and character set | Not validated. No input states a limit. [backend-api.md `GAP-BE-035`] | Not stated |
| Tag format and case | Not validated. Tag names are case-insensitive and normalised server-side. [requirements.md BR-005] | Server-side normalisation |

- **Contradiction.** US-002-03 rejects an empty or missing query, while BR-007 accepts a tag alone (`CON-WEB-120`).
- **Server errors.** A rejection from the server is displayed from its response and is never reinterpreted by the client.
<!-- /SECTION: web-app.search.validation -->
<!-- SECTION: web-app.search.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No input states a target standard (project spec `GAP-WEB-019`), so none is claimed. The behaviours below are proposals (`ASM-WEB-510`).

- **Focus management.**
  - On load of `/notes/search`, focus is placed on the keyword field.
  - After a search completes, focus stays on the field.
  - After an error that targets a field, focus moves to that field.
- **Keyboard paths.** The keyword field submits on Enter. The tag filter, sort control, result rows and next-page affordance are reachable and operable by keyboard. Controls are undesigned, so exact widgets are open (`GAP-WEB-706`).
- **Live regions.** The result count and the empty, error and search-unavailable messages are announced politely when they change (`INV-WEB-004`).
- **Form errors.** An inline error is programmatically associated with the keyword field, and the field's Error state conveys the error by more than colour. [design-brief.md components-inventory: `Input/Text` has an Error state]
- **Verification.** See `web-app.search.test-plan`.
<!-- /SECTION: web-app.search.accessibility -->
<!-- SECTION: web-app.search.i18n | status: BLOCKED -->
## I18n

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.search.i18n -->
<!-- SECTION: web-app.search.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma is not connected and a design brief is present, so `spec_mode` is `FULL-FROM-BRIEF`. Every entry is **unverified**: it was taken from design-brief.md and not checked against Figma. [RUN-CONTEXT `inputs.figma_mcp`; core 4.5]

| Reference | Kind | Source | Verification |
|---|---|---|---|
| `SearchScreen` (module M2; variants Default, Empty, Error) | screen name | design-brief.md screens-inventory | unverified |
| `Input/Text` | component | design-brief.md components-inventory | unverified |
| `Empty/State` | component | design-brief.md components-inventory | unverified |
| `Button/Primary` | component (use assumed, `ASM-WEB-504`) | design-brief.md components-inventory | unverified |
| Token IDs | token names | design-brief.md tokens-index (listed in `web-app.design-system`) | unverified; no component binding supplied (`GAP-WEB-705`) |

- **Frame IDs.** The brief supplies none, so none are recorded.
- **Outgoing flow edges.** The brief shows no outgoing transition from `SearchScreen`. The two edges into it come from `NoteEditorScreen` and `ApiScopeDocsScreen`. [design-brief.md screens-inventory; project spec `web-app.routing`, `GAP-WEB-023`]
- **Stories-map.** US-002-03, US-002-04, US-002-06 and US-006-04 all map to `SearchScreen`. [design-brief.md stories-map]
<!-- /SECTION: web-app.search.design-refs -->
<!-- SECTION: web-app.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are proposed.

**Component:**
- `search.keyword.renders-matches`
- `search.tag.filters-results`
- `search.combined.sends-both`
- `search.precondition.blocks-empty-submit`
- `search.empty.shows-empty-state-and-zero`
- `search.sort.client-side-pinned-first`
- `search.sort.selection-survives-search`
- `search.count.equals-rendered`
- `search.count.hidden-while-loading`
- `search.stale-response.is-discarded` (`INV-WEB-113`)

**End to end:**
- `e2e.search.keyword-then-open-note`
- `e2e.search.tag-and-keyword-paginate`
- `e2e.search.unauthenticated-redirects-to-login`

**Contract** (against `API-SEARCH-580505`, pin `sha256:a6f2ca3343de`):
- `contract.search.request-matches-published-parameters`
- `contract.search.sort-parameter-never-sent` (`INV-WEB-005`)
- `contract.search.never-called-without-keyword-or-tag` (`INV-WEB-112`)
- `contract.search.handles-empty-page-and-total`
- `contract.search.handles-validation-and-unavailable-errors`

**Accessibility** (no target standard supplied, `GAP-WEB-019`):
- `a11y.search.keyboard-submit-and-focus`
- `a11y.search.count-and-errors-announced`
- `a11y.search.field-error-association`
<!-- /SECTION: web-app.search.test-plan -->
<!-- SECTION: web-app.search.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

Items defined in the project spec are referenced here, not redefined.

**Referenced from the project spec.**
- **Gaps.** `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003`, `GAP-WEB-008`, `GAP-WEB-010`, `GAP-WEB-011`, `GAP-WEB-015`, `GAP-WEB-017`, `GAP-WEB-019`, `GAP-WEB-023`, `GAP-WEB-024`.
- **Contradictions.** `CON-WEB-002`, `CON-WEB-003`.
- **Assumptions.** `ASM-WEB-001`, `ASM-WEB-004`, `ASM-WEB-010`, `ASM-WEB-012`.

**Contradiction checks performed.**
- **Story to design:** none found.
- **Story to requirement:** `CON-WEB-120` found, and `CON-WEB-002` is relevant.
- **Requirement to requirement:** `CON-WEB-003` is relevant.
- **Design to design:** none found.

| ID | Kind | Statement |
|---|---|---|
| `CON-WEB-120` | non-critical | stories.md US-002-03 says a search with an empty or missing query returns a validation error. requirements.md BR-007 says search requires at least one of a keyword or a tag, and US-002-04 searches by tag alone. Reading used: BR-007. The client sends a search when a keyword or a tag is present. |
| `GAP-WEB-705` | gap | No input maps any token ID to `Input/Text`, `Empty/State` or `Button/Primary`, and no input says whether the search includes archived notes by default. The archived-inclusion parameter exists in the contract, but no story or requirement says when a web search should set it. Archived inclusion is therefore not set by the client. Closing artefact: token bindings from the design, and a decision on archived inclusion with the backend default. |
| `GAP-WEB-706` | gap | No design exists for the search-specific controls: the tag filter, result row, count, pagination or load-more control, and retry control. Their widget types are open. Closing artefact: frames for these. |
| `ASM-WEB-501` | assumption | `SearchScreen` variants map as Default to idle and populated, Empty to no matches, Error to failure. Confirm by inspecting the frames. |
| `ASM-WEB-502` | assumption | Further pages append to the loaded set, and client sort and the count apply to that accumulated set. Confirm with a pagination design. |
| `ASM-WEB-503` | assumption | An empty page beyond the first ends pagination with no error, because the server returns an empty list for a page beyond the results. [stories.md US-002-06] |
| `ASM-WEB-504` | assumption | `Input/Text` is the keyword field and `Button/Primary` is the submit control. Keyword and tag values are trimmed, and a whitespace-only keyword counts as absent. Confirm with the frames and a validation rule. |
| `ASM-WEB-505` | assumption | An unavailable-search response shows a search-unavailable error with a manual retry. There is no automatic retry, and the retry-after value is not used. Confirm with a retry policy. |
| `ASM-WEB-506` | assumption | Activating a result row navigates to `/notes/:id`. No story or frame states it. Confirm with the result-row design. |
| `ASM-WEB-507` | assumption | Search state is held in client state with no reflection in the URL. Confirm with a routing rule. |
| `ASM-WEB-508` | assumption | The count is not shown before the first search. Confirm with the design. |
| `ASM-WEB-509` | assumption | A failure while loading a page after the first keeps the loaded results visible and shows the error beside the next-page affordance. Confirm with a pagination design. |
| `ASM-WEB-510` | assumption | The focus, keyboard and live-region behaviours in `web-app.search.accessibility` are proposals, not requirements from any input. Confirm against an accessibility target. |
<!-- /SECTION: web-app.search.gaps -->
