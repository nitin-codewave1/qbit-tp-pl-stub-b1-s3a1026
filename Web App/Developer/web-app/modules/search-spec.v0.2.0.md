---
id: agent-developer/web_app/web-app/modules/search-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/search-SPEC.md
depends_on: [web_app:US-006-01, web_app:US-006-04]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/search-SPEC.md
---

<!-- SECTION: web-app.search.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: search
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-002, GAP-WEB-003, GAP-WEB-004]
contract_pin: "sha256:ceedbd350666"
scope: [/notes]
```

`scope` lists the one route this module owns (`web-app.routing`; the path is a proposal, `ASM-WEB-001`). `blocked_by` names the project-level items that block the plain-list load, the tag source, the protected-route rule and the missing design frames.
<!-- /SECTION: web-app.search.frontmatter -->
<!-- SECTION: web-app.search.purpose | status: DERIVED -->
## Purpose

**Responsible for** (`web-app.module-map`, row `search`):

- `SearchScreen`, which hosts both the notes list and the search results (`ASM-WEB-003`).
- Stories US-006-01 and US-006-04, and the pin toggle of US-006-06.
- The client-side sort control, the live result count, the keyword and tag filter, and the pager.
- Row rendering, with pinned notes first and a pin icon.
- The list, search and pin error states.

**Not responsible for:**

- Create and edit. These belong to `notes` (`NoteEditorScreen`).
- Note detail, archive and restore. These belong to `notes` (`NoteDetailScreen`).
- `ArchiveScreen` and the archived-only list. These belong to `archive`.
- `TagManagementScreen`, tag add and remove, and the distinct-tag list. No module spec exists for tags (`GAP-WEB-003`).
- Login, session handling and the protected-route rule (`login`; `GAP-WEB-001`).
- Any backend behaviour. Needs from the backend are stated in `GAP-WEB-001`, `GAP-WEB-002` and `GAP-WEB-003`.

**Dependencies.**

- `search → notes`: opening a result navigates to `/notes/:id`.
- `notes → search`: every successful note mutation invalidates cached search results (`web-app.state-management`).
<!-- /SECTION: web-app.search.purpose -->
<!-- SECTION: web-app.search.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Project invariants `INV-WEB-001` to `INV-WEB-005` apply and are not restated. Two further invariants are minted here:

- `INV-WEB-101`: Running a search, applying a tag filter, or changing page never changes the sort selection (US-006-04, "without changing the sort selection").
- `INV-WEB-102`: A search request is never sent with neither a keyword nor a tag (`requirements.md` BR-007).

**WEB-SEARCH-FR-001: Load the notes list.** `BLOCKED` by `GAP-WEB-002`.

- Given notes exist, when the notes list page loads, then the web app loads the active notes and renders each note's title and status.
- Until the gap closes, `/notes` offers search only and builds no plain-list load (`ASM-WEB-108`).
- Traces to: US-006-01.
- Verification: `search.list.loads-active-notes` (planned).
- API: none. The contract has no operation to list active notes.

**WEB-SEARCH-FR-002: Row rendering and pinned-first.**

- Given any result set, when it is rendered, then each row shows the note's title and status.
- Pinned notes render above unpinned notes with a pin icon, under every sort and in filtered and search results.
- Traces to: US-006-01; `requirements.md` BR-021; `INV-WEB-001`.
- Verification: `search.rows.pinned-first-under-every-sort` (planned).
- API: `API-SEARCH-580505`.

**WEB-SEARCH-FR-003: Sort control.**

- Given a list or search result is shown, when the page renders, then a sort control above the list offers "Newest first", "Oldest first" and "Title A-Z", with "Newest first" selected by default.
- Changing the sort reorders the loaded rows in the client and sends no request (`INV-WEB-003`).
- The sort keys follow `ASM-WEB-008`.
- Traces to: US-006-01, US-006-04; `requirements.md` BR-020.
- Verification: `search.sort.client-side-no-request` (planned).
- API: none.

**WEB-SEARCH-FR-004: Live result count.**

- Given a result set is rendered, then a live count above the list equals the number of notes currently rendered.
- The count updates on every search, filter, page change and pin change.
- Traces to: US-006-01, US-006-04; `requirements.md` BR-019; `INV-WEB-002`.
- Verification: `search.count.equals-rendered-rows` (planned).
- API: none. The response's `total` is not the count (`CON-WEB-001`).

**WEB-SEARCH-FR-005: Keyword search.**

- Given the user enters a keyword, when they submit, then the web app calls the search operation and renders the matches in the currently selected sort order.
- The sort selection does not change (`INV-WEB-101`), and the count updates to the number of matches.
- Traces to: US-006-04.
- Verification: `search.keyword.renders-in-selected-sort` (planned).
- API: `API-SEARCH-580505`.

**WEB-SEARCH-FR-006: Tag filter.**

- Given the user selects a tag filter, when they apply it, then the web app includes the tag filter in the search request and renders only the matching notes in the selected sort order.
- The count updates to match.
- With a keyword also present, both are sent in one request.
- How the user chooses a tag is `GAP-WEB-102`, and the default is `ASM-WEB-101`.
- Traces to: US-006-04.
- Verification: `search.tags.filter-in-request-and-count` (planned).
- API: `API-SEARCH-580505`.

**WEB-SEARCH-FR-007: Search guard.**

- Given the keyword is empty or whitespace-only and no tag is selected, when the user submits, then no request is sent (`INV-WEB-102`).
- An inline message asks for a keyword or a tag, and the current results are kept (`Q-WEB-102`).
- Traces to: US-006-04; `requirements.md` BR-007.
- Verification: `search.guard.no-request-without-query-or-tag` (planned).
- API: `API-SEARCH-580505`, not called.

**WEB-SEARCH-FR-008: Paging.**

- Given a result set spans more than one page, when the user moves between pages, then the web app requests that page with the same filters.
- The page size is the server default, so the web app sends no `page_size` (`requirements.md` NFR-003).
- Changing the keyword or tags resets to page 1 (`ASM-WEB-105`).
- The sort applies to the loaded page only (`CON-WEB-001`, default reading).
- Traces to: US-006-04 (search results are paginated, `API-SEARCH-580505`).
- Verification: `search.paging.requests-page-with-same-filters` (planned).
- API: `API-SEARCH-580505`.

**WEB-SEARCH-FR-009: Pin toggle.**

- Given a note row, when the user clicks its pin toggle, then the web app sets the note's pinned flag.
- On success the row moves to the top with a pin icon, or returns to its sort position when unpinned.
- Cached search results are then invalidated (`web-app.state-management`, policy item 2).
- On failure the row is unchanged (`ASM-WEB-106`; `GAP-WEB-104`).
- Traces to: US-006-06 (pin portion, `web-app.module-map`); `requirements.md` BR-021.
- Verification: `search.pin.toggle-moves-row-and-invalidates-cache` (planned).
- API: `API-NOTES-847551`.

**WEB-SEARCH-FR-010: Open a note.** `DERIVED`.

- Given a row, when the user opens it, then the web app navigates to `/notes/:id`.
- The target screen is owned by `notes`.
- Traces to: US-006-06 ("an active note is open"; `web-app.module-map` dependency `search → notes`).
- Verification: `search.rows.open-navigates-to-detail` (planned).
- API: none.

**WEB-SEARCH-FR-011: Failure states.**

- Given the search operation returns an error, or the network fails, when results are expected, then a user-facing error state replaces the list. It is never a blank screen.
- The state allows retry and keeps the user's inputs.
- A 503 is handled the same way (`web-app.state-management`, policy item 5).
- Traces to: US-006-01 (error criterion).
- Verification: `search.error.shows-state-and-keeps-inputs` (planned).
- API: `API-SEARCH-580505`.

**Story coverage (this module).**

| Story | Covered by | Status |
|---|---|---|
| US-006-01 | FR-001 to FR-004, FR-011 | Partly. The list load is `BLOCKED` (`GAP-WEB-002`). |
| US-006-04 | FR-003 to FR-008 | Covered, with the tag-source gap (`GAP-WEB-102`). |
<!-- /SECTION: web-app.search.requirements -->
<!-- SECTION: web-app.search.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes` (proposed, `ASM-WEB-001`) | `SearchScreen` | app | `BLOCKED`: the protected-route rule is undecided (`Q-BE-003`, `GAP-WEB-001`). The only role that can sign in to this surface is Team member (`web-app.authorization-ui`). |

