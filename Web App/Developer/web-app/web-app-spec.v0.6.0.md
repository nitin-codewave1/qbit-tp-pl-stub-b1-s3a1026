---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.6.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-002-01, web_app:US-002-02, web_app:US-002-03, web_app:US-002-04, web_app:US-002-05, web_app:US-002-06, web_app:US-003-01, web_app:US-003-02, web_app:US-003-03, web_app:US-003-04, web_app:US-003-05, web_app:US-003-06, web_app:US-004-01, web_app:US-004-02, web_app:US-004-03, web_app:US-004-04, web_app:US-004-05, web_app:US-004-06, web_app:US-005-01, web_app:US-005-02, web_app:US-005-03, web_app:US-005-04, web_app:US-005-05, web_app:US-005-06, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03, web_app:US-006-04, web_app:US-006-05, web_app:US-006-06]
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DRAFT-UNVERIFIED -->
## Overview

**Purpose.** The Web App is the browser client for `notes_api` (React 18 + Vite, hosted on AWS) [platform.md]. A Team member uses it to:
- sign in with an email one-time code;
- create, view, edit, search, archive, restore and pin notes;
- sort the notes list.

None of this requires calling the API directly. [stories.md US-006-01..06]

**Primary actor.** Team member. It is the only actor any EP-006 story names. [stories.md EP-006]

**Basis of this run.**
- **Run.** Bootstrap run for change request CR-130, lane `web_app`, consumer side. [feedback.md CR-130; RUN-CONTEXT `run_mode`]
- **What CR-130 adds.** A login screen and a code-entry screen that call the request-code and verify-code operations and show lockout, expiry and resend-limit errors. [feedback.md CR-130; stories.md US-006-05; requirements.md BR-022]
- **`spec_mode`.** `FULL-FROM-BRIEF`: the design brief is present and Figma is not connected. Every screen name, component name and token name here is an unverified claim taken from design-brief.md. [RUN-CONTEXT `inputs.figma_mcp`]
- **Contract pin.** `sha256:a6f2ca3343de`. [RUN-CONTEXT `upstream.contract_pin`] Schemas are referenced by ID and never restated here.
- **Baseline.** The approved revision is visible in feedback.md only as truncated excerpts (`ASM-WEB-013`). Every section is rewritten from the current inputs.

**Contract operations this surface uses.** IDs are cited as the contract supplies them, with no `@version` suffix (`GAP-WEB-015`). [backend-api.md `backend.api-surface`]

| Area | Operation IDs |
|---|---|
| Sign-in | `API-AUTH-707260` (request code), `API-AUTH-692907` (verify code) |
| Notes | `API-NOTES-232390` (create), `API-NOTES-045877` (get by id), `API-NOTES-847551` (partial edit, including pin and unpin), `API-NOTES-810640` (archive), `API-NOTES-917869` (unarchive), `API-NOTES-359413` (tag add), `API-NOTES-150096` (tag remove) |
| Search | `API-SEARCH-580505` |

**Upstream state that touches this surface.** These are open in backend-api.md. This spec builds on the backend's proposals and marks each dependent part `DRAFT-UNVERIFIED` or `BLOCKED`.
- **Credential returned by a successful verification:** `Q-BE-012`, `GAP-BE-302`.
- **Sign-in eligibility and note ownership:** `Q-BE-003`, `CON-BE-003`, `Q-BE-001`. The login-eligibility rule is corrupted upstream and note access is open.
- **Error envelope and code names:** proposals only (`ASM-BE-300`, `ASM-BE-301`). Which code is returned when several verify refusals apply at once is `GAP-BE-601`.
- **Notes list:** the contract has no list operation. The backend records the web request for one as unanswerable without a human decision. [backend-api.md `backend.lane-needs`, `HANDOFF-WEBAPP-200601`]

**Changes against the approved revision, driven by the current inputs.**
- **Screens.** The design brief now names `ArchivedNotesScreen` and `ApiScopeDocsScreen`. It has no note-detail or note-lifecycle screen.
- **Error code names.** The current backend registry is used (`OTP_RATE_LIMITED` for the resend limit, `VALIDATION_FAILED` for invalid input).
- **Verify refusals.** All four verify refusals now carry one shared status, so the error code is the only discriminator (`INV-WEB-010`).
- **Contract pin.** The pin differs from the one the approved revision cited.
- **Numbering.** Identifier numbering restarts in this bootstrap run. Identifiers of the approved revision are not carried.

**Corrupted input text.** Several supplied sentences carry an injected unrelated clause:
- requirements.md BR-022 ("3 resends times", "5 attempts wrong codes");
- requirements.md NFR-002;
- stories.md US-002-02;
- stories.md US-006-05 ("6 digits-digit", "5 attempts times").

Numeric limits are used only where backend-api.md states the same value: 6-digit numeric code, 10-minute expiry, 3 code requests per 10 minutes, 5 wrong codes, 15-minute lock. [backend-api.md `backend.overview`] The status US-002-02 meant to state is unknown and is not used.

**Run status.** `CON-WEB-001` is Critical: it changes an archive/restore state transition. Under core rule 8 and 10.1 this maps to `HALT-CRITICAL-CONTRADICTION`, and the credential question upstream (`Q-BE-012`) is blocking. The orchestrator should hold the run. The document is emitted complete so the human can resolve it. The dependent parts are `BLOCKED` in place: archive or restore of a note already in the target state, and credential storage.

