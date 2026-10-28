---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.6.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-002-01, web_app:US-002-02, web_app:US-002-03, web_app:US-002-04, web_app:US-002-05, web_app:US-002-06, web_app:US-003-01, web_app:US-003-02, web_app:US-003-03, web_app:US-003-04, web_app:US-003-05, web_app:US-003-06, web_app:US-004-01, web_app:US-004-02, web_app:US-004-03, web_app:US-004-04, web_app:US-004-05, web_app:US-004-06, web_app:US-005-01, web_app:US-005-02, web_app:US-005-03, web_app:US-005-04, web_app:US-005-05, web_app:US-005-06, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03, web_app:US-006-04, web_app:US-006-05, web_app:US-006-06]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DERIVED -->
## Overview

**Purpose.** The web app is the browser client for `notes_api`. A Team member can:

- list notes;
- create and edit notes;
- search by keyword and tag filter;
- sign in with an emailed one-time code;
- archive, restore and pin or unpin notes.

Sources: `stories.md` EP-006 (US-006-01 to US-006-06) and `requirements.md` BR-019 to BR-023. The stack is React 18 + Vite on AWS (`platform.md`, `feedback.md` preflight answer).

**Run basis.**
- Contract: `sha256:8e424bb2b40a` (`backend-api.md`). Its operations are cited by their supplied IDs, which carry no `@v` suffix in the contract.
- Design: `spec_mode` is `FULL-FROM-BRIEF`. `design-brief.md` is present and Figma MCP is not connected (`RUN-CONTEXT`), so every screen and token name below is an unverified claim.
- Inbound handoffs: `RUN-CONTEXT.upstream.handoffs` is empty, so no inbound handoff files were read.

**Primary actor.** Team member, the actor of every EP-006 story. No other role is named for the web surface (see `web-app.authorization-ui`).

**Upstream state that shapes this document.** The backend left three decisions open: `Q-BE-001`, `Q-BE-002` and `Q-BE-003`, and its `backend.auth-model` is `BLOCKED`.
- What a successful code verification issues is undecided, as are session lifetime, refresh, and whether notes endpoints require it (`GAP-WEB-001`).
- The contract has no operation to list notes, list archived notes, or list tags (`GAP-WEB-002`, `GAP-WEB-003`).
- `backend.lane-needs` records the web app's earlier requests on these points as not answerable by the backend. Sections that depend on them are marked `BLOCKED` below.

**Explicitly out of scope.**
- Attachment, file-upload and real-time UI. `BR-016` and `BR-017` exclude these capabilities from the API, and no EP-006 story asks the web app for them.
- Native-mobile behaviour (`BR-018`).
- Any backend behaviour. Needs from the backend are stated in `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003` and `GAP-WEB-009`.
- The Residency Report and Scope Documentation screens. The design brief lists them, but no web story requires them (`GAP-WEB-010`, `Q-WEB-001`, `ASM-WEB-005`).
- Tag add/remove controls (`GAP-WEB-015`).
<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. Screen names come from `design-brief.md` (`screens-inventory.md`). The paths below are proposals (`ASM-WEB-001`).

| Route id (screen) | Proposed path | Layout | Access rule | Source |
|---|---|---|---|---|
| `LoginScreen` | `/login` | public | public | US-006-05; BR-023 exception: request-code and verify-code need no session token |
| `CodeEntryScreen` | `/login/code` | public | public. Reachable only after an email was submitted; with none held, redirect to `/login` (`ASM-WEB-001`) | US-006-05 |
| `SearchScreen` (notes list and search) | `/notes` | app | see protected-route rule | US-006-01, US-006-04; `ASM-WEB-003` |
| `NoteEditorScreen` (create) | `/notes/new` | app | see protected-route rule | US-006-02 |
| `NoteEditorScreen` (edit) | `/notes/:id/edit` | app | see protected-route rule | US-006-03 |
| `NoteDetailScreen` | `/notes/:id` | app | see protected-route rule | US-006-02 ("navigates to the saved note"), US-006-06 |
| `TagsListScreen` | `/tags` | app | `BLOCKED`: no list-tags operation in the contract (`GAP-WEB-003`) | US-002-05 mapped in `stories-map.md` |
| `ResidencyReportScreen`, `ScopeDocumentationScreen` | none assigned | n/a | not routed until `Q-WEB-001` is answered (`ASM-WEB-005`) | `screens-inventory.md` |

