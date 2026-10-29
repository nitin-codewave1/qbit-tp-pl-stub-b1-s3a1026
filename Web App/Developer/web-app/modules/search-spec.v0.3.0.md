---
id: agent-developer/web_app/web-app/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/search-SPEC.md
depends_on: [web_app:US-006-01, web_app:US-006-04]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/search-SPEC.md
---

<!-- SECTION: web-app.search.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: search
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-002, GAP-WEB-003]
contract_pin: "sha256:9550f0d579d8"
scope: [/notes]
```

- `spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma is `not-connected` (`RUN-CONTEXT.inputs.figma_mcp`).
- `blocked_by` names three project-level gaps, all defined in `web-app.overview`:
  - `GAP-WEB-001`: session model undecided.
  - `GAP-WEB-002`: no list-notes operation.
  - `GAP-WEB-003`: no tag-list or remove-tag operation.
- `scope` is the one route this module owns (`web-app.routing`, `web-app.module-map`).
<!-- /SECTION: web-app.search.frontmatter -->
<!-- SECTION: web-app.search.purpose | status: DERIVED -->
## Purpose

**Responsible for** (`web-app.module-map`, row `search`; design label M2):
- The `/notes` page on `SearchScreen`:
  - the keyword input and the tag filter;
  - the submit control;
  - the client-side sort control;
  - the live result count;
  - the result rows, with the pin icon and pin toggle.
- The idle, loading, populated, empty-results and error states of that page.
- Stories `US-006-01` and `US-006-04`.
- The pin toggle of `US-006-06`, which the module map assigns to `search`. Archive and restore of `US-006-06` belong to `notes` and `archive`.

**Not responsible for:**
- Creating, viewing or editing a note (`/notes/new`, `/notes/:id`). Module `notes`. This module only navigates to `/notes/:id`.
- The archived-notes view and restore. Module `archive`.
- Tag add and remove, and `NoteTagsScreen`. Module `tags`, unrouted (`GAP-WEB-003`).
- Login, session and the protected-route rule. Module `login` and `web-app.auth-flows` (`GAP-WEB-001`).
- Any backend behaviour. Backend needs are `GAP-WEB-002`, `GAP-WEB-003` and `GAP-WEB-004` in `web-app.overview`.
- The plain "all notes" data source. The contract has no list operation (`GAP-WEB-002`), so the plain-list half of `US-006-01` is blocked here.
<!-- /SECTION: web-app.search.purpose -->
<!-- SECTION: web-app.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Rule IDs (`BR-`, `NFR-`) are from `requirements.md`. Stories are from `stories.md`. `INV-WEB-001` to `INV-WEB-005` are defined in `web-app.state-management`.

**`WEB-SEARCH-FR-001` Sort control.**
- Given the user opens `/notes`, when the page renders, then a sort control is shown above the list with the options Newest first, Oldest first and Title A-Z, and Newest first is selected.
- **Traces to.** US-006-01
- **Verification.** (planned) `search.sort.default-newest-first`
- **API.** none (client-only; `INV-WEB-003`)

**`WEB-SEARCH-FR-002` Plain list on load: BLOCKED.**
- US-006-01 says the page "calls GET /notes" and renders every note. The contract has no such operation, and search requires a keyword or a tag (`BR-007`). Until `GAP-WEB-002` closes, `/notes` opens in the idle state and makes no call (`ASM-WEB-119`).
- When the list operation exists: Given notes exist, when `/notes` loads, then the active list is fetched and rendered under FR-003 to FR-005.
- **Traces to.** US-006-01
- **Verification.** (planned) `search.list.loads-active-notes` (cannot run until `GAP-WEB-002` closes)
- **API.** none in the contract (`GAP-WEB-002`)

