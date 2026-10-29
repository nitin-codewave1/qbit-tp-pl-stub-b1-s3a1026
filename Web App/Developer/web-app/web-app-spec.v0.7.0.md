---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.7.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-002-01, web_app:US-002-02, web_app:US-002-03, web_app:US-002-04, web_app:US-002-05, web_app:US-002-06, web_app:US-003-01, web_app:US-003-02, web_app:US-003-03, web_app:US-003-04, web_app:US-003-05, web_app:US-003-06, web_app:US-004-01, web_app:US-004-02, web_app:US-004-03, web_app:US-004-04, web_app:US-004-05, web_app:US-004-06, web_app:US-005-01, web_app:US-005-02, web_app:US-005-03, web_app:US-005-04, web_app:US-005-05, web_app:US-005-06, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03, web_app:US-006-04, web_app:US-006-05, web_app:US-006-06]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DRAFT-UNVERIFIED -->
## Overview

**Purpose.** The web app is the browser client for `notes_api`. A Team member can:

- list notes;
- create and edit notes;
- search by keyword and tag filter;
- sign in with an emailed one-time code;
- archive, restore and pin or unpin notes.

Sources: `stories.md` US-006-01 to US-006-06 and `requirements.md` BR-019 to BR-023. The stack is React 18 + Vite hosted on AWS (`platform.md`).

**Run basis.**

- **Run mode.** `bootstrap` with an empty `existing`. `feedback.md` carries an earlier approved web-app document set. It is context only: no baseline or human lock is inherited from it, and its contract pin (`sha256:8e424bb2b40a`) differs from this run's.
- **Contract.** `sha256:ceedbd350666` (`backend-api.md`). Operation IDs are cited exactly as supplied. The contract's IDs carry no `@v` suffix.
- **Design.** `spec_mode` is `FULL-FROM-BRIEF`. `design-brief.md` is present and Figma is not connected (`RUN-CONTEXT.inputs.figma_mcp`). Every screen, component and token name below is an unverified claim, and no frame IDs were supplied.
- **Change in scope (CR-131).** Email one-time-code login on `LoginScreen` and `CodeEntryScreen`, showing lockout and expiry errors (`feedback.md`). The CR record lists expiry, resend limit and lockout as "not decided". `requirements.md` BR-022 states them, and `backend-api.md` agrees with BR-022 and with US-006-05: code valid 10 minutes, 3 resends per 10 minutes, 5 wrong codes lock for 15 minutes. This spec uses those figures.
- **Inbound handoffs.** `RUN-CONTEXT.upstream.handoffs` is empty, so none were read.
- **Identifier numbering.** `backend-api.md` cites rule, story and requirement numbers from its own set (`CON-WEB-005`). This spec cites `BR-`, `NFR-` and `US-` IDs only from `requirements.md` and `stories.md`. The backend is cited by operation ID and section name.

**Primary actor.** Team member, the actor of every EP-006 story. No other role is named for the web surface (`web-app.authorization-ui`).

**Upstream state that shapes this document.**

- The backend left `Q-BE-001`, `Q-BE-002` and `Q-BE-003` open, and `backend.auth-model` is `BLOCKED`. The success response of verify-code defines no credential. As a result, sign-in completion, session handling and the protected-route rule cannot be written (`GAP-WEB-001`, `GAP-WEB-009`).
- The contract has no operation to list active notes, list archived-only notes, list distinct tags, or remove a tag (`GAP-WEB-002`, `GAP-WEB-003`).

**Explicitly out of scope.**

- Attachment, real-time and native-mobile UI. `requirements.md` BR-016, BR-017 and BR-018 exclude them from the API, and no EP-006 story asks the web app for them (derived).
- Backend behaviour. Needs from the backend are stated in `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003`, `GAP-WEB-005`, `GAP-WEB-007` and `GAP-WEB-009`.
- `ResidencyReportScreen` and `ScopeDocumentationScreen`. The design brief lists them, but no web story requires them (`Q-WEB-001`, `ASM-WEB-005`).
- Tag add and remove controls and `TagManagementScreen`. The design lists the screen, but no web story requires it (`GAP-WEB-003`).
<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. Screen names come from `design-brief.md` (`screens-inventory.md`). The paths below are proposals (`ASM-WEB-001`).

