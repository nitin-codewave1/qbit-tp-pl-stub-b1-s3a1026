---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-002-01, web_app:US-002-02, web_app:US-002-03, web_app:US-002-04, web_app:US-002-05, web_app:US-002-06, web_app:US-003-01, web_app:US-003-02, web_app:US-003-03, web_app:US-003-04, web_app:US-003-05, web_app:US-003-06, web_app:US-004-01, web_app:US-004-02, web_app:US-004-03, web_app:US-004-04, web_app:US-004-05, web_app:US-004-06, web_app:US-005-01, web_app:US-005-02, web_app:US-005-03, web_app:US-005-04, web_app:US-005-05, web_app:US-005-06, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03, web_app:US-006-04, web_app:US-006-05, web_app:US-006-06]
commit_kind: fork
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: web_app:web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DRAFT-UNVERIFIED -->
## Overview

**Purpose.** The Web App is the browser client for `notes_api` (React 18 + Vite, hosted on AWS) [platform.md; feedback.md Preflight answers]. A Team member uses it to sign in with an email one-time code, create, view, edit, search, archive, restore and pin notes, and to sort the notes list. They do this without calling the API directly. [stories.md EP-006 US-006-01..06; feedback.md CR-129]

**Primary actor.** Team member. It is the only actor that any EP-006 story names. [stories.md EP-006]

**This revision.** A bootstrap run for change request CR-129, lane `web_app`, consumer side. [feedback.md CR-129; RUN-CONTEXT `run_mode`]
- **What CR-129 adds:** a login screen and a code-entry screen. They call the request-code and verify-code endpoints and show lockout, expiry and resend-limit errors. [feedback.md CR-129; stories.md US-006-05; requirements.md BR-022]
- **What is kept:** the rest of the surface (notes, search, sort, live count, pin, archive) keeps the behaviour of the previously approved revision supplied in feedback.md. Where current inputs differ from that revision, current inputs govern (`CON-WEB-006`).

**Contract.** This surface builds against contract pin `sha256:bd87f6e16dca`. [RUN-CONTEXT `upstream.contract_pin`] Schemas are referenced by ID and never restated here.
- **Sign-in:** `API-AUTH-707260` (request code), `API-AUTH-692907` (verify code).
- **Notes:**
  - `API-NOTES-232390` (create)
  - `API-NOTES-045877` (get by id)
  - `API-NOTES-847551` (partial edit, including pin and unpin)
  - `API-NOTES-810640` (archive)
  - `API-NOTES-917869` (unarchive)
  - `API-NOTES-359413` (tag add)
  - `API-NOTES-150096` (tag remove)
- **Search:** `API-SEARCH-580505`.

The contract supplies these IDs without an `@version` suffix. This document cites them as supplied. [backend-api.md `backend.api-surface`; `GAP-WEB-022`]

**Design basis.** `spec_mode: FULL-FROM-BRIEF`. The design brief is present and Figma is not connected. [RUN-CONTEXT `inputs.figma_mcp`] Every screen name, frame reference and token name here is an unverified claim taken from design-brief.md.

**Upstream state this surface depends on.** backend-api.md records open items that touch this surface:
- **Sign-in eligibility and note access:** who may sign in, and whether notes are per-user or shared (`Q-BE-003`, `CON-BE-003`).
- **Credential form returned on successful verification:** `Q-BE-012`.
- **Statuses of the verify refusals:** `GAP-BE-038`, `GAP-BE-053`.
- **Error codes:** the error envelope and code names are a backend proposal (`ASM-BE-300`, `ASM-BE-301`).

This spec builds on those proposals and marks every dependent part `DRAFT-UNVERIFIED` or `BLOCKED`.

**Corrupted input text.** Several supplied sentences carry an injected unrelated clause: BR-022 ("3 resends times", "5 attempts wrong codes"), NFR-002, US-002-02 and US-006-05 ("6 digits-digit", "5 attempts times"). The numeric limits are used only where backend-api.md states the same value: 6 digits, 10-minute expiry, 3 requests per 10 minutes, 5 wrong codes, 15-minute lock. [requirements.md BR-022; backend-api.md `backend.overview`] The status that US-002-02 meant to state is unknown and is not used.

**Run status.** `CON-WEB-001` is Critical because it changes a state transition, so under core rule 8 it maps to `HALT-CRITICAL-CONTRADICTION`. This document is still emitted complete. The orchestrator should hold the run. Archive and restore handling for a note already in the target state is `BLOCKED` until a human resolves `CON-WEB-001`. Other open items are non-critical; see `web-app.coverage`.

**Out of scope**, each cited:
- **Operator, compliance and administration functions.** They belong to other surfaces. [core I-1]
- **Residency, compliance and scope-documentation screens:** `ResidencyReportScreen` (M4), `ScopeDocumentationScreen` (M5). Their stories name non-Team-member actors, and no input assigns them to this surface. [design-brief.md screens; stories.md EP-004, EP-005; `GAP-WEB-020`]
- **Note deletion.** No delete operation exists in the contract and no story asks for one. [backend-api.md `backend.api-surface`]
- **Attachments and file upload.** Create and edit reject attachment payloads, so no attachment control is built. [requirements.md BR-016]
- **Real-time or push updates.** [requirements.md BR-017] "Live" in the result count means recomputed when the displayed set changes, not server push.
- **Mobile-specific behaviour.** [requirements.md BR-018]
- **Offline use.** No input requires it.
- **Tag add and remove controls.** The contract has the endpoints, but no web story or screen asks for them. [`GAP-WEB-004`]
<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. The paths below are proposed (`ASM-WEB-001`). Screen names and modules come from the design brief. [design-brief.md screens-inventory]