**Layout nesting.**
- A public layout wraps the two login routes.
- An authenticated app layout wraps every `/notes*` and `/tags` route. Its content depends on `GAP-WEB-004`, because the design has no frame for the notes list.

**Protected-route rule: `BLOCKED`.**
- Whether notes and search endpoints require a session is undecided (`Q-BE-003`, `GAP-WEB-001`).
- `BR-023` states "the existing notes and search endpoints require it", but its enforcement text still contains unfilled template placeholders. It is not usable as a rule.
- Unblocked by: a decision on `Q-BE-003`, or clean `BR-023` text.

**Unauthenticated redirect.**
- If the app layout is protected, the redirect target is `LoginScreen`, the only sign-in entry in the design.
- Whether the user returns to the originally requested route is unspecified (`GAP-WEB-013`).

**Known navigation.**
- `NoteDetailScreen` links to `SearchScreen` (two `ON_CLICK` transitions) and `ScopeDocumentationScreen` links to `SearchScreen` (`screens-inventory.md`).
- Creating a note navigates to the saved note (US-006-02).
- Signing in navigates to the notes list (`ASM-WEB-009`, `Q-WEB-002`).
- The design reports 4 flows but does not enumerate them (`design-brief.md` counts), so no further navigation is asserted (`GAP-WEB-013`).
<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DRAFT-UNVERIFIED -->
## Module map

Module labels M1 to M6 and screen titles come from `design-brief.md`. Kebab-case module names are derived from them (`ASM-WEB-002`). Functional requirement IDs (`WEB-<MODULE>-FR-nnn`) are minted in the module specs, not here.

| Module | Design label | Screens (route ownership) | Web stories | Contract operations |
|---|---|---|---|---|
| `login` | M6 | `LoginScreen`, `CodeEntryScreen` | US-006-05 | `API-AUTH-015365`, `API-AUTH-592570` |
| `editor` | M1 | `NoteEditorScreen` (`/notes/new`, `/notes/:id/edit`) | US-006-02, US-006-03 | `API-NOTES-232390`, `API-NOTES-847551` |
| `search` | M2 | `SearchScreen` (notes list and search), `TagsListScreen` | US-006-01, US-006-04, pin toggle of US-006-06 | `API-SEARCH-580505`, `API-NOTES-847551` (pin). List-all and list-tags are absent (`GAP-WEB-002`, `GAP-WEB-003`). |
| `note-detail` | M3 | `NoteDetailScreen` | archive and restore of US-006-06 | `API-NOTES-045877`, `API-NOTES-810640`, `API-NOTES-917869` |
| `residency-report` | M4 | `ResidencyReportScreen` | none | `API-RESIDENCY-913995` and others exist, but no web requirement uses them. No module spec until `Q-WEB-001`. |
| `scope-docs` | M5 | `ScopeDocumentationScreen` | none | `API-CONTRACTDOCS-304307` exists, but no web requirement uses it. No module spec until `Q-WEB-001`. |

**Dependency direction** (A → B means A depends on B):
- `editor → note-detail` (post-create navigation).
- `note-detail → search` (return link, `screens-inventory.md`).
- `search → note-detail` (opening a result).
- `login` has no module dependencies.
- Every module except `login` depends on the session decision (`GAP-WEB-001`).
- The `note-detail → search → note-detail` loop is navigation only, not a code dependency.
<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No state-management library is named in any input, so none is specified. The boundary and policy below are library-independent.

**Server state** (owned by `notes_api`, never edited locally):
- A note, by id (`API-NOTES-045877`).
- Search results, keyed by the full parameter set `q`, `tags`, `include_archived`, `page` and `limit` (`API-SEARCH-580505`).
- The active note list. This is `BLOCKED` by `GAP-WEB-002` because the contract has no list operation.

**Client state** (owned by the browser, never sent to the API):
- The sort selection: Newest first, Oldest first or Title A-Z. The default is Newest first (US-006-01). The API has no sort parameter (`BR-020`).
- The search text and selected tag filters, until submitted.
- The email held between `LoginScreen` and `CodeEntryScreen`.
- Editor form values.
- Login error and lock presentation state.

