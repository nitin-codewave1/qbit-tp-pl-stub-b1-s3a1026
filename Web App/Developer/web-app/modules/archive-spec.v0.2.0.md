---
id: agent-developer/web_app/web-app/modules/archive-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/archive-SPEC.md
depends_on: [web_app:US-006-06]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/archive-SPEC.md
---

<!-- SECTION: web-app.archive.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: archive
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-002, GAP-WEB-001]
contract_pin: "sha256:ceedbd350666"
scope: [/archive]
```

Derivation:

- **Module identity.** The module is `archive`, design label M3, owning `ArchiveScreen` (`web-app.module-map`; `design-brief.md`, `screens-inventory.md`).
- **`spec_mode`.** `design-brief.md` is present and Figma MCP is not connected (`RUN-CONTEXT`), so the mode is `FULL-FROM-BRIEF` (core 4.5).
- **`blocked_by`.** `GAP-WEB-002` is the absent archived-list operation. `GAP-WEB-001` is the undecided session model.
- **`scope`.** The only route proposed for this module is `/archive` (`web-app.routing`, `ASM-WEB-001`).
<!-- /SECTION: web-app.archive.frontmatter -->
<!-- SECTION: web-app.archive.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The archived-notes view (`ArchiveScreen`, `/archive`) and one action on it: restoring an archived note (US-006-06, second criterion; `web-app.module-map`, which assigns this module "restore from the archived view").

**Not responsible for.**

| Concern | Owner | Source |
|---|---|---|
| Archiving a note, and restore from `NoteDetailScreen` | `notes` module | `web-app.module-map` |
| Pin or unpin (third criterion of US-006-06) | `search` module | `web-app.module-map` |
| Note editor, note detail and tag controls. `INV-WEB-004` (edit and tag controls are never disabled because a note is archived) is enforced on `/notes/:id`, not here. | `notes` module | `web-app.state-management` |
| Login, session handling and the protected-route rule | `login` module and `GAP-WEB-001` | `web-app.routing` |
| Cache and invalidation policy | `web-app.state-management`, applied here, not redefined | |
| Backend behaviour, including the missing archived-list operation | backend; `GAP-WEB-002` | |

**What the module cannot do yet.** It cannot load the archived list. No contract operation returns archived-only notes, and search requires `q` or a tag and mixes active and archived notes when `include_archived` is set (`API-SEARCH-580505`). Everything that depends on that list is marked as such below (`GAP-WEB-002`).
<!-- /SECTION: web-app.archive.purpose -->
<!-- SECTION: web-app.archive.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

**WEB-ARCHIVE-FR-001 — Restore from the archived view.**
- **Given** an archived note is shown as a row on `ArchiveScreen`,
- **When** the user activates that row's restore control,
- **Then** the web app calls the unarchive operation for that note's id.
- **Traces to.** US-006-06 (second criterion).
- **Verification.** `archive.restore.calls-unarchive` (planned).
- **API.** → `API-NOTES-917869`.

**WEB-ARCHIVE-FR-002 — Restore success.**
- **Given** the unarchive call succeeds,
- **When** the response is received,
- **Then** the row is removed from `ArchiveScreen`, and the note appears in the active list on its next load.
- **Cache effects**, per `web-app.state-management` policy 1 and 2:
  - the returned fields are merged into the cached note-by-id entry;
  - all cached search results are invalidated;
  - the archived list and the active list are invalidated (both `BLOCKED` by `GAP-WEB-002`).
- **Traces to.** US-006-06 (second criterion: "reappears in the active list"); BR-011 (the default list excludes archived notes).
- **Verification.** `archive.restore.success-removes-row` (planned).
- **API.** → `API-NOTES-917869`.

**WEB-ARCHIVE-FR-003 — Single flight.**
- **Given** a restore request for a note is in flight,
- **When** the user activates its restore control again,
- **Then** no second request is sent, and the control shows its Loading state. Other rows stay usable.
- **Traces to.** US-006-06; BR-012 (archive and unarchive are idempotent and return consistent errors).
- **Verification.** `archive.restore.single-flight` (planned).
- **API.** → `API-NOTES-917869`.

**WEB-ARCHIVE-FR-004 — Note not found on restore.**
- **Given** the unarchive call returns 404,
- **When** the response is received,
- **Then** the row shows a "note not found" message.
  - Cached note data is not changed.
  - The user is offered a refresh of the list.
- **Traces to.** US-006-06; US-006-03 (404 convention, applied by analogy, `ASM-WEB-157`); `web-app.state-management` policy 4.
- **Verification.** `archive.restore.404-keeps-cache` (planned).
- **API.** → `API-NOTES-917869`.

**WEB-ARCHIVE-FR-005 — Conflict on restore.**
- **Given** the unarchive call returns 409,
- **When** the response is received,
- **Then** the web app fetches the note by id and acts on the note's current active or archived status.
  - If the note is active, it applies the FR-002 effects as for a success.
  - If the note is still archived, it shows a restore-conflict error on the row, keeps the row, and allows retry.
- **Traces to.** US-006-06; BR-012; `CON-WEB-151`, `ASM-WEB-151`.
- **Verification.** `archive.restore.409-reconciles-by-refetch` (planned).
- **API.** → `API-NOTES-917869`, `API-NOTES-045877`.

**WEB-ARCHIVE-FR-006 — Other failures.**
- **Given** the unarchive call fails by network error or 5xx,
- **When** the failure is detected,
- **Then** the row shows an error, keeps its place, leaves cached data unchanged, and allows retry.
- **Traces to.** US-006-06; `web-app.state-management` policy 4 and 5 (`ASM-WEB-011`).
- **Verification.** `archive.restore.failure-allows-retry` (planned).
- **API.** → `API-NOTES-917869`.

**WEB-ARCHIVE-FR-007 — Open an archived note.**
- **Given** an archived note is shown on `ArchiveScreen`,
- **When** the user selects the note,
- **Then** the web app navigates to `/notes/:id` (`NoteDetailScreen`).
- **Traces to.** US-006-06 ("an archived note is viewed"); `web-app.module-map` (`archive → notes`).
- **Verification.** `archive.row.opens-detail` (planned).
- **API.** → none in this module. The detail load belongs to the `notes` module.

**Invariants.**
- `INV-WEB-151`: after a restore is accepted (a 200, or a 409 reconciled to active by FR-005), the note never remains in the archived view.
- `INV-WEB-152`: at most one restore request per note is in flight at a time.

**Not written (BLOCKED).** Requirements for loading the archived list, ordering it, paging it and counting its rows cannot be written. No archived-only operation exists (`GAP-WEB-002`), and no input says whether sort, pinned-first or a result count apply to this view (`GAP-WEB-153`, `GAP-WEB-158`). Unblocked by: a backend operation or decision on `GAP-WEB-002`, and a product decision on `GAP-WEB-153`.
<!-- /SECTION: web-app.archive.requirements -->
<!-- SECTION: web-app.archive.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/archive` | `ArchiveScreen` | app (`web-app.routing`) | Team member, the only web role (`web-app.authorization-ui`). Whether a session is required is `BLOCKED` (`GAP-WEB-001`, `Q-BE-003`). If it is required, an unauthenticated visit redirects to `/login` (`web-app.routing`). |