- An unauthenticated visit redirects to `/login` if the app layout is protected (`web-app.routing`).
- The search text, tag filters, sort selection and page are client state, not URL state (`web-app.state-management`).
- No other route is owned by this module.
<!-- /SECTION: web-app.search.routes -->
<!-- SECTION: web-app.search.components | status: DRAFT-UNVERIFIED -->
## Components

Component names and token names come from `design-brief.md` (`components-inventory.md`, `tokens-index.md`). Token bindings are proposals derived from token names. Values are unseen and Figma is not connected (`ASM-WEB-104`). No visual value is stated here.

| Element | Component | Variants / states | Token bindings (proposed) |
|---|---|---|---|
| Keyword box | `Input/Text` (design lists it on `SearchScreen`) | Default, Focused, Filled, Error, Disabled | `UI/surface/input`; `UI/surface/input-focus` (Focused); `UI/border/input`; `UI/border/input-focus`; `UI/border/error` and `UI/text/error` (Error); `UI/text/primary`; `UI/text/placeholder`; `UI/surface/disabled`, `UI/border/disabled`, `UI/text/disabled` (Disabled) |
| Tag filter entry | `Input/Text` (`GAP-WEB-102`) | as above | as above |
| Submit / apply | `Button/Primary` | Default, Pressed, Loading, Disabled. The inventory lists 12 variants without naming them, so the variant is unspecified. | `UI/surface/button/primary`; `UI/surface/button/primary-hover`; `UI/text/on-primary`; `UI/surface/disabled` and `UI/text/disabled` (Disabled) |
| Note row | `Card/Item` | Default | `UI/surface/card`; `UI/border/default`; `UI/text/primary` (title); `UI/text/secondary` (status) |
| Pin icon | none | pinned, unpinned | `UI/icon/active` (pinned); `UI/icon/default` (unpinned) |
| Empty message | `Empty/State` | Default | `UI/text/secondary`; `UI/surface/default` |
| Error message | none | n/a | `UI/surface/toast/error`; `UI/text/error` |
| Sort control | none | n/a | none bound |
| Result count | none | n/a | none bound |
| Pager | none | n/a | none bound |
| Pin toggle | none | n/a | none bound |
| Loading indicator | none | n/a | none bound |