**Cache and invalidation policy.**
1. Every note-mutating operation returns the full note: edit, pin, archive, unarchive and tag operations (`API-NOTES-*`). Write the response into the note-by-id entry.
2. After any mutation that can change a note's membership in, or order within, a result set, invalidate all cached search results. This covers pin or unpin (`BR-021`), archive or unarchive (`BR-011`) and title or body edits (searchable fields, `API-SEARCH-580505`).
3. No time-based staleness rule exists in any input. Default: none; data refreshes on mutation and on navigation to a list (`ASM-WEB-004`).
4. A failed mutation never changes cached data. US-006-03 requires "does not apply local changes" on 404.
5. A search response of 503 with `Retry-After` shows the error state and allows retry no earlier than the supplied delay (`API-SEARCH-580505`, `backend.error-model`). The request must not hang.

**Search guard.** The web app does not call search with neither `q` nor a tag (`BR-007`, US-002-03). Whether the web app can show a plain list is `GAP-WEB-002` (`ASM-WEB-010`).

**Invariants.**
- `INV-WEB-001`: Pinned notes render above unpinned notes under every sort, including filtered and search results (`BR-021`).
- `INV-WEB-002`: The live result count equals the number of notes currently rendered (`BR-019`). It is not the API's `total` across pages.
- `INV-WEB-003`: The web app sends no sort parameter to the API (`BR-020`).
- `INV-WEB-004`: Edit and tag controls are never disabled because a note is archived (`BR-010`, US-003-05).

**Open tension.** Client-side sort and pagination conflict (`CON-WEB-001`).
<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

**Login (specifiable).**
1. On `LoginScreen`, the user submits an email address. The web app calls `API-AUTH-015365` (success 202, errors 400 and 415). It does not display a code (the response message never contains it) and moves to `CodeEntryScreen` (US-006-05).
2. On `CodeEntryScreen`, the user enters a 6-digit code (`BR-023`, US-006-05). The web app calls `API-AUTH-592570` (success 200, errors 400 and 415).
3. On `verified: true`, the web app "signs the user in" (US-006-05). The mechanism is `BLOCKED` below.

**Limits the UI reflects** (`BR-022`, US-006-05):
- A code is valid for 10 minutes.
- At most 3 resends are allowed per 10 minutes. The backend reads this as 3 requests including the first (`CON-BE-001`), so the web app shows no remaining-resends counter.
- 5 wrong codes lock the account for 15 minutes.

**Refusal states.** The backend's error-code names are proposals (`ASM-BE-006`), and several refusals share status 415, so the web app branches on the error `code`, not the status (`backend.error-model`).

| State | Web behaviour | Source |
|---|---|---|
| Wrong code | Distinct "wrong code" error | `BR-022` ("matching error for each case") |
| Expired code | Expired-code error with an option to request a new code | US-006-05 |
| Already-used code | Distinct "code already used" error | `BR-022`; copy unspecified |
| Resend limit reached | Resend-limit error; no further resend offered | US-006-05 |
| Account locked | Lockout error stating the account is locked for 15 minutes; code submission disabled | US-006-05 |

Fixed values (`BR-022`) are cited. Copy text for all states is supplied nowhere (`GAP-WEB-016`). The contract defines no remaining-lock-time field and no re-enable rule, so no countdown is specified (`GAP-WEB-009`).

**`BLOCKED`: session, refresh, expiry, logout.**
- `API-AUTH-592570` returns only a `verified` boolean. The credential type, lifetime, transport and any refresh are undecided (`Q-BE-003`, `GAP-WEB-001`).
- The contract has no refresh or logout operation, and no story describes expiry or logout.
- Mid-flow expiry behaviour (a session ending during editing) therefore cannot be written.
- Unblocked by: the answer to `Q-BE-003`, plus any logout or refresh operation in the contract.
<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DRAFT-UNVERIFIED -->
## Authorization UI

**Roles that sign in to this surface.** One: **Team member** (actor of US-006-01 to US-006-06).

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Team member | Notes list and search; New note; Note detail; Edit (`/notes/:id/edit`); Login screens while signed out | View, create, edit, search and filter, pin or unpin, archive, restore (EP-006 stories) |

No input differentiates actions by role or by note. The table assumes uniform capability (`ASM-WEB-006`), because the backend permission matrix is `BLOCKED` (`backend.auth-model`, `Q-BE-001`, `Q-BE-002`).

**Roles excluded, and why.**
- Stories name Developer, compliance auditor, platform operations engineer, operations engineer, API tech lead, QA engineer, product owner and backend developer. They appear only in API, infrastructure and process stories (EP-001 to EP-005) and have no web route. Listing them would speculate about other surfaces (I-1).
- The compliance auditor would become a web role only if `ResidencyReportScreen` is confirmed in scope (`Q-WEB-001`).
- The backend defines no roles at all (`backend.auth-model` is `BLOCKED`), so no backend role is imported.