**Notes.**
- The path is a proposal (`ASM-WEB-001`). The route table has no other `archive` routes.
- Outbound navigation: rows open `/notes/:id` (FR-007). The design shows no outgoing transitions from `ArchiveScreen` (`screens-inventory.md`).
- Inbound navigation: the only design transitions into `ArchiveScreen` come from `TagManagementScreen` and `ScopeDocumentationScreen`, neither of which is routed. No navigation component exists in the design, so no routed entry point to `/archive` is defined (`GAP-WEB-154`).
<!-- /SECTION: web-app.archive.routes -->
<!-- SECTION: web-app.archive.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `archive`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.archive.components -->
<!-- SECTION: web-app.archive.states | status: DRAFT-UNVERIFIED -->
## States

Design variants for `ArchiveScreen` are Default, Empty, Error and Filled (`screens-inventory.md`). The mapping below is an assumption (`ASM-WEB-156`).

**Route `/archive`: list states.** None of these is reachable until an archived-only list operation exists (`GAP-WEB-002`).

| State | Behaviour | Design variant |
|---|---|---|
| Loading | A loading presentation, unspecified (`GAP-WEB-155`) | none |
| Empty | `Empty/State` is shown; this is not an error. The wording is not supplied (`GAP-WEB-157`). | Empty |
| Populated | Archived-note rows, each with a restore control | Filled (Default is the shared base layout, `ASM-WEB-156`) |
| Error | A user-facing error state with retry, never a blank screen. Inputs the user has entered are retained (`ASM-WEB-011`). | Error |

