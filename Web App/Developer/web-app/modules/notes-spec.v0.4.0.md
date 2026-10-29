---
id: agent-developer/web_app/web-app/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/notes-SPEC.md
depends_on: [web_app:US-006-02, web_app:US-006-03, web_app:US-006-06]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/notes-SPEC.md
---

<!-- SECTION: web-app.notes.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: notes
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001]
contract_pin: "sha256:9550f0d579d8"
scope: ["/notes/new", "/notes/:id"]
```

- `spec_mode` follows `RUN-CONTEXT.inputs`: `design-brief.md` is present and Figma is `not-connected` (core 4.5).
- Route paths are proposals (`ASM-WEB-001` in `web-app.overview`).
- `GAP-WEB-001` (session model) blocks only the access rule of both routes. Every other part of the module is written.
<!-- /SECTION: web-app.notes.frontmatter -->
<!-- SECTION: web-app.notes.purpose | status: DERIVED -->
## Purpose

**Responsible for.** Everything a Team member does with one note on `NoteEditorScreen` (design module M1), derived from the `web-app.module-map` row for `notes`:
- creating a note (US-006-02);
- opening a saved note and editing its title and body (US-006-03);
- archiving an open active note and restoring an open archived note (US-006-06, first two criteria);
- keeping the cached note and the cached lists correct after each successful change, per `web-app.state-management`.

**Not responsible for** (owned elsewhere, referenced only):
- the notes list, search, sort, live count and the row pin toggle (`search`);
- the archived-notes view and its list (`archive`);
- tag add, remove and listing (`tags`, unrouted, `GAP-WEB-003`);
- login, session, protected-route redirect and sign-out (`login` and `GAP-WEB-001`);
- application chrome and navigation back to the list (`GAP-WEB-005`);
- backend behaviour (core I-1).

The pin criterion of US-006-06 is about "a notes row in the list", which `search` owns (`CON-WEB-054`).
<!-- /SECTION: web-app.notes.purpose -->
<!-- SECTION: web-app.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All contract operations are cited by ID and not restated. Planned verification names are listed in `web-app.notes.test-plan`.

**WEB-NOTES-FR-001: Create a note**
- Given the user is on `/notes/new` with a title and body entered, when they submit, then the web app calls `API-NOTES-232390` and, on success, navigates to `/notes/:id` for the saved note.
- Traces to. US-006-02 (criterion 1).
- Verification. `notes.create.submits-and-navigates` (planned).
- API. `API-NOTES-232390`.

**WEB-NOTES-FR-002: Create validation error shown inline**
- Given the title field is left empty, when the user submits, then the web app shows the API's validation error inline at the title field and no note is created. The client does not block the submit itself (`ASM-WEB-068`).
- Traces to. US-006-02 (criterion 2); `requirements.md` BR-001.
- Verification. `notes.create.empty-title-inline-error` (planned).
- API. `API-NOTES-232390`.

**WEB-NOTES-FR-003: Server-set creation time shown**
- Given the create request succeeds, when the response is received, then the note's server-set `created_at` is displayed on `/notes/:id` (`ASM-WEB-073`).
- Traces to. US-006-02 (criterion 3).
- Verification. `notes.create.shows-created-at` (planned).
- API. `API-NOTES-232390`, `API-NOTES-045877`.

**WEB-NOTES-FR-004: Create failure other than validation**
- Given the create request fails with a network error or a 5xx, when the failure is received, then the web app shows a retryable error and keeps the typed title and body.
- Traces to. US-006-02; `web-app.state-management` policy 6.
- Verification. `notes.create.retryable-failure-keeps-input` (planned).
- API. `API-NOTES-232390`.

**WEB-NOTES-FR-005: Open a saved note**
- Given the user opens `/notes/:id`, when the page loads, then the web app calls `API-NOTES-045877` and shows the note's title and body in the editor, with the controls in FR-010 and FR-011.
- Traces to. US-006-03 (criterion 1, "an existing note is opened in the editor").
- Verification. `notes.open.loads-into-editor` (planned).
- API. `API-NOTES-045877`.

**WEB-NOTES-FR-006: Load failure**
- Given the note cannot be loaded, when the response is received, then a 404 shows the not-found state and a network error or 5xx shows a retryable error state (`ASM-WEB-071`).
- Traces to. US-006-03 (criterion 2, extended to load); `web-app.state-management` policy 6.
- Verification. `notes.open.not-found-and-error` (planned).
- API. `API-NOTES-045877`.

**WEB-NOTES-FR-007: Save an edit**
- Given an existing note is open in the editor, when the user changes the title or body and saves, then the web app calls `API-NOTES-847551` with only the changed fields (`ASM-WEB-069`) and shows the updated content from the response. The user stays on `/notes/:id` (`ASM-WEB-070`).
- Traces to. US-006-03 (criterion 1).
- Verification. `notes.edit.saves-and-shows-updated` (planned).
- API. `API-NOTES-847551`.
- The story says "PUT/PATCH". The contract supplies PATCH only, so PATCH is used.

**WEB-NOTES-FR-008: Save returns 404**
- Given a save request returns 404, when the response is received, then the web app shows a "note not found" message and does not apply local changes to the cached note. The typed values stay visible in the form (`ASM-WEB-071`).
- Traces to. US-006-03 (criterion 2); `INV-WEB-005` in `web-app.state-management`.
- Verification. `notes.edit.save-404-no-local-apply` (planned).
- API. `API-NOTES-847551`.

**WEB-NOTES-FR-009: Save returns a validation error**
- Given a save request returns a field-level validation error (400), when the response is received, then the web app highlights the invalid field using the `Input/Text` Error state and shows the message at that field. If the field cannot be identified, the message is form-level (`ASM-WEB-068`, `GAP-WEB-004`).
- Traces to. US-006-03 (criterion 3).
- Verification. `notes.edit.save-400-highlights-field` (planned).
- API. `API-NOTES-847551`.

**WEB-NOTES-FR-010: Archive an open active note**
- Given an active note is open, when the user clicks archive, then the web app calls `API-NOTES-810640`. On success the note shows the archived state and the control offered becomes restore. All cached lists and search results are invalidated, so the note no longer appears in the default active list. The user stays on `/notes/:id` (`ASM-WEB-070`).
- Traces to. US-006-06 (criterion 1); `requirements.md` BR-009.
- Verification. `notes.archive.updates-state-and-invalidates` (planned).
- API. `API-NOTES-810640`.

**WEB-NOTES-FR-011: Restore an open archived note**
- Given an archived note is viewed, when the user clicks restore, then the web app calls `API-NOTES-917869`. On success the note shows the active state and the control offered becomes archive. All cached lists and search results are invalidated, so the note reappears in the active list.
- Traces to. US-006-06 (criterion 2).
- Verification. `notes.restore.updates-state-and-invalidates` (planned).
- API. `API-NOTES-917869`.

**WEB-NOTES-FR-012: Archive returns 409**
- Given archive returns 409, when the response is received, then the web app re-reads the note (`API-NOTES-045877`) and shows its current state with no error. This follows `ASM-WEB-009` and `CON-WEB-003` in `web-app.overview`.
- Traces to. `requirements.md` BR-012; `stories.md` US-003-01 (API lane).
- Verification. `notes.archive.409-rereads-state` (planned).
- API. `API-NOTES-810640`, `API-NOTES-045877`.

**WEB-NOTES-FR-013: Archive or restore fails otherwise**
- Given archive or restore returns 404, then the not-found state is shown. Given it fails with a network error or 5xx, then a retryable error is shown, the displayed status does not change (`INV-WEB-057`), and typed edits remain in the form untouched (`ASM-WEB-079`).
- Traces to. US-006-06; `web-app.state-management` policies 4 and 6.
- Verification. `notes.archive-restore.failure-leaves-state` (planned).
- API. `API-NOTES-810640`, `API-NOTES-917869`.

**WEB-NOTES-FR-014: Archived notes stay editable**
- Given an archived note is open, then title and body remain editable and saveable, and a save does not change the archive state.
- Traces to. `requirements.md` BR-010; `stories.md` US-003-05 (API lane, derived for the web).
- Verification. `notes.edit.archived-note-editable` (planned).
- API. `API-NOTES-847551`.

**WEB-NOTES-FR-015: Cache effects of a successful change**
- Given create, edit, archive or restore succeeds, then the cached note-by-id entry is replaced by the returned note. All cached search results and lists are invalidated (`web-app.state-management` policies 1 and 2). Rows do not change optimistically.
- Traces to. US-006-02, US-006-03, US-006-06; `web-app.state-management`.
- Verification. `notes.cache.replace-and-invalidate` (planned).
- API. `API-NOTES-232390`, `API-NOTES-847551`, `API-NOTES-810640`, `API-NOTES-917869`.

**WEB-NOTES-FR-016: One request at a time**
- Given a request from this module is in progress, then its triggering control shows `Button/Primary` Loading, no second request can be started from it, and the title and body are not editable until it completes (`ASM-WEB-072`).
- Traces to. US-006-02, US-006-03, US-006-06; `ASM-WEB-015` in `web-app.overview`.
- Verification. `notes.pending.blocks-duplicate-submit` (planned).
- API. none of its own.

**Not written: `BLOCKED` by `GAP-WEB-001`.**
- The protected-route rule for both routes.
- The unauthenticated redirect to `/login`.
- Handling of an expired or rejected session while the user is editing.
- The contract lists no 401 or 403 for the five operations used here.

Unblocked by the backend session decision (`Q-BE-003`) and a clean `requirements.md` BR-023.

**Invariants**
- `INV-WEB-056`: The web app never sends a note `id`, `created_at` or `updated_at` as user input from this module. They are server-set (`requirements.md` BR-002, BR-003; `stories.md` US-001-06).
- `INV-WEB-057`: The displayed archived or active state and the control offered always equal the last server-confirmed state. Nothing is optimistic (`ASM-WEB-014` in `web-app.overview`).
- `INV-WEB-058`: An archived note is never made read-only by the web app (`requirements.md` BR-010).

**Story coverage (owned stories)**

| Story | Covered by |
|---|---|
| US-006-02 | FR-001 to FR-004, FR-015, FR-016 |
| US-006-03 | FR-005 to FR-009, FR-014 to FR-016 |
| US-006-06 | FR-010 to FR-013, FR-015, FR-016. The pin criterion is owned by `search` (`CON-WEB-054`). |
<!-- /SECTION: web-app.notes.requirements -->
<!-- SECTION: web-app.notes.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes/new` | `NoteEditorScreen`, create (Empty variant, `ASM-WEB-067`) | app | `BLOCKED` (`GAP-WEB-001`) |
| `/notes/:id` | `NoteEditorScreen`, saved note and edit (Filled variant, `ASM-WEB-067`) | app | `BLOCKED` (`GAP-WEB-001`) |