**Out of scope**, each cited:
- **Operator, compliance and administration functions.** They belong to other surfaces. [core I-1]
- **Residency report and API scope documentation.** `ResidencyReportScreen` (M4) and `ApiScopeDocsScreen` (M5) serve stories whose actors are not Team members and which describe API or process behaviour. No input assigns them to this surface. [design-brief.md screens; stories.md EP-004, EP-005; `CON-WEB-005`, `GAP-WEB-021`]
- **Note deletion.** The contract has no delete operation and no story asks for one. [backend-api.md `backend.api-surface`]
- **Attachments and file upload.** Create and edit reject attachment payloads, so no attachment control is built. [requirements.md BR-016]
- **Real-time or push updates.** [requirements.md BR-017] "Live" in the result count means recomputed whenever the displayed set changes, not server push.
- **Mobile-specific behaviour.** [requirements.md BR-018]
- **Offline use.** No input requires it.
- **Tag add and remove controls and tag discovery.** The contract has tag-add and tag-remove operations, but no web story or screen asks for them (`GAP-WEB-003`).

### Open-item register

Each item is defined here once and referenced elsewhere by ID.

| ID | Kind | Statement |
|---|---|---|
| `CON-WEB-001` | Critical | stories.md US-003-01 and US-003-02 say archiving an archived note, and unarchiving an active note, return 200 idempotently with no error. requirements.md BR-012 says the operations are idempotent and "return a consistent error for invalid state transitions (e.g., archiving an already-archived note)". The backend lists a conflict for archive only, with status unstated (backend-api.md `GAP-BE-309`). If the stories govern, the client treats the response as success and shows the current state. If BR-012 governs, the client shows a state-conflict message. Handling for a note already in the target state is `BLOCKED` until a human chooses. |
| `CON-WEB-002` | non-critical | requirements.md BR-019 defines the live count as the number of notes rendered. stories.md US-006-04 says "the number of matches". With paginated search the two differ. Reading used: BR-019, the rendered count. |
| `CON-WEB-003` | non-critical | requirements.md BR-020 sorts client-side, but search is paginated (requirements.md NFR-003). Client sort orders only the pages loaded. stories.md US-002-03 also says results are ranked by relevance, which a default "Newest first" client sort overrides. Reading used: sort the loaded set, pinned first. |
| `CON-WEB-004` | non-critical | requirements.md BR-022 and stories.md US-006-05 limit "resends" to 3 per 10 minutes. The backend counts every code request, including the first, as one of 3 (backend-api.md `CON-BE-011`). Reading used: the server is authoritative and the client counts nothing (`INV-WEB-011`). |
| `CON-WEB-005` | non-critical | design-brief.md stories-map assigns every story from US-001-01 to US-005-06 to a screen or to none, including US-004-* to `ResidencyReportScreen` and US-005-* to `ApiScopeDocsScreen`. Those stories name API consumers, compliance or engineering actors, and describe API or process behaviour. Reading used: the stories govern. Picking the brief would add routes whose actor and access rule no input defines. |
| `CON-WEB-006` | non-critical | The change request is labelled CR-130 in feedback.md and CR-129 in the approved revision. The backend reports further labels (backend-api.md `CON-BE-019`). Reading used: CR-130, the current input. |
| `GAP-WEB-001` | gap | The contract has no operation that lists active notes (pinned first) and none that lists archived notes only. Search cannot stand in: it requires a keyword or tag, and `include_archived` mixes active and archived notes. stories.md US-006-01 names `GET /notes`. The list, live count, sort, pin toggle on rows, and archived view are `BLOCKED`. Closing artefact: backend contract operations for both lists. The backend needs to add them. |
| `GAP-WEB-002` | gap | No design exists for the authenticated shell and navigation, the notes list, the note view, the sort control, the live result count, the pin toggle and pin icon, or the tag filter control. design-brief.md has three components only (`Button/Primary`, `Empty/State`, `Input/Text`). Closing artefact: frames for these elements. |
| `GAP-WEB-003` | gap | No web story or screen covers tag add or remove (US-002-01, US-002-02), and the contract has no operation that lists distinct tags (US-002-05). Closing artefact: a decision on whether the web serves tags, plus stories and frames. |
| `GAP-WEB-004` | gap | What a successful verification returns (body token, cookie or other), its lifetime, and whether any refresh operation exists are all unanswered (`Q-BE-012`, `GAP-BE-302`). Credential storage and attachment are `BLOCKED`. Closing artefact: a backend decision on the credential. [backend-api.md `HANDOFF-WEBAPP-743710`] |
| `GAP-WEB-005` | gap | The error code names used for display are proposals (`ASM-BE-301`). Which code is returned when several verify refusals apply at once is open (`GAP-BE-601`). Closing artefact: a final error-code registry. [backend-api.md `HANDOFF-WEBAPP-163212`] |
| `GAP-WEB-006` | gap | The contract returns no remaining lock time, and the moment submission re-enables is not stated. The lockout error cannot show a countdown. Closing artefact: a backend field or a stated rule. |
| `GAP-WEB-007` | gap | `LoginScreen` and `CodeEntryScreen` each have a single variant. They have no error variant, no `Input/Text` mapping and no resend control. Closing artefact: frames for these. |
| `GAP-WEB-008` | gap | The sort basis for "Newest" (created or updated), title collation and case, tie-breaks, and whether the selection persists across reloads are not stated. Closing artefact: a sort rule. |
| `GAP-WEB-009` | gap | Optimistic behaviour for pin, and whether an archived note may be pinned, are not stated. Closing artefact: a pin rule. |
| `GAP-WEB-010` | gap | Empty-state and error copy are not supplied, and screen-level loading is not designed. Closing artefact: copy deck and loading frames. |
| `GAP-WEB-011` | gap | Pagination controls for search results are not designed. Closing artefact: frames. |
| `GAP-WEB-012` | gap | No sign-out operation exists in the contract, and no story or frame covers sign-out. Whether the server invalidates a credential is unstated. Closing artefact: a sign-out rule from backend and design. |
| `GAP-WEB-013` | gap | Whether unsaved editor content survives a redirect after a `401` is not stated. Closing artefact: a session rule. |
| `GAP-WEB-014` | gap | Who may sign in, whether notes are per-user or shared, and whether an inaccessible note returns 403 or 404 are unspecified (`Q-BE-003`, `CON-BE-003`, `Q-BE-001`). Closing artefact: the answers upstream. |
| `GAP-WEB-015` | gap | Contract IDs carry no `@version` suffix, so version pinning is by `contract_pin` only. Closing artefact: a versioned ID scheme in the contract. |
| `GAP-WEB-016` | gap | The existing login method is unnamed (`GAP-BE-085`), so `LoginScreen` is specified for the email one-time-code only. Closing artefact: a statement of the existing method. |
| `GAP-WEB-017` | gap | The brief supplies token IDs only: no token values, type scale or spacing scale. Closing artefact: the token bodies and type and spacing scales. |
| `GAP-WEB-018` | gap | No supported-browser matrix is supplied. Closing artefact: a browser matrix. |
| `GAP-WEB-019` | gap | No accessibility target is supplied. Closing artefact: a target standard. |
| `GAP-WEB-020` | gap | No bundle or first-load budget is supplied. Closing artefact: a performance budget. |
| `GAP-WEB-021` | gap | No input assigns stories EP-004 and EP-005, or the screens `ResidencyReportScreen` and `ApiScopeDocsScreen`, to a surface. Closing artefact: a decision on which surface owns them. |
| `GAP-WEB-022` | gap | US-003-06 describes API-level validation of malformed ids, and the contract documents no 400 for archive or unarchive. It has no web-visible behaviour. Closing artefact: a decision that the web does not serve it. |
| `GAP-WEB-023` | gap | `NoteEditorScreen` has two `ON_CLICK` transitions to `SearchScreen`, and the triggers are not named. [design-brief.md screens-inventory] Closing artefact: the trigger elements. |
| `GAP-WEB-024` | gap | stories.md US-002-04 filters by "one or more tags". The contract's search takes a single tag filter. Multi-tag filtering is not representable. Closing artefact: a decision on single or multiple tag filters. |
| `ASM-WEB-001` | assumption | Route paths are proposed. Confirm with the router design. |
| `ASM-WEB-002` | assumption | Unauthenticated users go to `/login`, keeping an in-app return target. After sign-in they go to the return target, otherwise to `/notes`. Confirm with a stated auth routing rule. |
| `ASM-WEB-003` | assumption | `NoteEditorScreen` serves create (Empty variant) and edit (Filled variant). `ArchivedNotesScreen` hosts the archived view and the archive and restore actions, following the stories-map. `Input/Text` is the keyword field on `SearchScreen`. Confirm by inspecting the frames. |
| `ASM-WEB-004` | assumption | "Newest" means creation time, descending. Confirm with the requirement owner. |
| `ASM-WEB-005` | assumption | The entered email is personal data and is held in memory only (ASM-BE-019). Confirm with a data-protection policy (`GAP-BE-017`). |
| `ASM-WEB-006` | assumption | Cached server state is discarded on sign-in and on a `401` that ends the session. Confirm with a session rule. |
| `ASM-WEB-007` | assumption | The client validates only that the email is non-empty (`GAP-BE-035`). Confirm with a validation rule. |
| `ASM-WEB-008` | assumption | The client maps errors using the backend's proposed `code` registry. Confirm with the final error model. |
| `ASM-WEB-009` | assumption | `OTP_CODE_USED` is handled like expired. Confirm with the story owner. |
| `ASM-WEB-010` | assumption | The live count is derived client-side from the displayed set. A server total would need a backend field. |
| `ASM-WEB-011` | assumption | The request-new-code option is hidden while the account is locked, because a new code does not lift the lock (`INV-BE-005`). Confirm with a lockout design. |
| `ASM-WEB-012` | assumption | The client requests a page size of 20 and never above 100 (requirements.md NFR-003), using the page parameters of `API-SEARCH-580505`. The backend has not confirmed these limits (`GAP-BE-500`). |
| `ASM-WEB-013` | assumption | The approved revision is the baseline, but its supplied excerpts are truncated. Current inputs govern everywhere, and the contract pin differs from the approved one. Confirm by supplying the full approved documents. |
| `ASM-WEB-014` | assumption | Route-level code splitting is proposed along the boundaries of the route table. Confirm with a performance budget. |
<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. The paths below are proposed (`ASM-WEB-001`). Screen names and modules come from the design brief. [design-brief.md screens-inventory]

