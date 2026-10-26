---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [US-006-01, US-004-01, US-005-01, US-006-02, US-001-03, US-001-06, US-006-03, US-001-02, US-006-05, US-002-01, US-002-02, US-002-05, US-006-04, US-006-06, US-003-05, US-004-05, US-004-02, US-004-04, US-004-06, US-004-03, US-005-02, US-005-06, US-001-05, US-003-06, US-005-03, US-005-04, US-005-05, US-001-04, US-003-04, US-001-01, US-002-03, US-002-04, US-002-06, US-003-01, US-003-02, US-003-03]
commit_kind: fork
confidence: agent
run_id: ed3b268d-0f39-5431-9196-9f249ebe81aa
okf_id: web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DERIVED -->
## Overview

**Purpose.** The browser client for `notes_api`. An authenticated Team member lists, creates, views, edits, searches, tags, archives and restores team notes without calling the API directly, and sorts the notes list. [stories.md EP-006, US-006-01..06; feedback.md CR-111]

**Primary actor.** Team member (authenticated). It is the only actor any EP-006 story names. [stories.md EP-006]

**Contract.** This surface builds against contract pin `sha256:300a749989fb`. [RUN-CONTEXT `upstream.contract_pin`] It consumes `API-NOTES-001@1` to `API-NOTES-008@1` and `API-SEARCH-001@1` unchanged. [backend-api.md `backend.api-surface`] Request and response schemas are referenced by those IDs and are not restated here.

**This revision.** Bootstrap run, including change request CR-111: a client-side sort control (Newest first, Oldest first, Title A-Z) on the notes list, persisted across reloads and applied during search. [feedback.md CR-111; preflight answers] The sort adds no API parameters. [requirements.md BR-020] The live result count follows BR-019. [requirements.md BR-019]

**Design basis.** `spec_mode: FULL-FROM-BRIEF`. The design brief is present and Figma is not connected. [RUN-CONTEXT `figma_mcp`] Every frame ID and token name in this document is an unverified claim. Screen names follow `design-brief.md` exactly.

**Run status.** `CON-WEB-003` is Critical under core rule 8, because it concerns an archive state transition. It maps to `HALT-CRITICAL-CONTRADICTION` (core 10.1). This document is emitted complete as requested. The orchestrator should hold the run. The affected archive branches are `BLOCKED` here.

**Out of scope**, each cited:

- **Operator and administration functions.** These belong to the Admin surface, which is not in this run. [core I-1]
- **Residency and compliance screens** `PreLaunchResidencyCheckScreen`, `ResidencyReportScreen` (M4), and `ApiDocumentationScreen`, `ScopeComplianceReportScreen` (M5). [design-brief.md screens] Their stories name non-Team-member actors. [stories.md US-004-01..06, US-005-01..06] No owning surface is selected for them. `GAP-WEB-001`.
- **Attachments and file upload.** Create and edit reject attachment payloads. [requirements.md BR-016] No attachment control is built.
- **Real-time or push updates.** None is exposed. [requirements.md BR-017] "Live" in the result count means it recalculates when the displayed set or filters change. It does not mean server push.
- **Mobile-specific behaviour.** [requirements.md BR-018]
- **Note deletion.** No delete operation is in the pinned contract. [backend-api.md `backend.api-surface`] No delete control is offered.
- **Offline use.** No input requires it. Not specified.

<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. The paths below are proposed under `ASM-WEB-001`. Screen names are from the design inventory.

| Route | Screen (module) | Protected | Required role | Basis |
|---|---|---|---|---|
| `/login` | none in design (`GAP-WEB-003`) | No | none | `ASM-WEB-002`, `Q-WEB-001` |
| `/notes` | notes list: no design screen (`GAP-WEB-002`) | Yes | Team member | US-006-01; CR-111 |
| `/notes/new` | `NoteCreateScreen` (M1) | Yes | Team member | US-006-02 |
| `/notes/:id` | `NoteDetailScreen` (M1) | Yes | Team member | US-006-02 ("navigates to the saved note"); stories-map US-001-03, US-001-06 |
| `/notes/:id/edit` | `NoteEditScreen` (M6) | Yes | Team member | US-006-03; stories-map US-001-02 |
| `/notes/:id/tags` | `TagManagementScreen` (M2) | Yes | Team member | US-006-05; stories-map US-002-01, US-002-02 |
| `/tags` | `TagListScreen` (M2) | Yes | Team member | US-002-05; blocked, see `WEB-TAGS-FR-002` |
| `/notes/search` | `SearchScreen` (M2) | Yes | Team member | US-006-04 |
| `/notes/archived` | `NoteArchiveScreen` (M3) | Yes | Team member | US-006-06; list blocked, see `WEB-ARCHIVE-FR-002` |