**Navigation owned here**
- Create success goes to `/notes/:id`, where `:id` is the identifier returned by `API-NOTES-232390` (US-006-02).
- Save, archive and restore keep the user on `/notes/:id` (`ASM-WEB-070`).
- `NoteEditorScreen` has no outgoing transition in the design (`screens-inventory.md`).
- No control back to `/notes` is specified, because application chrome has no frame (`GAP-WEB-005`).

**Route-level facts**
- `/notes/:id` has no separate `/edit` path. Viewing and editing share one screen (`ASM-WEB-067`, `GAP-WEB-010`).
- A not-found note is rendered on the same route and is not redirected.
<!-- /SECTION: web-app.notes.routes -->
<!-- SECTION: web-app.notes.components | status: DRAFT-UNVERIFIED -->
## Components

The inventory is `components-inventory.md`. The component lists in `screens-inventory.md` for `NoteEditorScreen` are `Empty/State` and `Input/Text`. Token bindings are proposals from token names, with values unseen (`ASM-WEB-075`). No visual value is hardcoded.

| Component | Used for | Variants and states (as supplied) | Proposed token bindings |
|---|---|---|---|
| `Input/Text` | title field and body field (`ASM-WEB-074`) | 5 variants. States: Default, Focused, Filled, Error, Disabled. | Default: `UI/surface/input`, `UI/border/input`, `UI/text/primary`, `UI/text/placeholder`. Focused: `UI/surface/input-focus`, `UI/border/input-focus`. Error: `UI/border/error`, `UI/text/error`. Disabled: `UI/surface/disabled`, `UI/border/disabled`, `UI/text/disabled`. |
| `Button/Primary` | create submit, save, archive and restore (`ASM-WEB-074`) | 12 variants (names not supplied). States: Default, Pressed, Loading, Disabled. | Default: `UI/surface/button/primary`, `UI/text/on-primary`. Hover: `UI/surface/button/primary-hover`. Disabled: `UI/surface/disabled`, `UI/text/disabled`. Loading: no token supplied. |
| `Empty/State` | not-found state (`ASM-WEB-071`) | 1 variant. State: Default. | `UI/text/secondary` on `UI/surface/default` (proposal). |
| Message region (plain text, no component exists) | validation, failure and success messages | none | Error: `UI/text/error`. Success: `UI/text/success`. |

