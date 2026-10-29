---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.8.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-002-01, web_app:US-002-02, web_app:US-002-03, web_app:US-002-04, web_app:US-002-05, web_app:US-002-06, web_app:US-003-01, web_app:US-003-02, web_app:US-003-03, web_app:US-003-04, web_app:US-003-05, web_app:US-003-06, web_app:US-004-01, web_app:US-004-02, web_app:US-004-03, web_app:US-004-04, web_app:US-004-05, web_app:US-004-06, web_app:US-005-01, web_app:US-005-02, web_app:US-005-03, web_app:US-005-04, web_app:US-005-05, web_app:US-005-06, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03, web_app:US-006-04, web_app:US-006-05, web_app:US-006-06]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DRAFT-UNVERIFIED -->
## Overview

**Purpose.** The web app is the browser client for `notes_api`. A Team member can:
- sign in with an emailed one-time code;
- list notes;
- create and edit notes;
- search by keyword and tag;
- archive, restore, and pin or unpin notes.

Sources: `stories.md` US-006-01 to US-006-06 and `requirements.md` BR-019 to BR-023. The stack is React 18 + Vite hosted on AWS (`platform.md`).

**Run basis.**
- **Run mode.** `bootstrap`, with an empty `existing`. `feedback.md` carries an earlier document set for this lane. It is context only: no baseline or lock is inherited, and screens it names that are absent from the current `design-brief.md` are not carried over.
- **Contract.** Pin `sha256:9550f0d579d8` (`backend-api.md`). Operations are cited by their supplied IDs (`API-…`). The contract IDs carry no `@v` suffix.
- **State of the contract.** The backend run behind it ended `HALT-BLOCKING-QUESTION`. `Q-BE-001`, `Q-BE-002` and `Q-BE-003` are unanswered, and `backend.auth-model` is `BLOCKED`. The pin may change when those are answered.
- **Design.** `spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma is `not-connected`. Every screen, component and token name in this document is an unverified claim. No frame IDs were supplied.
- **Change in scope (CR-132).** Email one-time-code login on `LoginScreen` and `CodeEntryScreen`, showing lockout and expiry errors (`feedback.md`).
- **Inbound handoffs.** `RUN-CONTEXT.upstream.handoffs` is empty, so none were read.
- **Identifiers.** The contract cites rule numbers (`BR-0nn`) from its own numbering, which differs from `requirements.md`. This document cites `BR-`, `NFR-` and `US-` IDs only from `requirements.md` and `stories.md`, and cites the backend by operation ID.

**Primary actor.** Team member, the actor of every EP-006 story (`ASM-WEB-005`).

**Explicitly out of scope.**
- Attachment, real-time and native-mobile UI. `requirements.md` BR-016, BR-017 and BR-018 exclude these from the API, and no EP-006 story asks the web app for them (derived).
- Backend behaviour. Needs from the backend are stated as `GAP-` items below.
- `InfrastructureProvisioningScreen`, `ResidencyReportScreen`, `ScopeDocumentationScreen` and `NoteTagsScreen`. They are in the design, but no web story requires them (`Q-WEB-002`, `GAP-WEB-003`, `GAP-WEB-011`).

**Run-level reconciliation: no figure is open.** The CR record leaves expiry, resend limit and lockout "not decided". The requirements state them, and they are used (`CON-WEB-001`). The figures are code valid 10 minutes, at most 3 resends per 10 minutes, and 5 wrong codes locking the account for 15 minutes (`requirements.md` BR-022, `stories.md` US-006-05).

### Open items raised in this document

Contradiction directions checked: story↔design, story↔requirement, requirement↔requirement, design↔design. Results are `CON-WEB-001` to `CON-WEB-003` and the `GAP-` items below. Design↔design found no conflict: counts and inventories agree. No contradiction touches a data shape, an authorisation rule, or a money/PII path, so none is Critical.

**Gaps**

| ID | Missing | Degrades | Done instead | Closes with |
|---|---|---|---|---|
| `GAP-WEB-001` | Session model. The verify-code success response defines no credential. No refresh or logout operation exists. Whether notes and search need a session is undecided (`Q-BE-003`). `requirements.md` BR-023 holds unfilled template placeholders. | `routing`, `auth-flows`, `authorization-ui` | Protected-route rule, sign-in completion, session expiry and sign-out marked `BLOCKED`. | Backend decision on `Q-BE-003` and a clean BR-023 stating which endpoints need a session. |
| `GAP-WEB-002` | List operations. `stories.md` US-006-01 says the web app calls `GET /notes`. The contract has no such operation, and search requires `q` or tags (`requirements.md` BR-007). No archived-only list exists either. | `routing`, `state-management`, `module-map`, `coverage` | Plain-list and archived-list data sources marked `BLOCKED`. | A backend operation to list active and archived-only notes with pagination. |
| `GAP-WEB-003` | Tag operations. The contract has no remove-tag and no list-distinct-tags operation. How the user picks tags for the US-006-04 filter is not designed. | `module-map`, `routing`, `coverage` | `NoteTagsScreen` not routed. No tag-picking control specified. | Backend operations and a web story. |
| `GAP-WEB-004` | Error detail. Retry-after for 429 and 503, remaining lock time, the 423 re-enable rule, and the field-detail shape of 400 are unstated (backend `GAP-BE-004`). | `auth-flows`, `state-management` | The UI keys on status code alone and shows no countdown. | Backend error-model decision. |
| `GAP-WEB-005` | Design frames. There are none for application chrome (header, navigation, sign-out), message or toast components, error variants of `LoginScreen` and `CodeEntryScreen` (Default only), the sort control, the pin toggle, or a not-found page. `LoginScreen` and `CodeEntryScreen` list no input component. | `routing`, `design-system`, `auth-flows` | Proposals marked `ASM-WEB-015` and `ASM-WEB-016`. | Design frames for each. |
| `GAP-WEB-006` | Type scale, spacing scale, breakpoints, and the layout at each breakpoint. | `design-system`, `responsive` | Both marked `BLOCKED`. | Design input stating them. |
| `GAP-WEB-007` | Supported browsers and versions. | `browser-support` | `BLOCKED`. | An NFR or platform statement. |
| `GAP-WEB-008` | Accessibility standard and conformance level. | `accessibility` | `BLOCKED`. | An NFR or platform statement. |
| `GAP-WEB-009` | Web performance targets and bundle budget. Requirements NFR-001 to NFR-012 are API-level. | `performance` | `BLOCKED`. | An NFR stating web targets. |
| `GAP-WEB-010` | US-006-01 maps to no screen (`stories-map.md`). The design has no note-detail screen, yet US-006-02 navigates "to the saved note". | `routing`, `module-map` | `ASM-WEB-003` and `ASM-WEB-004`. | A design frame or a mapping decision. |
| `GAP-WEB-011` | 30 stories (EP-001 to EP-005) are API or infrastructure stories ("via the API", residency, scope). `stories-map.md` attaches them to web screens, but no web story backs those screens. | `coverage`, `module-map`, `routing` | Listed uncovered with reasons. Screens not routed. | Web stories for those screens, or confirmation they are out of scope for the web app. |
| `GAP-WEB-012` | Sort and count over more than one page. Sorting is client-side with no API parameter (`requirements.md` BR-020). The live count equals the notes rendered (BR-019). Search results are paginated (default page size 20, max 100, `requirements.md` NFR-003). | `state-management` | `ASM-WEB-010`. | A decision on cross-page sort and count. |
| `GAP-WEB-013` | Who may sign in, and note ownership and access (`Q-BE-001`, `Q-BE-002`). Whether another person's note shows as "not found" or "forbidden" is unspecified. | `authorization-ui` | Ownership-dependent behaviour marked `BLOCKED`. | Backend decisions on `Q-BE-001` and `Q-BE-002`. |

**Contradictions** (none is Critical)

| ID | Claims | Consequence of each reading | Reading used |
|---|---|---|---|
| `CON-WEB-001` | The CR record says expiry, resend and lockout values are "not decided; ask". `requirements.md` BR-022, `stories.md` US-006-05 and the contract all state 10 minutes, 3 per 10 minutes, 5 wrong codes, 15 minutes. | Using the CR would leave the error copy undefined. Using the requirements fixes the copy. | Requirements (three agreeing inputs). |
| `CON-WEB-002` | BR-022 and US-006-05 say "3 resends per 10 minutes". `API-AUTH-015365` says "3 requests within 10 minutes". | The first allows one more send than the second. The web app never counts sends, so it reacts to the 429 only. Only the error wording differs. | The web app counts nothing. Wording is deferred to the module spec. |
| `CON-WEB-003` | `requirements.md` BR-012 says archive and unarchive are idempotent and also return a consistent error for an invalid transition. `API-NOTES-810640` lists 409. `stories.md` US-003-01 (API lane) says repeat archive returns 200. | A 200 reading means no web error handling is needed. A 409 reading means a double-click shows an error. | `ASM-WEB-009`: the web app treats 409 as "already in that state" and shows the current state. |

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-001` | Route paths are proposals. No input supplies any. | A route table from product or design. |
| `ASM-WEB-002` | Kebab-case module names are derived from the design labels M1 to M6. | Product naming. |
| `ASM-WEB-003` | `SearchScreen` hosts both the notes list and search results. It has Default, Empty and Error variants and uses `Empty/State` and `Input/Text`. | A design mapping for US-006-01. |
| `ASM-WEB-004` | `NoteEditorScreen` serves create (Empty) and the saved note and edit (Filled). Variant meanings are unverified. | A note-detail frame, or confirmation. |
| `ASM-WEB-005` | Team member is the only role that signs in on this surface. | Backend answers to `Q-BE-001` and `Q-BE-002`. |
| `ASM-WEB-006` | The email is held in memory only between `LoginScreen` and `CodeEntryScreen`. A direct visit to `CodeEntryScreen` redirects to `LoginScreen`. The email is PII. | A storage decision. |
| `ASM-WEB-007` | The client sends a verify request only when the entry is exactly 6 digits. Story text "6 digits-digit" is garbled; the contract says a 6-digit numeric code. A blocked attempt makes no call, so it cannot count toward the lock. | A backend ruling on malformed codes. |
| `ASM-WEB-008` | Lockout and code expiry are learned only from responses (423, 410). There is no client timer or attempt counter, because remaining time is not available (`GAP-WEB-004`). | `GAP-WEB-004` closed. |
| `ASM-WEB-009` | A 409 from archive or unarchive is handled by re-reading the note and showing its state, with no error. | A ruling on `CON-WEB-003`. |
| `ASM-WEB-010` | Sorting and the live count apply to the notes loaded into the list. | A ruling on `GAP-WEB-012`. |
| `ASM-WEB-011` | Sign-in lands on `/notes` (`Q-WEB-001` default). | `Q-WEB-001` answered. |
| `ASM-WEB-012` | There is no time-based cache staleness. Data refreshes on mutation and on navigating to a list. | A caching decision. |
| `ASM-WEB-013` | Network failure, 5xx and 503 show a retryable error state and keep the user's inputs. US-006-01 requires an error state only for list errors. | An error-handling decision. |
| `ASM-WEB-014` | Rows change on the success response. There are no optimistic updates. | An interaction decision. |
| `ASM-WEB-015` | `Input/Text` is used for the email and code fields, and for editor fields. `Button/Primary` is used for submit and resend. The screens' component lists in the inventory omit some of these. | Design frames. |
| `ASM-WEB-016` | `CodeEntryScreen` has a resend control, required by US-006-05 ("an option to request a new code"). | A design frame. |