| Route | Screen (module) | Protected | Basis |
|---|---|---|---|
| `/login` | `LoginScreen` (M6) | No | US-006-05; stories-map |
| `/login/code` | `CodeEntryScreen` (M6) | No, needs an email held in memory (see below) | US-006-05; stories-map |
| `/notes` | notes list: no design screen | Yes | US-006-01. `BLOCKED`: `GAP-WEB-001`, `GAP-WEB-002` |
| `/notes/new` | `NoteEditorScreen` (M1) | Yes | US-006-02; `ASM-WEB-003` |
| `/notes/search` | `SearchScreen` (M2) | Yes | US-006-04 |
| `/notes/archived` | `NoteLifecycleScreen` (M3) | Yes | stories-map US-003-01..03, US-006-06; `ASM-WEB-003`. `BLOCKED`: `GAP-WEB-003` |
| `/notes/:id` | `NoteDetailScreen` (M1) | Yes | US-006-02 ("navigates to the saved note"); US-006-06 |
| `/notes/:id/edit` | `NoteEditorScreen` (M1) | Yes | US-006-03; `ASM-WEB-003` |

**`INV-WEB-001`.** The static segments `new`, `search` and `archived` must match before `/notes/:id`. Otherwise the router reads them as note identifiers.

**Layout nesting.** Two layouts are assumed:
- **Public layout:** `/login` and `/login/code`.
- **Authenticated shell:** every other route.

No shell, header or navigation component exists in the design inventory (`GAP-WEB-002`).

**Protected-route rule.** Every route except `/login` and `/login/code` requires an authenticated session. A `401` from a protected endpoint is handled as in `web-app.auth-flows`.

**Unauthenticated redirect.** Redirect to `/login` and keep the requested in-app path as the return target. After a successful verification, go to the return target if present, otherwise to `/notes`. The return target accepts in-app paths only. [`ASM-WEB-002`]

**`/login/code` entry rule.** `CodeEntryScreen` is reachable only after a successful request-code call in the same session. If it is opened directly, with no email held in memory, redirect to `/login`. [`ASM-WEB-005`]

**Flow edges.** `ScopeDocumentationScreen` has two `ON_CLICK` transitions to `SearchScreen`. [design-brief.md screens-inventory] The source screen is out of scope (`GAP-WEB-020`), so the edges are not implemented. `SearchScreen` remains reachable from the authenticated shell.
<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module map

| Module | Feature area | Routes owned | Source screens and stories |
|---|---|---|---|
| `auth` | Email one-time-code sign-in, error display, session expiry handling | `/login`, `/login/code` | `LoginScreen`, `CodeEntryScreen` (M6); US-006-05 |
| `notes` | Browse with sort, live count and pin toggle; create; view; edit | `/notes`, `/notes/new`, `/notes/:id`, `/notes/:id/edit` | notes list (no design screen); `NoteEditorScreen`, `NoteDetailScreen` (M1); US-006-01..03, US-006-06 (pin) |
| `search` | Keyword and tag-filter search, with sort and live count | `/notes/search` | `SearchScreen` (M2); US-006-04 |
| `archive` | Archive and restore; archived view | `/notes/archived` | `NoteLifecycleScreen` (M3); US-006-06 |

**Excluded.** Design modules M4 and M5 (`ResidencyReportScreen`, `ScopeDocumentationScreen`). No route is minted for them. [`web-app.overview`; `GAP-WEB-020`]

**Shared concerns.**
- **Sort control and live result count.** Shared by the notes list and search. Search keeps the selected sort order. [stories.md US-006-01, US-006-04; requirements.md BR-019, BR-020]
- **Pin.** The toggle sits on list rows. Pinned notes precede unpinned in the list and in search. [stories.md US-006-01, US-006-06; requirements.md BR-021]

This grouping rearranges screens and stories cited elsewhere in this document. It is a derived structure, not a new fact. Module-level `WEB-<MOD>-FR-nnn` requirements are minted in the module specs of this run.
<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state or data-fetching library. The boundary below is conceptual. React 18 is the only stated runtime library. [platform.md]

**Server state.** Each item is read or written through its pinned endpoint:
- **Note detail:** `API-NOTES-045877`.
- **Search results:** `API-SEARCH-580505`.
- **Create:** `API-NOTES-232390`.
- **Edit and pin:** `API-NOTES-847551`.
- **Archive and restore:** `API-NOTES-810640`, `API-NOTES-917869`.
- **Tag add and remove:** `API-NOTES-359413`, `API-NOTES-150096`. No control is specified (`GAP-WEB-004`).
- **Notes list:** no operation exists in the contract. The permission matrix lists "Note: list" but the API surface has no list endpoint. Search cannot stand in for it, because search requires a keyword or a tag filter. [backend-api.md `backend.auth-model`, `backend.api-surface`; requirements.md BR-007; `GAP-WEB-001`] The web app needs, from backend, a list operation that returns active notes with pinned first. Until it exists, every list-bound behaviour below is `BLOCKED`.
- **Archived-only list and distinct-tag list:** no operation exists. [`GAP-WEB-003`]

**Client state.**
- Unsubmitted form values on `NoteEditorScreen`, `LoginScreen` and `CodeEntryScreen`.
- The search text and tag filter before a search is issued.
- The sort selection (see below).
- The email typed on `LoginScreen`, held in memory only (`INV-WEB-009`).
- The return target for the unauthenticated redirect (`ASM-WEB-002`).

