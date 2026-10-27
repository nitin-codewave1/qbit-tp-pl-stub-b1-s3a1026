---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [US-006-01, US-006-02, US-006-04, US-001-03, US-003-01, US-003-02, US-003-06, US-006-03, US-001-02, US-003-05, US-006-05, US-002-01, US-002-02, US-002-05, US-006-06, US-003-03, US-001-06, US-004-01, US-004-05, US-004-02, US-004-04, US-004-06, US-004-03, US-005-02, US-005-06, US-001-05, US-005-01, US-005-03, US-005-04, US-005-05, US-003-04, US-001-04, US-001-01, US-002-03, US-002-04, US-002-06]
commit_kind: fork
confidence: agent
run_id: 7d84fbe3-9004-5fef-a410-03ba0401d091
okf_id: web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DRAFT-UNVERIFIED -->
## Overview

**Purpose.** The Web App is the browser client for `notes_api`. A Team member uses it to list, create, view, edit, search, tag, archive, restore, and pin or unpin team notes, and to sort the notes list, without calling the API directly. [stories.md EP-006, US-006-01..06; requirements.md BR-019, BR-020, BR-021; backend-api.md `backend.overview`]

**Primary actor.** Team member (authenticated). It is the only actor that any EP-006 story names. [stories.md EP-006]

**Contract.** This surface builds against contract pin `sha256:40000773c79c`. [RUN-CONTEXT `upstream.contract_pin`] It consumes the CR-112 revisions `API-NOTES-001@2`, `API-NOTES-002@2`, `API-NOTES-003@2`, `API-NOTES-004@2` and `API-SEARCH-001@2`. It consumes `API-NOTES-005@1` to `API-NOTES-008@1` unchanged. [backend-api.md `backend.api-surface`] Request and response schemas are referenced by these IDs and are not restated here.

**This revision.** A bootstrap run for change request CR-112, lane `web_app`, consumer side. The change adds a per-row pin toggle that calls the API and renders pinned notes at the top with a pin icon. [feedback.md CR-112; backend-api.md `backend.coverage` HANDOFF-BE-001] The client-side sort (Newest first, Oldest first, Title A-Z) and the live result count are also in scope. They add no API parameters. [requirements.md BR-019, BR-020]

**Design basis.** `spec_mode: FULL-FROM-BRIEF`. The design brief is present and Figma is not connected. [RUN-CONTEXT `inputs.figma_mcp`] Every frame ID and token name in this document is an unverified claim. Screen names follow `design-brief.md` exactly. Two surfaces named by the stories have no design screen: the notes list and the archived view (see `web-app.module-map`).

**Run status.** Critical contradiction `CON-WEB-001` concerns an archive state transition, so under core rule 8 it is Critical and maps to `HALT-CRITICAL-CONTRADICTION`. This document is emitted complete as requested. The orchestrator should hold the run. Archive and restore response handling is `BLOCKED` until `CON-WEB-001` is resolved by a human.

**Out of scope**, each cited:

- **Operator and administration functions.** These belong to the Admin surface, which is not in this run. [core I-1]
- **Residency and compliance screens:** `InfrastructureProvisioningScreen`, `PreLaunchCheckScreen`, `ResidencyReportScreen` (M4), and `ApiSpecDocumentationScreen`, `ScopeComplianceReportScreen` (M5). Their stories name non-Team-member actors. [design-brief.md screens; stories.md EP-004, EP-005] No owning surface is selected for them. [GAP-WEB-024]
- **Note deletion.** No delete operation exists in the contract, and no story asks for one. No delete control is offered. [backend-api.md `backend.api-surface`]
- **Attachments and file upload.** Create and edit reject attachment payloads. No attachment control is built. [requirements.md BR-016]
- **Real-time or push updates.** None is exposed. [requirements.md BR-017] "Live" in the result count means it recalculates when the displayed set changes. It does not mean server push.
- **Mobile-specific behaviour.** [requirements.md BR-018]
- **Offline use.** No input requires it. Not specified.
- **Distinct-tag browsing and an archived-only list.** These are in the stories but have no endpoint in the contract. They are `BLOCKED`, not silently dropped. [WEB-TAGS-FR-002, WEB-ARCHIVE-FR-002]

<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. The paths below are proposed under `ASM-WEB-001`. Screen names come from the design inventory.