**`WEB-SEARCH-FR-003` Row rendering and pinned-first.**
- Given a result set is rendered, when the page shows it, then each row shows the note's title and status, and pinned rows appear above unpinned rows with a pin icon, within whichever sort is selected (`BR-021`, `INV-WEB-001`).
- **Traces to.** US-006-01, US-006-04
- **Verification.** (planned) `search.rows.pinned-first-under-every-sort`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-004` Live result count.**
- Given a result set is rendered, when the page shows it, then a count above the list equals the number of notes rendered (`BR-019`, `INV-WEB-002`), and it is 0 when the empty state is shown.
- The count is shown only in the populated and empty states (`ASM-WEB-125`).
- Over multiple pages, rendered versus total is open (`GAP-WEB-012`, `GAP-WEB-123`).
- **Traces to.** US-006-01, US-006-04
- **Verification.** (planned) `search.count.equals-rendered-notes`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-005` List empty state: BLOCKED with FR-002.**
- Given no notes exist, when the list loads, then an empty-state message and a count of 0 are shown instead of an error.
- **Traces to.** US-006-01
- **Verification.** (planned) `search.list.empty-state` (blocked on `GAP-WEB-002`)
- **API.** none in the contract (`GAP-WEB-002`)

**`WEB-SEARCH-FR-006` Error state.**
- Given a search request fails (service unavailable, other error status, or network failure), when the response or failure is received, then a user-facing error state replaces the list instead of a blank screen. It allows retry and keeps the user's inputs (`ASM-WEB-013`).
- **Traces to.** US-006-01
- **Verification.** (planned) `search.error.not-blank-and-retryable`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-007` Keyword search.**
- Given the user enters a keyword, when they submit, then the web app calls the search operation with that keyword (and any selected tags).
- It renders the matches in the currently selected sort order, without changing the sort selection (`INV-WEB-106`).
- It updates the count to the number of matches shown.
- **Traces to.** US-006-04
- **Verification.** (planned) `search.keyword.submit-renders-matches-keeps-sort`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-008` Tag filter.**
- Given the user selects one or more tag filters, when they apply them, then the request includes the tags (together with the keyword when one is entered).
- Only matching notes render, in the selected sort order, and the count updates to match.
- How tags are chosen is `ASM-WEB-117` (`GAP-WEB-003`).
- **Traces to.** US-006-04
- **Verification.** (planned) `search.tags.apply-filters-request-and-render`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-009` Empty results.**
- Given a search returns no matches, when the results load, then an empty-results message and a count of 0 are shown instead of an error.
- **Traces to.** US-006-04
- **Verification.** (planned) `search.results.empty-not-error`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-010` No-filter guard.**
- Given the keyword is empty or whitespace-only and no tag is selected, when the user submits, then no request is made and the page shows the idle state (`BR-007`, `INV-WEB-004`, `ASM-WEB-119`).
- **Traces to.** US-006-04
- **Verification.** (planned) `search.guard.no-call-without-keyword-or-tag`
- **API.** `API-SEARCH-580505` (never called in this case)

**`WEB-SEARCH-FR-011` Sort change is client-only.**
- Given results are displayed, when the user changes the sort selection, then the list reorders within the loaded set, the count is unchanged, no request is made and no sort parameter is ever sent (`BR-020`, `INV-WEB-003`, `INV-WEB-107`).
- Pinned notes stay above unpinned ones.
- **Traces to.** US-006-01, US-006-04
- **Verification.** (planned) `search.sort.client-side-no-request`
- **API.** none

**`WEB-SEARCH-FR-012` Pin toggle.**
- Given a row is displayed, when the user activates its pin toggle, then the web app sets the note's pinned flag through the edit operation.
- On success the row moves to the top with a pin icon, or returns to its sort position when unpinned. The change is applied on the response, not optimistically (`ASM-WEB-014`).
- All cached search results are then invalidated (`web-app.state-management`, policy 2).
- **Traces to.** US-006-06 (pin part, assigned to this module by `web-app.module-map`)
- **Verification.** (planned) `search.pin.toggle-moves-row-on-success`
- **API.** `API-NOTES-847551`

**`WEB-SEARCH-FR-013` Pin failure.**
- Given the pin request fails, when the response or failure is received, then the row and cached data are unchanged, the toggle is re-enabled and a message is shown (`ASM-WEB-124`, `GAP-WEB-124`).
- **Traces to.** US-006-06 (pin part), cache policy 4 in `web-app.state-management`
- **Verification.** (planned) `search.pin.failure-leaves-row-unchanged`
- **API.** `API-NOTES-847551`