**Rules applied to server state:**
- **`INV-WEB-002`.** The client never sends or computes `created_at` or `updated_at`, including optimistically. [requirements.md BR-003; stories.md US-001-06]
- **Tag names.** Tag names are displayed as the server returns them. Normalisation is server-side. [requirements.md BR-005]
- **`INV-WEB-003`.** Edit controls are never disabled because a note is archived. [requirements.md BR-010; stories.md US-003-05]
- **`INV-WEB-007`.** A pin change sends a partial edit that carries only the pinned flag. Title and body are not sent and do not change. [backend-api.md `API-NOTES-847551`, `INV-BE-008`]
- **Search precondition.** The client issues a search only when a keyword or at least one tag is present. [requirements.md BR-007; stories.md US-002-03]

**Sort (BR-020, US-006-01, US-006-04).**
- **Options.** Newest first, Oldest first, Title A-Z. Newest first is the default. [stories.md US-006-01]
- **Basis for "Newest".** The basis is not stated. The default is creation time, descending. [`ASM-WEB-004`; `GAP-WEB-006`]
- **`INV-WEB-005`.** Sorting is client-side and is never sent to the API. [requirements.md BR-020]
- **`INV-WEB-006`.** Pinned notes precede unpinned notes in every sort order, including search results. [requirements.md BR-021] The server order also puts pinned first. [backend-api.md `INV-BE-001`; order within a group is `GAP-BE-310`]
- **Search keeps the selection.** Running a search does not change the selected sort. [stories.md US-006-04]
- **Pagination.** Search is paginated, so client-side sort orders only the notes loaded so far. This is `CON-WEB-003`. The default applied is to sort the loaded set.
- **Unspecified.** Title collation, case handling, tie-breaks and whether the selection persists across reloads are unspecified. [`GAP-WEB-006`]

**Pin toggle (CR-112 baseline, US-006-06).**
- Each list row has a pin toggle that sets the pinned flag through `API-NOTES-847551`. On success the row moves to the top with a pin icon. Unpinning returns it to its sort position. [stories.md US-006-06]
- The client does not reorder rows optimistically. Pin on an archived note, and any optimistic behaviour, are unspecified. [`GAP-WEB-005`]
- The toggle and the icon have no design or component. [`GAP-WEB-002`]
- The pin toggle is `BLOCKED` on rows until the list exists (`GAP-WEB-001`).

**Live result count (BR-019, US-006-01, US-006-04).** Derived client-side from the displayed result set and not stored. [`ASM-WEB-010`]
- **Notes list.** The count equals the number of notes rendered, and shows `0` when the list is empty. [requirements.md BR-019; stories.md US-006-01]
- **Search.** The count equals the number of notes rendered for the current keyword and tag filter. [requirements.md BR-019] US-006-04 says "number of matches". With pagination these differ, which is `CON-WEB-002`. The default applied is BR-019, the rendered count.
- **`INV-WEB-004`.** The count is hidden while results load and is recomputed on every filter, sort or result change. A count from a previous filter never persists.

**Empty and error states.** An empty list or empty search result uses `Empty/State`, with no error. [stories.md US-006-01, US-006-04; design-brief.md components-inventory] Copy is not supplied (`GAP-WEB-012`). A failed fetch shows a user-facing error state, never a blank screen. [stories.md US-006-01] Screen-level loading is not designed (`GAP-WEB-013`).

**Cache and invalidation.**
- After create, edit, pin, archive or unarchive, invalidate the affected note detail, cached search results and the notes list. Archive and pin change list membership or order. [stories.md US-006-06; requirements.md BR-011]
- After a successful archive, the active list shrinks by one through refetch, not local arithmetic.
- Changing the sort selection re-sorts locally and needs no refetch.
- On sign-in, and on any `401` that clears the session, all cached server state is discarded so a later session never shows a previous session's notes. [`ASM-WEB-006`]
- The staleness window and retry policy are unspecified. No input states them.
- Archive and restore on a note already in the target state is `BLOCKED` on `CON-WEB-001`.
<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

Email one-time-code is an additional sign-in method. Existing login methods are unchanged. [feedback.md CR-129; backend-api.md `backend.overview`] The existing method is unnamed in every input (`GAP-BE-085`). `LoginScreen` is specified for email one-time-code only (`GAP-WEB-021`).

**Sign-in flow:**
1. On `LoginScreen`, the user enters an email address and submits. The app calls `API-AUTH-707260`. [stories.md US-006-05]
2. On success, the app opens `CodeEntryScreen` with the email held in memory. The acknowledgement is identical whether or not an account exists for the address, so no copy may imply account existence. [backend-api.md `API-AUTH-707260`]
3. The user enters the 6-digit numeric code. The input accepts digits only, up to 6. [stories.md US-006-05; backend-api.md `backend.overview`] The app calls `API-AUTH-692907` with the email and code.
4. On success, the credential is stored (see credential storage below) and the user goes to the return target or `/notes`. A user is created on first successful verification, with no separate registration step. [backend-api.md `ASM-BE-025`]

**Email field.** The client checks only that the field is non-empty. The server owns format validation. [`ASM-WEB-007`; backend-api.md `GAP-BE-035`]

**Components.** Submit uses `Button/Primary`. In-flight requests show its Loading state, and a disabled submission shows its Disabled state. Both states exist on the component. [design-brief.md components-inventory]