- The design has no component for the sort control, count, pager, pin toggle, loading indicator or message (`GAP-WEB-004`, `GAP-WEB-008`).
- Layout of these elements is not specified. A component inventory is not invented for them.
<!-- /SECTION: web-app.search.components -->
<!-- SECTION: web-app.search.states | status: DRAFT-UNVERIFIED -->
## States

Route `/notes`, `SearchScreen`. Design variants are Default, Empty and Error (`screens-inventory.md`). There is no Loading or Filled variant (`GAP-WEB-004`).

| State | Behaviour | Source |
|---|---|---|
| Idle (no query yet) | The keyword box and tag filter are shown with a prompt. No request is made. The count is not shown. | `ASM-WEB-108`; `GAP-WEB-002` |
| Loading | The previous results are replaced by a loading indicator, and the submit control shows its Loading state. No design for the indicator exists. | `components-inventory.md`; `GAP-WEB-004` |
| Empty: no search matches | An empty-results message and a count of 0, not an error. | US-006-04 |
| Empty: no notes exist | An empty-state message and a count of 0, not an error. `BLOCKED` with the list load. | US-006-01; `GAP-WEB-002` |
| Populated | Rows are shown in the selected sort, pinned first, with the live count and the pager when more than one page exists. | US-006-01, US-006-04 |
| Error | A user-facing error state with retry, keeping the user's inputs. A 503 shows the same state, and the retry delay is unstated (`GAP-WEB-005`). | US-006-01; `web-app.state-management` |
| Pin in progress | The row's toggle is disabled until the request settles. | `ASM-WEB-106` |
| Pin failed | The row is unchanged and a message is shown (`GAP-WEB-104`). | `ASM-WEB-106` |

