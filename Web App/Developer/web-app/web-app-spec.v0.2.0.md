---
id: agent-developer/web_app/web-app/WEB-APP-SPEC.md
type: spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/WEB-APP-SPEC.md
depends_on: [US-006-01, US-004-01, US-005-01, US-006-02, US-006-03, US-006-05, US-002-05, US-006-04, US-006-06, US-001-06, US-003-05, US-004-05, US-004-02, US-004-04, US-004-06, US-004-03, US-005-02, US-005-06, US-001-03, US-001-05, US-003-06, US-005-03, US-005-04, US-005-05, US-003-04, US-001-04, US-001-01, US-001-02, US-002-03, US-002-04, US-002-06, US-002-01, US-002-02, US-003-01, US-003-02, US-003-03]
commit_kind: fork
confidence: agent
run_id: bd40d909-f5d3-5811-94e2-22764694e8a7
okf_id: web-app/WEB-APP-SPEC.md
---

<!-- SECTION: web-app.overview | status: DERIVED -->
## Overview

**Purpose.** This surface is the browser client for `notes_api`. It lets an authenticated **Team member** list, create, view, edit, search, tag, archive and restore team notes without calling the API directly. [stories.md EP-006, US-006-01..06]

**Primary actor.** Team member (authenticated). It is the only actor any EP-006 story names, and the only end-user role the backend contract shows calling the Note/Tag/Search surface. [backend-api.md `backend.auth-model`, actor table]

**This revision.** Applies CR-106 [feedback.md]: an empty-state message and a live result count above the notes list, updating with search and tag filters. CR-106 changes no backend or API behaviour. `API-NOTES-004@1` and `API-SEARCH-001@1` are consumed unchanged. Screen names follow `design-brief.md` exactly. Names in the approved prior draft that do not appear in the design inventory (`NotesListScreen`, `NoteTagManagementScreen`, `ResidencyCheckScreen`, `ScopeComplianceReportScreen`) are withdrawn.

**Design basis.** `spec_mode: FULL-FROM-BRIEF`. The design brief is present and Figma is not connected [RUN-CONTEXT `figma_mcp`]. Every frame ID and token name in this document is an unverified claim.

**Out of scope**, each cited:

- **Operator and administration functions.** These belong to the Admin surface, which is not part of this run (invariant I-1).
- **Residency and compliance screens:** `PreLaunchCheckScreen`, `ResidencyReportScreen` (module M4) and `APISpecDocumentationScreen` (module M5) [design-brief.md, screens-inventory]. Their stories name Platform operations engineer, Compliance auditor, API tech lead, QA engineer and Product owner, never Team member [stories.md US-004-01..06, US-005-01..06]. No owning surface is selected for this run: `GAP-WEB-001`.
- **Attachments and file upload.** Create and edit reject attachment payloads [requirements.md BR-016]. No attachment control is built.
- **Real-time or live-update channels.** None is exposed [requirements.md BR-017]. "Live" in CR-106 means the count recalculates when the displayed result set or filters change. It does not mean server push.
- **Mobile-specific behaviour** [requirements.md BR-018].
- **Note deletion.** No delete operation appears in the pinned API surface [backend-api.md `backend.api-surface`]. No delete control is offered.
- **Offline use.** No input requires it. Not specified.
<!-- /SECTION: web-app.overview -->
<!-- SECTION: web-app.routing | status: DRAFT-UNVERIFIED -->
## Routing

No input supplies route paths. The paths below are proposed. Each is `ASM-WEB-001`. Screen names are from the design inventory.