**Invariant `INV-WEB-001`.** The static segments `/notes/new`, `/notes/search` and `/notes/archived` must match before `/notes/:id`. Otherwise the router reads them as note identifiers.

**Layout nesting.** All protected routes are assumed to sit under one authenticated shell. `GAP-WEB-004`: no header, navigation or layout component appears in the inventory.

**Protected-route rule.** Every route except `/login` requires an authenticated session. A `401` from any pinned endpoint is handled as in `web-app.auth-flows`.

**Unauthenticated redirect.** Redirect to `/login`, keeping the requested route as the return target (`ASM-WEB-002`).

**Flow edges.** `TagManagementScreen` has two `ON_CLICK → SearchScreen` transitions. [design-brief.md; screens-inventory] `ASM-WEB-004`: these are tag-filter navigations into `/notes/search` with the tag applied.

<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module map

| Module | Feature area | Routes owned | Source screens and stories |
|---|---|---|---|
| `notes` | Browse (with sort and live count), create, view, edit | `/notes`, `/notes/new`, `/notes/:id`, `/notes/:id/edit` | notes list (no design screen); `NoteCreateScreen`, `NoteDetailScreen`, `NoteEditScreen`; US-006-01..03 |
| `search` | Keyword and tag-filter search, with sort and live count | `/notes/search` | `SearchScreen`; US-006-04 |
| `tags` | Tag assignment on a note; tag discovery | `/notes/:id/tags`, `/tags` | `TagManagementScreen`, `TagListScreen`; US-006-05, US-002-05 |
| `archive` | Archive and restore; archived view | `/notes/archived` | `NoteArchiveScreen`; US-006-06 |

**Excluded.** Design modules M4 and M5 (see `web-app.overview`). No route is minted for them.

**Shared concern.** The sort control and its persisted preference belong to `notes` and are consumed by `search`. [feedback.md CR-111] `ASM-WEB-007`.

This grouping rearranges screens and stories already cited elsewhere in this document.

<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state or data-fetching library. `GAP-WEB-008` covers this. The boundary below is conceptual.

**Server state.** Note records, note tag lists and search results. Each is read through its pinned endpoint: `API-NOTES-003@1`, `API-NOTES-004@1`, `API-SEARCH-001@1`. Distinct tags have no pinned endpoint (`HANDOFF-WEB-001`). The archived-only list has none either (`HANDOFF-WEB-002`).

**Client state.**

- Unsubmitted form values on `NoteCreateScreen`, `NoteEditScreen` and `TagManagementScreen`.
- The search text and tag filter before a search is issued.
- The sort selection (`INV-WEB-005`). It is persisted across reloads in `localStorage`. [feedback.md preflight] It is not stored on the server.
- Page position, if the list is paginated (`GAP-WEB-022`).

**Rules applied to server state:**

- `INV-WEB-002`: the client never sends or computes `created_at` or `updated_at`, including optimistically. [requirements.md BR-003; stories.md US-001-06]
- Tag names are displayed as the server returns them. Normalisation is server-side. [requirements.md BR-005]
- `INV-WEB-003`: edit and tag controls are never gated on archive state. [requirements.md BR-010; stories.md US-003-05]

**Sort (CR-111).**