**Route `/archive`: restore states per row.** These are specifiable now.

| State | Behaviour |
|---|---|
| Idle | Restore control in its Default state |
| In flight | Restore control in its Loading state, with further activation ignored (FR-003) |
| Succeeded | The row is removed (FR-002). If it was the last row, the screen becomes Empty. |
| Not found (404) | Row-level "note not found" message; the row stays and a refresh is offered (FR-004) |
| Conflict (409) | A refetch decides the outcome (FR-005) |
| Failed (network or 5xx) | Row-level error with retry (FR-006) |

**Permission-denied.** The route may be permission-gated, but the protected-route rule is undecided (`GAP-WEB-001`). The backend defines no ownership rule and no 401 or 403 for note operations (`GAP-WEB-009`). No permission-denied state is specified, and no new gap is raised.

**Partial failure.** Not applicable. This module has no multi-item operation: restore acts on one note, and no bulk restore is required by any input.
<!-- /SECTION: web-app.archive.states -->
<!-- SECTION: web-app.archive.api-integration | status: VERIFIED -->
## API integration

References only. Request and response shapes are defined in the contract and not restated here (core rule 6).

| Operation | Pinned ID | Used by |
|---|---|---|
| Restore (unarchive) a note | `API-NOTES-917869` (`POST /notes/{id}/unarchive`; documented statuses 200, 404, 409) | FR-001 to FR-006 |
| Get a note by id | `API-NOTES-045877` | FR-005 (reconcile on 409) |
| List archived-only notes | none in the contract | `BLOCKED` (`GAP-WEB-002`) |

**Not used.**
- `API-SEARCH-580505` returns a mix of active and archived notes when `include_archived` is set, and requires `q` or a tag. It is not an archived-only list and is not used to emulate one.
- No 401 or 403 is documented for these operations (`GAP-WEB-009`).
<!-- /SECTION: web-app.archive.api-integration -->
<!-- SECTION: web-app.archive.validation | status: DERIVED -->
## Validation

**Client rules.** None. `ArchiveScreen` has no user-entered fields. The note id sent to the restore operation comes from a loaded list item and is never typed (derived from FR-001).

**Enforced server-side (contract).**
- An unknown note id returns 404.
- A conflicting state returns 409 (`API-NOTES-917869`, errors 404 and 409).
- The trigger for the 409 is not stated in the contract's error model (`CON-WEB-151`).

**Client guard.** FR-003 and `INV-WEB-152` prevent duplicate in-flight restores. This is a client behaviour, not a validation rule.
<!-- /SECTION: web-app.archive.validation -->
<!-- SECTION: web-app.archive.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No input states an accessibility standard, level or verification method, and `web-app.accessibility` was not produced (`GAP-WEB-156`). The behaviours below are proposals (`ASM-WEB-155`) and are not tied to a standard.