| Route | Screen (module) | Protected | Required role | Basis |
|---|---|---|---|---|
| `/login` | none in design | No | none | `GAP-WEB-003`, `ASM-WEB-002` |
| `/notes` | notes list: no design screen (`GAP-WEB-001`) | Yes | Team member | US-006-01; CR-112 |
| `/notes/new` | `NoteCreateScreen` (M1) | Yes | Team member | US-006-02 |
| `/notes/search` | `SearchScreen` (M2) | Yes | Team member | US-006-04 |
| `/notes/:id` | `NoteDetailScreen` (M3) | Yes | Team member | US-006-02 ("navigates to the saved note"); stories-map US-001-03, US-003-01, US-003-02, US-003-06 |
| `/notes/:id/edit` | `NoteEditScreen` (M1) | Yes | Team member | US-006-03; stories-map US-001-02, US-003-05 |
| `/notes/:id/tags` | `TagManagementScreen` (M2) | Yes | Team member | US-006-05; stories-map US-002-01, US-002-02 |
| `/tags` | `TagListScreen` (M2) | Yes | Team member | US-002-05; `BLOCKED`, see `WEB-TAGS-FR-002` |
| archived view | no design screen (`GAP-WEB-002`) | Yes | Team member | US-006-06, US-003-03; `BLOCKED`, see `WEB-ARCHIVE-FR-002` |

**Invariant `INV-WEB-001`.** The static segment `/notes/new` and `/notes/search` must match before `/notes/:id`. Otherwise the router reads them as note identifiers.

**Layout nesting.** All protected routes are assumed to sit under one authenticated shell. No header, navigation or layout component appears in the design inventory. [GAP-WEB-004]

**Protected-route rule.** Every route except `/login` requires an authenticated session. A `401` from any endpoint is handled as in `web-app.auth-flows`.

**Unauthenticated redirect.** Redirect to `/login`, keeping the requested route as the return target. [`ASM-WEB-002`]

**Flow edges.** `TagManagementScreen` has two `ON_CLICK → SearchScreen` transitions. [design-brief.md; screens-inventory] `ASM-WEB-004`: these are tag-filter navigations into `/notes/search` with that tag applied.

<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module map

| Module | Feature area | Routes owned | Source screens and stories |
|---|---|---|---|
| `notes` | Browse with sort, live count and pin toggle; create; view; edit | `/notes`, `/notes/new`, `/notes/:id`, `/notes/:id/edit` | notes list (no design screen); `NoteCreateScreen`, `NoteDetailScreen`, `NoteEditScreen`; US-006-01..03, US-006-06 (pin) |
| `search` | Keyword and tag-filter search, with sort and live count | `/notes/search` | `SearchScreen`; US-006-04 |
| `tags` | Tag add and remove on a note; tag discovery | `/notes/:id/tags`, `/tags` | `TagManagementScreen`, `TagListScreen`; US-006-05; US-002-05 (`BLOCKED`) |
| `archive` | Archive and restore; archived view | archive actions on `/notes/:id`; archived view has no route | `NoteDetailScreen` (archive action, by stories-map); US-006-06; archived view has no screen (`BLOCKED`) |

**Excluded.** Design modules M4 and M5. No route is minted for them. [`web-app.overview`]

**Shared concern.** The sort control and the live result count are shared by the notes list and search. US-006-04 requires that search keep the selected sort order. [stories.md US-006-01, US-006-04; requirements.md BR-020]

**Shared concern.** The pin toggle is on list rows. Pinned notes sort first in the list and in search. [stories.md US-006-01, US-006-06; requirements.md BR-021]

This grouping rearranges screens and stories already cited elsewhere in this document. It is a derived structure, not a new fact.

<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state or data-fetching library. [GAP-WEB-008] The boundary below is conceptual.

**Server state.** Note records, note tag lists, the notes list, search results and the pinned flag. Each is read through its pinned endpoint:

- Note detail: `API-NOTES-003@2`
- Notes list: `API-NOTES-004@2`
- Search: `API-SEARCH-001@2`
- Create: `API-NOTES-001@2`
- Edit and pin: `API-NOTES-002@2`
- Tag add and remove: `API-NOTES-005@1`, `API-NOTES-006@1`
- Archive and restore: `API-NOTES-007@1`, `API-NOTES-008@1`

Distinct tags have no pinned endpoint. [`HANDOFF-WEB-001`, `GAP-WEB-023`] The archived-only list has none either. [`HANDOFF-WEB-002`, `GAP-WEB-024`]

**Client state.**