| Route | Screen | Protected | Required role | Basis |
|---|---|---|---|---|
| `/login` | none in design (`GAP-WEB-003`) | No | — | `ASM-WEB-002` |
| `/notes` | notes list: no design screen (`GAP-WEB-002`) | Yes | Team member | US-006-01; CR-106 |
| `/notes/new` | `NoteCreateScreen` (M1) | Yes | Team member | design-brief screens-inventory; US-006-02 |
| `/notes/:id` | `NoteDetailScreen` (M1) | Yes | Team member | design-brief; derived from US-006-02 "navigates to the saved note" |
| `/notes/:id/edit` | `NoteEditScreen` (M1) | Yes | Team member | design-brief; US-006-03 |
| `/notes/:id/tags` | `TagManagementScreen` (M2) | Yes | Team member | design-brief; US-006-05 |
| `/tags` | `TagBrowserScreen` (M2) | Yes | Team member | design-brief; US-002-05 |
| `/notes/search` | `SearchScreen` (M2) | Yes | Team member | design-brief; US-006-04 |
| `/notes/archived` | `NoteArchiveScreen` (M3) | Yes | Team member | design-brief; US-006-06 |

**Invariant `INV-WEB-001`.** The static segments `/notes/new`, `/notes/search` and `/notes/archived` must match before `/notes/:id`. Otherwise the router treats them as note identifiers.

**Layout nesting.** All protected routes are assumed to sit under one authenticated shell. `GAP-WEB-004`: no header, navigation or layout component appears in the components inventory, so the shell cannot be specified.

**Protected-route rule.** Every route except `/login` requires an authenticated session. A `401` from any pinned endpoint is handled as in `web-app.auth-flows`.

**Unauthenticated redirect.** Redirect to `/login` (`ASM-WEB-002`).

**Flow edge.** `TagManagementScreen` has two `ON_CLICK → SearchScreen` transitions [design-brief.md, screens-inventory]. `ASM-WEB-004`: these are tag-filter navigations into `/notes/search` with the tag applied.
<!-- /SECTION: web-app.routing -->
<!-- SECTION: web-app.module-map | status: DERIVED -->
## Module map

| Module | Feature area | Routes owned | Source screens |
|---|---|---|---|
| `notes` | Browse, create, view, edit | `/notes`, `/notes/new`, `/notes/:id`, `/notes/:id/edit` | notes list (no design screen), `NoteCreateScreen`, `NoteDetailScreen`, `NoteEditScreen` |
| `search` | Keyword and tag-filter search | `/notes/search` | `SearchScreen` |
| `tags` | Tag assignment on a note; tag discovery | `/notes/:id/tags`, `/tags` | `TagManagementScreen`, `TagBrowserScreen` |
| `archive` | Archived view; archive and restore | `/notes/archived` | `NoteArchiveScreen` |

**Excluded.** Design modules M4 and M5 (see `web-app.overview`). No route is minted for them.

This grouping rearranges screens and stories already cited elsewhere in this document.
<!-- /SECTION: web-app.module-map -->
<!-- SECTION: web-app.state-management | status: DRAFT-UNVERIFIED -->
## State management

No input names a state or data-fetching library. `GAP-WEB-008` covers this. The boundary below is conceptual.

**Server state.** Note records, note tag lists and search results, each read through its pinned endpoint: `API-NOTES-003@1`, `API-NOTES-004@1`, `API-SEARCH-001@1`. Distinct tags have no pinned endpoint (`HANDOFF-WEB-001`). The archived-only list has none either (`HANDOFF-WEB-002`).

**Client state.** Unsubmitted form values on `NoteCreateScreen`, `NoteEditScreen` and `TagManagementScreen`. The search text and tag filter before a search is issued. Page position, if paginated.

**Rules applied to server state:**

- `INV-WEB-002`: the client never sends or computes `created_at` or `updated_at`, including optimistically [requirements.md BR-003; stories.md US-001-06].
- Tag names are displayed as the server returns them. Normalisation is server-side [requirements.md BR-005].
- Edit and tag controls are never gated on archive state. `INV-WEB-003` [requirements.md BR-010; stories.md US-003-05].

**Live result count (CR-106).** Derived client-side from the resolved result set. It is not stored separately.