**Error display.** The app discriminates on the machine-readable `code` of the error envelope, never on HTTP status alone. Status 415 is shared between the resend limit and file-upload rejection. [backend-api.md `backend.error-model`; `INV-WEB-010`] The code names are a backend proposal. [backend-api.md `ASM-BE-300`, `ASM-BE-301`; `ASM-WEB-008`]

| Condition | Backend code | Web behaviour |
|---|---|---|
| Wrong code | `OTP_CODE_INVALID` | Show a wrong-code error. Stay on `CodeEntryScreen`. Do not show attempts remaining: no input exposes them. |
| Code older than 10 minutes | `OTP_CODE_EXPIRED` | Show an expired-code error with an option to request a new code. [stories.md US-006-05; requirements.md BR-022] |
| Code already used | `OTP_CODE_USED` | Treated like expired, with an option to request a new code. US-006-05 is silent. [`ASM-WEB-009`] |
| 5 wrong codes, account locked | `OTP_ACCOUNT_LOCKED` | Show a lockout error stating the account is locked for 15 minutes. Disable code submission. [stories.md US-006-05; requirements.md BR-022] |
| More than 3 code requests in 10 minutes | `OTP_SEND_LIMIT_EXCEEDED` | Show a resend-limit error with no request-new-code option. [stories.md US-006-05; backend-api.md `API-AUTH-707260`] |
| Invalid input | `VALIDATION_ERROR` | Show field-level errors on the entry field. |

- **Lockout and new codes.** While locked, no verification succeeds, even with a correct code, and requesting a code is not refused. [backend-api.md `INV-BE-005`, `backend.overview`] The request-new-code option is therefore not offered during a lock. [`ASM-WEB-011`]
- **Lockout display.** The contract returns no remaining lock time, so the lockout error states the fixed 15 minutes and offers no countdown. The re-enable moment is unspecified (`GAP-WEB-011`).
- **Error presentation.** Neither `LoginScreen` nor `CodeEntryScreen` has an error variant or an `Input/Text` mapping, so how these errors look is undesigned (`GAP-WEB-007`).
- **Statuses and names.** The statuses of the verify refusals are not stated in any input, and the code names above are a proposal. [backend-api.md `GAP-BE-038`, `GAP-BE-053`; `GAP-WEB-010`]
- **Unclassified failures.** A network failure or 5xx shows a generic error. Behaviour when the email transport fails is not stated by any input.

**Server is authoritative.**
- **`INV-WEB-011`.** The client never counts attempts or resends, and never computes expiry or lock state from its own clock. Every refusal comes from the server response.
- **Resend wording.** US-006-05 says "3 resends"; the backend counts every code request including the first (`CON-WEB-004`). The client enforces neither reading.

**`INV-WEB-008`.** The global handling of `401` (below) does not apply to `API-AUTH-707260` or `API-AUTH-692907`. A `401` from verify may be a wrong-code refusal (`GAP-BE-053`), and it must show the local error, not redirect to `/login`.

**`INV-WEB-009`.** The email is personal data. [backend-api.md `ASM-BE-019`; `ASM-WEB-005`] It is held in memory only, never put in a URL, `localStorage` or a client log, and never exemplified with a real-looking address.

**Session expiry and `401`.** Any `401` from a protected endpoint clears the session and redirects to `/login` with the return target. No refresh operation exists in the contract, so expiry means signing in again by code. Credential lifetime is not stated. [backend-api.md `GAP-BE-302`; `GAP-WEB-008`]

**Mid-flow expiry.** A mutation that returns `401` is not applied locally and no optimistic result is shown. Whether unsaved editor content survives the redirect is unspecified. [`GAP-WEB-009`]

**Credential storage: `BLOCKED`.** What a successful verification returns (body token, cookie or other) is unanswered upstream, so where the web app keeps it, and how it attaches it to requests, cannot be written. [backend-api.md `Q-BE-012`; `GAP-WEB-008`] No storage location is assumed. Backend must state the credential form and its lifetime.

**Logout.** No operation, screen or control describes sign-out. [`GAP-WEB-009`]

**403.** The authorisation model is unresolved. Note ownership is `BLOCKED` upstream, so whether a note the user may not access returns `403` or `404` is unknown. [backend-api.md `backend.auth-model`, `CON-BE-003`; `GAP-WEB-023`] No `403` handling is specified, and `403` must not be treated as `401`.
<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DRAFT-UNVERIFIED -->
## Authorization UI

Exactly one role signs in to this surface: the Team member. Who is eligible to request, verify or receive a code is unanswered upstream. [backend-api.md `Q-BE-003`, `CON-BE-003`; `GAP-WEB-021`]

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Unauthenticated visitor (pre-sign-in) | `LoginScreen`, `CodeEntryScreen` only; any other route redirects to `/login` | Request a code, verify a code |
| Team member (authenticated) | Notes list, New note, Search, Archived notes | Create, view, edit, pin and unpin, archive, restore, search, change sort, view live count |

- **Backend alignment.** These actions match the backend permission matrix: anonymous callers may reach only the two sign-in operations, and the authenticated team member may use the notes and search operations. [backend-api.md `backend.auth-model`]
- **Pin.** Pin and unpin use the same partial-edit operation as edit, so no separate pin permission exists. [backend-api.md `API-NOTES-847551`]
- **Blocked or unspecified items:**
  - Notes list navigation is `BLOCKED` (`GAP-WEB-001`) and Archived notes navigation is `BLOCKED` (`GAP-WEB-003`).
  - Tag add and remove are unspecified (`GAP-WEB-004`).
  - The note access rule (shared versus per-owner) is unresolved (`CON-BE-003`). The UI shows no ownership indicator.
  - No delete action exists.