- Unsubmitted form values on `NoteCreateScreen`, `NoteEditScreen` and `TagManagementScreen`.
- The search text and tag filter before a search is issued.
- The sort selection (`INV-WEB-005`). It is persisted across reloads in `localStorage`. [requirements.md BR-020; stories.md US-006-01] It is not stored on the server.
- Page position, if the list is paginated. [`GAP-WEB-022`]

**Rules applied to server state:**

- `INV-WEB-002`: the client never sends or computes `created_at` or `updated_at`, including optimistically. [requirements.md BR-003; stories.md US-001-06]
- Tag names are displayed as the server returns them. Normalisation is server-side. [requirements.md BR-005]
- `INV-WEB-003`: edit and tag controls are never gated on archive state. [requirements.md BR-010; stories.md US-003-05]
- `INV-WEB-007`: a pin change sends a PATCH that carries only `pinned`. Title, body and tags are not sent and do not change. [backend-api.md `backend.coverage` INV-BE-002; HANDOFF-BE-001]

**Sort (BR-020, US-006-01, US-006-04).**

- Options: **Newest first**, **Oldest first**, **Title A-Z**. [stories.md US-006-01]
- Default is Newest first. [stories.md US-006-01] The sort basis for "Newest" is not stated. The default is `created_at` descending. [`ASM-WEB-005`]
- The sort is performed client-side and is never sent to the API. `INV-WEB-005`. [requirements.md BR-020]
- Pinned notes precede unpinned notes in every sort order, including filtered and search results. `INV-WEB-006`. [requirements.md BR-021]
- The sort selection does not change when search runs. Search results render in the selected order. [stories.md US-006-04]
- Collation for titles, case handling, tie-breaking on equal values and the `localStorage` key name are unspecified. [`GAP-WEB-021`]
- The server returns its own order: pinned first, then most recently updated first. The client re-sorts it. [backend-api.md `API-NOTES-004@2`, `CON-WEB-006`]

**Pin toggle (CR-112, US-006-06).**

- Each list row has a pin toggle. Pinning sends a PATCH of `pinned`. [stories.md US-006-06; backend-api.md `API-NOTES-002@2`]
- On success the row moves to the top with a pin icon. Unpinning returns the row to its sort position. [stories.md US-006-06]
- The client does not optimistically reorder rows. No input specifies optimistic pin behaviour. [`GAP-WEB-027`]
- Whether pin is available on an archived note is unspecified. [`GAP-WEB-025`]
- The pin toggle has no design or component. [`GAP-WEB-007`]

**Live result count (BR-019, US-006-01, US-006-04).** Derived client-side from the displayed result set. It is not stored separately.

- Notes list: equals the number of notes rendered for the active list. It shows `0` when the list is empty. [requirements.md BR-019; stories.md US-006-01]
- Search: equals the number of notes rendered for the current keyword and tag filters. [requirements.md BR-019] US-006-04 instead says the count equals the number of matches. `CON-WEB-003` records this. The default applied is BR-019, the rendered count.
- `INV-WEB-004`: the count is not shown while results load. It is recomputed on every filter, sort or result change, so a count from a previous filter never persists.
- `ASM-WEB-006`: the count is client-derived. A server total would need a new handoff.

**Empty state.** Shown when the resolved list is empty. It uses `Empty/State`. [`ASM-WEB-003`] Copy is not supplied. [`GAP-WEB-006`]

**Loading state.** No screen in the design inventory has a loading variant. [`GAP-WEB-005`]

**Cache and invalidation.** Derived from cited rules:

- After create, edit, tag add, tag remove, archive, unarchive or pin, invalidate the affected note detail, the notes list, search results, the archived list and the tag list. Archive, tag and pin changes alter list ordering or membership. [requirements.md BR-008, BR-011; stories.md US-006-06]
- After a successful archive, the active list count drops by one. This follows from invalidation, not from local arithmetic.
- Re-sorting is local and needs no refetch when the sort selection changes.
- The archive and restore branch for a note already in that state is affected by `CON-WEB-001`. Its response handling is `BLOCKED`.

The staleness window and retry policy are unspecified. [`GAP-WEB-008`]

<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

The backend names no authentication mechanism and exposes no authentication endpoint. [backend-api.md `backend.auth-model`, `GAP-BE-014`, `Q-BE-002`] The backend's assumed default is bearer-token authentication. This section is therefore largely defaults. Its one cited fact is that per-user authentication is mandatory. [feedback.md; backend-api.md `backend.auth-model`]

**Login.** No story or design screen covers login. [`GAP-WEB-003`] Default taken: `/login` collects the credential the backend's assumed bearer-token scheme requires. On success, return to the requested route.