**Permission-denied state.**
- Whether 401, 403 or a not-found response occurs for notes the user does not own is unknown (`Q-BE-002`, `GAP-WEB-011`).
- The module specs include a permission-denied state only once that is decided.
- The choice between "not allowed" and "does not exist" rendering is a security decision and is not made here.
<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

`spec_mode` is `FULL-FROM-BRIEF` (`RUN-CONTEXT.inputs.figma_mcp`: not-connected). Every name below comes from `design-brief.md` (`tokens-index.md`, `components-inventory.md`) and is unverified against Figma. No frame IDs are supplied anywhere.

**Tokens (71, IDs only; values are not inlined in the brief).**

| Group | Count | Members |
|---|---|---|
| `UI/border/*` | 10 | default, disabled, error, focus, input, input-focus, strong, subtle, success, warning |
| `UI/color/*` | 20 | black, disabled, error, error-subtle, hover-destructive, hover-ghost, hover-primary, hover-secondary, hover-success, info, info-subtle, overlay, primary, primary-hover, primary-subtle, success, success-subtle, warning, warning-subtle, white |
| `UI/icon/*` | 10 | active, default, disabled, error, info, inverse, on-primary, subtle, success, warning |
| `UI/surface/*` | 18 | button/{destructive, ghost, primary, primary-hover, secondary, success}; card, default, disabled, input, input-focus, overlay, secondary, tertiary; toast/{error, info, success, warning} |
| `UI/text/*` | 13 | disabled, error, info, inverse, link, link-hover, on-primary, placeholder, primary, secondary, success, tertiary, warning |

**Components (4).**

| Component | Role | Variants | States |
|---|---|---|---|
| `Button/Primary` | action | 12 | Default, Pressed, Loading, Disabled |
| `Card/Item` | container | 1 | Default |
| `Empty/State` | content | 1 | Default |
| `Input/Text` | entry | 5 | Default, Focused, Filled, Error, Disabled |

**Screen-to-component use** (`screens-inventory.md`): `NoteDetailScreen` uses Button/Primary, Empty/State and Input/Text. `NoteEditorScreen` and `SearchScreen` use Empty/State and Input/Text. `TagsListScreen` uses Card/Item. `LoginScreen`, `CodeEntryScreen` and `ResidencyReportScreen` use Button/Primary.

**`BLOCKED` parts.**
- **Type scale, spacing scale and breakpoints:** no input defines any. No token group in the index covers them (`GAP-WEB-005`).
- **Token values:** the brief lists IDs and file refs (`tokens/ui-*.md`) that were not supplied.
- **Component inventory gap:** the design has no pagination, sort-control, pin-toggle or result-count component (`GAP-WEB-004`, `GAP-WEB-014`). None is invented here.
- **Unblocked by:** the token value files, or a connected Figma MCP.
<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive

**BLOCKED.** the run did not produce this section. Nothing has been written in its place, and no claim about it should be read into this document.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**Blocked.** No input names supported browsers or versions. `platform.md` states only React 18 + Vite on AWS (`GAP-WEB-006`). Vite's defaults are not a requirement and are not cited as one.

**Unblocked by:** a platform or requirements statement of supported browsers and minimum versions.
<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input names an accessibility standard or level, or how conformance is verified (`GAP-WEB-007`).

**What the inputs do establish, without a target:**
- `Input/Text` has Focused and Error states (`components-inventory.md`).
- Validation errors are shown inline and the invalid field is highlighted (US-006-02, US-006-03).
- Errors are shown for lockout, expiry and resend limits (US-006-05).

How these are exposed to assistive technology is not stated. Focus management, keyboard paths and live regions are specified in the module specs once a target exists.

**Unblocked by:** a requirement naming the target standard and level, and the verification method.
<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: DRAFT-UNVERIFIED -->
## Performance

**Bundle budget: `BLOCKED`.** No input states a bundle-size or load-time budget for the web app (`GAP-WEB-008`). The latency targets in the inputs apply to the API, not the browser (`requirements.md` NFR-001, NFR-002).

**Route-level code splitting.** Proposed: the app layout routes and the login routes load as separate chunks, so a signed-out user does not download the editor or search code (`ASM-WEB-007`). No input requires this.