**Elements with no design frame** (`GAP-WEB-073`)
- The archived or active status indicator.
- The `created_at` display.
- The loading indicator for the page.
- The success message.

These are specified by behaviour only, with no token or layout bound.
<!-- /SECTION: web-app.notes.components -->
<!-- SECTION: web-app.notes.states | status: DRAFT-UNVERIFIED -->
## States

`NoteEditorScreen` variants are Default, Empty, Error and Filled (`screens-inventory.md`). Their meanings are unverified (`ASM-WEB-067`).

| Route | Loading | Empty | Populated | Error |
|---|---|---|---|---|
| `/notes/new` | No page load. On submit the button shows Loading and the fields are not editable (FR-016). | Blank form (Empty variant). | Typed values (Filled variant). | Inline validation error at the field (FR-002). A retryable failure message keeps the inputs (FR-004). |
| `/notes/:id` | The note is being fetched. No frame exists (`GAP-WEB-073`), so a plain loading indication is announced (see accessibility). | Note not found, shown with `Empty/State` (FR-006, FR-008). | Note loaded into the editor (Filled variant), archived or active state shown. | Load failure with retry (FR-006). Field highlight on a save 400 (FR-009). A retryable failure on save, archive or restore (FR-009, FR-013). |

**Permission-denied: not specified.**
- Whether these routes are permission-gated depends on `GAP-WEB-001`.
- Whether another person's note shows as "not found" or "forbidden" depends on `GAP-WEB-013`.
- The contract lists no 401 or 403 for the operations used.
- Nothing is asserted until both close.