| Route | Screen (module) | Protected | Basis |
|---|---|---|---|
| `/login` | `LoginScreen` (M6) | No | US-006-05 |
| `/login/code` | `CodeEntryScreen` (M6) | No, but needs an email held in memory (see below) | US-006-05; stories-map |
| `/notes` | notes list: no design screen | Yes | US-006-01. `BLOCKED`: `GAP-WEB-001`, `GAP-WEB-002` |
| `/notes/new` | `NoteEditorScreen` (M1), Empty variant | Yes | US-006-02; `ASM-WEB-003` |
| `/notes/:id` | note view: no design screen | Yes | US-006-02 ("navigates to the saved note"), US-006-06 ("an active note is open"). `GAP-WEB-002` |
| `/notes/:id/edit` | `NoteEditorScreen` (M1), Filled variant | Yes | US-006-03; `ASM-WEB-003` |
| `/notes/search` | `SearchScreen` (M2) | Yes | US-006-04 |
| `/notes/archived` | `ArchivedNotesScreen` (M3) | Yes | stories-map US-003-01..03, US-003-06. `BLOCKED`: `GAP-WEB-001` |

**`INV-WEB-001`.** The static segments `new`, `search` and `archived` must match before `/notes/:id`. Otherwise the router reads them as note identifiers.