- Options: **Newest first** orders by `created_at` descending. **Oldest first** orders by `created_at` ascending. **Title A-Z** orders by title ascending. The created-date basis was set in preflight. [feedback.md]
- Default is Newest first when no valid preference is stored. [stories.md US-006-01; `ASM-WEB-005`]
- The sort is performed client-side and is never sent to the API. `INV-WEB-005`. [requirements.md BR-020]
- The sort selection is unchanged by search. Search results render in the selected order. [stories.md US-006-04]
- Collation for titles, case handling, tie-breaking on equal timestamps or titles, and the `localStorage` key name are unspecified: `GAP-WEB-021`.
- Whether the sort reaches all results or only the loaded page depends on pagination: `CON-WEB-002`, `Q-WEB-003`.

**Live result count (CR-111, BR-019).** Derived client-side from the displayed result set. It is not stored separately.

- Notes list: the count equals the number of notes rendered for the active list. It shows `0` when the list is empty. [requirements.md BR-019; stories.md US-006-01]
- Search: the count equals the number of notes rendered for the current keyword and tag filters. [requirements.md BR-019] US-006-04 instead says it equals the number of matches. `CON-WEB-001` records this. `Q-WEB-002` resolves it by default to BR-019.
- `INV-WEB-004`: the count is not shown while results load. It is recomputed on every filter, sort or result change, so a count from a previous filter never persists.
- `ASM-WEB-006`: the count is client-derived. A server total would need `HANDOFF-WEB-005`.

**Empty state.** Shown when the resolved list is empty. It uses `Empty/State` (`ASM-WEB-003`). Copy is not supplied: `GAP-WEB-006`.

**Loading state.** No screen in the design inventory has a loading variant: `GAP-WEB-005`.

**Cache and invalidation.** Derived from cited rules:

- After create, edit, tag-add, tag-remove, archive or unarchive, invalidate the affected note detail, the notes list, search results, the archived list and the tag list. Archive and tag changes alter list membership. [requirements.md BR-008, BR-011; stories.md US-006-06]
- After a successful archive, the active list count drops by one. This follows from invalidation, not from local arithmetic.
- Re-sorting is local and needs no refetch when the sort selection changes.
- The archive branch for an already-archived note (or already-active note on restore) is affected by `CON-WEB-003`. Its response handling is BLOCKED.

The staleness window and retry policy are `GAP-WEB-008`.

<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

The backend names no authentication mechanism and exposes no authentication endpoint. [backend-api.md `backend.auth-model`, `GAP-BE-014`, `Q-BE-002`; `HANDOFF-WEB-003`] This section is therefore largely defaults. Its one cited fact is that per-user authentication is mandatory. [feedback.md]

**Login.** No story or design screen covers login: `GAP-WEB-003`.

- `Q-WEB-001` (class: `degradable`, default taken under `unattended: true`). Default: `/login` collects the credential the backend's bearer-token default requires (`Q-BE-002`). On success, return to the requested route.

**Session expiry and 401.** Any `401` from a pinned endpoint clears the client session and redirects to `/login`, keeping the requested route as the return target (`ASM-WEB-002`).

**Mid-flow expiry.** A mutation that returns `401` is not applied locally, and no optimistic result is shown. Whether unsaved draft content survives the redirect is not stated: `GAP-WEB-009`. No claim is made either way.

**Refresh.** No input describes silent refresh or token rotation: `GAP-WEB-010`.

**Logout.** No screen, control or story describes it: `GAP-WEB-011`.

**Credential storage.** Where the credential is held is not specified: `GAP-WEB-013`. No storage location is assumed. The sort preference in `localStorage` holds no credential. [`INV-WEB-005`]

**403.** The split between `401` and `403` is unresolved. [backend-api.md, `GAP-BE-007`] No `403` handling is specified: `GAP-WEB-012`. A `403` must not be treated as `401` without a decision.

<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DERIVED -->
## Authorization UI

Exactly one role signs in to this surface.

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Team member (authenticated) | Notes list, New note, Search, Tags, Archived notes | Create, view, edit, add and remove tags, archive and restore, search, change sort, view live count. Browse distinct tags is BLOCKED (`WEB-TAGS-FR-002`). |
| Unauthenticated | none; redirected to `/login` | none |

The Team member's enabled actions match the backend permission-matrix row for Team member, except that no delete action exists in the pinned contract. [backend-api.md `backend.auth-model`]