**Partial-failure: not applicable.** The module has no multi-item operation, so no state is specified and no gap is raised (W8).
<!-- /SECTION: web-app.notes.states -->
<!-- SECTION: web-app.notes.api-integration | status: DRAFT-UNVERIFIED -->
## API integration

Reference only. Shapes, field types and error semantics live in the contract (`backend-api.md`, pin `sha256:9550f0d579d8`).

| Operation | Contract ID | Used by | Status to UI state |
|---|---|---|---|
| Create note | `API-NOTES-232390` | FR-001 to FR-004 | 201: go to `/notes/:id`. 400: inline error. Other: retryable error. |
| Get note | `API-NOTES-045877` | FR-005, FR-006, FR-012 | 200: populate. 404: not-found state. Other: retryable error. |
| Edit note | `API-NOTES-847551` | FR-007 to FR-009, FR-014 | 200: show returned note. 400: field highlight. 404: not-found message, no local apply. Other: retryable error. |
| Archive note | `API-NOTES-810640` | FR-010, FR-012, FR-013 | 200: archived state. 404: not-found state. 409: re-read and show the current state. Other: retryable error. |
| Unarchive note | `API-NOTES-917869` | FR-011, FR-013 | 200: active state. 404: not-found state. Other: retryable error. |

**Notes**
- Contract IDs carry no `@v` suffix, as supplied (`web-app.overview`).
- Network failures and 5xx are handled generically (`ASM-WEB-013` in `web-app.overview`).
- The field-detail shape of a 400 is unstated (`GAP-WEB-004`, backend `GAP-BE-004`).
- Tags are accepted by the contract on create, but no web story requires them (`GAP-WEB-003`). This module does not send them.
- The `status` field's member values are unnamed in the contract (`GAP-WEB-075`).
<!-- /SECTION: web-app.notes.api-integration -->
<!-- SECTION: web-app.notes.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Enforced on client | Also enforced on server | Source |
|---|---|---|---|
| Title and body are required on create | No pre-check. The submit is sent and the server's 400 is shown (`ASM-WEB-068`). | Yes | US-006-02 (criterion 2); `requirements.md` BR-001; contract `API-NOTES-232390` |
| Title must not be empty on edit | No pre-check. The save is sent and the server's 400 is shown. | Yes | Contract `API-NOTES-847551` |
| Only changed fields are sent on save, and nothing is sent when nothing changed | Yes (`ASM-WEB-069`) | n/a | derived |
| No `id`, `created_at` or `updated_at` is ever submitted | Yes (`INV-WEB-056`) | Yes, client-supplied timestamps are ignored | `requirements.md` BR-002, BR-003; `stories.md` US-001-06 |
| No attachment or file input exists | Yes, no such control | Yes, 400 on attachments | `requirements.md` BR-016, NFR-006 |
| Maximum title and body length | No limit set by the client, so nothing is truncated | Unknown | `GAP-WEB-074` |
| Whitespace-only title or body | Not trimmed or judged by the client | Unknown | `GAP-WEB-074` |