**Largest-payload route (derived).**
- `/notes` (`SearchScreen`) at the maximum page size.
- `NFR-003` sets the default page size to 20 and the maximum to 100.
- Each search item carries a note's title, body, tags and timestamps (`API-SEARCH-580505`), so a 100-item page is the largest response the web app requests.
- The paging control is not designed (`GAP-WEB-014`). Whether the web app ever requests 100 is undecided, so the figure is an upper bound only.
- `NFR-001` and `NFR-012` bound search latency at the API, not the render cost of a 100-item list.

**Hosting.** The web app's own hosting region is not stated (`GAP-WEB-012`).
<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

**Legend.**
- **Direct:** a web story (EP-006) the web app implements.
- **Dependency:** an API story whose operation a Direct story consumes. No web requirement is minted from it.
- **Not web:** no web actor or UI in the story; the reason is given.

`WEB-<MODULE>-FR-nnn` IDs are minted in the module specs.

| Story | Coverage | Module / route | Note |
|---|---|---|---|
| US-001-01 | Dependency | `editor` `/notes/new` | `API-NOTES-232390` |
| US-001-02 | Dependency | `editor` `/notes/:id/edit` | `API-NOTES-847551` (PATCH only; contract has no PUT) |
| US-001-03 | Dependency | `note-detail` `/notes/:id` | `API-NOTES-045877` |
| US-001-04 | Dependency, `BLOCKED` | `search` `/notes` | No list operation in the contract (`GAP-WEB-002`) |
| US-001-05 | Dependency | `editor` | Field-level errors shown inline (US-006-02, US-006-03) |
| US-001-06 | Dependency | `editor` | Server `created_at` reflected (US-006-02) |
| US-002-01 | Not web | none | Tag add: no EP-006 story; design maps it to `NoteDetailScreen` (`GAP-WEB-015`) |
| US-002-02 | Not web | none | Tag remove: same (`GAP-WEB-015`) |
| US-002-03 | Dependency | `search` | `API-SEARCH-580505`, keyword (US-006-04) |
| US-002-04 | Dependency | `search` | Tag filter and combined filter (US-006-04) |
| US-002-05 | Dependency, `BLOCKED` | `search` `/tags` | No list-tags operation (`GAP-WEB-003`) |
| US-002-06 | Dependency | `search` | Paging UI undesigned (`GAP-WEB-014`) |
| US-003-01 | Dependency | `note-detail` | `API-NOTES-810640`; see `CON-WEB-002` |
| US-003-02 | Dependency | `note-detail` | `API-NOTES-917869` |
| US-003-03 | Dependency, `BLOCKED` | `search` | Archived-only list absent (`GAP-WEB-002`) |
| US-003-04 | Dependency, `BLOCKED` | `search` | Active-only list absent (`GAP-WEB-002`) |
| US-003-05 | Dependency | `editor`, `note-detail` | Constraint `INV-WEB-004` |
| US-003-06 | Not web | none | Malformed-id handling is server-side; the web app only uses ids it received |
| US-004-01 to US-004-06 | Not web | none | Infrastructure and audit stories with no web actor; design maps them to `ResidencyReportScreen` (`GAP-WEB-010`, `Q-WEB-001`) |
| US-005-01 | Not web | none | API surface definition |
| US-005-02 | Not web | none | Attachment rejection; the web app has no attachment control |
| US-005-03, US-005-04 | Not web | none | CI and scope-guard checks |
| US-005-05 | Not web | none | Documented scope list; design maps to `ScopeDocumentationScreen` (`GAP-WEB-010`) |
| US-005-06 | Not web | none | Product-manager process |
| US-006-01 | Direct, partly `BLOCKED` | `search` `/notes` | Sort, pinned-first and count are specifiable; fetching the list is `BLOCKED` (`GAP-WEB-002`, `GAP-WEB-004`) |
| US-006-02 | Direct | `editor` `/notes/new` | |
| US-006-03 | Direct | `editor` `/notes/:id/edit` | |
| US-006-04 | Direct | `search` `/notes` | Sort kept, count updated; `CON-WEB-001` |
| US-006-05 | Direct, partly `BLOCKED` | `login` | Refusal states specified; session issuance `BLOCKED` (`GAP-WEB-001`) |
| US-006-06 | Direct, partly `BLOCKED` | `note-detail`; pin in `search` | Archive and restore specified; list visibility depends on `GAP-WEB-002` |