**Roles excluded from this surface.** Actors are as named in `stories.md`. The web spec follows `stories.md` over `backend-api.md` where they differ (`CON-WEB-004`).

| Actor (stories.md) | Stories | Reason excluded |
|---|---|---|
| Platform operations engineer | US-004-01, US-004-05 | Not a Team member. Associated screens are M4, out of scope. |
| Compliance auditor | US-004-02, US-004-04, US-004-06 | Not a Team member. Associated screens are M4, out of scope. |
| Backend developer | US-004-03, US-005-02, US-005-06 | Not a Team member. Calls the API directly. No screen. |
| Developer integrating with notes_api | US-001-03, US-001-05, US-003-05 | Not a Team member. Calls the API directly. No screen. |
| Operations engineer | US-003-06 | Not a Team member. No screen. |
| API tech lead | US-005-01, US-005-03 | Not a Team member. Associated screen is M5, out of scope. |
| QA engineer | US-005-04 | Not a Team member. Associated screen is M5, out of scope. |
| Product owner | US-005-05 | Not a Team member. Associated screen is M5, out of scope. |

No Admin-surface actor is in scope for this run. [core I-1]

<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

**Tokens.** `design-brief.md` lists 71 token identifiers in five categories: `UI/border/*`, `UI/color/*`, `UI/icon/*`, `UI/surface/*` and `UI/text/*`. The index gives identifiers and file references only. No token value is supplied: `GAP-WEB-014`. Figma is not connected, so no identifier is verified. No visual value is stated or hard-coded here.

**Type scale and spacing.** No token belongs to either category: `GAP-WEB-015`. Status: BLOCKED for these two subsections.

**Components.** Two are inventoried. [design-brief.md; components-inventory]

- `Empty/State`: one variant, state Default. Mapped to `NoteCreateScreen`, `SearchScreen` and `NoteArchiveScreen`. [screens-inventory]
- `Input/Text`: five variants, states Default, Focused, Filled, Error, Disabled. Mapped to `NoteCreateScreen` only.

**Not covered by any component.**

- `NoteEditScreen` has no component mapping. Its form controls are unspecified: `GAP-WEB-016`.
- The sort control has no design or component: `GAP-WEB-007`.
- The notes list has no screen, so its layout is unspecified: `GAP-WEB-002`.
- Full component bodies (props, bindings) are omitted from the chain handoff and are not available to this run.

**Screen variants.** [screens-inventory]

| Screen | Variants |
|---|---|
| `NoteCreateScreen` | Default, Empty, Error, Filled |
| `NoteDetailScreen` | Default, Error |
| `NoteEditScreen` | Default, Error |
| `SearchScreen` | Default, Empty, Error |
| `NoteArchiveScreen` | Default, Empty, Error, Filled |
| `TagManagementScreen` | Default, Error, Filled |
| `TagListScreen` | Default, Filled |

<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behaviour

**Blocked.** No input defines a breakpoint, a viewport category or a layout change at any breakpoint. The design variants (Default, Empty, Error, Filled) are data states, not viewport states, and no breakpoint token exists. `GAP-WEB-017`.

**Unblocks with:** a responsive specification from the design source, covering the notes list (which has no screen) and the sort control's placement at each breakpoint.

<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**Blocked.** No platform input was supplied, and no input names a supported browser or version. `GAP-WEB-001`, `GAP-WEB-018`.

**Unblocks with:** a browser-support matrix from the product or platform requirements.

<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input states a target standard, a verification method, keyboard requirements for the sort control, or how the live result count is announced to assistive technology. `GAP-WEB-019`.

**Unblocks with:** an accessibility target and the announcement behaviour for the live count and for sort changes.

<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: BLOCKED -->
## Performance

**Blocked for the frontend budget.** No input states a bundle budget, a code-splitting strategy or a largest-payload route for this surface. `GAP-WEB-020`.

**Cited targets that bind this surface's requests.** These are backend targets, as stated in `requirements.md`:

- Search median latency ≤300ms under normal load. [NFR-001]
- Core CRUD p95 latency ≤200ms. [NFR-002]
- Search default page size 20, maximum 100, configurable by query parameter. [NFR-003]