**Attribution of a 400.** When the response does not identify the field, the error is attributed to whichever of title and body was empty in the submitted values. If neither was empty, it is shown form-level (`ASM-WEB-068`).
<!-- /SECTION: web-app.notes.validation -->
<!-- SECTION: web-app.notes.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No input names a standard or conformance level (`GAP-WEB-008`, and `web-app.accessibility` is `BLOCKED`). The behaviours below are proposals (`ASM-WEB-076`).

**Focus management**
- On `/notes/new`, focus starts on the title field.
- After a successful create, focus moves to the page heading of `/notes/:id`.
- On a 400, focus moves to the first invalid field.
- On a not-found state, focus moves to its heading.
- When archive is replaced by restore (or the reverse), the old control unmounts. Focus moves to the new control.

**Keyboard paths**
- All fields and controls are reachable in DOM order.
- Enter inside the body field inserts a newline. Submit and save are activated through their buttons.
- A control in the Loading state stays focusable and is marked busy. Activation is ignored while a request is pending (FR-016).

**Form errors**
- The label of each field is programmatically associated with it.
- An invalid field is marked invalid and referenced to its message.
- Messages are text and are never conveyed by colour alone.

**Live regions**
- Success after save, archive or restore is announced politely.
- Validation and failure messages are announced as alerts.
- The page loading state on `/notes/:id` is announced politely.
<!-- /SECTION: web-app.notes.accessibility -->
<!-- SECTION: web-app.notes.i18n | status: BLOCKED -->
## I18n

**Blocked.** No input names a locale list, a copy-key convention or any copy deck. One literal is supplied: the message "note not found" (`stories.md` US-006-03, criterion 2).

**Message slots the module needs** (no key or text is invented):
- note not found;
- the create validation message, shown inline;
- the save validation message;
- a retryable failure;
- saved, archived and restored confirmations;
- the archived-state label;
- the created-at label.

Date-time formatting is unspecified (`GAP-WEB-076`).

Unblocked by: a locale list, a copy-key convention and the copy for the slots above.
<!-- /SECTION: web-app.notes.i18n -->
<!-- SECTION: web-app.notes.design-refs | status: DRAFT-UNVERIFIED -->
## Design refs