**Layout nesting.** Two layouts are assumed:
- **Public layout:** `/login` and `/login/code`.
- **Authenticated shell:** every other route.

No shell, header or navigation component exists in the design inventory (`GAP-WEB-002`).

**Protected-route rule.** Every route except `/login` and `/login/code` requires an authenticated session. A `401` from a protected operation is handled as described in `web-app.auth-flows`.

**Unauthenticated redirect.** Redirect to `/login` and keep the requested in-app path as the return target. After a successful verification, go to the return target if present, otherwise to `/notes`. The return target accepts in-app paths only. [`ASM-WEB-002`]

**`/login/code` entry rule.** `CodeEntryScreen` is reachable only after a successful request-code call in the same session. If it is opened directly with no email held in memory, redirect to `/login`. [`ASM-WEB-005`]

**Flow edges.**
- `NoteEditorScreen` has two `ON_CLICK` transitions to `SearchScreen`. They are in scope, but the trigger elements are unnamed (`GAP-WEB-023`).
- `ApiScopeDocsScreen` has two `ON_CLICK` transitions to `SearchScreen`. The source screen is out of scope, so those edges are not implemented (`GAP-WEB-021`). `SearchScreen` remains reachable from the authenticated shell.
<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module map

The design brief tags screens M1 to M6 but names no feature areas. The grouping below rearranges cited screens and stories. It is a derived structure, not a new fact.

| Module | Feature area | Routes owned | Source screens and stories |
|---|---|---|---|
| `auth` | Email one-time-code sign-in, error display, session expiry handling | `/login`, `/login/code` | `LoginScreen`, `CodeEntryScreen` (M6); US-006-05 |
| `notes` | Notes list with sort, live count and pin toggle; create; view; edit | `/notes`, `/notes/new`, `/notes/:id`, `/notes/:id/edit` | notes list (no design screen); `NoteEditorScreen` (M1); US-006-01..03, US-006-06 (pin) |
| `search` | Keyword and tag-filter search, with sort and live count | `/notes/search` | `SearchScreen` (M2); US-006-04 |
| `archive` | Archive and restore; archived view | `/notes/archived` | `ArchivedNotesScreen` (M3); US-006-06 |

**Excluded.** Design modules M4 and M5 (`ResidencyReportScreen`, `ApiScopeDocsScreen`). No route is minted for them. [`web-app.overview`; `GAP-WEB-021`]

**Shared concerns.**
- **Sort control and live result count.** Shared by the notes list and search. Search keeps the selected sort order. [stories.md US-006-01, US-006-04; requirements.md BR-019, BR-020]
- **Pin.** The toggle sits on list rows. Pinned notes precede unpinned notes in the list and in search. [stories.md US-006-01, US-006-06; requirements.md BR-021]
<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state or data-fetching library. React 18 is the only stated runtime library. [platform.md] The boundary below is conceptual.

**Server state.** Each item is read or written through its pinned operation:
- **Note detail:** `API-NOTES-045877`.
- **Search results:** `API-SEARCH-580505`.
- **Create:** `API-NOTES-232390`.
- **Edit and pin:** `API-NOTES-847551`.
- **Archive and restore:** `API-NOTES-810640`, `API-NOTES-917869`.
- **Tag add and remove:** `API-NOTES-359413`, `API-NOTES-150096`. No control is specified (`GAP-WEB-003`).
- **Active-notes list and archived-only list:** no operation exists in the contract (`GAP-WEB-001`). Every list-bound behaviour below is `BLOCKED` until backend supplies both.
- **Distinct-tag list:** no operation exists (`GAP-WEB-003`).

**Client state.**
- Unsubmitted form values on `NoteEditorScreen`, `LoginScreen` and `CodeEntryScreen`.
- The keyword and tag filter before a search is issued.
- The sort selection.
- The email typed on `LoginScreen`, held in memory only (`INV-WEB-009`).
- The return target for the unauthenticated redirect (`ASM-WEB-002`).

**Rules applied to server state:**
- **`INV-WEB-002`.** The client never sends or computes `created_at` or `updated_at`, including optimistically. [requirements.md BR-003; stories.md US-001-06]
- **`INV-WEB-003`.** Edit controls are never disabled because a note is archived. [requirements.md BR-010; stories.md US-003-05]
- **`INV-WEB-007`.** A pin change sends a partial edit carrying only the pinned flag. Title and body are not sent and do not change. [backend-api.md `API-NOTES-847551`, `INV-BE-008`]
- **Tag display.** Tag names are displayed as the server returns them. Normalisation is server-side. [requirements.md BR-005]
- **Search precondition.** The client issues a search only when a keyword or at least one tag is present. [requirements.md BR-007; stories.md US-002-03]
- **Tag filter.** The client offers one tag filter, because the contract's search takes a single tag (`GAP-WEB-024`). The control is undesigned (`GAP-WEB-002`).

**Mutation outcomes.**
- **Validation failure.** The user sees inline field-level errors from the response and the invalid field is highlighted. [stories.md US-006-02, US-006-03; requirements.md NFR-011]
- **Note not found on save.** The user sees a "note not found" message and local changes are not applied. [stories.md US-006-03]
- **Session loss.** A `401` ends the session (`web-app.auth-flows`).
- **Permission refusal.** A `403` shows the permission-denied state (`web-app.authorization-ui`).
- **Server or network failure.** A user-facing error state appears, never a blank screen. [stories.md US-006-01]