The client-side sort adds no request and no backend cost. [BR-020]

**Largest-payload route.** Unknown. `GET /notes` is the candidate. Its page size is not stated for that endpoint: `GAP-BE-015`, `GAP-WEB-022`.

**Unblocks with:** a frontend performance budget and the pagination behaviour of `GET /notes`.

<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

### Invariants and assumptions

| ID | Statement | Basis |
|---|---|---|
| `INV-WEB-001` | Static route segments match before `/notes/:id` | routing |
| `INV-WEB-002` | Client never sends or computes `created_at` or `updated_at` | BR-003; US-001-06 |
| `INV-WEB-003` | Edit and tag controls are not gated on archive state | BR-010; US-003-05 |
| `INV-WEB-004` | Live count is hidden while loading and recomputed on change | CR-111; BR-019 |
| `INV-WEB-005` | Sort is client-side; never sent to the API; persisted in `localStorage` | BR-020; preflight |
| `ASM-WEB-001` | Route paths are proposed | no input supplies them |
| `ASM-WEB-002` | Unauthenticated redirect target is `/login` | no input supplies it |
| `ASM-WEB-003` | Empty state uses `Empty/State` on the notes list | no notes-list screen |
| `ASM-WEB-004` | `TagManagementScreen` transitions are tag-filter navigations | screens-inventory |
| `ASM-WEB-005` | Default sort is Newest first when no valid preference is stored | US-006-01; `ASM-WEB-005` is a fallback choice |
| `ASM-WEB-006` | Live count is client-derived until a server total exists | `HANDOFF-WEB-005` |
| `ASM-WEB-007` | The sort control is shared by notes list and search | feedback CR-111 ("persists during search") |

### Requirement register

Full Given/When/Then detail belongs in module specifications, which are not produced in this run.

| ID | Covers | Pinned API | Traces to | Verification (planned) | Status |
|---|---|---|---|---|---|
| `WEB-NOTES-FR-001` | Notes list: active notes with title and status; empty state; error state; live count | `API-NOTES-004@1` (parameters `GAP-BE-001`) | US-006-01, US-001-04, US-003-04; BR-011, BR-019 | `notes-list.empty`, `notes-list.count` | DRAFT-UNVERIFIED |
| `WEB-SORT-FR-001` | Sort control (Newest first default, Oldest first, Title A-Z); persisted across reloads; applied to list and search | none (client-side) | CR-111; US-006-01, US-006-04; BR-020 | `sort.default-newest`, `sort.persists-reload`, `sort.applies-to-search` | DRAFT-UNVERIFIED; `GAP-WEB-007`, `GAP-WEB-021`, `CON-WEB-002` |
| `WEB-NOTES-FR-002` | Create note; inline validation; navigate to saved note | `API-NOTES-001@1` | US-006-02, US-001-01; BR-001, BR-004 | `notes-create.submit` | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-003` | Edit note; not-found and field-error handling | `API-NOTES-002@1` (PATCH, `ASM-BE-001`) | US-006-03, US-001-02; BR-003, BR-004, BR-010 | `notes-edit.not-found`, `notes-edit.field-errors` | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-004` | Note detail view | `API-NOTES-003@1` | US-006-02 (navigation); US-001-03 (supporting) | `notes-detail.render` | DRAFT-UNVERIFIED |
| `WEB-SEARCH-FR-001` | Keyword and tag-filter search; pagination; empty results; live count | `API-SEARCH-001@1` | US-006-04, US-002-03, US-002-04, US-002-06; BR-007, BR-019; NFR-001, NFR-003 | `search.empty`, `search.count` | DRAFT-UNVERIFIED; `CON-WEB-001` |
| `WEB-TAGS-FR-001` | Add and remove tags on a note; display the returned tag list | `API-NOTES-005@1`, `API-NOTES-006@1` | US-006-05, US-002-01, US-002-02; BR-005, BR-006 | `note-tags.add`, `note-tags.remove` | DRAFT-UNVERIFIED |
| `WEB-TAGS-FR-002` | Browse distinct tags | none pinned (`HANDOFF-WEB-001`) | US-002-05; BR-008 | `tag-list.render` | **BLOCKED** |
| `WEB-ARCHIVE-FR-001` | Archive and restore a note | `API-NOTES-007@1`, `API-NOTES-008@1` | US-006-06, US-003-01, US-003-02; BR-009, BR-012 | `note-archive.toggle` | DRAFT-UNVERIFIED; already-archived and already-active branches **BLOCKED** (`CON-WEB-003`) |
| `WEB-ARCHIVE-FR-002` | Archived-notes view | none pinned (`HANDOFF-WEB-002`) | US-003-03; BR-011 (exception) | `archive-view.list` | **BLOCKED** |