- **Keyboard.**
  - Each row has two stops: the row's link to `/notes/:id`, and the restore button.
  - The restore button activates with Enter and Space.
  - The Loading state keeps focus on the button and sets it as busy.
- **Focus after a successful restore.**
  - Move focus to the next row's restore control.
  - If none remains, move focus to the screen heading, or to the `Empty/State` content when it appears.
  - Focus is never dropped to the document body.
- **Live regions.**
  - A polite live region announces "note restored" after a success (wording `GAP-WEB-157`).
  - Row-level errors (404, 409 conflict, failure) are announced assertively.
  - The same live region announces the screen becoming Empty.
- **Errors.** Each row-level error is text associated with its restore control, not conveyed by colour alone. The `UI/text/error` binding is a colour; the message text carries the meaning.
- **Focus visibility.** Focus indicators use the proposed `UI/border/focus` binding (`ASM-WEB-153`).
<!-- /SECTION: web-app.archive.accessibility -->
<!-- SECTION: web-app.archive.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input names a locale or a copy catalogue (`GAP-WEB-157`).

Strings this module needs:
- empty-view message;
- "restore" control label;
- restore-succeeded announcement;
- note-not-found message;
- restore-conflict message;
- generic failure and retry labels;
- list-load error message.

Unblocked by: a supplied locale list and copy for these strings.
<!-- /SECTION: web-app.archive.i18n -->
<!-- SECTION: web-app.archive.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

| Item | Value | Verification |
|---|---|---|
| Screen | `ArchiveScreen` "Archive", module M3 | Unverified. From `design-brief.md`, `screens-inventory.md`. |
| Variants | Default, Empty, Error, Filled | Unverified |
| Components | `Button/Primary`, `Empty/State` | Unverified |
| Frame IDs | None supplied | Not applicable |
| Story mapping | US-006-06 maps to `ArchiveScreen` (`stories-map.md`). So do US-003-01, US-003-02 and US-003-06, which are API stories this module does not own. | Unverified |

`spec_mode` is `FULL-FROM-BRIEF`: Figma MCP is not connected, so every screen, variant, component and token name is an unverified claim (core 4.5).
<!-- /SECTION: web-app.archive.design-refs -->
<!-- SECTION: web-app.archive.test-plan | status: DERIVED -->
## Test plan

All tests are planned, with names proposed from the requirements above.

**Component.**

| Test | Covers |
|---|---|
| `archive.restore.calls-unarchive` | FR-001 |
| `archive.restore.single-flight` | FR-003, `INV-WEB-152` |
| `archive.restore.success-removes-row` | FR-002, `INV-WEB-151` |
| `archive.restore.404-keeps-cache` | FR-004 |
| `archive.restore.failure-allows-retry` | FR-006 |
| `archive.row.opens-detail` | FR-007 |
| `archive.empty-state-after-last-restore` | the Empty state, derived from FR-002 |

**E2E.** The restore flow, and any flow that starts from loading the archived list, is `BLOCKED` until `GAP-WEB-002` closes. Restore can be exercised against mocked rows in the meantime.

**Contract.** Against `API-NOTES-917869`:
- 200 handled per FR-002;
- 404 handled per FR-004;
- 409 handled per FR-005, including both refetch outcomes.

**Accessibility.** Focus after restore, live-region announcements and keyboard operation of the restore control, as proposed in `web-app.archive.accessibility`. The pass criteria depend on the standard (`GAP-WEB-156`).
<!-- /SECTION: web-app.archive.test-plan -->
<!-- SECTION: web-app.archive.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, assumptions and questions

**Referenced from the project spec (defined there, not re-minted).**