| Screen | Proposed path | Layout | Access rule | Source |
|---|---|---|---|---|
| `LoginScreen` | `/login` | public | Public. Request-code and verify-code are callable without a session token (`requirements.md` BR-023, Exception). | US-006-05 |
| `CodeEntryScreen` | `/login/code` | public | Public. Reachable only while an email is held in memory; otherwise redirect to `/login` (`ASM-WEB-001`). | US-006-05 |
| `SearchScreen` (notes list and search) | `/notes` | app | See the protected-route rule. | US-006-01, US-006-04; `ASM-WEB-003` |
| `NoteEditorScreen` (create) | `/notes/new` | app | See the protected-route rule. | US-006-02 |
| `NoteDetailScreen` | `/notes/:id` | app | See the protected-route rule. | US-006-02 ("navigates to the saved note"), US-006-06 ("note is open", "archived note is viewed") |
| `NoteEditorScreen` (edit) | `/notes/:id/edit` | app | See the protected-route rule. | US-006-03 |
| `ArchiveScreen` | `/archive` | app | See the protected-route rule. Its data source is `BLOCKED` (`GAP-WEB-002`). | `stories-map.md` maps US-006-06 to it |
| `TagManagementScreen` | none | n/a | Not routed: no web story, and the contract has no list-tags or remove-tag operation (`GAP-WEB-003`). | `screens-inventory.md` |
| `ResidencyReportScreen`, `ScopeDocumentationScreen` | none | n/a | Not routed (`Q-WEB-001`, `ASM-WEB-005`). | `screens-inventory.md` |

**Layout nesting.**

- A public layout wraps `/login` and `/login/code`.
- An app layout wraps every other routed screen.
- The design has no frame for application chrome (header, navigation, sign-out control), and the components inventory has four components, none of them navigation (`GAP-WEB-008`).

**Protected-route rule: `BLOCKED`.**

- Whether notes and search operations require a session is undecided (`Q-BE-003`, `GAP-WEB-001`).
- `requirements.md` BR-023 states that the existing notes and search endpoints require the session result. Its Enforcement text still holds unfilled template placeholders, so it cannot be used as a rule (`GAP-WEB-015`).
- Unblocked by: the backend decision on `Q-BE-003` and a clean BR-023.

**Unauthenticated redirect.** The only sign-in entry in the design is `LoginScreen`, so if the app layout is protected, an unauthenticated visit redirects to `/login` (derived). Whether the user returns to the originally requested route afterwards is `Q-WEB-002`. Its default is no return-to behaviour.

**Navigation known from the inputs.**

- Design `ON_CLICK` transitions: `TagManagementScreen` → `SearchScreen` (twice) and → `ArchiveScreen`. `ScopeDocumentationScreen` → `ArchiveScreen` and → `SearchScreen` (`screens-inventory.md`).
- `NoteDetailScreen`, `LoginScreen` and `CodeEntryScreen` have no outgoing transitions in the design.
- Story navigation:
  - Request-code success shows `CodeEntryScreen` (US-006-05).
  - Create success navigates to the saved note (US-006-02).
  - Sign-in lands on `/notes` (`ASM-WEB-009`, `Q-WEB-002`).
- The design reports 5 flows but does not enumerate them (`design-brief.md`, Counts), so no further navigation is asserted.
<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DRAFT-UNVERIFIED -->
## Module map

Module labels M1 to M6 and screen titles come from `design-brief.md`. Kebab-case module names are derived from them (`ASM-WEB-002`). Functional requirement IDs (`WEB-<MODULE>-FR-nnn`) are minted in the module specs, not here.