**`WEB-SEARCH-FR-014` Request lifecycle.**
- Given a search request is pending, when the user looks at the page, then the submit control shows its Loading state and cannot be activated again.
- A response to a superseded request is discarded (`ASM-WEB-121`).
- **Traces to.** US-006-04 ("when they submit"), `ASM-WEB-121`
- **Verification.** (planned) `search.lifecycle.no-double-submit-latest-wins`
- **API.** `API-SEARCH-580505`

**`WEB-SEARCH-FR-015` Result truncation notice.**
- Given a search matches more notes than the one page requested, when results render, then only that page renders and a notice states that not all matches are shown (`ASM-WEB-120`, `GAP-WEB-123`).
- **Traces to.** US-006-04, `BR-019`
- **Verification.** (planned) `search.pagination.truncation-notice`
- **API.** `API-SEARCH-580505`

**Invariants minted here**
- `INV-WEB-106`: A search submission never changes the sort selection (US-006-04).
- `INV-WEB-107`: A sort change issues no request and sends no parameter (`BR-020`).
<!-- /SECTION: web-app.search.requirements -->
<!-- SECTION: web-app.search.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes` | `SearchScreen` | app (`web-app.routing`) | `BLOCKED`: whether notes and search require a session is undecided (`Q-BE-003`, `GAP-WEB-001`, `GAP-WEB-015`). No role or ownership predicate is asserted. |

- Search text, selected tags and the sort selection are client state. They are not placed in the URL, so the route takes no parameters (`ASM-WEB-127`).
- **Outbound navigation.** Opening a result row goes to `/notes/:id`, owned by `notes` (`web-app.module-map`). The design has no outgoing transition from `SearchScreen` (`screens-inventory.md`), so no other navigation is asserted.
- **Inbound design transitions.** `NoteTagsScreen` and `ScopeDocumentationScreen` each link to `SearchScreen` twice in the design. Neither is routed (`GAP-WEB-003`, `Q-WEB-002`).
- **Unauthenticated redirect.** `BLOCKED` with the protected-route rule (`web-app.routing`).
<!-- /SECTION: web-app.search.routes -->
<!-- SECTION: web-app.search.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `search`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.search.components -->
<!-- SECTION: web-app.search.states | status: DRAFT-UNVERIFIED -->
## States

`SearchScreen` has the design variants Default, Empty and Error (`screens-inventory.md`). The mapping below is unverified (`ASM-WEB-125`). The design has no Loading variant.

**Route `/notes`**

| State | Trigger | What is shown | Count | Source |
|---|---|---|---|---|
| Idle | page opened, or submit with no keyword and no tag | form, sort control, and an `Empty/State` message. No request is made. | hidden | FR-002, FR-010; `ASM-WEB-119` |
| Loading | a search request is pending | submit control in Loading, not re-activatable; prior results are not shown as current | hidden | FR-014 |
| Populated | success with at least one item | rows under the selected sort, pinned first | rendered count | FR-003, FR-004 |
| Empty | success with zero items | empty-results message (`Empty/State`) | 0 | FR-009 |
| Error | service-unavailable response, other failure, or network failure | user-facing error with retry, inputs kept | hidden | FR-006 |
| Pin in progress | pin request pending | that row's toggle disabled; rows otherwise unchanged | unchanged | FR-012 |
| Pin failed | pin request failed | message; row unchanged; toggle re-enabled | unchanged | FR-013 |

**Plain-list empty state** (no notes exist yet) is `BLOCKED` on `GAP-WEB-002` (FR-005).

**Permission-denied: `BLOCKED`.**
- Whether `/notes` is permission-gated depends on `GAP-WEB-001`.
- How "not allowed" versus "does not exist" is shown depends on `GAP-WEB-013`.
- Unblocked by: the backend decisions on `Q-BE-002` and `Q-BE-003`.

**Partial-failure: not applicable.** This module has no multi-item operation. Pin acts on one note.
<!-- /SECTION: web-app.search.states -->
<!-- SECTION: web-app.search.api-integration | status: DERIVED -->
## API integration

Operations are cited by the IDs supplied in `backend-api.md` (pin `sha256:9550f0d579d8`). Shapes, field types and error bodies are defined there and are not restated.

| UI action | Operation | Statuses the UI handles |
|---|---|---|
| Submit keyword and/or tags | `API-SEARCH-580505` | 200 → populated or empty by item count. 400 → form-level error (field-detail shape unknown, `GAP-WEB-004`); the guard in FR-010 should prevent it. 503 → error state with retry, retry-after unknown (`GAP-WEB-004`). Other status or network failure → error state (`ASM-WEB-013`). |
| Pin or unpin a row | `API-NOTES-847551` | 200 → row updates (FR-012). 400, 404 and other failures → FR-013 (`GAP-WEB-124`). |
| Open a row | none (navigation to `/notes/:id`, `notes` module) | n/a |
| Plain list | none in the contract | `GAP-WEB-002` |
| Tag list | none in the contract | `GAP-WEB-003` |

**Parameters this module sends.** The keyword, the tags, `page` and `page_size` (`ASM-WEB-120`). It never sends `include_archived` (`ASM-WEB-118`) and never sends a sort parameter (`INV-WEB-003`).

**Session handling:** `BLOCKED` (`GAP-WEB-001`). No 401 or 403 behaviour is asserted.
<!-- /SECTION: web-app.search.api-integration -->
<!-- SECTION: web-app.search.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule (client) | Also enforced server-side? | Source |
|---|---|---|
| The keyword is trimmed. A whitespace-only keyword counts as empty. | Partly: search with neither keyword nor tag is rejected (400) | `BR-007`, `INV-WEB-004` |
| No request is made when the keyword is empty and no tag is selected. | Yes (same rule) | FR-010 |
| Tag entries are trimmed, lowercased, empty entries dropped, duplicates removed. | Unknown: tag rules are unstated (`GAP-WEB-125`) | `BR-005`; `ASM-WEB-117` |
| The sort selection is one of three fixed values and never sent to the API. | n/a | `BR-020` |
| `include_archived` is never sent. | n/a | `ASM-WEB-118` |
| `page` is 1 and `page_size` is 100 (the maximum). | Yes: larger or invalid page sizes return 400 | `NFR-003`; `API-SEARCH-580505`; `ASM-WEB-120` |

**Not validated on the client:** tag character set, length and maximum count. No input states them (`GAP-WEB-125`). Server-reported invalid input is shown as a form-level error until the 400 detail shape is known (`GAP-WEB-004`).
<!-- /SECTION: web-app.search.validation -->
<!-- SECTION: web-app.search.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

The conformance level is not stated (`GAP-WEB-008`, `web-app.accessibility` is `BLOCKED`). Every behaviour below is a proposal (`ASM-WEB-126`).

- **Keyboard.**
  - Tab order runs keyword field, tag entry, submit control, sort control, then result rows, each with its pin toggle.
  - Enter in the keyword or tag field submits.
  - The sort control and pin toggles are operable with the keyboard alone.
- **Landmarks and labels.** The form is exposed as a search landmark. Each field and the sort control has a programmatic label.
- **Pin state.**
  - The pin toggle exposes its pressed state.
  - The pin icon has a text alternative stating "pinned", so state does not depend on icon appearance alone.
- **Live regions.**
  - The result count is a polite live region, so changes after a search, pin or sort are announced.
  - Error messages are announced as alerts.
- **Focus.**
  - After submit, focus stays on the submit control while its Loading state shows.
  - After a pin success the row moves, so focus remains on that row's toggle in its new position.
  - On error, focus does not jump. The alert announces it and the retry control is reachable by Tab.
- **Form errors.** A field-level error is associated with its field. A form-level error is announced as an alert. Error state is conveyed in text as well as by `UI/border/error`.
<!-- /SECTION: web-app.search.accessibility -->
<!-- SECTION: web-app.search.i18n | status: BLOCKED -->
## Internationalisation

**Blocked.** No input states supported locales, copy keys or message text.

The stories require these message slots (text not supplied):
- the empty-state message (US-006-01);
- the empty-results message (US-006-04);
- the user-facing list error (US-006-01);
- the pin-failure message (FR-013);
- the truncation notice (FR-015).

Unblocked by: a locale list and copy for each slot.
<!-- /SECTION: web-app.search.i18n -->
<!-- SECTION: web-app.search.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma is `not-connected` and no frame IDs were supplied, so nothing here is verified against Figma.

| Reference | Value | Verification |
|---|---|---|
| Screen | `SearchScreen` (design label M2) | name from `design-brief.md`; unverified |
| Frame ID | none supplied | unverified |
| Variants | Default, Empty, Error | listed in `screens-inventory.md`; mapping unverified (`ASM-WEB-125`) |
| Components listed | `Empty/State`, `Input/Text` | `screens-inventory.md`; `Button/Primary` is used for submit but not listed for this screen (`ASM-WEB-123`) |
| Tokens | names listed in `web-app.search.components` | from `tokens-index.md`; values unseen; bindings are proposals |
| Story mapping | `US-006-04` → `SearchScreen`; `US-006-01` maps to no screen | `stories-map.md`; see `GAP-WEB-010` |

**Not in the design** (`GAP-WEB-005`): the sort control, pin icon, pin toggle, note row, result count, a message component, and Loading and populated variants.
<!-- /SECTION: web-app.search.design-refs -->
<!-- SECTION: web-app.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are planned. Names are proposals.

**Component**
- `search.sort.default-newest-first`
- `search.sort.client-side-no-request`
- `search.rows.pinned-first-under-every-sort`
- `search.count.equals-rendered-notes`
- `search.results.empty-not-error`
- `search.error.not-blank-and-retryable`
- `search.guard.no-call-without-keyword-or-tag`
- `search.lifecycle.no-double-submit-latest-wins`
- `search.pagination.truncation-notice`
- `search.pin.failure-leaves-row-unchanged`

**E2E** (against a contract-conformant stub, since the backend's session model is open)
- `search.keyword.submit-renders-matches-keeps-sort`
- `search.tags.apply-filters-request-and-render`
- A combined keyword and tag search.
- `search.pin.toggle-moves-row-on-success`
- A service-unavailable response showing the error state, then retry succeeding.
- `search.list.loads-active-notes` and `search.list.empty-state` are blocked until `GAP-WEB-002` closes.

**Contract**
- Every `API-SEARCH-580505` request omits a sort parameter and `include_archived`.
- Every such request carries a keyword or at least one tag.
- `page_size` never exceeds 100.
- The `API-NOTES-847551` pin request sends only the pinned flag.
- The responses handled match the pinned contract (`sha256:9550f0d579d8`).

**Accessibility**
- An automated scan of each state.
- A keyboard-only run of the full flow.
- Live-region announcements of the count and of errors.
- Focus remains on the toggle after a pin moves its row.
- The target standard is open (`GAP-WEB-008`).
<!-- /SECTION: web-app.search.test-plan -->
<!-- SECTION: web-app.search.gaps | status: DERIVED -->
## Gaps, contradictions, assumptions and questions

**Project-level items this module depends on** (defined in `web-app.overview`, not re-defined here): `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003`, `GAP-WEB-004`, `GAP-WEB-005`, `GAP-WEB-008`, `GAP-WEB-010`, `GAP-WEB-012`, `GAP-WEB-013`, `CON-WEB-001`.

**Contradiction checks.** Four directions checked. No new `CON-` was minted.
- Story ↔ design: no screen for `US-006-01`, and no sort control or pin icon (`GAP-WEB-010`, `GAP-WEB-005`).
- Story ↔ requirement: `US-006-01` says "GET /notes" while `BR-007` requires a keyword or tag (`GAP-WEB-002`).
- Requirement ↔ requirement: the count rule (`BR-019`) versus paginated results (`NFR-003`) is `GAP-WEB-012`.
- Design ↔ design: none found.

**Gaps raised here**

| ID | Missing | Degrades | Done instead | Closes with |
|---|---|---|---|---|
| `GAP-WEB-123` | A pagination control, and a story or design for multi-page results. `US-006-04` says the count equals matches, while `BR-019` says it equals notes rendered. | FR-004, FR-015 | Request `page` 1 with the maximum `page_size`, and show a truncation notice (`ASM-WEB-120`). | A pagination story or design, and a ruling on `GAP-WEB-012`. |
| `GAP-WEB-124` | Pin failure behaviour. No story states it, and the contract lists 400 and 404 for the edit operation. | FR-013 | Row unchanged and a message (`ASM-WEB-124`). | A story or decision on pin errors. |
| `GAP-WEB-125` | Tag rules (case, character set, length, maximum count) and tag-input validation. | `web-app.search.validation` | Trim, lowercase and de-duplicate only (`ASM-WEB-117`). | The backend tag-rules decision (backend `GAP-BE-003`). |
| `GAP-WEB-126` | Design frames for the idle and Loading states, and a populated variant. | `web-app.search.states`, `web-app.search.design-refs` | Mapping in `ASM-WEB-125`. | Design frames for each. |

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-117` | Tags are chosen by typing free text in `Input/Text`, trimmed, lowercased (`BR-005`) and de-duplicated. Selected tags show as removable chips. | `Q-WEB-105` answered; tag rules. |
| `ASM-WEB-118` | The module never sends `include_archived`, so archived notes never appear here. The archived view belongs to `archive`. | A decision on archived notes in search. |
| `ASM-WEB-119` | Until `GAP-WEB-002` closes, `/notes` opens in an idle state and builds no plain-list load. Submitting with no keyword and no tag stays idle with no request. | The list operation; `Q-WEB-107`. |
| `ASM-WEB-120` | The module requests `page` 1 with `page_size` 100 (the maximum, `NFR-003`) and renders that page. If matches exceed it, a notice says not all are shown. | `GAP-WEB-123` closed. |
| `ASM-WEB-121` | A search runs only on submit, as the stories say. Editing fields does not fetch. The latest submission wins and responses from superseded requests are discarded. | A trigger decision. |
| `ASM-WEB-122` | Sorting applies within the pinned group and within the unpinned group. Newest and Oldest first sort on `created_at`. Title A-Z compares titles case-insensitively. No input names the key. | A ruling on the sort key. |
| `ASM-WEB-123` | The note row, sort control, pin icon, pin toggle, count, tag chips and message region have no design component and use plain elements. All token bindings are proposals from token names, with values unseen. `Button/Primary` serves as the submit control. | Design frames or Figma verification. |
| `ASM-WEB-124` | A failed pin leaves the row unchanged and shows a message. The edit operation's 404 reads as "note not found". Other failures read as retryable. The toggle is disabled while the request is pending. | `GAP-WEB-124` closed. |
| `ASM-WEB-125` | Variant mapping: Default is the initial and populated page, Empty is zero results, Error is a failure, and Loading is shown through `Button/Primary` Loading. The count is shown only when populated or empty, so it never shows a false 0. | Design frames (`GAP-WEB-126`). |
| `ASM-WEB-126` | The accessibility behaviours in this module are proposals, because no standard is stated. | A stated standard and level (`GAP-WEB-008`). |
| `ASM-WEB-127` | Search text, tags and sort are client state and are not persisted in the URL or across reloads. | `Q-WEB-106` answered. |

**Questions** (unattended run: degradable defaults are adopted, none blocking)

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-105` | degradable | How does the user choose tags to filter by? | Typed free text with removable chips (`ASM-WEB-117`). |
| `Q-WEB-106` | degradable | Should the search text, tags and sort persist in the URL or across reloads? | No (`ASM-WEB-127`). |
| `Q-WEB-107` | degradable | When keyword and tags are cleared and the user submits, should the plain list show? | Idle state until a list operation exists (`ASM-WEB-119`). |
<!-- /SECTION: web-app.search.gaps -->