**Session expiry and 401.** Any `401` from an endpoint clears the client session and redirects to `/login`, keeping the requested route as the return target. [`ASM-WEB-002`]

**Mid-flow expiry.** A mutation that returns `401` is not applied locally, and no optimistic result is shown. Whether unsaved draft content survives the redirect is not stated. [`GAP-WEB-009`] No claim is made either way.

**Refresh.** No input describes silent refresh or token rotation. [`GAP-WEB-010`]

**Logout.** No screen, control or story describes it. [`GAP-WEB-011`]

**Credential storage.** Where the credential is held is not specified. [`GAP-WEB-013`] No storage location is assumed. The sort preference in `localStorage` holds no credential. [`INV-WEB-005`]

**403.** The split between `401` and `403` is unresolved. [backend-api.md `GAP-BE-007`] No `403` handling is specified. [`GAP-WEB-012`] A `403` must not be treated as `401` without a decision.

**Error body.** The error envelope shape is not confirmed by any input. [backend-api.md `ASM-BE-008`, `GAP-BE-006`] Client error display must not depend on a field name until that is confirmed. [`GAP-WEB-028`]

<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DERIVED -->
## Authorization UI

Exactly one role signs in to this surface.

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Team member (authenticated) | Notes list, New note, Search, Tags, Archived notes | Create, view, edit, add and remove tags, pin and unpin, archive and restore, search, change sort, view live count. Browse distinct tags is `BLOCKED` (`WEB-TAGS-FR-002`). Archived view is `BLOCKED` (`WEB-ARCHIVE-FR-002`). |
| Unauthenticated | none; redirected to `/login` | none |

The Team member's enabled actions match the backend permission-matrix row for Team member. Pin and unpin fall under Note Update, so no separate pin permission exists. [backend-api.md `backend.auth-model`] No delete action exists in the contract.

**Roles excluded from this surface.** Actors are as named in `stories.md`. The web spec follows `stories.md` over `backend-api.md` where they differ. [`CON-WEB-004`]

| Actor (stories.md) | Stories | Reason excluded |
|---|---|---|
| Platform operations engineer | US-004-01, US-004-05 | Not a Team member. Associated screens are M4, out of scope. |
| Compliance auditor | US-004-02, US-004-04, US-004-06 | Not a Team member. Associated screens are M4, out of scope. |
| Backend developer | US-004-03, US-005-02, US-005-06 | Not a Team member. Calls the API directly. No screen. |
| Developer integrating with notes_api | US-001-03, US-001-05, US-003-05 | Not a Team member. Calls the API directly. No screen in this surface. |
| Operations engineer | US-003-06 | Not a Team member. No screen. |
| API tech lead | US-005-01, US-005-03 | Not a Team member. Associated screen is M5, out of scope. |
| QA engineer | US-005-04 | Not a Team member. Associated screen is M5, out of scope. |
| Product owner | US-005-05 | Not a Team member. Associated screen is M5, out of scope. |

No Admin-surface actor is in scope for this run. [core I-1]

<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

**Tokens.** `design-brief.md` lists 71 token identifiers in five categories: `UI/border/*`, `UI/color/*`, `UI/icon/*`, `UI/surface/*` and `UI/text/*`. The index gives identifiers and file references only. No token value is supplied. [`GAP-WEB-014`] Figma is not connected, so no identifier is verified. No visual value is stated or hard-coded here.

**Type scale and spacing.** No token belongs to either category. [`GAP-WEB-015`] Status: `BLOCKED` for these two subsections.

**Components.** Two are inventoried. [design-brief.md; components-inventory]

- `Empty/State`: one variant, state Default. Mapped to `NoteCreateScreen`, `SearchScreen`. [screens-inventory]
- `Input/Text`: five variants, states Default, Focused, Filled, Error, Disabled. Mapped to `NoteCreateScreen`. [screens-inventory]

**Not covered by any component.**

- The notes list has no screen, so its layout, row structure and pin placement are unspecified. [`GAP-WEB-001`]
- `NoteEditScreen` has no component mapping. Its form controls are unspecified. [`GAP-WEB-016`]
- The sort control, the pin toggle and the pin icon have no design or component. [`GAP-WEB-007`]
- Full component bodies (props, bindings) are omitted from the chain handoff and are not available to this run.

**Screen variants.** [screens-inventory]