- Notes list: the count equals the number of notes currently rendered for the active list [requirements.md BR-019]. The count shows `0` when the list is empty [stories.md US-006-01 AC2].
- Search: the count equals the number of notes rendered for the current keyword and tag filters [requirements.md BR-019]. This reading is contradicted in part by US-006-04. See `CON-WEB-001`.
- The count is not shown while the result set is loading. It is recomputed on every filter change or result change, so a count from a previous filter never persists. `INV-WEB-004`.
- `ASM-WEB-006`: the count is client-derived from the displayed set, not from a server total. This holds until `HANDOFF-WEB-005` resolves.
- Paginated count semantics are `Q-WEB-002`.

**Empty state.** Shown when the resolved list is empty. Uses `Empty/State` (`ASM-WEB-003`, since no notes-list screen exists). Copy is not supplied: `GAP-WEB-006`.

**Loading state.** No screen in the design inventory has a loading variant. `GAP-WEB-005`.

**Cache and invalidation.** Derived from cited rules:

- After create, edit, tag-add, tag-remove, archive or unarchive, invalidate the affected note detail, the notes list, search results, the archived list and the tag list. Archive and tag changes alter list membership [requirements.md BR-008, BR-011; stories.md US-006-06].
- After a successful archive, the active list count drops by one. This follows from invalidation, not from local arithmetic.
- An archive request for a note the client believes is active, when the server reports it already archived, is affected by `CON-WEB-003`. The response branch is BLOCKED.

The mechanism, including staleness window and retry policy, is `GAP-WEB-008`.
<!-- /SECTION: web-app.state-management -->
<!-- SECTION: web-app.auth-flows | status: DRAFT-UNVERIFIED -->
## Auth flows

The backend names no authentication mechanism and exposes no authentication endpoint [backend-api.md `backend.auth-model`, `GAP-BE-014`; `Q-BE-002`; `HANDOFF-WEB-003`]. This section is therefore largely defaults. Its one cited fact is that per-user authentication is mandatory [feedback.md].

**Login.** No story or design screen covers login (`GAP-WEB-003`).

- `Q-WEB-001` (class: `degradable`, auto-resolved under `unattended: true`). Default: a `/login` route collects the credential the backend's bearer-token default requires (`Q-BE-002`). On success, return to the requested route.

**Session expiry and 401.** Any `401` from a pinned endpoint clears the client session and redirects to `/login`, keeping the requested route as the return target (`ASM-WEB-002`).

**Mid-flow expiry.** A mutation that returns `401` is not applied locally. No optimistic result is shown. Whether unsaved draft content survives the redirect is not stated by any input: `GAP-WEB-009`. No claim is made either way.

**Refresh.** No input describes silent refresh or token rotation: `GAP-WEB-010`.

**Logout.** No screen, control or story describes it: `GAP-WEB-011`.

**Credential storage.** Where the credential is held is not specified: `GAP-WEB-013`. No storage location is assumed.

**403.** The split between `401` and `403` is unresolved [backend-api.md, `GAP-BE-007`]. No `403` handling is specified: `GAP-WEB-012`. A `403` must not be treated as `401` without a decision.
<!-- /SECTION: web-app.auth-flows -->
<!-- SECTION: web-app.authorization-ui | status: DERIVED -->
## Authorization UI

Exactly one role signs in to this surface.

| Role | Visible navigation | Enabled actions |
|---|---|---|
| Team member (authenticated) | Notes list, Create note, Search, Tags, Archived notes | Create, view, edit, add and remove tags, archive and restore, search, browse tags (BLOCKED, `WEB-TAGS-FR-002`). The live count is visible on the notes list and search. |
| Unauthenticated | none; redirected to `/login` | none |

Team member's enabled actions match the backend permission-matrix row for Team member, except that no delete action exists in the pinned surface [backend-api.md `backend.auth-model`].

**Roles excluded from this surface, with reasons.** Each actor is named in `stories.md`.