`spec_mode` is `FULL-FROM-BRIEF`. Figma is `not-connected` and `design-brief.md` supplies names only. **No frame IDs were supplied, so none are asserted.**

| Design element (name from the brief) | Detail claimed | Verification |
|---|---|---|
| `NoteEditorScreen` (module M1) | Variants Default, Empty, Error, Filled. Components `Empty/State`, `Input/Text`. No outgoing transitions. | Unverified claim. Not checked against Figma. |
| `Input/Text`, `Button/Primary`, `Empty/State` | States and variant counts as in `web-app.notes.components`. | Unverified claim. |
| Tokens named in `web-app.notes.components` | Names appear in `tokens-index.md`. Values and component bindings were not supplied. | Unverified claim. |
| Note-detail screen | None exists in the design (`GAP-WEB-010`). | n/a |

Variant meanings, frame IDs and token values are unverified until Figma verification or a brief with frame references.
<!-- /SECTION: web-app.notes.design-refs -->
<!-- SECTION: web-app.notes.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are proposals. No framework is named by any input.

**Component**
- `notes.create.empty-title-inline-error`
- `notes.edit.save-400-highlights-field`
- `notes.pending.blocks-duplicate-submit`
- `notes.archive-restore.control-follows-status` (`INV-WEB-057`)
- `notes.edit.archived-note-editable` (`INV-WEB-058`)
- `notes.cache.replace-and-invalidate`

**End-to-end**
- `notes.create.submits-and-navigates`
- `notes.create.shows-created-at`
- `notes.open.loads-into-editor`
- `notes.edit.saves-and-shows-updated`
- `notes.edit.save-404-no-local-apply`
- `notes.archive.updates-state-and-invalidates`
- `notes.restore.updates-state-and-invalidates`
- `notes.archive.409-rereads-state`

**Contract**
- Every request from this module uses only the five operations in `web-app.notes.api-integration`.
- No request body from this module carries `id`, `created_at` or `updated_at` (`INV-WEB-056`).
- Each status in the integration table maps to its UI state against the pinned contract.

**Accessibility**
- Focus moves as in the accessibility section after create, a 400, a not-found state and the archive to restore swap.
- A form-error and live-region check for FR-002, FR-009 and FR-013.
- The conformance level cannot be set until `GAP-WEB-008` closes.

**Not plannable until unblocked.** Protected-route, expiry and 401 tests (`GAP-WEB-001`).
<!-- /SECTION: web-app.notes.test-plan -->
<!-- SECTION: web-app.notes.gaps | status: DERIVED -->
## Gaps

**Open items from the project spec that bite this module.** Defined in `web-app.overview`, referenced here.
- `GAP-WEB-001`: session model. It blocks the access rule of both routes.
- `GAP-WEB-003`: tags. No tag control is specified.
- `GAP-WEB-004`: 400 detail shape. The field attribution rests on `ASM-WEB-068`.
- `GAP-WEB-005`: missing design frames.
- `GAP-WEB-008`: accessibility standard.
- `GAP-WEB-010`: no note-detail screen.
- `GAP-WEB-013`: "not found" versus "forbidden".
- `CON-WEB-003`: archive idempotence versus the contract's 409.

**New gaps**

| ID | Missing | Degrades | Done instead | Closing artefact |
|---|---|---|---|---|
| `GAP-WEB-073` | Design frames for: the page loading state, the archived or active indicator, archive and restore controls, the `created_at` display, success and failure messages, and the not-found page. Variant names for `Button/Primary` (12) and whether `Input/Text` supports multiline are also unstated. | `components`, `states`, `design-refs` | Behaviour only, with no token bound (`ASM-WEB-074`). | Design frames for each element. |
| `GAP-WEB-074` | Maximum length of title and body, and the rule for whitespace-only values. The backend must state these. | `validation` | No client limit and no trimming. | A backend or NFR statement. |
| `GAP-WEB-075` | The `status` field's member values are unnamed in the contract. The backend must enumerate them. | `api-integration`, FR-010, FR-011 | Archive or restore is chosen from the note's archived state without naming members. | A contract revision enumerating the values. |
| `GAP-WEB-076` | Date-time display format and time zone. | `i18n`, FR-003 | Browser locale (`ASM-WEB-073`). | A design or i18n decision. |