| Module | Design label | Screens | Web stories | Contract operations |
|---|---|---|---|---|
| `login` | M6 | `LoginScreen`, `CodeEntryScreen` | US-006-05 | `API-AUTH-015365`, `API-AUTH-592570` |
| `notes` | M1 | `NoteEditorScreen`, `NoteDetailScreen` | US-006-02, US-006-03, archive and restore of US-006-06 | `API-NOTES-232390`, `API-NOTES-847551`, `API-NOTES-045877`, `API-NOTES-810640`, `API-NOTES-917869` |
| `search` | M2 | `SearchScreen` (notes list and search results) | US-006-01, US-006-04, pin toggle of US-006-06 | `API-SEARCH-580505`, `API-NOTES-847551` (pin). Active-list and tag-list operations are absent (`GAP-WEB-002`, `GAP-WEB-003`). |
| `archive` | M3 | `ArchiveScreen` | restore from the archived view (US-006-06, per `stories-map.md`) | `API-NOTES-917869`. Archived-list operation absent (`GAP-WEB-002`). |
| `tags` | M2 | `TagManagementScreen` | none | `API-NOTES-359413` (add) exists. Remove and list are absent. No module spec until `GAP-WEB-003` closes. |
| `residency-report` | M4 | `ResidencyReportScreen` | none | No module spec until `Q-WEB-001` is answered. |
| `scope-docs` | M5 | `ScopeDocumentationScreen` | none | No module spec until `Q-WEB-001` is answered. |

**Dependency direction** (A → B means A depends on B):

- `notes → search`. Every successful note mutation invalidates cached search results (`web-app.state-management`).
- `search → notes` and `archive → notes`. Opening a note navigates to `/notes/:id`.
- `login` depends on no module.
- Every module except `login` depends on the session decision (`GAP-WEB-001`).
- The `search ↔ notes` pair is navigation and cache invalidation only, not a code dependency.
<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state-management library, so none is specified. The boundary and policy below are library-independent.

**Server state** (owned by `notes_api`, never edited locally):

- A note, by id (`API-NOTES-045877`).
- Search results, keyed by the full parameter set `q`, `tags`, `include_archived`, `page` and `page_size` (`API-SEARCH-580505`).
- The active note list and the archived-only list. Both are `BLOCKED` (`GAP-WEB-002`).

**Client state** (browser-owned, never sent to the API):

- The sort selection: Newest first, Oldest first or Title A-Z. The default is Newest first (US-006-01). The API receives no sort parameter (BR-020).
- The search text and selected tag filters, until submitted.
- The email held between `LoginScreen` and `CodeEntryScreen`. It is held in memory only (`ASM-WEB-001`).
- Editor form values.
- Login error presentation, including whether code submission is disabled after a lockout. Whether the lockout presentation survives a reload is open (`GAP-WEB-005`).

**Cache and invalidation policy.**

1. On a successful mutation, merge the fields the response returns into the cached note-by-id entry. Create and edit return the full note. Archive, unarchive and tag-add return a subset (see the pinned operations), so cached fields not returned are kept.
2. After any successful mutation that can change a note's membership in, or order within, a result set, invalidate all cached search results. This covers:
   - create;
   - title or body edit (searchable fields);
   - pin or unpin (BR-021);
   - archive or unarchive (search excludes archived notes unless `include_archived` is set, `API-SEARCH-580505`);
   - tag add.

   This is derived from `API-SEARCH-580505`.
3. No input defines time-based staleness. The default is none: data refreshes on mutation and on navigation to a list (`ASM-WEB-004`).
4. A failed mutation never changes cached data. US-006-03 requires that a 404 on save "does not apply local changes".
5. Network failures, 5xx responses and 503 from search show a user-facing error state that allows retry and keeps the user's inputs (`ASM-WEB-011`; US-006-01 requires a user-facing error state for list errors). The 503 retry-after value is unstated (`GAP-WEB-005`).

**Sort key.** "Newest first" and "Oldest first" sort on `created_at`, and "Title A-Z" compares titles case-insensitively (`ASM-WEB-008`). No input names the key or the comparison.

**Search guard.** The web app never calls search with neither `q` nor a tag (BR-007, `API-SEARCH-580505`). A whitespace-only keyword is treated as empty at module level.