**Sort (BR-020, US-006-01, US-006-04).**
- **Options.** Newest first, Oldest first, Title A-Z. Newest first is the default. [stories.md US-006-01]
- **Basis for "Newest".** The basis is not stated. The default applied is creation time, descending (`ASM-WEB-004`, `GAP-WEB-008`).
- **`INV-WEB-005`.** Sorting is client-side and is never sent to the API. [requirements.md BR-020]
- **`INV-WEB-006`.** Pinned notes precede unpinned notes in every sort order, including search results. [requirements.md BR-021] The server order also puts pinned first. [backend-api.md `INV-BE-001`]
- **Search keeps the selection.** Running a search does not change the selected sort. [stories.md US-006-04]
- **Pagination interaction.** Client sort orders only the loaded results, and it overrides server relevance ranking (`CON-WEB-003`). The default applied is to sort the loaded set.
- **Unspecified.** Collation, case handling, tie-breaks and persistence of the selection are open (`GAP-WEB-008`).

**Pagination (search).**
- The client requests a page size of 20 and never above 100. [requirements.md NFR-003; `ASM-WEB-012`]
- The response carries a total count (`API-SEARCH-580505`). The next page is offered while the loaded count is below that total.
- Pagination controls are undesigned (`GAP-WEB-011`).

**Pin toggle (US-006-06).**
- Each list row has a pin toggle that sets the pinned flag through `API-NOTES-847551`. On success the row moves to the top with a pin icon. Unpinning returns it to its sort position. [stories.md US-006-06]
- The client does not reorder rows optimistically.
- Pin on an archived note, and any optimistic behaviour, are unspecified (`GAP-WEB-009`).
- The toggle and the icon have no design (`GAP-WEB-002`).
- The toggle is `BLOCKED` on rows until the list exists (`GAP-WEB-001`).

**Live result count (BR-019, US-006-01, US-006-04).** Derived client-side from the displayed result set and not stored (`ASM-WEB-010`).
- **List.** The count equals the number of notes rendered and shows `0` when the list is empty. [requirements.md BR-019; stories.md US-006-01]
- **Search.** The count equals the number of notes rendered for the current keyword and tag filter. [requirements.md BR-019] US-006-04 says "number of matches" (`CON-WEB-002`). The default applied is BR-019.
- **`INV-WEB-004`.** The count is hidden while results load and is recomputed on every filter, sort or result change. A count from a previous filter never persists.

**Empty and error states.**
- An empty list or empty search result uses `Empty/State`, with a count of 0 and no error. [stories.md US-006-01, US-006-04; design-brief.md components-inventory]
- Copy is not supplied, and screen-level loading is not designed (`GAP-WEB-010`).

**Cache and invalidation.**
- After create, edit, pin, archive or unarchive, invalidate the affected note detail, cached search results and the notes list. Archive and pin change list membership or order. [stories.md US-006-06; requirements.md BR-011]
- After a successful archive, the active list shrinks by refetch, not local arithmetic.
- Changing the sort selection re-sorts locally and needs no refetch.
- On sign-in, and on any `401` that ends the session, all cached server state is discarded so a later session never shows a previous session's notes. [`ASM-WEB-006`]
- The staleness window and retry policy are unspecified. No input states them.
- Archive or restore on a note already in the target state is `BLOCKED` on `CON-WEB-001`.
<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

Email one-time-code sign-in is additive. Existing login methods are unchanged. [feedback.md CR-130; backend-api.md `backend.overview`] The existing method is unnamed in every input, so `LoginScreen` is specified for the one-time-code only (`GAP-WEB-016`).

**Sign-in flow:**
1. On `LoginScreen`, the user enters an email address and submits. The app calls `API-AUTH-707260`. [stories.md US-006-05]
2. On success, the app opens `CodeEntryScreen` with the email held in memory. The acknowledgement is identical whether or not an account exists for the address, so no copy may imply account existence. [backend-api.md `API-AUTH-707260`, `ASM-BE-400`]
3. The user enters the 6-digit numeric code. The input accepts digits only, up to 6. [stories.md US-006-05; backend-api.md `backend.overview`] The app calls `API-AUTH-692907` with the email and code.
4. On success, the credential is stored (see credential storage below). The user goes to the return target or `/notes`. A user is created on first successful verification, with no registration step. [backend-api.md `ASM-BE-025`]

**Email field.** The client checks only that the field is non-empty. The server owns format validation. [`ASM-WEB-007`; backend-api.md `GAP-BE-035`]

**Components.** Submit uses `Button/Primary`. In-flight requests show its Loading state and a disabled submission shows its Disabled state. Both states exist on the component. [design-brief.md components-inventory]

**Error display.** The app discriminates on the machine-readable `code` of the error response, never on HTTP status alone. Several different failures share one status. [backend-api.md `backend.error-model`; `INV-WEB-010`] The code names are a backend proposal (`ASM-WEB-008`, `GAP-WEB-005`).