| Screen | Variants |
|---|---|
| `NoteCreateScreen` | Default, Empty, Error, Filled |
| `NoteDetailScreen` | Default, Empty, Error, Filled |
| `NoteEditScreen` | Default, Error |
| `SearchScreen` | Default, Empty, Error |
| `TagManagementScreen` | Default, Error, Filled |
| `TagListScreen` | Default, Filled |

<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behaviour

**Blocked.** No input defines a breakpoint, a viewport category, or a layout change at any breakpoint. The design variants (Default, Empty, Error, Filled) are data states, not viewport states. No breakpoint token exists. [`GAP-WEB-017`]

**Unblocks with:** a responsive specification from the design source, covering the notes list (which has no screen), the pin placement on rows, and the sort control's placement at each breakpoint.

<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**Blocked.** The platform input names the framework (React 18 with Vite) but no supported browser or version. [platform.md; `GAP-WEB-018`]

**Unblocks with:** a browser-support matrix from the product or platform requirements.

<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input states a target standard, a verification method, keyboard requirements for the sort control or the pin toggle, or how the live result count and the pin state are announced to assistive technology. [`GAP-WEB-019`]

**Unblocks with:** an accessibility target and announcement behaviour for the live count, the sort change, and the pin toggle state.

<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: DRAFT-UNVERIFIED -->
## Performance

**Bundle budget and code splitting: blocked.** No input states a bundle budget, a code-splitting strategy, or a largest-payload route for this surface. [`GAP-WEB-020`] The platform input names React 18 and Vite, which makes route-level splitting possible, but it does not require it.

**Cited targets that bind this surface's requests.** The two requirement sources state different targets. Both are cited, and the conflict is recorded. [`CON-WEB-005`]

- Search median latency ≤300 ms under normal load. [requirements.md NFR-001]
- Core CRUD p95 latency ≤200 ms. [requirements.md NFR-002]
- Search default page size 20, maximum 100, configurable by query parameter. [requirements.md NFR-003]
- The contract states search p95 <300 ms and page sizes up to 100. [backend-api.md `backend.nfr` NFR-006, NFR-007]

The client-side sort and the pin toggle add no request for the sort. The pin toggle adds one PATCH per action. [requirements.md BR-020; backend-api.md `API-NOTES-002@2`]

**Largest-payload route.** Unknown. `GET /notes` is the candidate. Its default and maximum page size are not stated for that endpoint. [`GAP-WEB-022`, backend-api.md `GAP-BE-015`]

**Unblocks with:** a frontend performance budget and the pagination behaviour of `GET /notes`.

<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

### Requirement register

Full Given/When/Then detail belongs in module specifications, which are not produced in this run.