**Invariants.**

- `INV-WEB-001`: Pinned notes render above unpinned notes under every sort, including filtered and search results (BR-021).
- `INV-WEB-002`: The live result count equals the number of notes currently rendered (BR-019, US-006-01, US-006-04). It is not the API's `total` across pages.
- `INV-WEB-003`: The web app sends no sort parameter to the API (BR-020).
- `INV-WEB-004`: Edit and tag controls are never disabled because a note is archived (BR-010, US-003-05).
- `INV-WEB-005`: While a lockout is shown, code submission is disabled (US-006-05).

**Open tension.** Client-side sort conflicts with API pagination (`CON-WEB-001`). Default reading: sort and pinned-first apply to the loaded page only, and the count is the rendered number.
<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

**Login (specifiable).**

1. On `LoginScreen` the user submits an email address. The web app calls `API-AUTH-015365`. On success it shows `CodeEntryScreen` (US-006-05).
2. On `CodeEntryScreen` the user enters the 6-digit numeric code (format per `API-AUTH-592570`). The web app calls `API-AUTH-592570` with the held email and the code (US-006-05).
3. On success the web app "signs the user in" (US-006-05). The mechanism is `BLOCKED` below.
4. The request-code response carries a validity period in seconds. The web app may show it as "valid for 10 minutes" (BR-022). It never shows a code.

**Limits the UI reflects** (BR-022, US-006-05, matching `backend-api.md`):

- A code is valid for 10 minutes.
- At most 3 resends are allowed per 10 minutes. The backend counts 3 requests in total (`CON-BE-004`). The web app therefore shows no remaining-resends counter and reacts only to the refusal (`CON-WEB-003`).
- 5 wrong codes lock the account for 15 minutes.

**Request-code outcomes.** The web app branches on the HTTP status of each operation, because the statuses are distinct within each operation. It does not depend on the backend's proposed error-code names (`ASM-BE-007`).

| Status | Web behaviour | Source |
|---|---|---|
| 202 | Show `CodeEntryScreen`. When resending from `CodeEntryScreen`, stay on it and confirm that a new code was requested. | `API-AUTH-015365`; US-006-05 |
| 400 | Inline error on the email field. The field-detail shape is open (`GAP-WEB-007`). | `API-AUTH-015365` |
| 429 | Resend-limit error; no further resend is offered. No retry-after value is defined (`GAP-WEB-005`). | `API-AUTH-015365`; US-006-05 |
| 500 | Generic failure state with retry (`ASM-WEB-011`). | `API-AUTH-015365` |

**Verify-code outcomes.**

| Status | Web behaviour | Source |
|---|---|---|
| 200 | Sign-in completion. `BLOCKED` below. | `API-AUTH-592570` |
| 400 | Inline error on the code field. | `API-AUTH-592570` |
| 401 | Wrong-code error. | `API-AUTH-592570`; BR-022 ("matching error for each case") |
| 409 | Already-used-code error. | `API-AUTH-592570`; BR-022 |
| 410 | Expired-code error with an option to request a new code. | `API-AUTH-592570`; US-006-05 |
| 423 | Lockout error stating the account is locked for 15 minutes; code submission disabled (`INV-WEB-005`). The design inventory shows `Button/Primary` has a Disabled state. | `API-AUTH-592570`; US-006-05; `components-inventory.md` |
| 500 | Generic failure state with retry (`ASM-WEB-011`). | `API-AUTH-592570` |

- When several refusals apply, the backend decides which status returns (`ASM-BE-003`). The web app applies no client-side precedence.
- The wording of every error is supplied nowhere except the lockout phrase "locked for 15 minutes" (`GAP-WEB-006`).
- The design has only a Default variant for `LoginScreen` and `CodeEntryScreen`, and no message component (`GAP-WEB-004`).
- The contract defines no remaining lock time and no re-enable rule. No countdown is specified, and when submission becomes enabled again is undefined (`GAP-WEB-005`).