### Story coverage

All 36 supplied stories appear below.

| Story | Named actor | Disposition | Requirement(s) |
|---|---|---|---|
| US-001-01 | Team member | Backend story; web counterpart | WEB-NOTES-FR-002 |
| US-001-02 | Team member | Backend story; web counterpart | WEB-NOTES-FR-003 |
| US-001-03 | Developer | Not this surface's actor; detail view consumes the same endpoint | WEB-NOTES-FR-004 |
| US-001-04 | Team member | Web counterpart | WEB-NOTES-FR-001 |
| US-001-05 | Developer | Not this surface's actor; field errors displayed | WEB-NOTES-FR-002, WEB-NOTES-FR-003 |
| US-001-06 | Team member | Display rule only | INV-WEB-002 |
| US-002-01 | Team member | Web counterpart | WEB-TAGS-FR-001 |
| US-002-02 | Team member | Web counterpart | WEB-TAGS-FR-001 |
| US-002-03 | Team member | Web counterpart | WEB-SEARCH-FR-001 |
| US-002-04 | Team member | Web counterpart | WEB-SEARCH-FR-001 |
| US-002-05 | Team member | Web counterpart; **BLOCKED** | WEB-TAGS-FR-002 |
| US-002-06 | Team member | Web counterpart | WEB-SEARCH-FR-001 |
| US-003-01 | Team member | Web counterpart; already-archived branch **BLOCKED** | WEB-ARCHIVE-FR-001 |
| US-003-02 | Team member | Web counterpart; already-active branch **BLOCKED** | WEB-ARCHIVE-FR-001 |
| US-003-03 | Team member | Web counterpart; **BLOCKED** | WEB-ARCHIVE-FR-002 |
| US-003-04 | Team member | Web counterpart | WEB-NOTES-FR-001 |
| US-003-05 | Developer | Not this surface's actor; web policy applies | INV-WEB-003 |
| US-003-06 | Operations engineer | Not this surface's actor | `GAP-WEB-001` |
| US-004-01 | Platform operations engineer | Not this surface's actor | `GAP-WEB-001` |
| US-004-02 | Compliance auditor | Not this surface's actor | `GAP-WEB-001` |
| US-004-03 | Backend developer | Not this surface's actor | `GAP-WEB-001` |
| US-004-04 | Compliance auditor | Not this surface's actor | `GAP-WEB-001` |
| US-004-05 | Platform operations engineer | Not this surface's actor | `GAP-WEB-001` |
| US-004-06 | Compliance auditor | Not this surface's actor | `GAP-WEB-001` |
| US-005-01 | API tech lead | Not this surface's actor | `GAP-WEB-001` |
| US-005-02 | Backend developer | Not this surface's actor | `GAP-WEB-001` |
| US-005-03 | API tech lead | Not this surface's actor | `GAP-WEB-001` |
| US-005-04 | QA engineer | Not this surface's actor | `GAP-WEB-001` |
| US-005-05 | Product owner | Not this surface's actor | `GAP-WEB-001` |
| US-005-06 | Backend developer | Not this surface's actor | `GAP-WEB-001` |
| US-006-01 | Team member | Web-owned; CR-111 sort; live count | WEB-NOTES-FR-001, WEB-SORT-FR-001 |
| US-006-02 | Team member | Web-owned | WEB-NOTES-FR-002, WEB-NOTES-FR-004 |
| US-006-03 | Team member | Web-owned | WEB-NOTES-FR-003 |
| US-006-04 | Team member | Web-owned; CR-111 sort; live count | WEB-SEARCH-FR-001, WEB-SORT-FR-001; `CON-WEB-001` |
| US-006-05 | Team member | Web-owned | WEB-TAGS-FR-001 |
| US-006-06 | Team member | Web-owned | WEB-ARCHIVE-FR-001 |