**Roles excluded from this surface:**

| Excluded | Why |
|---|---|
| Platform/DevOps Engineer, Release/CI Pipeline Owner, Compliance/Security Auditor, Engineering Lead (backend actors) | The backend auth model says these act on infrastructure and the pipeline and are not API roles. [backend-api.md `backend.auth-model`] Their screens (M4) are out of scope (`GAP-WEB-020`). |
| Platform operations engineer, compliance auditor (stories.md actors, EP-004) | Not a Team member. They map only to `ResidencyReportScreen` (M4), out of scope. |
| API tech lead, QA engineer, product owner (stories.md actors, EP-005) | Not a Team member. They map only to `ScopeDocumentationScreen` (M5), out of scope. |
| Backend developer, developer integrating with notes_api, operations engineer, integration developer | They call the API directly. No screen in any input is for them. |

No role from another surface is listed, to avoid speculating about it. [core I-1]
<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

**Tokens.** The design brief lists 71 token identifiers in five categories: `UI/border/*`, `UI/color/*`, `UI/icon/*`, `UI/surface/*` and `UI/text/*`. It gives identifiers and file references only, and no value is supplied. [design-brief.md tokens-index; `GAP-WEB-015`] Figma is not connected, so no identifier is verified. No visual value is stated or hard-coded here.

**Token bindings.** The index includes error-oriented tokens (for example `UI/text/error`, `UI/border/error`, `UI/color/error-subtle`, `UI/surface/toast/error`). Which token binds to which element, including the lockout, expiry and resend-limit errors, is not stated. No binding is asserted. [`GAP-WEB-015`]

**Type scale and spacing: `BLOCKED`.** No token category covers either. Breakpoints are covered in `web-app.responsive`.

**Components.** Three are inventoried. [design-brief.md components-inventory, screens-inventory]
- `Button/Primary`: role action, 12 variants, states Default, Pressed, Loading, Disabled. Mapped to `LoginScreen`, `CodeEntryScreen`.
- `Empty/State`: role content, 1 variant, state Default. Mapped to `NoteEditorScreen`, `NoteLifecycleScreen`, `SearchScreen`.
- `Input/Text`: role entry, 5 variants, states Default, Focused, Filled, Error, Disabled. Mapped to `NoteEditorScreen`, `SearchScreen`.

Full component bodies (props, bindings) are omitted from the handoff and are unavailable to this run.

**Screen variants.** [design-brief.md screens-inventory]

| Screen | Variants |
|---|---|
| `LoginScreen` | Default |
| `CodeEntryScreen` | Default |
| `NoteDetailScreen` | Default, Error |
| `NoteEditorScreen` | Default, Empty, Error, Filled |
| `NoteLifecycleScreen` | Default, Empty, Error, Filled |
| `SearchScreen` | Default, Empty, Error, Filled |

**Not covered by any screen or component:**
- The notes list, its layout, the sort control, the live count, the pin toggle and pin icon, and the authenticated shell. [`GAP-WEB-002`]
- Error variants for `LoginScreen` and `CodeEntryScreen`, and any `Input/Text` on them. Login and code entry need an email field and a code field, but neither screen lists `Input/Text`. A resend control is also undesigned. [`GAP-WEB-007`]
- Loading variants on every screen. `Button/Primary` has a Loading state, but no screen-level loading exists. [`GAP-WEB-013`]
- Pagination controls. [`GAP-WEB-014`]
<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behaviour

**Blocked.** No input defines a breakpoint, a viewport category or a layout change at any breakpoint. The design variants (Default, Empty, Error, Filled) are data states, not viewport states, and the token index has no breakpoint token. [design-brief.md; `GAP-WEB-016`]

**Unblocks with:** a responsive specification from the design source. It must cover the notes list (which has no screen), the sort control and pin placement on rows, and `LoginScreen` and `CodeEntryScreen` at each breakpoint.
<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**Blocked.** The platform input names React 18 and Vite but no supported browser or version. [platform.md; `GAP-WEB-017`]

**Unblocks with:** a browser-support matrix from the product or platform requirements.
<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** the run did not produce this section. Nothing has been written in its place, and no claim about it should be read into this document.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: DRAFT-UNVERIFIED -->
## Performance

**Bundle budget: `BLOCKED`.** No input states a bundle budget. [`GAP-WEB-019`]

**Route-level code splitting.** Not required by any input. React 18 with Vite is stated [platform.md], which makes it possible. The default, as a design choice, is one split per top-level route group: the public sign-in routes, and the authenticated routes. [`GAP-WEB-019`]

**Largest-payload route.** Search at the maximum page size is the only route with a documented upper bound on item count (maximum page size 100). [requirements.md NFR-003] The notes list is unbounded, and its operation does not exist (`GAP-WEB-001`). Sign-in routes carry the smallest payloads, with no list data.

**Cited targets that bind this surface's requests:**
- Search median latency at or under 300 ms under normal load. [requirements.md NFR-001]
- Core CRUD p95 latency at or under 200 ms. [requirements.md NFR-002]
- Search default page size 20, maximum 100, set by query parameter. [requirements.md NFR-003] backend-api.md says the default and maximum are unstated and states only that the latency target holds up to page size 100. [backend-api.md `GAP-BE-500`, `backend.nfr`] Whether the backend implements 20 and 100 is unconfirmed. [`GAP-WEB-014`]
- Search availability, as measured upstream, is 99.9% monthly for CRUD and search. No target is stated for the sign-in operations. [backend-api.md `backend.nfr`, `GAP-BE-301`]