| Actor (as named in stories.md) | Stories | Reason excluded |
|---|---|---|
| Platform operations engineer | US-004-01, US-004-05 | Not a Team member. Associated screens are M4, out of scope. |
| Compliance auditor | US-004-02, US-004-04, US-004-06 | Not a Team member. Associated screen is M4, out of scope. |
| Backend developer | US-004-03, US-005-02, US-005-06 | Not a Team member. Calls the API directly, with no screen. |
| Developer integrating with notes_api | US-001-03, US-001-05, US-003-05 | Not a Team member. Calls the API directly, with no screen. |
| Operations engineer | US-003-06 | Not a Team member. No screen. |
| API tech lead | US-005-01, US-005-03 | Not a Team member. Associated screen is M5, out of scope. |
| QA engineer | US-005-04 | Not a Team member. Associated screen is M5, out of scope. |
| Product owner | US-005-05 | Not a Team member. Associated screen is M5, out of scope. |

`backend-api.md` attributes some of these actors to different story numbers. `CON-WEB-004` records the mismatch. This table follows `stories.md`.
<!-- /SECTION: web-app.authorization-ui -->
<!-- SECTION: web-app.design-system | status: DRAFT-UNVERIFIED -->
## Design system

**Tokens.** `design-brief.md` lists 71 token identifiers in five categories: `UI/border/*`, `UI/color/*`, `UI/icon/*`, `UI/surface/*`, `UI/text/*`. The index gives identifiers and file references only. No token value is supplied: `GAP-WEB-014`. No visual value is stated or hard-coded here.

**Type scale and spacing.** No token belongs to either category. `GAP-WEB-015`.

**Components.** Two are inventoried [design-brief.md, components-inventory]:

- `Empty/State`: one variant, state Default. Used on `NoteCreateScreen`, `SearchScreen` and `NoteArchiveScreen` [screens-inventory].
- `Input/Text`: five variants, states Default, Focused, Filled, Error, Disabled. Used on `NoteCreateScreen` only. `NoteEditScreen` has no component mapping, so its form controls are unspecified: `GAP-WEB-016`.

Full component bodies (props, bindings) are omitted from the chain handoff and are not available to this run.
<!-- /SECTION: web-app.design-system -->
<!-- SECTION: web-app.responsive | status: BLOCKED -->
## Responsive behaviour

**Blocked.** No input defines a breakpoint, a viewport category or a layout change. The design variants (Default, Empty, Error, Filled) are data states, not viewport states, and no breakpoint token exists. `GAP-WEB-017`. Unblocks with a responsive specification from the design source.
<!-- /SECTION: web-app.responsive -->
<!-- SECTION: web-app.browser-support | status: BLOCKED -->
## Browser support

**Blocked.** No platform input was supplied and no input names a supported browser or version. `GAP-WEB-018`. Unblocks with a browser-support matrix from the product or platform requirements.
<!-- /SECTION: web-app.browser-support -->
<!-- SECTION: web-app.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input states a target standard, a verification method, or how the live result count is announced to assistive technology. `GAP-WEB-019`. Unblocks with an accessibility target and the announcement behaviour for CR-106.
<!-- /SECTION: web-app.accessibility -->
<!-- SECTION: web-app.performance | status: BLOCKED -->
## Performance

**Blocked.** No input states a frontend bundle budget, a code-splitting strategy or a largest-payload route. The NFRs in `requirements.md` are backend latency and throughput targets. `GAP-WEB-020`. Unblocks with a frontend performance budget.
<!-- /SECTION: web-app.performance -->
<!-- SECTION: web-app.coverage | status: DERIVED -->
## Coverage

### Requirement register

Full Given/When/Then detail belongs in module specifications, which are not produced in this run. Every requirement here cites its traces, its pinned API and its planned verification.