**Questions** (unattended run: degradable defaults are adopted; none blocking)

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-001` | degradable | After sign-in, return the user to the originally requested route? | No; land on `/notes` (`ASM-WEB-011`). |
| `Q-WEB-002` | degradable | Should `InfrastructureProvisioningScreen`, `ResidencyReportScreen` and `ScopeDocumentationScreen` be routed in the web app? | Not routed. |
| `Q-WEB-003` | degradable | Should `CodeEntryScreen` let the user go back and correct the email? | No control; the user restarts at `/login`. |
| `Q-WEB-004` | degradable | Does lockout also disable the resend control? | No; US-006-05 disables code submission only, and `API-AUTH-015365` stays callable while locked. |
<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. Screen names come from `design-brief.md` (`screens-inventory.md`). The paths are proposals (`ASM-WEB-001`).

| Screen | Proposed path | Layout | Access | Source |
|---|---|---|---|---|
| `LoginScreen` | `/login` | public | Public. Sign-in must be reachable without a session (derived). | US-006-05 |
| `CodeEntryScreen` | `/login/code` | public | Public. Reachable only while an email is held in memory, otherwise redirect to `/login` (`ASM-WEB-006`). | US-006-05 |
| `SearchScreen` (list and search) | `/notes` | app | Protected-route rule below. | US-006-01, US-006-04; `ASM-WEB-003` |
| `NoteEditorScreen` (create) | `/notes/new` | app | Protected-route rule below. | US-006-02 |
| `NoteEditorScreen` (saved note and edit) | `/notes/:id` | app | Protected-route rule below. | US-006-02, US-006-03, US-006-06; `ASM-WEB-004` |
| `ArchivedNotesScreen` | `/archive` | app | Protected-route rule below. Its data source is `BLOCKED` (`GAP-WEB-002`). | `stories-map.md` maps US-006-06 to it |
| `NoteTagsScreen` | none | n/a | Not routed (`GAP-WEB-003`). Design transitions: `ON_CLICK→SearchScreen` twice. | `screens-inventory.md` |
| `InfrastructureProvisioningScreen`, `ResidencyReportScreen`, `ScopeDocumentationScreen` | none | n/a | Not routed (`Q-WEB-002`, `GAP-WEB-011`). `ScopeDocumentationScreen` has `ON_CLICK→SearchScreen` twice. | `screens-inventory.md` |

**Layout nesting.**
- A public layout wraps `/login` and `/login/code`.
- An app layout wraps every other routed screen.
- No frame exists for application chrome (header, navigation, sign-out), and there is no not-found page (`GAP-WEB-005`).

**Protected-route rule: `BLOCKED`.**
- Whether notes and search operations require a session is undecided (`Q-BE-003`, `GAP-WEB-001`).
- `requirements.md` BR-023 says the existing notes and search endpoints require "it", but its Enforcement text still holds unfilled placeholders, so it cannot serve as the rule.
- This is unblocked by the backend decision on `Q-BE-003` and a clean BR-023.

**Unauthenticated redirect.**
- The only sign-in entry is `LoginScreen`, so if the app layout is protected, an unauthenticated visit redirects to `/login` (derived, conditional on the rule above).
- After sign-in the user lands on `/notes`. Returning to the requested route is `Q-WEB-001`, default no.

**Navigation known from the inputs.**
- Request-code success shows `CodeEntryScreen` (US-006-05).
- Create success navigates to the saved note (US-006-02).
- Archive and restore act on an open note, and restore is offered on an archived note (US-006-06).
- The design lists 4 flows but does not enumerate them (`design-brief.md`, Counts), so no further navigation is asserted.
<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DRAFT-UNVERIFIED -->
## Module map

Module labels M1 to M6 and screen titles come from `design-brief.md`. Kebab-case names are derived (`ASM-WEB-002`). Functional requirement IDs (`WEB-<MODULE>-FR-nnn`) are minted in the module specs, not in this document.

| Module | Design label | Screens | Routes owned | Web stories | Contract operations |
|---|---|---|---|---|---|
| `login` | M6 | `LoginScreen`, `CodeEntryScreen` | `/login`, `/login/code` | US-006-05 | `API-AUTH-015365`, `API-AUTH-592570` |
| `notes` | M1 | `NoteEditorScreen` | `/notes/new`, `/notes/:id` | US-006-02, US-006-03; archive and pin of US-006-06 | `API-NOTES-232390`, `API-NOTES-045877`, `API-NOTES-847551`, `API-NOTES-810640`, `API-NOTES-917869` |
| `search` | M2 | `SearchScreen` | `/notes` | US-006-01, US-006-04; pin toggle of US-006-06 | `API-SEARCH-580505`, `API-NOTES-847551`. The list operation is absent (`GAP-WEB-002`). |
| `archive` | M3 | `ArchivedNotesScreen` | `/archive` | restore of US-006-06 | `API-NOTES-917869`. The archived-list operation is absent (`GAP-WEB-002`). |
| `tags` | M2 | `NoteTagsScreen` | none | none | `API-NOTES-359413` exists. Remove and list are absent (`GAP-WEB-003`). No module spec until closed. |
| `residency` | M4 | `InfrastructureProvisioningScreen`, `ResidencyReportScreen` | none | none | No module spec until `Q-WEB-002` is answered. |
| `scope-docs` | M5 | `ScopeDocumentationScreen` | none | none | No module spec until `Q-WEB-002` is answered. |

**Dependency direction** (A → B means A depends on B):
- `notes → search`. A successful note mutation invalidates cached search results (`web-app.state-management`).
- `search → notes` and `archive → notes`. Opening a note navigates to `/notes/:id`.
- `login` depends on no module.
- Every module except `login` waits on the session decision (`GAP-WEB-001`).
- These are navigation and cache-invalidation links only, not code dependencies.
<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state-management library, so none is specified. The boundary and policy below are library-independent.

**Server state** (owned by `notes_api`, never edited locally):
- A note by id (`API-NOTES-045877`).
- Search results, keyed by the full parameter set of `API-SEARCH-580505`.
- The active and archived-only lists: `BLOCKED` (`GAP-WEB-002`).

**Client state** (browser-owned, never sent to the API):
- The sort selection: Newest first, Oldest first or Title A-Z. The default is Newest first (US-006-01). The API receives no sort parameter (`requirements.md` BR-020).
- The search text and selected tags until submitted.
- The email held between `LoginScreen` and `CodeEntryScreen`, in memory only (`ASM-WEB-006`).
- Editor form values and login error presentation.

**Cache and invalidation policy.**
1. On a successful mutation, replace the cached note-by-id entry with the note the response returns.
2. After any successful create, edit, pin or unpin, archive or unarchive, invalidate all cached search results and lists. These can change a note's membership in, or order within, a result set (derived from `API-SEARCH-580505`, which excludes archived notes by default and orders pinned first).
3. There is no time-based staleness. Data refreshes on mutation and on navigation to a list (`ASM-WEB-012`).
4. A failed mutation never changes cached data. US-006-03 requires this for a 404 on save.
5. Rows move or change on the success response, not optimistically (`ASM-WEB-014`).
6. Network failure, 5xx and 503 show a retryable error state that keeps the user's inputs (`ASM-WEB-013`). Retry-after values are unstated (`GAP-WEB-004`).

**Sort and count over pages.** Sorting and the live count apply to the notes loaded into the list (`ASM-WEB-010`, `GAP-WEB-012`).

**Invariants.**
- `INV-WEB-001`: Pinned notes render above unpinned notes under every sort, filter and search (`requirements.md` BR-021).
- `INV-WEB-002`: The live count equals the number of notes rendered (`requirements.md` BR-019).
- `INV-WEB-003`: The web app adds no sort parameter to any API call (`requirements.md` BR-020).
- `INV-WEB-004`: The web app never calls `API-SEARCH-580505` with neither a keyword nor a tag (`requirements.md` BR-007). A whitespace-only keyword counts as empty.
- `INV-WEB-005`: A 404 on save never applies local changes (`stories.md` US-006-03).
<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

**Login (specifiable now).**
1. `LoginScreen`: the user enters an email and submits. The client checks only that it is non-empty, and the server's 400 handles format.
2. The web app calls `API-AUTH-015365`. On 202 it shows `CodeEntryScreen` (US-006-05) and holds the email in memory (`ASM-WEB-006`).
3. `CodeEntryScreen`: the user enters the code and submits. The client sends it only when it is exactly 6 digits (`ASM-WEB-007`).
4. The web app calls `API-AUTH-592570`. A success response signs the user in. What that means is `BLOCKED` below.
5. A pending request shows `Button/Primary` in its Loading state and cannot be activated twice (`ASM-WEB-015`). This avoids duplicate requests that would consume the resend limit.

**Status → UI state.** Meanings are in the contract and are not restated.

| Operation | Status | UI state |
|---|---|---|
| `API-AUTH-015365` | 202 | Show `CodeEntryScreen`. |
| `API-AUTH-015365` | 400 | Field-level error on the email (shape per `GAP-WEB-004`). |
| `API-AUTH-015365` | 429 | Resend-limit error. On `CodeEntryScreen` the resend control is disabled (US-006-05: no more resends offered). |
| `API-AUTH-015365` | 500 | Retryable failure message. |
| `API-AUTH-592570` | 200 | Signed in (`BLOCKED`). |
| `API-AUTH-592570` | 400 | Field-level error on the code. |
| `API-AUTH-592570` | 401 | Wrong-code message. |
| `API-AUTH-592570` | 409 | Code-already-used message with the option to request a new code. |
| `API-AUTH-592570` | 410 | Expired-code error with the option to request a new code (US-006-05). |
| `API-AUTH-592570` | 423 | Lockout error stating the account is locked for 15 minutes, and code submission disabled (US-006-05). |
| `API-AUTH-592570` | 500 | Retryable failure message. |

**Lockout and expiry rules.**
- Lockout and expiry are derived only from the 423 and 410 responses. The client keeps no attempt counter or timer (`ASM-WEB-008`).
- The resend control is not disabled by lockout (`Q-WEB-004`).
- The figures shown are those in US-006-05 and `requirements.md` BR-022 (`CON-WEB-001`).
- No countdown is shown, because remaining lock time is not available (`GAP-WEB-004`).

**`BLOCKED` until `GAP-WEB-001` closes:**
- What a successful verify-code gives the browser: credential type, lifetime and transport.
- Refresh behaviour.
- Session expiry, including expiry mid-flow while the user is on a protected route.
- Logout.
- The protected-route redirect behaviour on 401.

Unblocked by: the backend session decision (`Q-BE-003`) and a clean `requirements.md` BR-023.

**Design coverage.** `LoginScreen` and `CodeEntryScreen` have a Default variant only. Error presentation is a proposal until error variants and a message component exist (`GAP-WEB-005`).
<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DRAFT-UNVERIFIED -->
## Authorization UI

**Roles that can sign in to this surface.** One: Team member (`ASM-WEB-005`). The backend's matrix is `BLOCKED` (`backend.auth-model`), so no role list was inherited.

| Role | Visible navigation | Enabled actions | Source |
|---|---|---|---|
| Not signed in | `/login`, `/login/code` | request a code, verify a code, resend a code | US-006-05 |
| Team member | Notes `/notes`, New note `/notes/new`, Archive `/archive` | list and search; create; edit; archive, restore, pin or unpin | US-006-01 to US-006-06 |

**Not decided.**
- Whether every action applies to every note, or only notes the user owns, is unknown (`Q-BE-002`).
- How "not allowed" versus "does not exist" is shown is also unknown (`GAP-WEB-013`).
- Whether a signed-out user sees any routed screen other than the two login screens depends on `GAP-WEB-001`.
- The navigation control itself has no design frame (`GAP-WEB-005`).

**Roles excluded, and why.** Excluded: Developer, Operations engineer, Platform operations engineer, Compliance auditor, Backend developer, API tech lead, QA engineer and Product owner. They are the actors of EP-001 to EP-005 stories (`stories.md`), which describe API or infrastructure behaviour. No web route or EP-006 story serves them. Listing them would be speculation about another surface (I-1).
<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

Every name below comes from `design-brief.md`. Figma is `not-connected`, so all are unverified, and no values were supplied. No component-to-token binding was supplied (component bodies are omitted from the handoff).

**Tokens (71, IDs only)** — `tokens-index.md`:
- `UI/border/` default, disabled, error, focus, input, input-focus, strong, subtle, success, warning.
- `UI/color/` black, disabled, error, error-subtle, hover-destructive, hover-ghost, hover-primary, hover-secondary, hover-success, info, info-subtle, overlay, primary, primary-hover, primary-subtle, success, success-subtle, warning, warning-subtle, white.
- `UI/icon/` active, default, disabled, error, info, inverse, on-primary, subtle, success, warning.
- `UI/surface/` button/destructive, button/ghost, button/primary, button/primary-hover, button/secondary, button/success, card, default, disabled, input, input-focus, overlay, secondary, tertiary, toast/error, toast/info, toast/success, toast/warning.
- `UI/text/` disabled, error, info, inverse, link, link-hover, on-primary, placeholder, primary, secondary, success, tertiary, warning.

**Components (3)** — `components-inventory.md`:

| Component | Role | Variants | States |
|---|---|---|---|
| `Button/Primary` | action | 12 | Default, Pressed, Loading, Disabled |
| `Empty/State` | content | 1 | Default |
| `Input/Text` | entry | 5 | Default, Focused, Filled, Error, Disabled |

**Components used per screen and screen variants** — `screens-inventory.md`:

| Screen | Components listed | Variants |
|---|---|---|
| `LoginScreen` | `Button/Primary` | Default |
| `CodeEntryScreen` | `Button/Primary` | Default |
| `NoteEditorScreen` | `Empty/State`, `Input/Text` | Default, Empty, Error, Filled |
| `SearchScreen` | `Empty/State`, `Input/Text` | Default, Empty, Error |
| `ArchivedNotesScreen` | `Button/Primary`, `Empty/State` | Default, Empty, Error, Filled |
| `NoteTagsScreen` | `Input/Text` | Default, Error, Filled |
| `InfrastructureProvisioningScreen` | none | Default, Error |
| `ResidencyReportScreen`, `ScopeDocumentationScreen` | `Button/Primary` | Default, Error |

**Gaps in the design.**
- No sort control, pin icon or toggle, message or toast component, or navigation component exists (`GAP-WEB-005`).
- The login screens list no input component (`ASM-WEB-015`).

**Type scale, spacing scale: `BLOCKED`.** No input states them (`GAP-WEB-006`). Unblocked by design input stating them. No visual value is hardcoded in any spec; only token references are used.
<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive

**Blocked.** No input states breakpoints or the layout change at any breakpoint (`GAP-WEB-006`). `design-brief.md` lists none.

Unblocked by: design input stating the breakpoints and the layout at each.
<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**Blocked.** No input names supported browsers or versions (`GAP-WEB-007`). `platform.md` names React 18 + Vite and AWS hosting only.

Unblocked by: an NFR or platform statement listing browsers and versions.
<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** the run did not produce this section. Nothing has been written in its place, and no claim about it should be read into this document.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: BLOCKED -->
## Performance

**Blocked.** No input states a web bundle budget or a load-time target (`GAP-WEB-009`). Requirements NFR-001 to NFR-012 are API-level. The only payload bound in the inputs is the search page size, default 20 and maximum 100 (`requirements.md` NFR-003). It does not establish a largest-payload route for the web app.

Unblocked by: an NFR stating web performance targets and a bundle budget.
<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DRAFT-UNVERIFIED -->
## Coverage

Requirement IDs are minted in the module specs, which this run does not write. This table maps each story to the owning module, the contract operations, and the governing `requirements.md` rules.

**EP-006 (served by this surface)**

| Story | Module | Operations | Governing rules | Status |
|---|---|---|---|---|
| US-006-01 | `search` | list operation absent | BR-019, BR-020, BR-021 | Blocked on `GAP-WEB-002`. Sort, count, pin icon, empty and error states are specifiable. `GAP-WEB-010`. |
| US-006-02 | `notes` | `API-NOTES-232390` | none beyond the story | Covered. The destination "saved note" rests on `ASM-WEB-004`. Inline error shape per `GAP-WEB-004`. |
| US-006-03 | `notes` | `API-NOTES-847551`, `API-NOTES-045877` | `INV-WEB-005` | Covered. The story says PUT/PATCH; the contract offers PATCH. Field highlighting per `GAP-WEB-004`. |
| US-006-04 | `search` | `API-SEARCH-580505` | BR-007, BR-019, BR-020, BR-021 | Partial. Tag selection source per `GAP-WEB-003`. |
| US-006-05 | `login` | `API-AUTH-015365`, `API-AUTH-592570` | BR-022 | Partial. Sign-in completion `BLOCKED` (`GAP-WEB-001`). Lockout, expiry and resend errors are specified. |
| US-006-06 | `notes`, `search`, `archive` | `API-NOTES-810640`, `API-NOTES-917869`, `API-NOTES-847551` | BR-021 | Partial. The archived view needs `GAP-WEB-002`. `CON-WEB-003`. |

**EP-001 to EP-005 (not served by this surface: API or infrastructure stories, `GAP-WEB-011`)**

| Story | Relation to this surface |
|---|---|
| US-001-01 | Behaviour consumed by US-006-02. |
| US-001-02 | Behaviour consumed by US-006-03. |
| US-001-03 | Behaviour consumed by US-006-02 and US-006-03. |
| US-001-04 | Would be consumed by US-006-01; no such operation in the contract (`GAP-WEB-002`). |
| US-001-05 | Behaviour consumed by US-006-02 and US-006-03. |
| US-001-06 | Server timestamps shown per US-006-02. |
| US-002-01 | No web story; no tag UI (`GAP-WEB-003`). |
| US-002-02 | No web story; operation absent (`GAP-WEB-003`). |
| US-002-03 | Behaviour consumed by US-006-04. |
| US-002-04 | Behaviour consumed by US-006-04. |
| US-002-05 | No web story; operation absent (`GAP-WEB-003`). |
| US-002-06 | Pagination consumed by US-006-04 (`GAP-WEB-012`). |
| US-003-01 | Behaviour consumed by US-006-06. |
| US-003-02 | Behaviour consumed by US-006-06. |
| US-003-03 | Would be consumed by US-006-06; operation absent (`GAP-WEB-002`). |
| US-003-04 | Would be consumed by US-006-01; operation absent (`GAP-WEB-002`). |
| US-003-05 | Edit allowed on archived notes; consumed by US-006-03. |
| US-003-06 | No web story. |
| US-004-01 to US-004-06 | Infrastructure and residency; no web story (`Q-WEB-002`). |
| US-005-01 to US-005-06 | API scope and compliance; no web story. |

Every one of the 36 supplied story IDs appears above: US-001-01 to US-001-06, US-002-01 to US-002-06, US-003-01 to US-003-06, US-004-01 to US-004-06, US-005-01 to US-005-06, and US-006-01 to US-006-06.
<!-- /SECTION: web-app.coverage -->
<!-- SECTION: web-app.lane-needs | status: VERIFIED -->
## Lane needs

### Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-552660` | `api_contract` | Define the session model: what a successful verify-code response gives the browser (credential type, lifetime, transport), refresh and logout operations, and which notes and search endpoints require a session. Also supply a clean BR-023 without template placeholders. | The protected-route rule, sign-in completion, session expiry and sign-out are BLOCKED without it. | yes | `web-app.routing`, `web-app.auth-flows`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-460580` | `api_contract` | Add operations to list active notes and to list archived-only notes, with pagination, and without requiring a keyword or tag. | US-006-01 and the archived view in US-006-06 need list data sources. The contract has none, and search requires q or tags. | yes | `web-app.routing`, `web-app.state-management`, `web-app.module-map`, `web-app.coverage` |
| `HANDOFF-WEBAPP-863088` | `api_contract` | Add a remove-tag operation and a list-distinct-tags operation, and supply a web story saying how the user picks tags for the filter. | NoteTagsScreen cannot be routed and the US-006-04 tag-picking control cannot be specified. | no | `web-app.module-map`, `web-app.routing`, `web-app.coverage` |
| `HANDOFF-WEBAPP-457990` | `api_contract` | State the error model: retry-after for 429 and 503, remaining lock time on 423, the 423 re-enable rule, and the field-detail shape of 400. | The UI keys on status code only and shows no countdown or field-level detail. | no | `web-app.auth-flows`, `web-app.state-management` |
| `HANDOFF-WEBAPP-712561` | `screen` | Supply design frames for application chrome (header, navigation, sign-out), message and toast components, error variants of LoginScreen and CodeEntryScreen, the sort control, the pin toggle, a not-found page, and an input component on the login screens. | These parts exist only as proposals marked ASM-WEB-015 and ASM-WEB-016. | no | `web-app.routing`, `web-app.design-system`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-854794` | `screen` | State the type scale, spacing scale, breakpoints and the layout at each breakpoint. | The responsive section is BLOCKED and the design-system scales are BLOCKED. | no | `web-app.design-system`, `web-app.responsive` |
| `HANDOFF-WEBAPP-593210` | `nfr` | List the supported browsers and versions for the web app. | The browser-support section is BLOCKED. | no | `web-app.browser-support` |
| `HANDOFF-WEBAPP-223037` | `nfr` | State the accessibility standard and conformance level for the web app. | The accessibility section is BLOCKED. | no | `web-app.accessibility` |
| `HANDOFF-WEBAPP-467885` | `nfr` | State web performance targets (load time) and a bundle budget. | The performance section is BLOCKED. NFR-001 to NFR-012 are API-level. | no | `web-app.performance` |
| `HANDOFF-WEBAPP-329761` | `screen` | Supply a design frame for note detail, or decide which screen US-006-01 maps to and where US-006-02 navigates to the saved note. | US-006-01 maps to no screen and the design has no note-detail screen. SearchScreen and NoteEditorScreen are assumed. | no | `web-app.routing`, `web-app.module-map` |
| `HANDOFF-WEBAPP-121536` | `story` | Supply web stories for the screens InfrastructureProvisioningScreen, ResidencyReportScreen, ScopeDocumentationScreen and NoteTagsScreen, or confirm that they are out of scope for the web app. | 30 stories in EP-001 to EP-005 are API or infrastructure stories, so these screens have no web story and are not routed. | no | `web-app.coverage`, `web-app.module-map`, `web-app.routing` |
| `HANDOFF-WEBAPP-856732` | `business_rule` | Decide how sorting and the live count behave when results span more than one page, since sorting is client-side and results are paginated. | Sort and count currently apply only to the notes loaded into the list (ASM-WEB-010). | no | `web-app.state-management` |
| `HANDOFF-WEBAPP-838983` | `business_rule` | Decide who may sign in, whether notes are owned per user, and whether another person's note shows as not found or forbidden (Q-BE-001, Q-BE-002). | Ownership-dependent behaviour and the not-allowed versus does-not-exist display are BLOCKED. | no | `web-app.authorization-ui` |
<!-- /SECTION: web-app.lane-needs -->