| Condition | Operation | Backend code (proposed) | Web behaviour |
|---|---|---|---|
| Wrong code | verify | `OTP_CODE_INVALID` | Show a wrong-code error and stay on `CodeEntryScreen`. Do not show attempts remaining: no input exposes them. |
| Code older than 10 minutes | verify | `OTP_CODE_EXPIRED` | Show an expired-code error with an option to request a new code. [stories.md US-006-05; requirements.md BR-022] |
| Code already used | verify | `OTP_CODE_USED` | Treated like expired, with an option to request a new code (`ASM-WEB-009`). US-006-05 is silent. |
| 5 wrong codes, account locked | verify | `OTP_ACCOUNT_LOCKED` | Show a lockout error stating the account is locked for 15 minutes. Disable code submission. [stories.md US-006-05; requirements.md BR-022] |
| More than 3 code requests in 10 minutes | send | `OTP_RATE_LIMITED` | Show a resend-limit error with no request-new-code option. [stories.md US-006-05] |
| Invalid input | send, verify | `VALIDATION_FAILED` | Show field-level errors on the entry field. |
| Server error or network failure | send, verify | none required | Show a generic try-again error. Behaviour when the email transport fails is not stated by any input. |

**Lockout and new codes.**
- While locked, no verification succeeds, even with a correct code, and requesting a code is not refused. [backend-api.md `INV-BE-005`, `backend.overview`]
- A new code does not lift the lock, so the request-new-code option is not offered during a lock (`ASM-WEB-011`).
- The contract returns no remaining lock time. The lockout error therefore states the fixed 15 minutes, with no countdown, and the re-enable moment is unspecified (`GAP-WEB-006`).

**Error presentation.** Neither screen has an error variant, an `Input/Text` mapping or a resend control, so how these errors look is undesigned (`GAP-WEB-007`).

**Server is authoritative.**
- **`INV-WEB-011`.** The client never counts attempts or resends, and never computes expiry or lock state from its own clock. Every refusal comes from the server response.
- **Resend wording.** US-006-05 says "3 resends" and the backend counts every request (`CON-WEB-004`). The client enforces neither reading.

**`INV-WEB-008`.** A `401` from `API-AUTH-707260` or `API-AUTH-692907` never triggers the session-expiry redirect. These two operations are the sign-in itself, so there is no session to end. It shows the local error.

**`INV-WEB-009`.** The email is personal data (`ASM-WEB-005`). It is held in memory only. It is never put in a URL, `localStorage` or a client log, and is never exemplified with a real-looking address.

**Session expiry and `401`.**
- Any `401` from a protected operation ends the session and redirects to `/login` with the return target.
- No refresh operation exists in the contract, so expiry means signing in again by code. Credential lifetime is not stated (`GAP-WEB-004`).
- A mutation that returns `401` is not applied locally and no optimistic result is shown.
- Whether unsaved editor content survives the redirect is unspecified (`GAP-WEB-013`).

**Credential storage: `BLOCKED`.** What a successful verification returns is unanswered upstream (`Q-BE-012`, `GAP-WEB-004`). Where the web app keeps the credential, and how it attaches it to requests, therefore cannot be written, and no storage location is assumed. Backend must state the credential form, its lifetime and any refresh operation.

**Sign-out.** No sign-out operation or story exists (`GAP-WEB-012`). Clearing client session state on sign-out follows from `ASM-WEB-006`, but server-side invalidation is unspecified.
<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DRAFT-UNVERIFIED -->
## Authorization UI

**Roles that can sign in to this surface.** Exactly one: **Team member**. It is the only actor any EP-006 story names. [stories.md EP-006] The backend defines no API roles (backend-api.md `backend.auth-model` is `BLOCKED`), and who may sign in at all is open (`GAP-WEB-014`).

**Excluded roles, and why.**
- **Developer integrating with the API, backend developer, API tech lead, QA engineer, operations engineer, product owner:** the stories that name them describe API or process behaviour and have no web route. [stories.md EP-001, EP-003, EP-005]
- **Platform operations engineer, compliance auditor:** their stories (EP-004) map only to the out-of-scope `ResidencyReportScreen` (`GAP-WEB-021`).
- **Any other role in the backend's matrix:** none is defined, and listing one here would be speculation about another surface. [core I-1]

**Team member: navigation to enabled actions.** No role differentiation exists, so no control is hidden by role. Every action below is granted by the stories.

| Visible navigation | Enabled actions | Basis |
|---|---|---|
| Notes list (`/notes`) | view, sort, pin and unpin, open a note | US-006-01, US-006-06. `BLOCKED`: `GAP-WEB-001` |
| New note (`/notes/new`) | create | US-006-02 |
| Note view and edit (`/notes/:id`, `/notes/:id/edit`) | edit, archive, restore | US-006-03, US-006-06 |
| Search (`/notes/search`) | keyword search, tag filter, sort | US-006-04 |
| Archived (`/notes/archived`) | view archived, restore | US-003-01..03 map; `BLOCKED`: `GAP-WEB-001` |
| Sign out | none specified | `GAP-WEB-012` |

**Not signed in.** This is a pre-authentication state, not a role. Only `/login` and `/login/code` are reachable. All other routes redirect (`web-app.routing`).

**Permission-denied state.** On permission-gated routes, a `403` renders a permission-denied state, distinct from a `404` not-found state.
- Which of the two the server returns for a note the user may not access is a security decision that no input makes (`GAP-WEB-014`). The web shows whatever the server returns and does not disclose more.
- The permission-denied state has no design (`GAP-WEB-002`).
<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

The design brief supplies token IDs only. Token values are not inlined, and no value is written here. Specs refer to tokens by ID. [design-brief.md tokens-index] Every name below is an unverified claim (Figma not connected). The brief reports 71 tokens in five groups.