**Contradiction sweep.**

| Direction | Result |
|---|---|
| Story ↔ design | The design maps tag add/remove and archive/restore to `NoteDetailScreen`. No design frame exists for the notes list or the pin toggle (`GAP-WEB-004`, `GAP-WEB-015`). No direct contradiction otherwise. |
| Story ↔ requirement | `CON-WEB-002`. Garbled text in `BR-022`, `BR-023` and US-006-05 (template substitution) is read through the values repeated consistently across `BR-022`, US-006-05 and the contract. |
| Requirement ↔ requirement | `CON-WEB-001`. `BR-022` says "resends", the contract says "requests" (`CON-BE-001`). |
| Design ↔ design | None found. |

**Register of open items.**

*Contradictions (non-critical; neither side resolved)*
- `CON-WEB-001`: `BR-020` and US-006-04 sort on the client, and `BR-019` counts rendered notes. Search is paginated (`NFR-003`, `API-SEARCH-580505`). Consequence of sorting loaded items only: Title A-Z ignores unloaded pages. Consequence of loading all pages: up to the full result set is fetched. Default: sort only the loaded items, keep pinned-first, and count the rendered items.
- `CON-WEB-002`: US-003-01 returns 200 idempotently for re-archive. The contract lists 409 for archive (`ASM-BE-004`), and `BR-012` asks for both. Default: treat a 409 as "already archived", refresh the note and show no failure (`ASM-WEB-008`).

*Questions (degradable; defaults taken, unattended)*
- `Q-WEB-001`: Are `ResidencyReportScreen` and `ScopeDocumentationScreen` part of the web app? Default: no (`ASM-WEB-005`).
- `Q-WEB-002`: Where does sign-in land? Default: the notes list (`ASM-WEB-009`).

The blocking decisions are upstream (`Q-BE-001`, `Q-BE-002`, `Q-BE-003`). They are not duplicated here.

*Assumptions*
- `ASM-WEB-001`: Route paths are proposals; no input supplies them. `CodeEntryScreen` is reachable only with an email held.
- `ASM-WEB-002`: Module names are derived from design labels M1 to M6 and screen titles.
- `ASM-WEB-003`: `SearchScreen` hosts both the default notes list and search results, because the sort control and result count are shared (`BR-019`, `BR-021`, US-006-01, US-006-04) and no frame exists for the list.
- `ASM-WEB-004`: No time-based cache staleness; refresh on mutation and navigation.
- `ASM-WEB-005`: Residency and scope screens are unspecified until `Q-WEB-001` is answered.
- `ASM-WEB-006`: Team members have uniform capability, pending `Q-BE-001` and `Q-BE-002`.
- `ASM-WEB-007`: Route-level code splitting by layout.
- `ASM-WEB-008`: A 409 on archive is reconciled as success (`CON-WEB-002`).
- `ASM-WEB-009`: Sign-in lands on `/notes` (`Q-WEB-002`).
- `ASM-WEB-010`: The web app never calls search with neither `q` nor a tag (`BR-007`).