**Permission-denied.** The route is permission-gated only if the protected-route rule requires a session (`GAP-WEB-001`). No ownership rule or 401/403 for note operations is defined (`GAP-WEB-009`). The state is therefore not specified. This is not an omission.

**Partial-failure.** Not applicable. This module has no multi-item operation: search is one request and pin is single-note.
<!-- /SECTION: web-app.search.states -->
<!-- SECTION: web-app.search.api-integration | status: DERIVED -->
## API integration

Reference only. Request and response shapes are not restated here (core rule 6).

| Operation | Pinned ID | Used by | Statuses the web app handles |
|---|---|---|---|
| Search notes | `API-SEARCH-580505` | FR-002, FR-004 to FR-008, FR-011 | 200; 400; 503; 500 |
| Edit note (pin flag) | `API-NOTES-847551` | FR-009 | 200; 404; 409; 400; 415 |

The web app branches on status, as in `web-app.auth-flows`.

- **Search 200.** Render the items, with the screen's own count (`INV-WEB-002`).
- **Search 400.** Unexpected, because the client guards its inputs (`web-app.search.validation`). Show the Error state. Field-detail shape is open (`GAP-WEB-007`).
- **Search 503 and 500.** Error state with retry.
- **Pin 200.** Merge the response into the cached note and invalidate search results (`web-app.state-management`).
- **Pin 404.** The note no longer exists. Show a "note not found" message and leave the row unchanged (`ASM-WEB-106`).
- **Pin 409.** Show a failure message. The row is unchanged and the user may retry (`ASM-WEB-106`).
- **Pin 400 and 415.** Unexpected. Show a failure message.

**Operations the module needs that the contract does not have:**

- List active notes (FR-001): `GAP-WEB-002`.
- List archived-only notes: `GAP-WEB-002`.
- List distinct tags, for tag-filter options: `GAP-WEB-003`.
<!-- /SECTION: web-app.search.api-integration -->
<!-- SECTION: web-app.search.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side |
|---|---|---|
| Keyword is trimmed, and whitespace-only counts as empty | yes (`web-app.state-management`) | yes. An empty `q` is refused with 400 (`API-SEARCH-580505`). |
| At least one of keyword or tag is present before a request is sent (`INV-WEB-102`) | yes | yes, per the contract's description of `API-SEARCH-580505` (`CON-BE-003`, `ASM-BE-009`) |
| Tag entries are trimmed and lowercased, empty entries dropped, duplicates collapsed | yes (`ASM-WEB-101`) | unstated. Tag rules are open (`GAP-BE-010`). |
| Tag length, characters and count | no client rule. None is supplied. | unknown (`GAP-BE-010`) |
| Page value comes from the pager only, never typed | yes | yes. Invalid values are refused with 400 (`API-SEARCH-580505`). |
| Sort is one of the three offered values | yes | not applicable (`INV-WEB-003`) |
| `include_archived` is not sent (`ASM-WEB-102`) | yes | the server default applies |

A 400 is shown as an Error state. A field-level display is not specified (`GAP-WEB-007`).
<!-- /SECTION: web-app.search.validation -->
<!-- SECTION: web-app.search.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

**Target standard: unstated.** No input names a standard, level or verification method (`GAP-WEB-103`). The behaviours below are module requirements derived from the controls, not claims of conformance.

**Keyboard path.** The tab order is:

1. keyword box;
2. tag filter;
3. submit;
4. sort control;
5. rows in order, each with its open action and then its pin toggle;
6. pager.

Enter in the keyword box submits.

**Focus management.**

- After a search, focus stays on the control that was used. It is not moved to the results.
- After a pin toggle, focus follows that note's toggle to the row's new position.
- On an Error state, focus stays on the retry control after it is activated.

**Live regions** (`ASM-WEB-107`):

- The result count is in a polite live region, announced when it changes.
- Error and pin-failure messages are announced as alerts.
- The idle prompt is not announced.

**Controls.**

- The pin toggle exposes a pressed state and an accessible name that includes the note title. Its pinned state does not rely on the icon alone.
- The sort control is operable by keyboard and exposes its selected value.
- Row open actions are focusable links.

**Form errors.** The inline message for a missing keyword and tag (FR-007) is programmatically associated with the keyword box, which takes its Error state (`components-inventory.md`).
<!-- /SECTION: web-app.search.accessibility -->
<!-- SECTION: web-app.search.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input lists locales or supplies copy keys or text for this module (`GAP-WEB-105`).

The only wording supplied is the sort labels "Newest first", "Oldest first" and "Title A-Z" (US-006-01). Copy is needed for:

- the idle prompt;
- the empty-results message and the empty-state message;
- the missing-query message;
- the error and retry text;
- the pin failure and "note not found" messages;
- the status labels;
- the pager text;
- the count text.