| Group | Count | Token IDs |
|---|---|---|
| border | 10 | `UI/border/default`, `UI/border/disabled`, `UI/border/error`, `UI/border/focus`, `UI/border/input`, `UI/border/input-focus`, `UI/border/strong`, `UI/border/subtle`, `UI/border/success`, `UI/border/warning` |
| color | 20 | `UI/color/black`, `UI/color/disabled`, `UI/color/error`, `UI/color/error-subtle`, `UI/color/hover-destructive`, `UI/color/hover-ghost`, `UI/color/hover-primary`, `UI/color/hover-secondary`, `UI/color/hover-success`, `UI/color/info`, `UI/color/info-subtle`, `UI/color/overlay`, `UI/color/primary`, `UI/color/primary-hover`, `UI/color/primary-subtle`, `UI/color/success`, `UI/color/success-subtle`, `UI/color/warning`, `UI/color/warning-subtle`, `UI/color/white` |
| icon | 10 | `UI/icon/active`, `UI/icon/default`, `UI/icon/disabled`, `UI/icon/error`, `UI/icon/info`, `UI/icon/inverse`, `UI/icon/on-primary`, `UI/icon/subtle`, `UI/icon/success`, `UI/icon/warning` |
| surface | 18 | `UI/surface/button/destructive`, `UI/surface/button/ghost`, `UI/surface/button/primary`, `UI/surface/button/primary-hover`, `UI/surface/button/secondary`, `UI/surface/button/success`, `UI/surface/card`, `UI/surface/default`, `UI/surface/disabled`, `UI/surface/input`, `UI/surface/input-focus`, `UI/surface/overlay`, `UI/surface/secondary`, `UI/surface/tertiary`, `UI/surface/toast/error`, `UI/surface/toast/info`, `UI/surface/toast/success`, `UI/surface/toast/warning` |
| text | 13 | `UI/text/disabled`, `UI/text/error`, `UI/text/info`, `UI/text/inverse`, `UI/text/link`, `UI/text/link-hover`, `UI/text/on-primary`, `UI/text/placeholder`, `UI/text/primary`, `UI/text/secondary`, `UI/text/success`, `UI/text/tertiary`, `UI/text/warning` |

**Components in the inventory.** Their token bindings belong to the module specs. [design-brief.md components-inventory]
- `Button/Primary`: action, 12 variants, states Default, Pressed, Loading, Disabled.
- `Empty/State`: content, 1 variant, state Default.
- `Input/Text`: entry, 5 variants, states Default, Focused, Filled, Error, Disabled.

**Not supplied** (`GAP-WEB-017`):
- token values;
- a type scale;
- a spacing scale.

Breakpoints are covered in `web-app.responsive`. No visual value is hardcoded anywhere in this specification.
<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behaviour

**Blocked.** design-brief.md names no breakpoints and no layout change at any width, and Figma is not connected. Layout is out of scope rather than guessed. [`GAP-WEB-017`]

**Unblocked by:** a design brief or Figma frames that state breakpoints and the layout change at each one, including the authenticated shell and notes list that are not yet designed (`GAP-WEB-002`).
<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**BLOCKED.** the run did not produce this section. Nothing has been written in its place, and no claim about it should be read into this document.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input states an accessibility target standard or how it is verified. design-brief.md and requirements.md are silent. [`GAP-WEB-019`]

**Unblocked by:** a stated target standard and a verification method. Until then, focus management, keyboard paths and live regions cannot be specified against a target. Module specs may still record behaviours the stories name.
<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: DRAFT-UNVERIFIED -->
## Performance

**Web budget.** No bundle budget, first-load budget or web latency target is supplied (`GAP-WEB-020`). The NFRs in requirements.md (NFR-001..012) are all API-side. In particular, the search latency target is the API's. The web shows a loading state while waiting and sets no target of its own.

**Largest-payload route.** `/notes/search` is the largest known payload: up to 100 results per page, requesting 20 by default. [requirements.md NFR-003; `ASM-WEB-012`; backend-api.md `API-SEARCH-580505`] The notes list (`/notes`) cannot be assessed, because its operation, and so whether it is paginated, is not defined (`GAP-WEB-001`).

**Route-level code splitting (proposal only).** Split along the route table (`ASM-WEB-014`):
- the public group (`/login`, `/login/code`);
- the `notes` routes;
- `/notes/search`;
- `/notes/archived`.

No budget confirms that this split is necessary or sufficient.
<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

Every supplied story is mapped to the web module that serves it, or listed as not covered with its reason. Module-level requirement IDs are minted in the module specs.

| Story | Web module | Covered through | Status |
|---|---|---|---|
| US-001-01 | notes | US-006-02 | Covered |
| US-001-02 | notes | US-006-03 | Covered |
| US-001-03 | notes | US-006-02, US-006-03 | Covered |
| US-001-04 | notes | US-006-01 | Open: `GAP-WEB-001` (no list operation) |
| US-001-05 | notes | US-006-02, US-006-03 | Covered |
| US-001-06 | notes | US-006-02, `INV-WEB-002` | Covered |
| US-002-01 | none | none | Not covered: `GAP-WEB-003` |
| US-002-02 | none | none | Not covered: `GAP-WEB-003` |
| US-002-03 | search | US-006-04 | Covered |
| US-002-04 | search | US-006-04 | Covered; `GAP-WEB-024` (one tag only) |
| US-002-05 | none | none | Not covered: `GAP-WEB-003` (no operation) |
| US-002-06 | search | US-006-04, requirements.md NFR-003 | Open: `GAP-WEB-011` (controls undesigned) |
| US-003-01 | archive | US-006-06 | Open: `CON-WEB-001` |
| US-003-02 | archive | US-006-06 | Open: `CON-WEB-001` |
| US-003-03 | archive | US-006-06 | Open: `GAP-WEB-001` (no archived-only list) |
| US-003-04 | notes | US-006-01 | Open: `GAP-WEB-001` |
| US-003-05 | notes | `INV-WEB-003` | Covered; tag part `GAP-WEB-003` |
| US-003-06 | none | none | Not covered: `GAP-WEB-022` |
| US-004-01 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-004-02 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-004-03 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-004-04 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-004-05 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-004-06 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-005-01 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-005-02 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-005-03 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-005-04 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-005-05 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-005-06 | none | none | Not covered: out of scope, `GAP-WEB-021`, `CON-WEB-005` |
| US-006-01 | notes | `/notes` | Open: `GAP-WEB-001`, `GAP-WEB-002` |
| US-006-02 | notes | `NoteEditorScreen` (Empty) | Covered |
| US-006-03 | notes | `NoteEditorScreen` (Filled) | Covered |
| US-006-04 | search | `SearchScreen` | Covered |
| US-006-05 | auth | `LoginScreen`, `CodeEntryScreen` | Covered; credential storage `BLOCKED` (`GAP-WEB-004`) |
| US-006-06 | archive, notes | `ArchivedNotesScreen`; pin on rows | Open: `CON-WEB-001`; pin `BLOCKED` on `GAP-WEB-001` |