The web app imposes no client timeout and no retry policy: none is stated. These targets are backend targets. The web app's own budget (first load, interaction) is unspecified.
<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

Every supplied story is listed, 36 in all. "Covering input" cites supplied requirement IDs. Module-level `WEB-<MOD>-FR-nnn` IDs are minted in the module specs of this run. Requirement IDs are cited as numbered in requirements.md (`CON-WEB-005`).

| Story | Module | Covering input | Status |
|---|---|---|---|
| US-001-01 | `notes` | BR-001; via US-006-02 | covered by the editor submitting a create |
| US-001-02 | `notes` | BR-002 (immutable id); via US-006-03 | covered by the editor submitting an edit |
| US-001-03 | `notes` | `NoteDetailScreen`; `API-NOTES-045877` | covered |
| US-001-04 | `notes` | BR-011 (default list excludes archived) | **blocked**: no list operation (`GAP-WEB-001`) |
| US-001-05 | `notes` | BR-004; NFR-011 | covered: field-level errors shown inline |
| US-001-06 | `notes` | BR-003; `INV-WEB-002` | covered |
| US-002-01 | none | BR-005 | **uncovered**: no web story or screen for tag add (`GAP-WEB-004`) |
| US-002-02 | none | BR-006 | **uncovered**: no web story or screen for tag remove (`GAP-WEB-004`); the story text is corrupted |
| US-002-03 | `search` | BR-007; NFR-001; via US-006-04 | covered |
| US-002-04 | `search` | via US-006-04 | covered |
| US-002-05 | none | BR-008 | **uncovered**: no endpoint and no web story (`GAP-WEB-003`) |
| US-002-06 | `search` | NFR-003 | covered; pagination UI and parameter names unconfirmed (`GAP-WEB-014`, `CON-WEB-003`) |
| US-003-01 | `archive` | BR-009; BR-012; via US-006-06 | covered; already-archived handling `BLOCKED` (`CON-WEB-001`) |
| US-003-02 | `archive` | BR-009; BR-012; via US-006-06 | covered; already-active handling `BLOCKED` (`CON-WEB-001`) |
| US-003-03 | `archive` | BR-011 (exception) | **blocked**: no archived-only list operation (`GAP-WEB-003`) |
| US-003-04 | `notes` | BR-011 | **blocked**: no list operation (`GAP-WEB-001`) |
| US-003-05 | `notes` | BR-010; `INV-WEB-003` | covered |
| US-003-06 | `notes`, `archive` | none | covered web-side only: a `400` on an invalid id shows an error state. The validation itself is backend-owned. |
| US-004-01 | none | none | **uncovered**: platform operations actor; M4 out of scope (`GAP-WEB-020`) |
| US-004-02 | none | none | **uncovered**: compliance auditor; M4 out of scope (`GAP-WEB-020`) |
| US-004-03 | none | none | **uncovered**: backend developer; M4 out of scope (`GAP-WEB-020`) |
| US-004-04 | none | none | **uncovered**: compliance auditor; M4 out of scope (`GAP-WEB-020`) |
| US-004-05 | none | none | **uncovered**: platform operations actor; M4 out of scope (`GAP-WEB-020`) |
| US-004-06 | none | none | **uncovered**: compliance auditor; M4 out of scope (`GAP-WEB-020`) |
| US-005-01 | none | none | **uncovered**: API tech lead; M5 out of scope (`GAP-WEB-020`) |
| US-005-02 | none | BR-016 | **uncovered**: backend rule; the web app builds no attachment control (`GAP-WEB-020`) |
| US-005-03 | none | BR-017 | **uncovered**: API governance; M5 out of scope (`GAP-WEB-020`) |
| US-005-04 | none | none | **uncovered**: QA actor; M5 out of scope (`GAP-WEB-020`) |
| US-005-05 | none | none | **uncovered**: product owner; M5 out of scope (`GAP-WEB-020`) |
| US-005-06 | none | BR-018 | **uncovered**: backend rule (`GAP-WEB-020`) |
| US-006-01 | `notes` | BR-019; BR-020; BR-021 | **blocked** for the list (`GAP-WEB-001`, `GAP-WEB-002`); sort and count rules are specified |
| US-006-02 | `notes` | BR-001; BR-003 | covered; `NoteEditorScreen` (`ASM-WEB-003`) |
| US-006-03 | `notes` | BR-004; NFR-011 | covered; `NoteEditorScreen` (`ASM-WEB-003`) |
| US-006-04 | `search` | BR-007; BR-019; BR-020; BR-021 | covered; `DRAFT-UNVERIFIED` (`CON-WEB-002`, `CON-WEB-003`, `GAP-WEB-014`) |
| US-006-05 | `auth` | BR-022 | covered; `DRAFT-UNVERIFIED` (`GAP-WEB-007`, `GAP-WEB-008`, `GAP-WEB-010`, `GAP-WEB-011`) |
| US-006-06 | `archive`, `notes` | BR-009; BR-021 | covered; archive and restore on the single-note view, pin on list rows `BLOCKED` until the list exists (`GAP-WEB-001`); no archive-state conflict handling (`CON-WEB-001`) |