**Summary.** 36 stories. 6 are web-owned (US-006-01..06). 14 are Team member stories with a web counterpart. 4 are non-Team-member stories this surface reads or displays. 12 belong to actors outside this surface (US-004-01..06, US-005-01..06).

### Open items

**Critical contradiction.** `CON-WEB-003` (archive idempotence): US-003-01 AC3 and US-003-02 AC3 say re-archiving or re-restoring returns `200` idempotently. BR-012 says invalid transitions return a consistent error, for example archiving an already-archived note. Backend `ASM-BE-005` assumes `409`, but cites US-002-04 AC2, which does not contain that criterion. Dependent branches: `WEB-ARCHIVE-FR-001`. Handoff: `HANDOFF-WEB-004`.

**Non-critical contradictions.**

- `CON-WEB-001`: live count is "rendered" under BR-019 and "matches" under US-006-04. These differ under pagination. Default taken: BR-019.
- `CON-WEB-002`: sort is client-side (BR-020), but a paginated list can only be sorted across pages it has loaded (NFR-003). Default taken: sort applies to the loaded set (`Q-WEB-003`).
- `CON-WEB-004`: `backend-api.md` attributes actors to different story numbers than `stories.md` does (for example, the compliance auditor at US-001-04). Default taken: `stories.md`.
- `CON-WEB-005`: `backend-api.md` cites NFR-006 as "search p95 < 300ms", while `requirements.md` gives search as a median target under NFR-001 and NFR-006 as a payload rule. Default taken: `requirements.md`.

**Questions** (budget 12; 3 used; all `degradable`; defaults taken under `unattended: true`).

- `Q-WEB-001`: login. Default in `web-app.auth-flows`.
- `Q-WEB-002`: count semantics under pagination. Default: BR-019 (`CON-WEB-001`).
- `Q-WEB-003`: sort across pages. Default: sort applies to the loaded set (`CON-WEB-002`).

**Handoffs to backend.**

- `HANDOFF-WEB-001`: a distinct-tag list endpoint is absent from the contract. Needed by US-002-05 and BR-008.
- `HANDOFF-WEB-002`: an archived-only list endpoint is absent. Needed by US-003-03 and the BR-011 exception.
- `HANDOFF-WEB-003`: an authentication mechanism and endpoint are absent. [`GAP-BE-014`, `Q-BE-002`]
- `HANDOFF-WEB-004`: resolve the archive idempotence response (`CON-WEB-003`).
- `HANDOFF-WEB-005`: a server-reported total count for list and search, and the page parameters for `GET /notes` (`GAP-BE-001`, `GAP-BE-015`, `GAP-WEB-022`).

**Gaps.** `GAP-WEB-001` no platform input. `GAP-WEB-002` no notes-list screen. `GAP-WEB-003` no login screen or story. `GAP-WEB-004` no shell or navigation component. `GAP-WEB-005` no loading variants. `GAP-WEB-006` no empty-state or empty-results copy. `GAP-WEB-007` no sort-control design. `GAP-WEB-008` no state or cache mechanism. `GAP-WEB-009` draft preservation on redirect unspecified. `GAP-WEB-010` no refresh behaviour. `GAP-WEB-011` no logout. `GAP-WEB-012` no `403` handling. `GAP-WEB-013` no credential storage location. `GAP-WEB-014` no token values. `GAP-WEB-015` no type scale or spacing. `GAP-WEB-016` no `NoteEditScreen` controls. `GAP-WEB-017` no breakpoints. `GAP-WEB-018` no browser support. `GAP-WEB-019` no accessibility target. `GAP-WEB-020` no frontend performance budget. `GAP-WEB-021` sort collation, case, tie-break and storage key unspecified. `GAP-WEB-022` pagination of `GET /notes` unknown.

<!-- /SECTION: web-app.coverage -->
