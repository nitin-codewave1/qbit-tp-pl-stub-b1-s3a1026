---
id: agent-developer/web_app/web-app/modules/archive-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/archive-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/archive-SPEC.md
---

<!-- SECTION: web-app.archive.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: archive
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-002]
contract_pin: "sha256:9550f0d579d8"
scope: [/archive]
```

Sources: `RUN-CONTEXT` (`inputs.figma_mcp: not-connected`, with `design-brief.md` present, gives `FULL-FROM-BRIEF` under core 4.5); project spec `web-app.module-map`.
<!-- /SECTION: web-app.archive.frontmatter -->
<!-- SECTION: web-app.archive.purpose | status: DERIVED -->
## Purpose

**Responsible for**
- The archived-notes view, `ArchivedNotesScreen`, design module M3, route `/archive` (`web-app.module-map`, `web-app.routing`).
- The **restore** action on an archived note: "Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive endpoint and the note reappears in the active list" (`stories.md` US-006-06, restore clause).
- The empty, error and populated presentation of the archived view (design variants Default, Empty, Error, Filled; `design-brief.md` screens-inventory).

**Not responsible for**
- The archive action on an open note and the pin or unpin toggle. These belong to `notes` and `search` (`web-app.module-map`, the `notes` row).
- The note detail and editor screens at `/notes/:id`. They are owned by `notes`. This module only navigates to them.
- The active list at `/notes`, its sort control and its live count. They are owned by `search`.
- Session handling, sign-in and the protected-route rule. These are owned by `login` and `web-app.auth-flows`, and are `BLOCKED` (`GAP-WEB-001`).
- Backend behaviour. The absent archived-list operation is a backend need (`GAP-WEB-002`), not specified here.
- Tag operations (`tags`) and the residency and scope-docs screens, which are not routed.

**Derivation.** The split follows the project module map: `archive` owns "restore of US-006-06", and `notes` owns "archive and pin of US-006-06".
<!-- /SECTION: web-app.archive.purpose -->
<!-- SECTION: web-app.archive.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**WEB-ARCHIVE-FR-001 — Restore an archived note**
- Given an archived note is shown in the archived view, when the user activates its restore control, then the web app calls `API-NOTES-917869` for that note id.
- On success, the cached note-by-id entry is merged with the fields the response returns (project cache policy 1). All cached search results and lists are invalidated (project cache policy 2). The note no longer appears in the archived view, and it appears in the active list on its next load.
- **Traces to.** US-006-06 (restore clause); `requirements.md` BR-009 (archive is a status flag, so restore loses no data).
- **Verification.** (planned) `archive.restore.success_removes_row_and_invalidates_lists`.
- **API.** `API-NOTES-917869`.

**WEB-ARCHIVE-FR-002 — Restore changes UI only on the success response**
- Given a restore request is pending, when the response has not arrived, then the row is unchanged, the restore control shows `Button/Primary` Loading and cannot be activated twice, and no optimistic update is made (`ASM-WEB-014`, `ASM-WEB-015`).
- **Traces to.** US-006-06; project `web-app.state-management` rule 5.
- **Verification.** (planned) `archive.restore.pending_blocks_double_activation`.
- **API.** `API-NOTES-917869`.

**WEB-ARCHIVE-FR-003 — Restore of a note that no longer exists**
- Given the unarchive response is 404, when it is received, then the web app shows a "note not found" message. The row and all cached data are unchanged (`INV-WEB-156`, `ASM-WEB-172`).
- **Traces to.** US-006-06; `stories.md` US-003-02 (404 for an unknown id, API lane). The "do not apply local changes" behaviour is borrowed from US-006-03 (`ASM-WEB-172`).
- **Verification.** (planned) `archive.restore.404_shows_not_found_keeps_row`.
- **API.** `API-NOTES-917869`.

**WEB-ARCHIVE-FR-004 — Restore failure other than 404**
- Given the request fails with a network error, a 5xx, or any status the contract does not list for this operation, when the failure is received, then the web app shows a retryable error message that leaves the row and caches unchanged (`ASM-WEB-013`).
- Retry-after values are unstated (`GAP-WEB-004`), so none is shown.
- **Traces to.** US-006-06; `ASM-WEB-013`.
- **Verification.** (planned) `archive.restore.failure_is_retryable_and_non_destructive`.
- **API.** `API-NOTES-917869`.

**WEB-ARCHIVE-FR-005 — Open an archived note**
- Given an archived note is shown, when the user opens it, then the web app navigates to `/notes/:id`, which `notes` owns (`web-app.module-map`, dependency `archive → notes`).
- **Traces to.** US-006-06 ("Given an archived note is viewed").
- **Verification.** (planned) `archive.row.open_navigates_to_note`.
- **API.** `API-NOTES-045877` (consumed by `notes`, not called here).

**WEB-ARCHIVE-FR-006 — Show the archived notes (BLOCKED)**
- The archived view must show the notes that are archived. This requirement cannot be completed: the contract has no operation that lists archived-only notes, and search excludes archived notes unless `include_archived` is set while still requiring `q` or tags (`API-SEARCH-580505`; `requirements.md` BR-007; `GAP-WEB-002`).
- Pagination, ordering and result count are also unspecified for this view (`GAP-WEB-174`).
- Unblocked by: a backend operation that lists archived-only notes with pagination, delivered through the contract.
- **Traces to.** `stories.md` US-006-06 ("archived note is viewed"); `design-brief.md` stories-map (US-006-06 maps to `ArchivedNotesScreen`). The backend-lane story US-003-03 describes the missing operation.
- **Verification.** (planned) `archive.list.renders_archived_notes`.
- **API.** None exists.

**WEB-ARCHIVE-FR-007 — Access (BLOCKED)**
- Whether `/archive` requires a session, and what an unauthenticated visit does, is undecided (`GAP-WEB-001`, `Q-BE-003`). No requirement is written until it closes.

**Invariants**
- `INV-WEB-156`: A failed restore (404, network error or 5xx) never changes cached data or the displayed rows. This follows the project rule that a failed mutation never changes cached data (`web-app.state-management` rule 4).
- `INV-WEB-157`: A note leaves the archived view only after a successful unarchive response (`ASM-WEB-014`).

**Directions checked.**
- story ↔ design: no conflict. Restore maps to `ArchivedNotesScreen`.
- story ↔ requirement: no new conflict.
- requirement ↔ requirement: `requirements.md` BR-012 (consistent error for invalid transitions) against the contract's single 404 on unarchive is `CON-WEB-003`, defined in the project spec and not re-raised.
- design ↔ design: no conflict.

No new `CON-` was minted.
<!-- /SECTION: web-app.archive.requirements -->
<!-- SECTION: web-app.archive.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission | Source |
|---|---|---|---|---|
| `/archive` | `ArchivedNotesScreen` | app | `BLOCKED`: whether a session is required is undecided (`GAP-WEB-001`). The only role that signs in on this surface is Team member (`web-app.authorization-ui`). | `web-app.routing`; `design-brief.md` |

- The path is a proposal (`ASM-WEB-001`).
- Outbound navigation from this module is to `/notes/:id` (open an archived note). The design shows no outgoing transitions for `ArchivedNotesScreen` (`screens-inventory.md`), so any other navigation is not asserted. The control that leads to `/archive` has no design frame (`GAP-WEB-005`).
<!-- /SECTION: web-app.archive.routes -->
<!-- SECTION: web-app.archive.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `archive`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.archive.components -->
<!-- SECTION: web-app.archive.states | status: DRAFT-UNVERIFIED -->
## States

**Route `/archive`**

| State | Presentation | Basis |
|---|---|---|
| Loading | Shown while the archived list loads. The design has no Loading variant (`GAP-WEB-175`). Its data source is `BLOCKED` (`GAP-WEB-002`). | `ArchivedNotesScreen` variants are Default, Empty, Error, Filled |
| Empty | `Empty/State` inside the Empty variant. It is not an error. | Design variant Empty; `stories.md` US-003-03 (empty list, API lane); `ASM-WEB-168` |
| Populated | Filled variant: archived notes, each with a restore control and an open action. | Design variant Filled; `ASM-WEB-168` |
| Error | Error variant: user-facing error state with retry that keeps the page. Never a blank screen (compare US-006-01). Retry-after is unstated (`GAP-WEB-004`). | Design variant Error; `ASM-WEB-013` |
| Default | The initial state before data arrives. | `ASM-WEB-168` |

**Restore, per row**
- **Pending:** the restore control shows Loading and is not activatable (`WEB-ARCHIVE-FR-002`).
- **Not found (404):** message shown, row unchanged (`WEB-ARCHIVE-FR-003`).
- **Failure:** retryable message, row unchanged (`WEB-ARCHIVE-FR-004`).
- **Success:** row removed from the view (`INV-WEB-157`). If this was the last archived note, the Empty state shows (`ASM-WEB-169`).

**Permission-denied.** `/archive` is permission-gated only if a session is required, which is undecided. It is `BLOCKED` on `GAP-WEB-001`, and on `GAP-WEB-013` for the "not allowed" versus "does not exist" distinction.

**Partial-failure.** Not applicable. The module has no multi-item operation: restore acts on one note, and no bulk restore is in any input. No gap is raised for it.
<!-- /SECTION: web-app.archive.states -->
<!-- SECTION: web-app.archive.api-integration | status: DERIVED -->
## API integration

Reference only. Operation meanings, shapes and error sets are in the pinned contract (`sha256:9550f0d579d8`, `backend-api.md`) and are not restated.

| Operation | Contract ID | Used by | Status → UI state |
|---|---|---|---|
| Unarchive a note | `API-NOTES-917869` | `WEB-ARCHIVE-FR-001` to `-004` | 200: success. 404: not-found message. Other outcomes: retryable failure. |
| List archived-only notes | none in the contract | `WEB-ARCHIVE-FR-006` | `BLOCKED` (`GAP-WEB-002`) |
| Get a note | `API-NOTES-045877` | opened by `notes` after navigation | not called by this module |

- `requirements.md` BR-012 asks for a consistent error on invalid transitions. The contract lists no 409 for unarchive (`CON-WEB-003`, `ASM-WEB-009`). Any unlisted status falls under `WEB-ARCHIVE-FR-004`.
<!-- /SECTION: web-app.archive.api-integration -->
<!-- SECTION: web-app.archive.validation | status: DERIVED -->
## Validation

- The module has no user-entered fields, so there is no client-side form validation.
- The only identifier sent is the note id taken from a row the app itself rendered. The client does not construct or edit ids.
- Server-side outcomes are surfaced rather than pre-empted. The contract lists 404 for unknown ids (`API-NOTES-917869`), and the client shows it (`WEB-ARCHIVE-FR-003`).
- No server-side enforcement is claimed beyond what the contract lists.
- A malformed id returning 400 (`stories.md` US-003-06, API lane) is not listed for this operation in the contract. It cannot arise from a rendered row, so no handling or gap is added.
<!-- /SECTION: web-app.archive.validation -->
<!-- SECTION: web-app.archive.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No input states an accessibility standard or level (`GAP-WEB-008`), so the behaviours below are proposals (`ASM-WEB-171`).

- **Keyboard.** The restore control and the open action in each row are reachable and activatable by keyboard, in row order.
- **Focus.**
  - After a successful restore, focus moves to the next row's first control, or to the previous row if the last row was removed.
  - If the list becomes empty, focus moves to the empty-state heading.
  - A failed restore leaves focus on the restore control that was activated.
- **Live regions.**
  - A polite region announces "note restored" and the remaining count change.
  - The not-found and failure messages use an alert region.
- **Pending.** A Loading restore control exposes a busy and disabled state to assistive technology.
- **Form errors.** None apply, since the module has no fields.
<!-- /SECTION: web-app.archive.accessibility -->
<!-- SECTION: web-app.archive.i18n | status: BLOCKED -->
## Internationalisation

**Blocked.** No input names supported locales, a copy-key scheme or a translation process. `platform.md` names the stack only.

Unblocked by: a statement of supported locales and the copy-key convention. The strings this module will need are: the restore label, the empty-state message, the note-not-found message, the retryable-failure message, and the restored announcement.
<!-- /SECTION: web-app.archive.i18n -->
<!-- SECTION: web-app.archive.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma is `not-connected`, so every name below is an unverified claim from `design-brief.md`. No frame IDs were supplied.

| Reference | Value | Verification |
|---|---|---|
| Screen | `ArchivedNotesScreen`, "Archived Notes", module M3 | Unverified; from `screens-inventory.md` |
| Variants | Default, Empty, Error, Filled | Unverified; their meanings are `ASM-WEB-168` |
| Components | `Button/Primary`, `Empty/State` | Unverified; from `screens-inventory.md` and `components-inventory.md` |
| Frame ID | none supplied | `GAP-WEB-005` |
| Tokens | names in `web-app.archive.components` | Unverified; values unseen |
| Related story mapping | US-006-06 → `ArchivedNotesScreen` | From `stories-map.md` |

- The design shows no outgoing transitions for this screen.
- No row, message or navigation frame exists (`GAP-WEB-173`, `GAP-WEB-005`).
<!-- /SECTION: web-app.archive.design-refs -->
<!-- SECTION: web-app.archive.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are planned.

**Component**
- `archive.empty_state_renders_without_error`
- `archive.restore_control_loading_blocks_second_activation`
- `archive.row_removed_only_after_success`
- `archive.last_row_removed_shows_empty_state`
- `archive.failure_keeps_row_and_shows_message`

**E2E** (runnable once `GAP-WEB-001` and `GAP-WEB-002` close)
- `archive.restore_note_then_appears_in_active_list`
- `archive.restore_404_shows_not_found`
- `archive.open_archived_note_navigates_to_note`
- `archive.unauthenticated_visit_behaviour` (blocked on `GAP-WEB-001`)

**Contract**
- `archive.contract.unarchive_operation_present_at_pin` for `API-NOTES-917869`
- `archive.contract.archived_list_operation_present` (fails until `GAP-WEB-002` closes)
- `archive.contract.unarchive_error_set_matches_handled_statuses`

**Accessibility**
- `archive.a11y.focus_after_restore`
- `archive.a11y.live_region_announces_restore`
- `archive.a11y.alert_on_failure`
- `archive.a11y.keyboard_reaches_all_row_controls`

Expectations are fixed only after an accessibility standard is stated (`GAP-WEB-008`).
<!-- /SECTION: web-app.archive.test-plan -->
<!-- SECTION: web-app.archive.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Gaps (defined in the project spec, cited here)**

| ID | Effect on this module |
|---|---|
| `GAP-WEB-001` | Session model undecided. `/archive` access rule and permission-denied are `BLOCKED`. |
| `GAP-WEB-002` | No archived-only list operation. The archived view cannot load its data (`WEB-ARCHIVE-FR-006`). |
| `GAP-WEB-004` | Retry-after and error detail unstated. No retry timing is shown. |
| `GAP-WEB-005` | Missing design frames: message component, navigation, frame IDs. |
| `GAP-WEB-008` | No accessibility standard. Accessibility behaviours are proposals. |
| `GAP-WEB-013` | "Not allowed" versus "not found" display is unspecified. |

**Gaps minted here**

| ID | Missing | Degrades | Done instead | Closing artefact |
|---|---|---|---|---|
| `GAP-WEB-173` | No archived-note row or card component in the design inventory (3 components only). | `components`, `states` | Row structure left unspecified. | A design frame for the row. |
| `GAP-WEB-174` | Page size, ordering, pagination control and result count for the archived view. The sort control (`requirements.md` BR-020) and live count (BR-019) are stated for the active notes list only. | `requirements`, `states` | None specified. | A backend list operation with pagination, and a decision on whether the sort and count apply to `/archive`. |
| `GAP-WEB-175` | No Loading variant on `ArchivedNotesScreen`. | `states` | A loading presentation is required but unframed. | A design frame. |

**Contradictions.** `CON-WEB-003` (archive and unarchive idempotency versus the contract's error set) is defined in the project spec and applies here. No new `CON-` was minted.

**Questions (unattended: degradable defaults adopted)**

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-155` | degradable | Should restore ask for confirmation? | No. Restore is non-destructive and reversible (`requirements.md` BR-009). |
| `Q-WEB-156` | degradable | Can a note be opened from the archived view? | Yes, at `/notes/:id` (`WEB-ARCHIVE-FR-005`). |

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-167` | The restore control is a `Button/Primary` on each row of the archived view. `stories.md` does not say where it sits. | A design frame. |
| `ASM-WEB-168` | Variant meanings: Default is the initial state, Empty is no archived notes, Filled is a populated list, Error is a load failure. | Figma verification. |
| `ASM-WEB-169` | After a restore the user stays on `/archive`, and the Empty state shows when the last note is restored. | A navigation decision. |
| `ASM-WEB-170` | Token bindings are proposals from token names. Values are unseen. | Figma verification. |
| `ASM-WEB-171` | The accessibility behaviours in this module are proposals. | An accessibility standard and level. |
| `ASM-WEB-172` | On a 404 the row and cache are left unchanged, mirroring US-006-03. | A ruling. |
| `ASM-WEB-173` | The archived view is reached through navigation that the design does not frame. Only the route is specified. | Design frames for navigation. |

Project-level assumptions used unchanged: `ASM-WEB-001`, `ASM-WEB-009`, `ASM-WEB-013`, `ASM-WEB-014`, `ASM-WEB-015`.
<!-- /SECTION: web-app.archive.gaps -->