*Gaps (each names its closing artefact)*
- `GAP-WEB-001`: Session credential, lifetime, transport, refresh, logout, and whether notes and search require a session are undecided. **Backend must supply** the answer to `Q-BE-003` and any refresh and logout operations. Closing artefact: that decision, or clean `BR-023` text.
- `GAP-WEB-002`: The contract has no list-active-notes or list-archived-only operation. Search cannot substitute, because it requires `q` or a tag (`BR-007`) and `include_archived` mixes both states. **Backend must supply** both operations. Closing artefact: contract operations.
- `GAP-WEB-003`: The contract has no list-distinct-tags operation (US-002-05, `BR-008`). **Backend must supply** it. It is also the only source of tag-filter options. Closing artefact: a contract operation.
- `GAP-WEB-004`: The design has no frame for the notes list (US-006-01 maps to none), the pin toggle or the archived view. Closing artefact: design frames.
- `GAP-WEB-005`: Type scale, spacing, breakpoints, token values and frame IDs are absent. Closing artefact: token value files, or a connected Figma MCP.
- `GAP-WEB-006`: Browser support is unstated. Closing artefact: a platform statement.
- `GAP-WEB-007`: The accessibility target and verification are unstated. Closing artefact: a requirement.
- `GAP-WEB-008`: No web bundle or load budget. Closing artefact: an NFR.
- `GAP-WEB-009`: Remaining lock time and the re-enable rule, precedence when several verify refusals apply at once, whether requesting a new code is allowed while locked, and the final error-code registry. **Backend must supply** these. Closing artefact: contract additions (`backend.error-model` is a proposal, `ASM-BE-006`).
- `GAP-WEB-010`: EP-001 to EP-005 stories have no web actor, yet the design maps them to two screens. The compliance-auditor role is undefined for the web. Closing artefact: the answer to `Q-WEB-001` and, if in scope, web stories.
- `GAP-WEB-011`: The permission-denied rendering and the ownership model are unknown. Closing artefact: the answers to `Q-BE-001` and `Q-BE-002`.
- `GAP-WEB-012`: The web app's own hosting region and client logging are unstated. `BR-013` scopes EU residency to `notes_api`. Closing artefact: a platform statement.
- `GAP-WEB-013`: Post-login destination, return-to-route behaviour and the four design flows are not enumerated. Closing artefact: flow definitions.
- `GAP-WEB-014`: The pagination control and paging behaviour are undesigned. Closing artefact: design frames and a story.
- `GAP-WEB-015`: Tag add and remove controls are mapped to `NoteDetailScreen` by the design, but no web story requires them. Closing artefact: a web story.
- `GAP-WEB-016`: No copy strings or locale for the UI messages. Closing artefact: a copy deck.
<!-- /SECTION: web-app.coverage -->
<!-- SECTION: web-app.lane-needs | status: VERIFIED -->
## Lane needs

### Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-500592` | `api_contract` | Decide and define the session model: what a successful code verification issues (credential type, lifetime, transport), refresh and logout operations, and whether notes and search endpoints require a session (Q-BE-003). | The protected-route rule, login completion, session expiry and logout cannot be specified without it (GAP-WEB-001). | yes | `web-app.routing`, `web-app.auth-flows`, `web-app.authorization-ui`, `web-app.state-management` |
| `HANDOFF-WEBAPP-732181` | `api_contract` | Add operations to list active notes and to list archived-only notes, since search requires q or a tag and include_archived mixes both states. | The notes list (US-006-01) and archived and active views cannot be fetched without them (GAP-WEB-002). | yes | `web-app.module-map`, `web-app.state-management`, `web-app.coverage` |
| `HANDOFF-WEBAPP-263975` | `api_contract` | Add an operation that lists distinct tags, usable for the tag filter options and the tags list screen. | TagsListScreen and the tag filter options have no data source (GAP-WEB-003). | no | `web-app.routing`, `web-app.module-map`, `web-app.coverage` |
| `HANDOFF-WEBAPP-452996` | `api_contract` | Specify the remaining lock time, the re-enable rule, precedence among simultaneous verify refusals, whether a new code can be requested while locked, and the final error-code registry. | Lockout and refusal UI states cannot be fully defined, and no countdown can be specified (GAP-WEB-009). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-078157` | `screen` | Provide design frames for the notes list, pin toggle, archived view, sort control, result count and pagination control. | The design has no frame for these, so their layout and components cannot be specified (GAP-WEB-004, GAP-WEB-014). | no | `web-app.routing`, `web-app.design-system`, `web-app.coverage` |
| `HANDOFF-WEBAPP-024946` | `nfr` | State the supported browsers and versions, the accessibility standard and level with how it is verified, the web bundle and load budget, and the breakpoints, type scale and spacing scale. | These sections are blocked because no input defines them (GAP-WEB-005 to GAP-WEB-008). | no | `web-app.responsive`, `web-app.browser-support`, `web-app.accessibility`, `web-app.performance`, `web-app.design-system` |
| `HANDOFF-WEBAPP-924046` | `business_rule` | Decide the ownership and permission model, and whether non-owned notes return 401, 403 or not-found (Q-BE-001, Q-BE-002). | The permission-denied state and role capabilities are assumed to be uniform until it is decided (GAP-WEB-011). | no | `web-app.authorization-ui`, `web-app.routing` |
| `HANDOFF-WEBAPP-944632` | `story` | Confirm whether the Residency Report and Scope Documentation screens belong in the web app. If so, supply web stories for them and for tag add and remove controls (Q-WEB-001, GAP-WEB-015). | The design maps these screens and controls, but no web story requires them, so they remain unspecified. | no | `web-app.overview`, `web-app.module-map`, `web-app.coverage` |
<!-- /SECTION: web-app.lane-needs -->