**Totals.** 36 stories in scope:
- 11 covered;
- 9 covered with an open or blocked part;
- 16 not covered by this surface, each with its reason shown.

**Contradiction checks performed.**
- **Story to design.** `CON-WEB-005` found.
- **Story to requirement.** `CON-WEB-001`, `CON-WEB-002`, `CON-WEB-004` found.
- **Requirement to requirement.** `CON-WEB-001` (inside BR-012) and `CON-WEB-003` found.
- **Design to design.** None found.
- **Input labelling.** `CON-WEB-006` found.
<!-- /SECTION: web-app.coverage -->
<!-- SECTION: web-app.lane-needs | status: VERIFIED -->
## Lane needs

### Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-846910` | `api_contract` | Add contract operations that list active notes (pinned first, paginated or not) and list archived notes only. Search cannot substitute because it requires a keyword or tag and include_archived mixes active and archived notes. | The notes list, live count, sort, pin toggle on rows and the archived view cannot be built without a list operation (GAP-WEB-001). | yes | `web-app.routing`, `web-app.state-management`, `web-app.authorization-ui`, `web-app.performance`, `web-app.coverage` |
| `HANDOFF-WEBAPP-653797` | `api_contract` | State what a successful API-AUTH-692907 verification returns (body token, cookie or other), its lifetime, and whether any refresh operation exists. | Credential storage and attachment to requests cannot be specified without it (GAP-WEB-004, Q-BE-012). | yes | `web-app.auth-flows`, `web-app.coverage` |
| `HANDOFF-WEBAPP-229710` | `business_rule` | Decide whether archiving an already-archived note and unarchiving an active note returns success idempotently (stories US-003-01/02) or a state-conflict error (requirements BR-012), and state the status and error code. | Client handling of archive/restore on a note already in the target state is BLOCKED on this critical contradiction (CON-WEB-001). | yes | `web-app.overview`, `web-app.state-management`, `web-app.coverage` |
| `HANDOFF-WEBAPP-752894` | `api_contract` | Publish the final error-code registry and the precedence when several verify refusals apply at once (invalid, expired, used, locked), since all share one status. | Error display discriminates on code names that are only proposals (GAP-WEB-005, GAP-BE-601). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-159759` | `api_contract` | Return remaining lock time (or a stated re-enable rule) on the OTP_ACCOUNT_LOCKED error. | The lockout error cannot show a countdown or know when submission re-enables (GAP-WEB-006). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-385244` | `screen` | Provide frames for the authenticated shell and navigation, notes list, note view, sort control, live count, pin toggle/icon, tag filter, permission-denied state, search pagination, loading states, and LoginScreen/CodeEntryScreen error variants with resend control. | None of these elements is designed; the brief has only three components (GAP-WEB-002, GAP-WEB-007, GAP-WEB-011). | no | `web-app.routing`, `web-app.state-management`, `web-app.authorization-ui`, `web-app.auth-flows`, `web-app.design-system` |
| `HANDOFF-WEBAPP-777968` | `business_rule` | Define the sort rule (basis of Newest, collation, tie-breaks, persistence), pin rules (optimistic behaviour, pinning archived notes), and whether client sort or server relevance governs paginated search. | Sort and pin behaviour is applied on assumptions (GAP-WEB-008, GAP-WEB-009, CON-WEB-003). | no | `web-app.state-management` |
| `HANDOFF-WEBAPP-903114` | `business_rule` | State who may sign in, whether notes are per-user or shared, whether an inaccessible note returns 403 or 404, and the sign-out/session invalidation rule. | Authorization UI and sign-out cannot be specified (GAP-WEB-014, GAP-WEB-012). | no | `web-app.authorization-ui`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-589476` | `nfr` | Supply a supported-browser matrix, an accessibility target standard with verification method, a web bundle/first-load budget, and responsive breakpoints. | These sections are blocked or unbudgeted without them (GAP-WEB-018, GAP-WEB-019, GAP-WEB-020). | no | `web-app.browser-support`, `web-app.accessibility`, `web-app.performance`, `web-app.responsive` |
| `HANDOFF-WEBAPP-172260` | `handoff-brief` | Decide which surface owns EP-004 and EP-005 stories and the ResidencyReportScreen and ApiScopeDocsScreen, and whether the web serves tag add/remove and multi-tag filtering. | The web spec excludes these screens and stories pending ownership (GAP-WEB-021, GAP-WEB-003, GAP-WEB-024). | no | `web-app.overview`, `web-app.routing`, `web-app.module-map`, `web-app.coverage` |
<!-- /SECTION: web-app.lane-needs -->