| ID | Covers | Pinned API | Traces to | Verification (planned) | Status |
|---|---|---|---|---|---|
| `WEB-NOTES-FR-001` | Notes list: render active notes; empty state; live count; error state (CR-106) | `API-NOTES-004@1` (parameters `GAP-BE-001`) | US-006-01, US-003-04, US-001-04; BR-011, BR-019; NFR-003 | `notes-list.empty-state`, `notes-list.count-equals-rendered` (planned) | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-002` | Create note; inline validation; navigate to saved note | `API-NOTES-001@1` | US-006-02, US-001-01, US-001-06; BR-001, BR-004 | `notes-create.submit` (planned) | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-003` | Edit note; not-found and validation handling | `API-NOTES-002@1` (method PATCH per backend `ASM-BE-001`) | US-006-03, US-001-02, US-001-06; BR-003, BR-010 | `notes-edit.not-found`, `notes-edit.field-errors` (planned) | DRAFT-UNVERIFIED |
| `WEB-NOTES-FR-004` | Note detail view | `API-NOTES-003@1` | US-006-02 (navigation); US-001-03 (supporting) | `notes-detail.render` (planned) | DRAFT-UNVERIFIED |
| `WEB-SEARCH-FR-001` | Keyword and tag-filter search; pagination; empty results; live count (CR-106) | `API-SEARCH-001@1` | US-006-04, US-002-03, US-002-04, US-002-06; BR-007; NFR-001, NFR-003 | `search.empty-results`, `search.count-equals-rendered` (planned) | DRAFT-UNVERIFIED |
| `WEB-TAGS-FR-001` | Add and remove tags on a note; display returned tag list | `API-NOTES-005@1`, `API-NOTES-006@1` | US-006-05, US-002-01, US-002-02; BR-005, BR-006 | `note-tags.add`, `note-tags.remove` (planned) | DRAFT-UNVERIFIED |
| `WEB-TAGS-FR-002` | Browse distinct tags | none pinned (`HANDOFF-WEB-001`) | US-002-05; BR-008 | `tag-browser.list` (planned) | **BLOCKED** |
| `WEB-ARCHIVE-FR-001` | Archive and restore a note | `API-NOTES-007@1`, `API-NOTES-008@1` | US-006-06, US-003-01, US-003-02; BR-009, BR-012 | `note-archive.toggle` (planned) | DRAFT-UNVERIFIED; already-archived branch **BLOCKED** (`CON-WEB-003`) |
| `WEB-ARCHIVE-FR-002` | Archived-notes view | none pinned (`HANDOFF-WEB-002`) | US-003-03; BR-011 exception | `archive-view.list` (planned) | **BLOCKED** |

### Story coverage

Every supplied story appears below.