**Totals.** 36 stories: 17 covered, 4 blocked, 15 uncovered. Of the uncovered, 12 are EP-004 and EP-005 stories owned by other actors (`GAP-WEB-020`), and 3 are tag-related stories that no web story or screen asks for (`GAP-WEB-003`, `GAP-WEB-004`).

**Contradiction checks run.** All four directions were checked.
- **Story to design:** only the missing error variants (`GAP-WEB-007`); no direct contradiction.
- **Story to requirement:** `CON-WEB-001` (Critical), `CON-WEB-002`, `CON-WEB-004`.
- **Requirement to requirement:** `CON-WEB-003`, `CON-WEB-005`.
- **Design to design:** none found.
- **Input to baseline:** `CON-WEB-006`.

**Contradictions:**
- **`CON-WEB-001` (Critical).** US-003-01 and US-003-02 say archiving an already-archived note, or unarchiving an active one, returns 200 idempotently with no error. BR-012 says the operations are idempotent and also return a consistent error for invalid transitions, for example archiving an already-archived note. The contract lists a `409` for archive and none for unarchive. [stories.md; requirements.md BR-012; backend-api.md `API-NOTES-810640`, `API-NOTES-917869`, `GAP-BE-309`] If 200 holds, the UI treats the call as success. If 409 holds, the UI shows a conflict error with the state unchanged. This is Critical because it changes the set of valid transitions. Dependent handling is `BLOCKED`. Not resolved here.
- **`CON-WEB-002`.** BR-019 says the count is the number of notes rendered. US-006-04 says it is the number of matches. With pagination these differ. Default applied: the rendered count.
- **`CON-WEB-003`.** BR-020 says sort is client-side with no API parameters. NFR-003 and US-002-06 say search is paginated. A client-side sort orders only the loaded notes, so "Newest first" is not a global order across pages. Default applied: sort the loaded set.
- **`CON-WEB-004`.** US-006-05 and BR-022 limit "3 resends per 10 minutes". The backend limits code requests to 3 within 10 minutes, counting the first. [backend-api.md `CON-BE-011`] Default applied: the web app does not count and displays the server's refusal.
- **`CON-WEB-005`.** Rule numbering differs between inputs. backend-api.md cites, for example, BR-020 to BR-032, NFR-016 and pinned ordering as BR-019. In requirements.md, BR-019 is the live result count, BR-021 is pinned ordering, and the OTP limits are in BR-022 only. Default applied: each ID is cited from the file where it is read. Traceability between the two files is unconfirmed.
- **`CON-WEB-006`.** The previously approved revision in feedback.md names screens `NoteCreateScreen`, `NoteEditScreen`, `TagManagementScreen`, `TagListScreen` and `ResidencyReportScreen`'s sibling `ApiSpecDocumentationScreen`, and a different contract pin and API IDs. The current design brief has `NoteEditorScreen`, `NoteLifecycleScreen`, `LoginScreen` and `CodeEntryScreen`, and the current contract differs. Default applied: current inputs govern.

**Gaps raised in this document:**
- `GAP-WEB-001`: no notes-list operation in the contract. Closing artefact: a backend contract with a list operation returning active notes, pinned first.
- `GAP-WEB-002`: no design for the notes list, authenticated shell, sort control, live count, pin toggle or pin icon. Closing artefact: designs for them.
- `GAP-WEB-003`: no archived-only list and no distinct-tag list operation. Closing artefact: backend operations or a decision that none are needed.
- `GAP-WEB-004`: no web story or design for tag add and remove. Closing artefact: a story and screen.
- `GAP-WEB-005`: pin on archived notes and optimistic pin behaviour unspecified. Closing artefact: a rule.
- `GAP-WEB-006`: sort basis, collation, ties and persistence unspecified. Closing artefact: a rule.
- `GAP-WEB-007`: no error variants, no `Input/Text` and no resend control on the login and code-entry frames. Closing artefact: updated frames.
- `GAP-WEB-008`: credential form, lifetime, storage and refresh unspecified. Closing artefact: answers to `Q-BE-012` and `GAP-BE-302`.
- `GAP-WEB-009`: logout and draft survival across a `401` unspecified. Closing artefact: a rule or story.
- `GAP-WEB-010`: statuses and error-code names for verify refusals unconfirmed. Closing artefact: the backend's final error model (`GAP-BE-038`, `GAP-BE-053`, `ASM-BE-301`).
- `GAP-WEB-011`: no remaining lock time is exposed. Closing artefact: a contract field or a stated UI rule.
- `GAP-WEB-012`: no copy for error, empty or success states, and no locales. Closing artefact: a copy deck.
- `GAP-WEB-013`: no screen-level loading designs. Closing artefact: loading variants.
- `GAP-WEB-014`: search parameter names, the archived-inclusion default, the page size and the total field are proposed or unconfirmed, and pagination controls are undesigned. Closing artefact: a confirmed contract and a design.
- `GAP-WEB-015`: no token values, type scale, spacing or token bindings. Closing artefact: the full token files.
- `GAP-WEB-016`: no breakpoints. Closing artefact: a responsive spec.
- `GAP-WEB-017`: no browser support. Closing artefact: a browser matrix.
- `GAP-WEB-018`: no accessibility target. Closing artefact: an accessibility target.
- `GAP-WEB-019`: no bundle budget. Closing artefact: a budget.
- `GAP-WEB-020`: M4 and M5 screens, and EP-004 and EP-005 stories, have no owning surface or signing-in actor. Closing artefact: a decision on the owning surface.
- `GAP-WEB-021`: existing login methods and sign-in eligibility unknown. Closing artefact: answers to `Q-BE-003` and `GAP-BE-085`.
- `GAP-WEB-022`: contract IDs carry no version suffix. Closing artefact: a contract with versions.
- `GAP-WEB-023`: `403` versus `404` for inaccessible notes unresolved. Closing artefact: the access rule behind `CON-BE-003`.