Unblocked by: the locale list and a copy deck for the above.
<!-- /SECTION: web-app.search.i18n -->
<!-- SECTION: web-app.search.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode` is `FULL-FROM-BRIEF`. Figma MCP is not connected, so every reference below is an unverified claim.

| Reference | Value | Verification |
|---|---|---|
| Screen | `SearchScreen`, module M2 | Unverified. Cited from `screens-inventory.md`. |
| Frame ID | none supplied (`GAP-WEB-106`) | none |
| Variants | Default, Empty, Error | Unverified. |
| Components on screen | `Empty/State`, `Input/Text` | Unverified. |
| Story mapping | US-006-04 → `SearchScreen`; US-006-01 → no screen (`stories-map.md`) | Unverified. `ASM-WEB-003` places the list on `SearchScreen`. |

**Variant mapping** (proposed):

- Default is idle or populated.
- Empty is no matches.
- Error is failure.

Tokens used are named in `web-app.search.components`. All are unverified.
<!-- /SECTION: web-app.search.design-refs -->
<!-- SECTION: web-app.search.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All test names are planned.

**Component.**

- `search.rows.pinned-first-under-every-sort` (FR-002)
- `search.sort.client-side-no-request` (FR-003)
- `search.count.equals-rendered-rows` (FR-004)
- `search.guard.no-request-without-query-or-tag` (FR-007)
- `search.pin.toggle-moves-row-and-invalidates-cache` (FR-009)
- `search.error.shows-state-and-keeps-inputs` (FR-011)

**E2E.**

- `search.keyword.renders-in-selected-sort` (FR-005)
- `search.tags.filter-in-request-and-count` (FR-006)
- `search.paging.requests-page-with-same-filters` (FR-008)
- `search.rows.open-navigates-to-detail` (FR-010)
- `search.list.loads-active-notes` (FR-001), held until `GAP-WEB-002` closes

**Contract.**

- `contract.API-SEARCH-580505.request-and-response-fields-consumed`
- `contract.API-SEARCH-580505.status-400-503-500-handled`
- `contract.API-NOTES-847551.pinned-flag-roundtrip`

**Accessibility.**

- `a11y.search.keyboard-path`
- `a11y.search.count-live-region`
- `a11y.search.pin-focus-follows-row`

Pass criteria cannot be set against a standard until `GAP-WEB-103` closes.
<!-- /SECTION: web-app.search.test-plan -->
<!-- SECTION: web-app.search.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Checks performed** (four directions):

- Story ↔ design: US-006-01 has no mapped screen (`stories-map.md`). Handled by `ASM-WEB-003`, `GAP-WEB-004`.
- Story ↔ requirement: one new contradiction, `CON-WEB-101`.
- Requirement ↔ requirement: `CON-WEB-001` (project) is inherited, not re-minted.
- Design ↔ design: nothing found. The brief carries no layout detail.

**Inherited, cited and not re-minted:**

- Gaps: `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003`, `GAP-WEB-004`, `GAP-WEB-005`, `GAP-WEB-007`, `GAP-WEB-008`, `GAP-WEB-009`.
- Contradiction: `CON-WEB-001`.
- Assumptions: `ASM-WEB-001`, `ASM-WEB-003`, `ASM-WEB-008`.
- Backend: `CON-BE-003`, `ASM-BE-009`, `GAP-BE-010`.

| ID | Class / severity | Statement | Closing artefact |
|---|---|---|---|
| `GAP-WEB-102` | non-critical | How the user chooses tags to filter by is not designed. The contract has no list-tags operation. `Q-WEB-101` default applies. | A design frame for the tag filter and the distinct-tags operation (`GAP-WEB-003`). |
| `GAP-WEB-103` | non-critical | The accessibility standard, level and verification method are not stated. | An NFR or platform statement. |
| `GAP-WEB-104` | non-critical | US-006-06 gives no failure criteria for the pin toggle. Behaviour on 404, 409 and network failure is assumed (`ASM-WEB-106`). | Acceptance criteria for pin failures. |
| `GAP-WEB-105` | non-critical | No locales or copy are supplied for this module (`web-app.search.i18n`). | The locale list and a copy deck. |
| `GAP-WEB-106` | non-critical | No frame IDs are supplied and Figma is not connected. | Frame IDs or a connected Figma. |
| `CON-WEB-101` | non-critical | US-002-03 says matches are returned ranked by relevance. US-006-04 and `requirements.md` BR-020 say results render in the user-selected client-side sort. Reading adopted: US-006-04 and BR-020, so the selected sort overrides relevance order. Picking the other side would mean relevance order with no effective sort control. | A decision on whether relevance order is ever shown. |
| `Q-WEB-101` | degradable | How does the user choose tags to filter by? Default: typed free-text entries (`ASM-WEB-101`). Not asked (unattended). | A design decision. |
| `Q-WEB-102` | degradable | When the keyword is cleared and no tag is selected and the user submits, is the plain list shown? Default: no request is sent, an inline message is shown and current results are kept (FR-007). Not asked (unattended). | A decision, after `GAP-WEB-002`. |
| `ASM-WEB-101` | assumption | Tag entries are typed free text, trimmed and lowercased before sending, because `requirements.md` BR-005 normalises tags to lowercase on write. | Confirmation, or the tag rules (`GAP-BE-010`). |
| `ASM-WEB-102` | assumption | The module does not send `include_archived`, so archived notes never appear in search. The archived view belongs to `archive`. | A decision on archived notes in search. |
| `ASM-WEB-104` | assumption | `Card/Item` is reused for the note row and `Button/Primary` for the submit control. Token bindings are proposals from token names, with values unseen. | Design annotations or Figma verification. |
| `ASM-WEB-105` | assumption | A change of keyword or tags resets the page to 1. Changing the sort never fetches. | A decision. |
| `ASM-WEB-106` | assumption | A failed pin leaves the row unchanged and shows a message. 404 reads as "note not found" and 409 as a retryable failure. The toggle is disabled while the request is in progress. | `GAP-WEB-104` closed. |
| `ASM-WEB-107` | assumption | The "live" result count is a polite live region, and error messages are alerts. | An accessibility decision (`GAP-WEB-103`). |
| `ASM-WEB-108` | assumption | Until `GAP-WEB-002` closes, `/notes` opens in an idle state and builds no plain-list load. | The list operation. |
<!-- /SECTION: web-app.search.gaps -->