**Contradiction** (not Critical)
- `CON-WEB-054`: the `web-app.module-map` row for `notes` lists "archive and pin of US-006-06", and the row for `search` lists the "pin toggle of US-006-06". The pin criterion concerns "a notes row in the list". If `notes` also owned a pin control, a second pin control would appear on `/notes/:id`. Reading used: `notes` owns archive and restore on the open note, and `search` owns the row pin toggle. A pin change from the list only reaches this module through cache invalidation.

**Direction checks run**
- Story↔design: US-006-02 "navigates to the saved note" has no frame (`GAP-WEB-010`).
- Story↔requirement: no conflict on the owned stories.
- Requirement↔requirement: `requirements.md` BR-012 versus the contract's 409 (`CON-WEB-003`).
- Design↔design: no conflict found.

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-067` | `NoteEditorScreen` at `/notes/:id` is one editable screen with save, archive and restore controls. Empty is the create form and Filled is the loaded note. Variant meanings are unverified. | A design frame or mapping. |
| `ASM-WEB-068` | The client does not pre-validate emptiness (US-006-02 wording). A 400 with no field identified is attributed to the empty field among title and body, else shown form-level. | `GAP-WEB-004` closed. |
| `ASM-WEB-069` | Save sends only changed title or body. The save control is disabled while nothing has changed. | An interaction decision. |
| `ASM-WEB-070` | After save, archive or restore the user stays on `/notes/:id` and sees the new state from the response (`Q-WEB-056` default). | `Q-WEB-056` answered. |
| `ASM-WEB-071` | A 404 on load, save, archive or restore shows the same not-found state. On a save 404 the typed values stay in the form, and the cached note is unchanged. | Design frames. |
| `ASM-WEB-072` | While a request is pending, the control shows Loading and the title and body are not editable. | Design annotation. |
| `ASM-WEB-073` | Only `created_at` is displayed, formatted in the browser locale. | `GAP-WEB-076` closed. |
| `ASM-WEB-074` | `Button/Primary` is reused for create, save, archive and restore. `Input/Text` is used for both title and body. | `GAP-WEB-073` closed. |
| `ASM-WEB-075` | Token bindings are proposals from token names, with values unseen. | Figma verification. |
| `ASM-WEB-076` | The accessibility behaviours are proposals, because no standard is stated. | `GAP-WEB-008` closed. |
| `ASM-WEB-077` | Edits are last-write-wins with no conflict UI (`stories.md` US-001-02). The contract lists no 409 for edit. | A conflict-handling decision. |
| `ASM-WEB-078` | Leaving `/notes/:id` with unsaved edits shows no prompt (`Q-WEB-055` default). | `Q-WEB-055` answered. |
| `ASM-WEB-079` | Archive or restore acts on the saved note only. Unsaved typed edits stay in the form, unsaved. | An interaction decision. |

**Questions** (unattended run: degradable defaults adopted, none blocking)

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-055` | degradable | Should leaving the editor with unsaved changes prompt the user? | No prompt (`ASM-WEB-078`). |
| `Q-WEB-056` | degradable | After archive or restore, does the user stay on the note or return to a list? | Stay on `/notes/:id` (`ASM-WEB-070`). |

**Invariants minted:** `INV-WEB-056`, `INV-WEB-057`, `INV-WEB-058`, defined in `web-app.notes.requirements`.

**Needs from other surfaces, stated in words** (recorded as lane needs by the later step)
- The backend must state the title and body length limits and the whitespace rule (`GAP-WEB-074`).
- The backend must enumerate the `status` values (`GAP-WEB-075`).
- Design must supply the frames in `GAP-WEB-073`.
<!-- /SECTION: web-app.notes.gaps -->