**Assumptions:**
- `ASM-WEB-001`: route paths are proposed. Confirm with the router design.
- `ASM-WEB-002`: unauthenticated users go to `/login`, keeping an in-app return target. Confirm with a stated auth routing rule.
- `ASM-WEB-003`: `NoteEditorScreen` serves create (Empty) and edit (Filled); `NoteLifecycleScreen` hosts archive and restore and the archived view. Confirm by inspecting the frames.
- `ASM-WEB-004`: "Newest" means creation time, descending. Confirm with the requirement owner.
- `ASM-WEB-005`: the email is personal data and is held in memory only. Confirm with a data-protection policy (`GAP-BE-017`).
- `ASM-WEB-006`: cached server state is discarded on sign-in and on a `401` that ends the session. Confirm with a session rule.
- `ASM-WEB-007`: the client validates only that the email is non-empty. Confirm with a validation rule (`GAP-BE-035`).
- `ASM-WEB-008`: the client maps errors using the backend's proposed `code` registry. Confirm with the final error model.
- `ASM-WEB-009`: `OTP_CODE_USED` is handled like expired. Confirm with the story owner.
- `ASM-WEB-010`: the live count is client-derived. A server total would need a backend field.
- `ASM-WEB-011`: the request-new-code option is hidden while locked. Confirm with a lockout design.
<!-- /SECTION: web-app.coverage -->
<!-- SECTION: web-app.lane-needs | status: VERIFIED -->
## Lane needs

### Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-200601` | `api_contract` | Define a notes list operation returning active (non-archived) notes with pinned first, plus an archived-only list operation. The contract has neither; search cannot substitute because it requires a keyword or tag. | The notes list, live count, sort, pin toggle on rows and archived view cannot be built without a list endpoint (GAP-WEB-001, GAP-WEB-003). | yes | `web-app.state-management`, `web-app.routing`, `web-app.authorization-ui`, `web-app.module-map` |
| `HANDOFF-WEBAPP-743710` | `api_contract` | State what a successful verify-code response returns (body token, cookie or other), its lifetime, and whether any refresh operation exists. | Credential storage and attachment to requests, and session expiry handling, cannot be written without it (Q-BE-012, GAP-BE-302, GAP-WEB-008). | yes | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-163212` | `api_contract` | Confirm the HTTP statuses and final machine-readable error codes for verify-code refusals (wrong, expired, used, locked) and request-code resend limit. Also state whether a remaining lock time is returned. | Error display discriminates on proposed codes (OTP_CODE_INVALID etc.) that are unconfirmed, and the lockout message cannot show a countdown (GAP-WEB-010, GAP-WEB-011). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-123864` | `business_rule` | Resolve whether archiving an already-archived note (or unarchiving an active one) returns 200 idempotently or a conflict error such as 409. | Stories US-003-01/02 and BR-012 contradict (CON-WEB-001, Critical); archive and restore handling for notes already in the target state is blocked. | no | `web-app.state-management`, `web-app.coverage` |
| `HANDOFF-WEBAPP-518901` | `business_rule` | Specify the sort basis for 'Newest first' (created vs updated), title collation and tie-breaks, whether sort persists, and whether pin applies to archived notes. Also clarify how client-side sort and the live count behave across paginated search results. | Sort, count and pin behaviour rely on defaults assumed here (ASM-WEB-004, CON-WEB-002, CON-WEB-003, GAP-WEB-005, GAP-WEB-006). | no | `web-app.state-management`, `web-app.coverage` |
| `HANDOFF-WEBAPP-097443` | `business_rule` | State who is eligible to sign in via email code, whether notes are per-user or shared, and whether inaccessible notes return 403 or 404. | Authorization UI and 403 handling are unspecified (Q-BE-003, CON-BE-003, GAP-WEB-021, GAP-WEB-023). | no | `web-app.auth-flows`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-752655` | `screen` | Provide designs for the notes list, authenticated shell and navigation, sort control, live count, pin toggle/icon, login and code-entry error variants with Input/Text and resend control, screen-level loading states, pagination controls, and breakpoints. | These elements have no design frames, so they cannot be specified (GAP-WEB-002, GAP-WEB-007, GAP-WEB-013, GAP-WEB-014, GAP-WEB-016). | yes | `web-app.design-system`, `web-app.routing`, `web-app.state-management`, `web-app.auth-flows`, `web-app.responsive` |
| `HANDOFF-WEBAPP-087716` | `nfr` | Provide a supported browser matrix, an accessibility target, and a bundle/first-load budget for the web app. | These three sections are blocked or unspecified without them (GAP-WEB-017, GAP-WEB-018, GAP-WEB-019). | no | `web-app.browser-support`, `web-app.accessibility`, `web-app.performance` |
| `HANDOFF-WEBAPP-965208` | `story` | Decide whether web stories and screens exist for tag add/remove and for the M4/M5 screens (residency report, scope documentation), and which surface owns them. | Tag stories US-002-01/02/05 and EP-004/EP-005 stories are uncovered by this surface (GAP-WEB-004, GAP-WEB-020). | no | `web-app.coverage`, `web-app.overview`, `web-app.module-map` |
<!-- /SECTION: web-app.lane-needs -->