**Mid-flow code expiry.** A code that expires while `CodeEntryScreen` is open is handled by the 410 row above. The web app does not run its own expiry timer. The only timing input is the validity period returned by `API-AUTH-015365`.

**`BLOCKED`: session, refresh, session expiry, logout.**

- `API-AUTH-592570` defines no credential in its success response. The credential type, lifetime, transport, refresh behaviour, logout operation and whether notes and search require a session are all undecided (`Q-BE-003`, `GAP-WEB-001`).
- Without them, sign-in completion, mid-flow session expiry and sign-out cannot be written.
- Unblocked by: the backend session-model decision and a clean `requirements.md` BR-023 (`GAP-WEB-015`).
<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DRAFT-UNVERIFIED -->
## Authorization UI

**Roles that can sign in to this surface.** One: Team member. It is the actor of every EP-006 story, and no input names another web role.

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Team member | Notes list and search (`/notes`), note detail, note editor (create and edit), archived view (`/archive`, data `BLOCKED` by `GAP-WEB-002`) | Create (`API-NOTES-232390`); edit title or body (`API-NOTES-847551`); pin or unpin (`API-NOTES-847551`); archive (`API-NOTES-810640`); restore (`API-NOTES-917869`); search (`API-SEARCH-580505`). Edit and tag controls stay enabled on archived notes (`INV-WEB-004`). There is no tag add or remove control (`GAP-WEB-003`). |

- **Capability.** Team members are assumed to have uniform capability (`ASM-WEB-006`), pending `Q-BE-001` and `Q-BE-002`.
- **Permission-denied state.** None is specified. The backend defines no ownership rule and documents no 401 or 403 for note operations (`GAP-WEB-009`).
- **Unauthenticated visitor.** This is a state, not a role. Whether such a visitor can reach anything beyond `/login` and `/login/code` depends on the protected-route rule, which is `BLOCKED` (`GAP-WEB-001`).

**Roles excluded, and why.** `backend-api.md` defines no roles (`backend.auth-model` is `BLOCKED`), so there is no backend matrix to filter. The actors of EP-001 to EP-005 other than Team member are excluded because no route, screen or web story exists for them:

- Developer integrating with the API;
- backend developer;
- API tech lead;
- QA engineer;
- product owner;
- operations engineer;
- platform operations engineer;
- compliance auditor.

If `Q-WEB-001` is answered yes, the compliance auditor and product owner may need adding.
<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

`spec_mode` is `FULL-FROM-BRIEF`. All names below come from `design-brief.md` (`tokens-index.md`, `components-inventory.md`). Token values are not inlined in any input, and no Figma verification was possible. Every name is an unverified claim. Specs bind visuals by token name only; no visual value is stated anywhere in this document.

**Tokens (71, by group).**

| Group | Names |
|---|---|
| `UI/border/` (10) | default, disabled, error, focus, input, input-focus, strong, subtle, success, warning |
| `UI/color/` (20) | black, disabled, error, error-subtle, hover-destructive, hover-ghost, hover-primary, hover-secondary, hover-success, info, info-subtle, overlay, primary, primary-hover, primary-subtle, success, success-subtle, warning, warning-subtle, white |
| `UI/icon/` (10) | active, default, disabled, error, info, inverse, on-primary, subtle, success, warning |
| `UI/surface/` (18) | button/destructive, button/ghost, button/primary, button/primary-hover, button/secondary, button/success, card, default, disabled, input, input-focus, overlay, secondary, tertiary, toast/error, toast/info, toast/success, toast/warning |
| `UI/text/` (13) | disabled, error, info, inverse, link, link-hover, on-primary, placeholder, primary, secondary, success, tertiary, warning |

**Components (4).**

| Component | Role | Variants | States |
|---|---|---|---|
| `Button/Primary` | action | 12 | Default, Pressed, Loading, Disabled |
| `Card/Item` | container | 1 | Default |
| `Empty/State` | content | 1 | Default |
| `Input/Text` | entry | 5 | Default, Focused, Filled, Error, Disabled |