| ID | Covers | Pinned API | Traces to | Verification (planned) | Status |
|---|---|---|---|---|---|
| `WEB-NOTES-FR-001` | Notes list: active notes with title and status; empty state; error state; live count | `API-NOTES-004@2` (parameters `GAP-WEB-022`) | US-006-01, US-003-04, US-001-04; BR-011, BR-019 | `notes-list.empty`, `notes-list.count`, `notes-list.error` | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-002` | Create note; inline validation; navigate to saved note | `API-NOTES-001@2` | US-006-02, US-001-01, US-001-05; BR-001, BR-004 | `notes-create.submit`, `notes-create.inline-error` | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-003` | Edit note; not-found and field-error handling | `API-NOTES-002@2` (PATCH, `ASM-BE-001`) | US-006-03, US-001-02, US-003-05; BR-003, BR-004, BR-010 | `notes-edit.not-found`, `notes-edit.field-errors` | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-004` | Note detail view; error state for malformed or unknown identifiers | `API-NOTES-003@2` | US-006-02 (navigation), US-001-03, US-003-06 (partial) | `notes-detail.render`, `notes-detail.bad-id` | DRAFT-UNVERIFIED |
| `WEB-PIN-FR-001` | Pin toggle per list row; PATCH of `pinned` only; row moves to top with pin icon; unpin returns to sort position | `API-NOTES-002@2` | US-006-06; CR-112; BR-021; INV-BE-002 | `pin.toggle`, `pin.moves-to-top`, `pin.unpin-returns` | DRAFT-UNVERIFIED; `GAP-WEB-007`, `GAP-WEB-025`, `GAP-WEB-027` |
| `WEB-SORT-FR-001` | Sort control (Newest first default, Oldest first, Title A-Z); persisted across reloads; applied to list and search with pinned-first | none (client-side) | US-006-01, US-006-04; BR-020, BR-021 | `sort.default-newest`, `sort.persists-reload`, `sort.applies-to-search`, `sort.pinned-first` | DRAFT-UNVERIFIED; `GAP-WEB-021`, `CON-WEB-006` |
| `WEB-SEARCH-FR-001` | Keyword and tag-filter search; sort and live count applied to results | `API-SEARCH-001@2` | US-006-04, US-002-03, US-002-04; BR-019 | `search.keyword`, `search.tag-filter`, `search.empty`, `search.count` | DRAFT-UNVERIFIED; `CON-WEB-003` |
| `WEB-TAGS-FR-001` | Tag add and remove on a note; display server-returned tag list | `API-NOTES-005@1`, `API-NOTES-006@1` | US-006-05, US-002-01, US-002-02, US-003-05 | `tags.add`, `tags.remove` | DRAFT-UNVERIFIED |
| `WEB-TAGS-FR-002` | Browse distinct tags for filter discovery | none in contract | US-002-05; BR-008 | `tags.list` (planned) | BLOCKED: `GAP-WEB-023`, `HANDOFF-WEB-001` |
| `WEB-ARCHIVE-FR-001` | Archive and restore a note; invalidation of lists; response handling for already-in-state | `API-NOTES-007@1`, `API-NOTES-008@1` | US-006-06, US-003-01, US-003-02; BR-007, BR-008, BR-012 | `archive.toggle`, `archive.already-in-state` (planned) | BLOCKED: `CON-WEB-001` (Critical) |
| `WEB-ARCHIVE-FR-002` | Archived-only list view | none in contract | US-003-03, US-006-06; BR-011 exception | `archive.list` (planned) | BLOCKED: `GAP-WEB-002`, `GAP-WEB-024`, `HANDOFF-WEB-002` |

### Invariants and assumptions

| ID | Statement | Basis |
|---|---|---|
| `INV-WEB-001` | Static route segments match before `/notes/:id` | routing |
| `INV-WEB-002` | Client never sends or computes `created_at` or `updated_at` | BR-003; US-001-06 |
| `INV-WEB-003` | Edit and tag controls are not gated on archive state | BR-010; US-003-05 |
| `INV-WEB-004` | Live count is hidden while loading and recomputed on change | BR-019 |
| `INV-WEB-005` | Sort is client-side, never sent to the API, persisted in `localStorage` | BR-020 |
| `INV-WEB-006` | Pinned notes render above unpinned in every sort order, including filtered and search results | BR-021 |
| `INV-WEB-007` | A pin change sends only `pinned`; title, body and tags are unchanged | backend INV-BE-002; HANDOFF-BE-001 |
| `ASM-WEB-001` | Route paths are proposed | no input supplies them |
| `ASM-WEB-002` | Unauthenticated redirect target is `/login` with return target | no input supplies it |
| `ASM-WEB-003` | Empty state uses `Empty/State` on the notes list | no notes-list screen |
| `ASM-WEB-004` | `TagManagementScreen` transitions are tag-filter navigations into `/notes/search` | screens-inventory |
| `ASM-WEB-005` | "Newest first" orders by `created_at` descending | US-006-01 names the order but not its basis |
| `ASM-WEB-006` | Live count is client-derived until a server total exists | BR-019 |
| `ASM-WEB-007` | Pin toggle is available on list rows only; not specified on detail or search rows | US-006-06 names list rows only |

### Open items

| ID | Kind | Item |
|---|---|---|
| `GAP-WEB-001` | gap | No design screen for the notes list. Layout, row structure, pin placement and sort control placement are unspecified. |
| `GAP-WEB-002` | gap | No design screen for the archived view. Its route and layout are unspecified. |
| `GAP-WEB-003` | gap | No login or session screen. Login behaviour is defaulted under `web-app.auth-flows`. |
| `GAP-WEB-004` | gap | No header, navigation or layout component in the inventory. |
| `GAP-WEB-005` | gap | No loading variant in any screen. |
| `GAP-WEB-006` | gap | Empty-state copy is not supplied. US-006-01 requires an empty-state message. |
| `GAP-WEB-007` | gap | No design or component for the sort control, pin toggle or pin icon. |
| `GAP-WEB-008` | gap | No state or data-fetching library named. Staleness and retry policy unspecified. |
| `GAP-WEB-009` | gap | Whether unsaved draft content survives a `401` redirect is unstated. |
| `GAP-WEB-010` | gap | No refresh or token rotation described. |
| `GAP-WEB-011` | gap | No logout described. |
| `GAP-WEB-012` | gap | `403` handling unspecified. The `401`/`403` split is `GAP-BE-007`. |
| `GAP-WEB-013` | gap | Credential storage location unspecified. |
| `GAP-WEB-014` | gap | Token values not supplied. Figma not connected. |
| `GAP-WEB-015` | gap | Type scale and spacing tokens absent. |
| `GAP-WEB-016` | gap | `NoteEditScreen` form controls have no component mapping. |
| `GAP-WEB-017` | gap | No breakpoints supplied. |
| `GAP-WEB-018` | gap | No supported browser versions supplied. |
| `GAP-WEB-019` | gap | No accessibility target or announcement behaviour supplied. |
| `GAP-WEB-020` | gap | No bundle budget or code-splitting strategy supplied. |
| `GAP-WEB-021` | gap | Sort collation, case handling, tie-breaking and the `localStorage` key are unspecified. |
| `GAP-WEB-022` | gap | `GET /notes` parameters, default and maximum page size are unspecified. Backend `GAP-BE-001`, `GAP-BE-015`. |
| `GAP-WEB-023` | gap | No endpoint for distinct tags. US-002-05 and BR-008 require one. `HANDOFF-WEB-001`. |
| `GAP-WEB-024` | gap | No archived-only list endpoint. BR-011 names one as an exception. Also no owning surface for M4 and M5 screens. `HANDOFF-WEB-002`. |
| `GAP-WEB-025` | gap | Whether pin is available on archived notes, and how archived notes sort. Backend `GAP-BE-019`. |
| `GAP-WEB-026` | gap | No maximum pinned count is stated, so the UI has no cap. Backend `GAP-BE-018`. |
| `GAP-WEB-027` | gap | Concurrent PATCH conflict response is unspecified. Backend `GAP-BE-022`. Pin optimism is therefore not specified. |
| `GAP-WEB-028` | gap | The error envelope shape is unconfirmed. Backend `ASM-BE-008`, `GAP-BE-006`. |
| `CON-WEB-001` | contradiction, **Critical** | See below. |
| `CON-WEB-002` | contradiction | Preflight says "existing pin field and endpoint, use as-is". CR-112 and the contract add `pinned`. Non-critical for the web: the web consumes the `@2` shape the contract states. The conflict concerns backend rollout history. |
| `CON-WEB-003` | contradiction | Search live count: BR-019 counts rendered notes; US-006-04 counts matches. Default BR-019. |
| `CON-WEB-004` | contradiction | Backend contract cites story and feedback IDs that differ from the supplied inputs (archive is `US-002-04` in the contract and `US-003-01` in stories.md). Web follows stories.md. |
| `CON-WEB-005` | contradiction | NFR numbering and targets differ. Contract: search p95 <300 ms (NFR-006), page 100 (NFR-007). Requirements: search median ≤300 ms (NFR-001), CRUD p95 ≤200 ms (NFR-002), default 20 and max 100 (NFR-003). Web cites requirements. |
| `CON-WEB-006` | contradiction | Server order is pinned-first then most recently updated. Web default is Newest first by `created_at`. Web re-sorts client-side, so display follows the web default. |
| `HANDOFF-WEB-001` | handoff | Backend to add a distinct-tags endpoint. Required by US-002-05. |
| `HANDOFF-WEB-002` | handoff | Backend to add an archived-only list, or confirm `include_archived` semantics without `q` or `tags`. Required by US-003-03. |

**`CON-WEB-001` (Critical).** Archive and restore on a note already in that state.

- Claim A: an already-archived note returns `200` idempotently with no state change. [stories.md US-003-01 AC3]
- Claim A′: an already-active note returns `200` idempotently on unarchive. [stories.md US-003-02 AC3]
- Claim B: archive and unarchive "return a consistent error for invalid state transitions (e.g., archiving an already-archived note)". [requirements.md BR-012]
- Claim C: an already-archived note returns `409` (assumed). [backend-api.md `ASM-BE-005`]

BR-012 itself says both idempotent success and an error. The web cannot choose a response branch until this is resolved. Consequence of Claim A: archive and restore never show an error for repeats. Consequence of Claim C: the web must show a conflict message. `WEB-ARCHIVE-FR-001` is `BLOCKED` on this item.

### Story coverage

All 36 supplied stories are listed. Stories in EP-001 to EP-005 are backend or residency stories. They are listed as covered by this surface only where the stories-map links a design screen, and otherwise as out of run.

| Story | Covered by | Coverage |
|---|---|---|
| US-001-01 | `WEB-NOTES-FR-002` (`NoteCreateScreen`) | covered for the web create path; API is backend |
| US-001-02 | `WEB-NOTES-FR-003` (`NoteEditScreen`) | covered for the web edit path |
| US-001-03 | `WEB-NOTES-FR-004` (`NoteDetailScreen`) | covered for the web detail path |
| US-001-04 | `WEB-NOTES-FR-001` (traces to US-006-01) | out of run; no screen in stories-map |
| US-001-05 | `WEB-NOTES-FR-002` (`NoteCreateScreen`) | covered for the web validation display |
| US-001-06 | `INV-WEB-002` (`NoteEditScreen`) | covered for the client rule |
| US-002-01 | `WEB-TAGS-FR-001` (`TagManagementScreen`) | covered |
| US-002-02 | `WEB-TAGS-FR-001` (`TagManagementScreen`) | covered |
| US-002-03 | `WEB-SEARCH-FR-001` (`SearchScreen`) | covered |
| US-002-04 | `WEB-SEARCH-FR-001` (`SearchScreen`) | covered |
| US-002-05 | `WEB-TAGS-FR-002` | **BLOCKED**: `GAP-WEB-023`, `HANDOFF-WEB-001` |
| US-002-06 | none for this surface; list and search pagination in `GAP-WEB-022` | out of run for the web |
| US-003-01 | `WEB-ARCHIVE-FR-001` | **BLOCKED**: `CON-WEB-001` (Critical) |
| US-003-02 | `WEB-ARCHIVE-FR-001` | **BLOCKED**: `CON-WEB-001` (Critical) |
| US-003-03 | `WEB-ARCHIVE-FR-002` | **BLOCKED**: `GAP-WEB-002`, `GAP-WEB-024`, `HANDOFF-WEB-002` |
| US-003-04 | `WEB-NOTES-FR-001` | covered |
| US-003-05 | `WEB-NOTES-FR-003`, `WEB-TAGS-FR-001`, `INV-WEB-003` | covered |
| US-003-06 | `WEB-NOTES-FR-004` | partial: archive-side 400 display is in the `BLOCKED` archive requirement |
| US-004-01 | none; residency screen (M4) | out of run |
| US-004-02 | none; residency screen (M4) | out of run |
| US-004-03 | none; backend and infrastructure | out of run |
| US-004-04 | none; backend and infrastructure | out of run |
| US-004-05 | none; residency screen (M4) | out of run |
| US-004-06 | none; residency screen (M4) | out of run |
| US-005-01 | none; API scope, M5 | out of run |
| US-005-02 | none; backend validation | out of run |
| US-005-03 | none; backend protocol | out of run |
| US-005-04 | none; scope test suite | out of run |
| US-005-05 | none; documentation, M5 | out of run |
| US-005-06 | none; backend validation | out of run |
| US-006-01 | `WEB-NOTES-FR-001`, `WEB-SORT-FR-001`, `WEB-PIN-FR-001` | covered; pin row and sort placement `GAP-WEB-001` |
| US-006-02 | `WEB-NOTES-FR-002` | covered |
| US-006-03 | `WEB-NOTES-FR-003` | covered |
| US-006-04 | `WEB-SEARCH-FR-001`, `WEB-SORT-FR-001` | covered; live count `CON-WEB-003` |
| US-006-05 | `WEB-TAGS-FR-001` | covered |
| US-006-06 | `WEB-PIN-FR-001` (covered); `WEB-ARCHIVE-FR-001` (`BLOCKED`) | partial: archive `BLOCKED` |

**Totals.** 36 stories supplied. 6 belong to this surface (US-006-01..06). Of those, 4 are covered, 1 is partial, and 1 is partly `BLOCKED`. Of the 30 backend and residency stories, 13 are linked to a web screen by the stories-map and are listed above. The remaining 17 are out of run. Stories with `BLOCKED` coverage: US-002-05, US-003-01, US-003-02, US-003-03.

**CR-112 web pin toggle.** Backend `HANDOFF-BE-001` requests the pin toggle, the PATCH of `API-NOTES-002@2`, rendering in server order with no client re-sort, and adaptation to the `@2` list and search endpoints. This surface treats `HANDOFF-BE-001` as an instruction for the pin toggle only. It does not render in server order alone, because BR-020 requires client-side sorting. The conflict is recorded in `CON-WEB-006`.

<!-- /SECTION: web-app.coverage -->