| ID | Relevance |
|---|---|
| `GAP-WEB-001` | Session model undecided, so the protected-route rule is `BLOCKED` |
| `GAP-WEB-002` | No archived-only list operation |
| `GAP-WEB-004` | No message or toast component in the design |
| `GAP-WEB-009` | No ownership rule; no 401 or 403 documented |
| `ASM-WEB-001` | Route paths are proposals |
| `ASM-WEB-011` | Failure states allow retry and keep inputs |

**Gaps raised here.**

| ID | Missing | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-WEB-152` | The design has no row component for `ArchiveScreen`, and no input says which fields a row shows | components, states | A design frame for the archived row |
| `GAP-WEB-153` | No input says whether sort, pinned-first order (BR-021), pin icon, or a result count (BR-019) apply to the archived view | requirements | A product decision |
| `GAP-WEB-154` | No routed entry point to `/archive`: the design's inbound transitions come only from unrouted screens, and there is no navigation component (see also `GAP-WEB-008`) | routes | A design flow or navigation frame |
| `GAP-WEB-155` | No loading presentation in the design or the component inventory | states | A design annotation |
| `GAP-WEB-156` | No accessibility standard, level or verification method | accessibility, test-plan | A platform or NFR statement |
| `GAP-WEB-157` | No copy and no locale list | i18n, states | Copy and locales |
| `GAP-WEB-158` | Page size, paging and behaviour at scale of the archived list are unknown | requirements | Defined by the operation that closes `GAP-WEB-002` |

**Contradiction.**

`CON-WEB-151` (non-critical): restore of an already-active note.
- **Claim A.** US-003-02 (`stories.md`): unarchiving an already-active note returns 200 idempotently, with no error or state change.
- **Claim B.** `API-NOTES-917869` (`backend-api.md`, api-surface) lists 409 among its errors. The backend error model gives 409 only for archiving an already-archived note, so the unarchive 409 has no stated trigger.
- **Consequence of A.** If the 409 means "already active", the web app should treat it as success.
- **Consequence of B.** If the 409 means something else, such as a concurrent write conflict, treating it as success would remove a row for a note that is still archived.
- **Reading adopted.** FR-005 refetches the note and decides by its status (`ASM-WEB-151`).
- **Checks run for this module.**
  - Story ↔ design: no difference found. `design-brief.md` carries no behaviour beyond variant names.
  - Story ↔ requirement: this item.
  - Requirement ↔ requirement: BR-012 says both "idempotent" and "return a consistent error for invalid state transitions". It is read as the same ambiguity.
  - Design ↔ design: no difference found.

**Assumptions.**

| ID | Assumption |
|---|---|
| `ASM-WEB-151` | A 409 on restore is reconciled by refetching the note and acting on its status (FR-005). |
| `ASM-WEB-152` | `Card/Item` is reused for archived rows and `Button/Primary` for the restore control. The design maps neither to this use. |
| `ASM-WEB-153` | Token bindings are proposals from token names in `tokens-index.md`; values are unseen. |
| `ASM-WEB-154` | The restore control sits on `ArchiveScreen` rows. Restore from `NoteDetailScreen` belongs to the `notes` module, and "an archived note is viewed" (US-006-06) is covered by both modules without overlap in behaviour. |
| `ASM-WEB-155` | The accessibility behaviours proposed in this module are not tied to any standard. |
| `ASM-WEB-156` | Design variants map as Empty to no archived notes, Error to list-load failure and Filled to a populated list. Default is the shared base layout. |
| `ASM-WEB-157` | The 404 convention of US-006-03 (do not apply local changes) is applied to restore by analogy. |
| `ASM-WEB-158` | No confirmation step precedes restore, because no input requires one and the action is reversible by archiving again (`Q-WEB-151` default). |

**Questions (degradable; unattended run, so defaults taken).**
- `Q-WEB-151`: does restore need a confirmation step? Default: no (`ASM-WEB-158`).
- `Q-WEB-152`: after a restore, does the user stay on `/archive`? Default: yes, with the row removed.
<!-- /SECTION: web-app.archive.gaps -->