| Story | Named actor | Disposition | Requirement(s) |
|---|---|---|---|
| US-001-01 | Team member (goal stated "via the API") | Backend acceptance; web counterpart | WEB-NOTES-FR-002 |
| US-001-02 | Team member | Backend acceptance; web counterpart | WEB-NOTES-FR-003 |
| US-001-03 | Developer | Not this surface's actor | GAP-WEB-001; detail view consumes the same endpoint under WEB-NOTES-FR-004 |
| US-001-04 | Team member | Web-owned | WEB-NOTES-FR-001 |
| US-001-05 | Developer | Not this surface's actor | GAP-WEB-001; field errors displayed under WEB-NOTES-FR-002, -003 |
| US-001-06 | Team member | Web-owned (display rule) | WEB-NOTES-FR-002, -003, -004; `INV-WEB-002` |
| US-002-01 | Team member | Web counterpart | WEB-TAGS-FR-001 |
| US-002-02 | Team member | Web counterpart | WEB-TAGS-FR-001 |
| US-002-03 | Team member | Web counterpart | WEB-SEARCH-FR-001 |
| US-002-04 | Team member | Web counterpart | WEB-SEARCH-FR-001 |
| US-002-05 | Team member | Web counterpart (BLOCKED) | WEB-TAGS-FR-002 |
| US-002-06 | Team member | Web counterpart | WEB-SEARCH-FR-001 |
| US-003-01 | Team member | Web counterpart; already-archived branch BLOCKED | WEB-ARCHIVE-FR-001 |
| US-003-02 | Team member | Web counterpart; already-active branch as stated | WEB-ARCHIVE-FR-001 |
| US-003-03 | Team member | Web-owned (BLOCKED) | WEB-ARCHIVE-FR-002 |
| US-003-04 | Team member | Web counterpart | WEB-NOTES-FR-001 |
| US-003-05 | Developer integrating | Not this surface's actor; web policy stated | GAP-WEB-001; `INV-WEB-003` |
| US-003-06 | Operations engineer | Not this surface's actor | GAP-WEB-001 |
| US-004-01 | Platform operations engineer | Not this surface's actor | GAP-WEB-001 |
| US-004-02 | Compliance auditor | Not this surface's actor | GAP-WEB-001 |
| US-004-03 | Backend developer | Not this surface's actor | GAP-WEB-001 |
| US-004-04 | Compliance auditor | Not this surface's actor | GAP-WEB-001 |
| US-004-05 | Platform operations engineer | Not this surface's actor | GAP-WEB-001 |
| US-004-06 | Compliance auditor | Not this surface's actor | GAP-WEB-001 |
| US-005-01 | API tech lead | Not this surface's actor | GAP-WEB-001 |
| US-005-02 | Backend developer | Not this surface's actor | GAP-WEB-001 |
| US-005-03 | API tech lead | Not this surface's actor | GAP-WEB-001 |
| US-005-04 | QA engineer | Not this surface's actor | GAP-WEB-001 |
| US-005-05 | Product owner | Not this surface's actor | GAP-WEB-001 |
| US-005-06 | Backend developer | Not this surface's actor | GAP-WEB-001 |
| US-006-01 | Team member | Web-owned; CR-106 | WEB-NOTES-FR-001; `CON-WEB-001`, `CON-WEB-002` |
| US-006-02 | Team member | Web-owned | WEB-NOTES-FR-002, WEB-NOTES-FR-004 |
| US-006-03 | Team member | Web-owned | WEB-NOTES-FR-003 |
| US-006-04 | Team member | Web-owned; CR-106 | WEB-SEARCH-FR-001; `CON-WEB-001` |
| US-006-05 | Team member | Web-owned | WEB-TAGS-FR-001 |
| US-006-06 | Team member | Web-owned | WEB-ARCHIVE-FR-001 |

Thirty-six stories are supplied. Six are web-owned (US-006-01..06). Sixteen are Team member stories with a web counterpart (US-001-01, -02, -04, -06; US-002-01..06; US-003-01..04). Four are not this surface's actor (US-001-03, -05; US-003-05, -06). Sixteen are not this surface's actor (US-004-01..06; US-005-01..06). The rows above give each one's disposition.

### Open items

- **Assumptions:** `ASM-WEB-001` (route paths), `ASM-WEB-002` (`/login` and return target), `ASM-WEB-003` (`Empty/State` for the notes list), `ASM-WEB-004` (tag click navigates to filtered search), `ASM-WEB-005` (`include_archived` always sent explicitly), `ASM-WEB-006` (count derived client-side).
- **Questions:** `Q-WEB-001` (login mechanism, degradable, default applied), `Q-WEB-002` (count under pagination, degradable, default applied).
- **Contradictions:** `CON-WEB-001` to `CON-WEB-004` (non-critical), `CON-WEB-005` (non-critical), `CON-WEB-003` (**Critical**; see below).
- **Gaps:** `GAP-WEB-001` to `GAP-WEB-020`, plus `GAP-WEB-021` to `GAP-WEB-029` raised in the sections above.
- **Handoffs to backend:** `HANDOFF-WEB-001` (distinct-tags endpoint), `-002` (archived-only list endpoint), `-003` (authentication endpoint), `-004` (`GET /notes` parameters and paging), `-005` (search total count), `-006` (already-archived response, resolves `CON-WEB-003`).

**Note on `CON-WEB-003`.** Classified Critical under core rule 8, because it changes a state transition. The halt disposition (`HALT-CRITICAL-CONTRADICTION`) belongs to the orchestrator. This document is emitted in full. The dependent content is BLOCKED as stated above.
<!-- /SECTION: web-app.coverage -->
