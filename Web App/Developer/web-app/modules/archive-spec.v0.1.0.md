---
id: agent-developer/web_app/web-app/modules/archive-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/archive-SPEC.md
depends_on: [web_app:US-003-01, web_app:US-003-02, web_app:US-003-03, web_app:US-006-06]
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:web-app/modules/archive-SPEC.md
---

<!-- SECTION: web-app.archive.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: archive
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [CON-WEB-001, GAP-WEB-001]
contract_pin: "sha256:a6f2ca3343de"
scope: [/notes/archived]
```

- **`spec_mode`.** The design brief is present and Figma is not connected. Every screen and component name here is an unverified claim from design-brief.md. [RUN-CONTEXT `inputs.figma_mcp`; core 4.5]
- **`blocked_by`.**
  - `CON-WEB-001` (Critical) blocks handling of archive or restore on a note already in the target state.
  - `GAP-WEB-001` blocks the archived list, because the contract has no archived-only list operation.
- **`scope`.** `/notes/archived` is the only route the project module map assigns to `archive`. [project spec `web-app.module-map`, `web-app.routing`]
<!-- /SECTION: web-app.archive.frontmatter -->
<!-- SECTION: web-app.archive.purpose | status: DERIVED -->
## Purpose

**Responsible for:**
- **Archive and restore behaviour.** What the web app does when a Team member archives an active note or restores an archived one, and what the user sees as a result. [stories.md US-006-06, US-003-01, US-003-02]
- **Archived view.** The archived-notes view at `/notes/archived`. [stories.md US-003-03; project spec `web-app.module-map`]
- **Cache effects.** The cache invalidation these actions cause.

**Not responsible for.** Each of these is referenced, never re-specified:

| Concern | Owner |
|---|---|
| Pin and unpin on list rows (the pin part of US-006-06) | `notes` module. [project spec `web-app.module-map`] |
| The note view route `/notes/:id`, which hosts the archive control for an active note | `notes` module. |
| Edit and tag operations on archived notes (`INV-WEB-003`, US-003-05) | `notes` module. |
| Sort, live count and search | `notes` and `search` modules. |
| Session expiry and the `401` redirect | `web-app.auth-flows`. |
| Malformed-id validation (US-003-06) | Not served by the web. [`GAP-WEB-022`] |
| The residency and scope screens | Out of scope for this surface. [`GAP-WEB-021`] |
| Deletion | No delete operation exists in the contract. |

**Stories owned.** US-003-01, US-003-02, US-003-03, US-006-06 (archive and restore part only).
<!-- /SECTION: web-app.archive.purpose -->
<!-- SECTION: web-app.archive.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**Invariants minted by this module:**
- **`INV-WEB-162`.** The client never infers a note's archived or active status or list membership from local arithmetic. After an archive or restore it refetches. [project spec `web-app.state-management` cache rules]
- **`INV-WEB-163`.** Archive and restore requests carry only the note id. They never send title, body, tags, pinned or timestamps. [backend-api.md `API-NOTES-810640`, `API-NOTES-917869`; `INV-WEB-002`]

### WEB-ARCHIVE-FR-001: Archive an active note
- **Given** an active note is open,
- **When** the user clicks archive,
- **Then** the web app calls the archive operation, and the note disappears from the default active list.
- **After success.** See `ASM-WEB-552`. The control is hosted on the note view. [`CON-WEB-170`, `GAP-WEB-756`]
- **Traces to.** US-006-06, US-003-01.
- **Verification.** (planned) `archive_active_note_removes_it_from_active_list`.
- **API.** `API-NOTES-810640`.

### WEB-ARCHIVE-FR-002: Restore an archived note
- **Given** an archived note is viewed,
- **When** the user clicks restore,
- **Then** the web app calls the unarchive operation, and the note reappears in the active list.
- **Where offered.** The control appears wherever an archived note is viewed: the archived view here, and the note view hosted by `notes`. [`ASM-WEB-003`, `GAP-WEB-756`]
- **Traces to.** US-006-06, US-003-02.
- **Verification.** (planned) `restore_archived_note_returns_it_to_active_list`.
- **API.** `API-NOTES-917869`.

### WEB-ARCHIVE-FR-003: Archived list view
- **Given** some notes are archived and some are active,
- **When** the user opens `/notes/archived`,
- **Then** only archived notes are shown.
- **Status.** `BLOCKED` on `GAP-WEB-001`: the contract has no archived-only list. Search with `include_archived` mixes active and archived notes, so it is not used as a substitute.
- **Traces to.** US-003-03.
- **Verification.** (planned) `archived_view_lists_only_archived_notes`.
- **API.** none available (`GAP-WEB-001`).

### WEB-ARCHIVE-FR-004: Empty archived list
- **Given** no notes are archived,
- **When** the archived view loads,
- **Then** the empty state is shown, not an error. [stories.md US-003-03; design-brief.md `Empty/State`]
- **Status.** Copy is not supplied. [`GAP-WEB-010`]
- **Traces to.** US-003-03.
- **Verification.** (planned) `archived_view_empty_shows_empty_state_not_error`.
- **API.** none available (`GAP-WEB-001`).

### WEB-ARCHIVE-FR-005: Archived list failure
- **Given** the archived list request fails,
- **When** the view loads,
- **Then** a user-facing error state is shown, never a blank screen. [project spec `web-app.state-management`, citing stories.md US-006-01]
- **Status.** The error variant is assumed per `ASM-WEB-554`.
- **Traces to.** US-003-03.
- **Verification.** (planned) `archived_view_failure_shows_error_state`.
- **API.** none available (`GAP-WEB-001`).

### WEB-ARCHIVE-FR-006: Note not found on archive or restore
- **Given** the target note does not exist,
- **When** the server returns the not-found response,
- **Then** the user sees a note-not-found message and no local state change is applied. [stories.md US-003-01, US-003-02 for the server behaviour; `ASM-WEB-551` for the web display]
- **Traces to.** US-003-01, US-003-02.
- **Verification.** (planned) `archive_restore_404_shows_not_found_without_local_change`.
- **API.** `API-NOTES-810640`, `API-NOTES-917869`.

### WEB-ARCHIVE-FR-007: Note already in the target state
- **Given** a note is already archived (for archive) or already active (for restore),
- **Then** the stories require an idempotent success with no state change, and BR-012 requires a consistent error. [stories.md US-003-01, US-003-02; requirements.md BR-012]
- **Status.** `BLOCKED` on `CON-WEB-001`. No client behaviour is specified until a human chooses.
- **Traces to.** US-003-01, US-003-02.
- **Verification.** (planned) `archive_already_archived_handling`, written once `CON-WEB-001` is resolved.
- **API.** `API-NOTES-810640`, `API-NOTES-917869`.

### WEB-ARCHIVE-FR-008: Cache invalidation
- **Given** an archive or restore succeeds,
- **When** the response is received,
- **Then** the affected note detail, cached search results, the notes list and the archived list are invalidated and refetched (`INV-WEB-162`). Active-list membership changes, so no local arithmetic is used. [project spec `web-app.state-management`; stories.md US-006-06]
- **Traces to.** US-006-06.
- **Verification.** (planned) `archive_restore_invalidates_list_search_and_detail_caches`.
- **API.** `API-NOTES-810640`, `API-NOTES-917869`.

### WEB-ARCHIVE-FR-009: Session loss and permission refusal
- **Given** an archive or restore returns `401`,
- **Then** the session ends and the user is redirected to `/login` with the return target. The mutation is not applied locally. [project spec `web-app.auth-flows`]
- **Given** it returns `403`,
- **Then** the permission-denied state is shown. [project spec `web-app.authorization-ui`]
- **Traces to.** US-006-06 (derived from the project-level auth rules).
- **Verification.** (planned) `archive_restore_401_redirects`, `archive_restore_403_shows_permission_denied`.
- **API.** `API-NOTES-810640`, `API-NOTES-917869`.

### WEB-ARCHIVE-FR-010: Request payload
- **Given** the user triggers archive or restore,
- **Then** the request carries only the note id (`INV-WEB-163`).
- **Traces to.** US-006-06.
- **Verification.** (planned) `archive_restore_requests_send_only_note_id`.
- **API.** `API-NOTES-810640`, `API-NOTES-917869`.

### WEB-ARCHIVE-FR-011: No duplicate submission
- **Given** an archive or restore request is in flight for a note,
- **Then** the control for that note is disabled until the request settles. [`ASM-WEB-556`]
- **Traces to.** US-006-06 (derived).
- **Verification.** (planned) `archive_restore_control_disabled_while_in_flight`.
- **API.** `API-NOTES-810640`, `API-NOTES-917869`.
<!-- /SECTION: web-app.archive.requirements -->
<!-- SECTION: web-app.archive.routes | status: DRAFT-UNVERIFIED -->
## Routes

Route paths are proposed. [`ASM-WEB-001`]

| Route | Page | Layout | Required permission | Basis |
|---|---|---|---|---|
| `/notes/archived` | `ArchivedNotesScreen` (M3) | Authenticated shell | Authenticated Team member. No role differentiation exists. [project spec `web-app.authorization-ui`] | design-brief.md stories-map US-003-01..03; list `BLOCKED` on `GAP-WEB-001` |

**Route ordering.** `archived` is a static segment and matches before `/notes/:id` (`INV-WEB-001`).

**Hosted elsewhere.** The archive and restore controls on `/notes/:id` are hosted by the `notes` module's route and are not re-specified here. [`GAP-WEB-756`]

**Redirect.** An unauthenticated visit redirects to `/login` with the return target. [project spec `web-app.routing`]
<!-- /SECTION: web-app.archive.routes -->
<!-- SECTION: web-app.archive.components | status: UNKNOWN -->
## Components

**Status.** `DRAFT-UNVERIFIED`. Token bindings are not supplied, so the table is not a verified binding table.

| Component | Variants and states used | Token bindings |
|---|---|---|
| `ArchivedNotesScreen` | Default, Empty, Error, Filled [design-brief.md screens-inventory] | not supplied (`GAP-WEB-757`) |
| `Empty/State` | 1 variant, Default state. Used for the empty archived list. [design-brief.md components-inventory] | not supplied (`GAP-WEB-757`) |
| Archive control, restore control, archived-row element, in-flight and error feedback | none designed | none (`GAP-WEB-756`, `GAP-WEB-002`) |

- **Not assumed.** `Button/Primary` is not named on `ArchivedNotesScreen` in the brief. It is not assumed for these controls.
- **Tokens.** The brief supplies token IDs only, with no mapping to components. [design-brief.md tokens-index] No token is bound and no visual value is hardcoded.
<!-- /SECTION: web-app.archive.components -->
<!-- SECTION: web-app.archive.states | status: DRAFT-UNVERIFIED -->
## States

### `/notes/archived`

| State | Behaviour | Basis |
|---|---|---|
| Loading | Not designed. The result count is hidden while loading. [`GAP-WEB-010`, `INV-WEB-004`] | project spec |
| Empty | `Empty/State`, no error (FR-004). | stories.md US-003-03 |
| Populated | Archived notes with a restore control (FR-002, FR-003). `BLOCKED` on `GAP-WEB-001`. | stories.md US-003-03 |
| Error | User-facing error state, never blank (FR-005). | project spec `web-app.state-management` |
| Permission-denied | `403` renders the permission-denied state, distinct from not-found. The route is authenticated-only, so the state applies to `403` responses. Undesigned. [`GAP-WEB-002`] | project spec `web-app.authorization-ui` |

### Archive and restore action states

These apply on whichever route hosts the control.

| State | Behaviour |
|---|---|
| In flight | Control disabled (FR-011). |
| Success | Caches invalidated and refetched (FR-008). Archive then follows `ASM-WEB-552`. Restore stays in place and the note leaves the archived list on refetch. |
| Not found | FR-006. |
| Already in target state | `BLOCKED` on `CON-WEB-001`. |
| `401` | Session redirect (FR-009). |
| `403` | Permission-denied (FR-009). |
| Server or network failure | Generic user-facing error. No input states a retry policy. |

**Partial failure.** Not applicable. Archive and restore each act on one note, and no multi-item operation exists in the contract or the stories.
<!-- /SECTION: web-app.archive.states -->
<!-- SECTION: web-app.archive.api-integration | status: DERIVED -->
## API integration

References only. Shapes, fields and error sets are defined at the pinned IDs. [backend-api.md `backend.api-surface`]

| Operation | Pinned ID | Used by |
|---|---|---|
| Archive a note | `API-NOTES-810640` | FR-001, FR-006..FR-011 |
| Restore (unarchive) a note | `API-NOTES-917869` | FR-002, FR-006..FR-011 |
| Read a note, to know its status when deciding which control to offer | `API-NOTES-045877` | FR-001, FR-002 |
| List archived notes only | none (`GAP-WEB-001`) | FR-003..FR-005 |

- **Contract pin.** `sha256:a6f2ca3343de`. IDs carry no `@version` suffix. [`GAP-WEB-015`]
- **Error handling.** Errors are discriminated on the machine-readable `code`, using the proposed registry. [`ASM-WEB-008`]
<!-- /SECTION: web-app.archive.api-integration -->
<!-- SECTION: web-app.archive.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side |
|---|---|---|
| The note id comes from a route or row the app rendered. The user types nothing, so there is no field validation. | yes | Malformed-id handling is described in US-003-06, but the contract documents no `400` for archive or restore, so it is not relied on. [`GAP-WEB-022`] |
| Offer archive only for a note the app knows to be active, and restore only for one known to be archived, using `status` from the server. | yes | State-transition check on the server. [requirements.md BR-012; `CON-WEB-001`] |
| No title, body, tag, pinned or timestamp field is sent (`INV-WEB-163`). | yes | Timestamps are server-set. [requirements.md BR-003] |

**No client enforcement of idempotency.** The idempotency versus error question is open (`CON-WEB-001`).
<!-- /SECTION: web-app.archive.validation -->
<!-- SECTION: web-app.archive.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

**Target standard.** None is supplied, so there is no standard to verify against. [`web-app.accessibility` is `BLOCKED`; `GAP-WEB-019`] The behaviours below are proposals. [`ASM-WEB-555`]

- **Keyboard.** Archive and restore are reachable and operable by keyboard. A disabled in-flight control keeps its place in the focus order.
- **Focus after success.**
  - After archive, focus moves to the destination heading, so focus is not lost when the note leaves the active list.
  - After restore from the archived view, focus moves to the next row, or to the heading if the list is now empty.
- **Live region.** Success and failure outcomes (restored, archived, note not found, permission denied) are announced politely. Errors are announced assertively.
- **No form errors.** There is no user-entered field in this module.
- **Controls.** Archive and restore carry accessible names that state the action and, where several rows appear, the note title.
<!-- /SECTION: web-app.archive.accessibility -->
<!-- SECTION: web-app.archive.i18n | status: BLOCKED -->
## i18n

**Blocked.** No input supplies copy keys, a copy deck or a locale list. [`GAP-WEB-010`] Empty-state, error, success and note-not-found copy cannot be written without inventing text.

**Unblocked by:** a copy deck and a stated locale list. This module will need copy for:
- archived-list empty state;
- archived-list error state;
- note-not-found;
- permission-denied;
- archive and restore success feedback, if any.
<!-- /SECTION: web-app.archive.i18n -->
<!-- SECTION: web-app.archive.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma is not connected, so everything here is an **unverified claim** from design-brief.md. No frame IDs are supplied, and none are written. [core 4.5, `FULL-FROM-BRIEF`]

| Reference | Value | Verification |
|---|---|---|
| Screen | `ArchivedNotesScreen`, module M3 | unverified |
| Variants | Default, Empty, Error, Filled | unverified |
| Components on the screen | `Empty/State` | unverified |
| Outgoing flow edges | none listed for this screen | unverified |
| Stories mapped to this screen | US-003-01, US-003-02, US-003-03 (and US-003-06, not served by the web) | unverified |
| US-006-06 | maps to no screen (`CON-WEB-170`) | unverified |
| Frame IDs and token bindings | not supplied (`GAP-WEB-757`) | none possible |
<!-- /SECTION: web-app.archive.design-refs -->
<!-- SECTION: web-app.archive.test-plan | status: DERIVED -->
## Test plan

All test names are planned.

**Component:**
- `archived_view_empty_shows_empty_state_not_error`
- `archived_view_failure_shows_error_state`
- `archive_restore_control_disabled_while_in_flight`
- `archive_restore_403_shows_permission_denied`

**E2E:**
- `archive_active_note_removes_it_from_active_list`
- `restore_archived_note_returns_it_to_active_list`
- `archived_view_lists_only_archived_notes` (blocked on `GAP-WEB-001`)
- `archive_restore_401_redirects`
- `archive_restore_404_shows_not_found_without_local_change`

**Contract:**
- `archive_restore_requests_send_only_note_id`
- `archive_restore_operations_present_in_pin` (checks `API-NOTES-810640` and `API-NOTES-917869` against `sha256:a6f2ca3343de`)
- `archive_already_archived_handling` (blocked on `CON-WEB-001`)

**Cache:**
- `archive_restore_invalidates_list_search_and_detail_caches`

**Accessibility:**
- keyboard operation of archive and restore;
- focus after archive and after restore;
- live-region announcements.

These are checked against the proposals in `ASM-WEB-555`, because no target standard exists (`GAP-WEB-019`).
<!-- /SECTION: web-app.archive.test-plan -->
<!-- SECTION: web-app.archive.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Cited from the project spec** (defined there, not restated):
- `CON-WEB-001` (Critical), `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-010`, `GAP-WEB-015`, `GAP-WEB-019`, `GAP-WEB-021`, `GAP-WEB-022`.
- `ASM-WEB-001`, `ASM-WEB-003`, `ASM-WEB-008`.
- `INV-WEB-001`, `INV-WEB-002`, `INV-WEB-003`, `INV-WEB-004`.

**Minted by this module:**

| ID | Kind | Statement |
|---|---|---|
| `CON-WEB-170` | non-critical | stories.md US-006-06 puts archive and restore in the web app, but design-brief.md stories-map assigns US-006-06 to no screen. Reading used: the story governs, and the controls are hosted on the note view and the archived view (`ASM-WEB-003`). The note view has no design (`GAP-WEB-002`). |
| `GAP-WEB-755` | gap | No input states where the user lands after archive or restore, whether any confirmation or success feedback appears, or whether a confirmation prompt precedes the action. Closing artefact: an interaction design or rule. |
| `GAP-WEB-756` | gap | No design for the archive and restore controls, their in-flight state, or success and failure feedback. The note view that hosts them is undesigned. Closing artefact: frames for these elements. |
| `GAP-WEB-757` | gap | No token-to-component bindings for `ArchivedNotesScreen` or `Empty/State`. The brief gives token IDs only. Closing artefact: the component bodies with token bindings. |
| `GAP-WEB-758` | gap | The archived list's page size, sort order, live count and whether pinning applies are not stated. BR-019 to BR-021 name only the active list and search. Closing artefact: a rule for the archived list. |
| `ASM-WEB-551` | assumption | A not-found response on archive or restore shows the same note-not-found message as a failed save (stories.md US-006-03), with no local change. Confirm with the story owner. |
| `ASM-WEB-552` | assumption | After a successful archive from the note view, the user is taken to `/notes`. Confirm with `GAP-WEB-755`. |
| `ASM-WEB-553` | assumption | No confirmation prompt precedes archive, because archiving is a reversible soft state that never removes data. [requirements.md BR-009] Confirm with `GAP-WEB-755`. |
| `ASM-WEB-554` | assumption | `ArchivedNotesScreen` variants map as Filled to populated, Empty to no archived notes, and Error to failure. Default is the initial presentation and implies no behaviour. Confirm by inspecting the frames. |
| `ASM-WEB-555` | assumption | The focus, keyboard and live-region behaviours in `web-app.archive.accessibility` are proposals, because no accessibility target is supplied. |
| `ASM-WEB-556` | assumption | The archive or restore control is disabled while its request is in flight, to prevent duplicate submission. No input states this. |
<!-- /SECTION: web-app.archive.gaps -->