The inventory has no message or toast component, no select control for the sort control, no pin toggle, no pager and no navigation component (`GAP-WEB-004`, `GAP-WEB-008`).

**Type scale, spacing scale, breakpoints: `BLOCKED`.** No input defines them (`GAP-WEB-010`). Unblocked by: design annotations or a platform statement giving them.
<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behaviour

**BLOCKED.** No input states breakpoints or the layout change at any of them. `design-brief.md` lists screens, variants, components and tokens, but no frame dimensions or responsive variants (`GAP-WEB-010`).

Unblocked by: design frames or annotations that give the breakpoints and the layout at each.
<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**BLOCKED.** `platform.md` names the stack (React 18 + Vite, AWS) but no supported browsers or versions. No other input does either (`GAP-WEB-011`).

Unblocked by: a stated list of supported browsers and versions in a platform document or NFR.
<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: UNKNOWN -->
<!-- SECTION-NOTE: see anchor below; this placeholder is intentionally not used -->
<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: BLOCKED -->
## Performance

**BLOCKED.** the run did not produce this section. Nothing has been written in its place, and no claim about it should be read into this document.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: BLOCKED -->
## Coverage

**BLOCKED.** the run did not produce this section. Nothing has been written in its place, and no claim about it should be read into this document.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.coverage -->
<!-- SECTION: web-app.lane-needs | status: VERIFIED -->
## Lane needs

### Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-752470` | `api_contract` | Define the session model: the credential returned by verify-code (type, lifetime, transport), refresh behaviour, a logout operation, and whether notes and search operations require a session. | Sign-in completion, the protected-route rule, session expiry and sign-out cannot be specified without it. | yes | `web-app.routing`, `web-app.auth-flows`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-526592` | `api_contract` | Add operations to list active notes and to list archived-only notes, with pagination. | The notes list on /notes and the ArchiveScreen have no data source in the contract. | yes | `web-app.routing`, `web-app.module-map`, `web-app.state-management`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-701819` | `api_contract` | Add operations to list distinct tags and to remove a tag from a note. | The tag filter and TagManagementScreen cannot be built without a tag list or tag removal. | no | `web-app.module-map`, `web-app.authorization-ui`, `web-app.routing` |
| `HANDOFF-WEBAPP-733353` | `api_contract` | Specify retry-after values for 429 and 503 responses, remaining lock time and re-enable rule on 423, and the field-detail shape of 400 errors. | Lockout presentation, resend-limit handling, retry behaviour and inline field errors are undefined without them. | no | `web-app.auth-flows`, `web-app.state-management` |
| `HANDOFF-WEBAPP-850530` | `business_rule` | Provide a clean BR-023 with its Enforcement text filled in, stating which endpoints require a session. | BR-023 contains unfilled template placeholders so it cannot serve as the protected-route rule. | no | `web-app.routing`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-943440` | `screen` | Provide design frames for application chrome (header, navigation, sign-out), message/toast components, error variants of login screens, and the sort control and pin toggle. | The design has only four components and Default variants for login screens, leaving navigation and error states unspecified. | no | `web-app.routing`, `web-app.design-system`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-380598` | `screen` | Provide the type scale, spacing scale, and breakpoints with the layout at each breakpoint. | The responsive section and the design-system scales cannot be written without them. | no | `web-app.design-system`, `web-app.responsive` |
| `HANDOFF-WEBAPP-513896` | `nfr` | State supported browsers and versions, performance targets, and accessibility conformance level for the web app. | These sections are blocked or unproduced because no input gives the targets. | no | `web-app.browser-support`, `web-app.performance`, `web-app.accessibility` |
| `HANDOFF-WEBAPP-427792` | `story` | Decide whether the web app needs stories for ResidencyReportScreen, ScopeDocumentationScreen and TagManagementScreen, and whether sign-in should return the user to the originally requested route. | These screens are in the design but no web story requires them, so they are unrouted. | no | `web-app.overview`, `web-app.routing`, `web-app.module-map` |
<!-- /SECTION: web-app.lane-needs -->
