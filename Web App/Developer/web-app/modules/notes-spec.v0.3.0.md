---
id: agent-developer/web_app/web-app/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/notes-SPEC.md
depends_on: [web_app:US-006-02, web_app:US-006-03]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/notes-SPEC.md
---

<!-- SECTION: web-app.notes.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: notes
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-009]
contract_pin: "sha256:ceedbd350666"
scope: [/notes/new, /notes/:id, /notes/:id/edit]
```

- The routes are proposals (`ASM-WEB-001`), so this section is `DRAFT-UNVERIFIED`.
- `GAP-WEB-001` (session model) and `GAP-WEB-009` (permission model) are defined in the project spec. They block the protected-route rule and the permission-denied state. They do not block the rest of the module.
<!-- /SECTION: web-app.notes.frontmatter -->
<!-- SECTION: web-app.notes.purpose | status: DERIVED -->
## Purpose

**Responsible for** (project spec `web-app.module-map`, module `notes`, design label M1):

- Creating a note in the web editor (`NoteEditorScreen` at `/notes/new`), US-006-02.
- Editing the title or body of an existing note (`NoteEditorScreen` at `/notes/:id/edit`), US-006-03.
- Showing a single note (`NoteDetailScreen` at `/notes/:id`). This is where create navigates to ("navigates to the saved note", US-006-02) and where the server-set `created_at` is shown.
- Archiving and restoring a note from `NoteDetailScreen`, which are the first two criteria of US-006-06. The module map assigns these to this module, but this run's story list does not (`CON-WEB-051`).

**Not responsible for:**

- The notes list, search, sort, result count and pin toggle. These belong to module `search`; the pin toggle is the third criterion of US-006-06.
- `ArchiveScreen` and the archived-only list. These belong to module `archive`.
- Tag add, remove and listing (`GAP-WEB-003`). No tag control is specified here.
- Login, session, token handling and the protected-route rule. These belong to `login` and `web-app.auth-flows`, and are `BLOCKED` by `GAP-WEB-001`.
- Residency and scope screens (`Q-WEB-001`, `ASM-WEB-005`).
- Any backend behaviour.
<!-- /SECTION: web-app.notes.purpose -->
<!-- SECTION: web-app.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Every requirement below depends on the proposed routes (`ASM-WEB-001`). FRs that handle error responses also depend on `GAP-WEB-007`, because no input defines the field-detail shape. Names such as "Team member" come from `stories.md`.

**WEB-NOTES-FR-001. Create submits and navigates.**
- Given a Team member on `/notes/new` has entered a title and a body,
- When they submit,
- Then the web app calls `API-NOTES-232390` with the title and body, and on success navigates to `/notes/:id` for the returned note.
- **Traces to.** US-006-02
- **Verification.** `notes.create.submits_and_navigates_to_saved_note` (planned)
- **API.** `API-NOTES-232390`

**WEB-NOTES-FR-002. Create validation error shown inline.**
- Given the title field is left empty on `/notes/new`,
- When the user submits,
- Then the web app shows the API's validation error inline, creates no note, stays on `/notes/new` and keeps the entered values.
- Where the response identifies the field, the error is shown on that field. Otherwise it is shown at form level (`GAP-WEB-007`).
- **Traces to.** US-006-02; BR-001; NFR-011
- **Verification.** `notes.create.shows_validation_error_inline_without_creating` (planned)
- **API.** `API-NOTES-232390`

**WEB-NOTES-FR-003. Server-set `created_at` is reflected.**
- Given the create request succeeds,
- When the response is received,
- Then the note's server-set `created_at` appears on `NoteDetailScreen`, taken from the create response (the cache write is specified in `web-app.state-management`).
- The date format and timezone are `GAP-WEB-054`.
- **Traces to.** US-006-02; BR-003
- **Verification.** `notes.detail.shows_server_created_at_after_create` (planned)
- **API.** `API-NOTES-232390`

**WEB-NOTES-FR-004. Editor loads the note.**
- Given a Team member opens `/notes/:id/edit`,
- When the screen loads,
- Then the web app fetches the note with `API-NOTES-045877` and fills the title and body fields from the response.
- A 404 on load shows the "note not found" state (`ASM-WEB-054`).
- **Traces to.** US-006-03
- **Verification.** `notes.edit.loads_note_into_form` (planned)
- **API.** `API-NOTES-045877`

**WEB-NOTES-FR-005. Save edit.**
- Given an existing note is open in the editor,
- When the user changes the title or body and saves,
- Then the web app calls `API-NOTES-847551` (the contract provides PATCH only) and shows the updated content from the response.
- On success the note-by-id cache is updated and cached search results are invalidated (`web-app.state-management`).
- Post-save location is `Q-WEB-051`. Default: stay on the editor with the form refilled from the response (`ASM-WEB-052`).
- **Traces to.** US-006-03
- **Verification.** `notes.edit.saves_and_shows_updated_content` (planned)
- **API.** `API-NOTES-847551`

**WEB-NOTES-FR-006. Save returns 404.**
- Given the save request returns 404,
- When the response is received,
- Then the web app shows a "note not found" message and applies no local changes.
- The cached note is untouched (`INV-WEB-051`), and the values the user typed stay visible in the form (`ASM-WEB-055`).
- **Traces to.** US-006-03
- **Verification.** `notes.edit.404_shows_not_found_and_keeps_cache` (planned)
- **API.** `API-NOTES-847551`

**WEB-NOTES-FR-007. Save returns a field-level validation error.**
- Given a save request returns a field-level validation error,
- When the response is received,
- Then the web app highlights the invalid field using the `Input/Text` Error state and shows the error message with it.
- If the response names no field, a form-level error is shown instead (`GAP-WEB-007`).
- **Traces to.** US-006-03; NFR-011
- **Verification.** `notes.edit.highlights_invalid_field` (planned)
- **API.** `API-NOTES-847551`

**WEB-NOTES-FR-008. Detail screen shows the note.**
- Given a Team member opens `/notes/:id` (after create, or from elsewhere),
- When the note loads with `API-NOTES-045877`,
- Then the screen shows the title, body, status (active or archived) and `created_at`.
- Other displayed fields are `GAP-WEB-054`.
- **Traces to.** US-006-02; US-006-06 ("an active note is open")
- **Verification.** `notes.detail.renders_note` (planned)
- **API.** `API-NOTES-045877`

**WEB-NOTES-FR-009. Archive.**
- Given an active note is open on `/notes/:id`,
- When the user clicks archive,
- Then the web app calls `API-NOTES-810640`, shows the note as archived with a restore control, and invalidates cached search results so the note leaves the default active list.
- A 409 is handled under `ASM-WEB-051`.
- The user stays on the screen (`ASM-WEB-061`).
- **Traces to.** US-006-06; BR-009
- **Verification.** `notes.detail.archive_updates_state_and_invalidates_search` (planned)
- **API.** `API-NOTES-810640`

**WEB-NOTES-FR-010. Restore.**
- Given an archived note is viewed on `/notes/:id`,
- When the user clicks restore,
- Then the web app calls `API-NOTES-917869`, shows the note as active with an archive control, and invalidates cached search results so the note reappears in the active list.
- A 409 is handled under `ASM-WEB-051`.
- **Traces to.** US-006-06; BR-009
- **Verification.** `notes.detail.restore_updates_state_and_invalidates_search` (planned)
- **API.** `API-NOTES-917869`

**WEB-NOTES-FR-011. Edit stays reachable on archived notes.**
- Given a note on `/notes/:id` has either status,
- When the screen renders,
- Then an edit control that navigates to `/notes/:id/edit` is present and enabled (`INV-WEB-004`).
- The control's existence is an assumption (`ASM-WEB-062`).
- **Traces to.** US-006-03; BR-010
- **Verification.** `notes.detail.edit_control_enabled_when_archived` (planned)
- **API.** none

**WEB-NOTES-FR-012. Client sends only client-owned fields.**
- Given the editor submits a create or an edit,
- When the request is built,
- Then it contains only the title and body (on edit, only the changed ones).
- It never contains `id`, `created_at`, `updated_at`, `pinned`, tags or file or binary data (`INV-WEB-052`).
- **Traces to.** US-006-02; US-006-03; BR-003; BR-016
- **Verification.** `notes.contract.requests_contain_only_client_owned_fields` (planned)
- **API.** `API-NOTES-232390`, `API-NOTES-847551`

**Invariants.**
- `INV-WEB-051`: A failed save never alters the displayed or cached saved note. Only a successful response replaces it (US-006-03; `web-app.state-management` policy item 4).
- `INV-WEB-052`: The web app never sends server-owned fields (`id`, timestamps) (BR-002, BR-003).
<!-- /SECTION: web-app.notes.requirements -->
<!-- SECTION: web-app.notes.routes | status: DRAFT-UNVERIFIED -->
## Routes

All paths are proposals (`ASM-WEB-001`). The layout names come from `web-app.routing`.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes/new` | `NoteEditorScreen` (create) | app | Authenticated Team member. Enforcement `BLOCKED` (`GAP-WEB-001`). |
| `/notes/:id` | `NoteDetailScreen` | app | Authenticated Team member. Enforcement `BLOCKED` (`GAP-WEB-001`). |
| `/notes/:id/edit` | `NoteEditorScreen` (edit) | app | Authenticated Team member. Enforcement `BLOCKED` (`GAP-WEB-001`). |

- The only role is Team member, with uniform capability (`ASM-WEB-006`). The ownership rule is `GAP-WEB-009`.
- **Unauthenticated redirect.** Per `web-app.routing`, a visitor is redirected to `/login` if the app layout is protected.
- **Navigation.**
  - The create-success target is `/notes/:id` (US-006-02).
  - The edit control on `/notes/:id` goes to `/notes/:id/edit` (`ASM-WEB-062`).
  - Neither screen has any outgoing transition in the design (`screens-inventory.md`, `GAP-WEB-055`).
  - The control that reaches `/notes/new` lives in module `search` or the app layout, which this module does not specify (`GAP-WEB-008`).
<!-- /SECTION: web-app.notes.routes -->
<!-- SECTION: web-app.notes.components | status: DRAFT-UNVERIFIED -->
## Components

Component names, roles and states come from `components-inventory.md`. Token names come from `tokens-index.md`. The bindings are proposals, because token values are unseen and Figma is not connected (`ASM-WEB-056`). No visual value is stated anywhere in this section.

**`Input/Text`** (role: entry; states Default, Focused, Filled, Error, Disabled). It is used for the title field on `NoteEditorScreen` (design screen list). The body field's form is `GAP-WEB-053`.

| State | Proposed token bindings |
|---|---|
| Default | `UI/surface/input`, `UI/border/input`, `UI/text/placeholder` |
| Focused | `UI/surface/input-focus`, `UI/border/input-focus` |
| Filled | `UI/surface/input`, `UI/border/input`, `UI/text/primary` |
| Error | `UI/border/error`, `UI/text/error` (message), `UI/icon/error` |
| Disabled | `UI/surface/disabled`, `UI/border/disabled`, `UI/text/disabled` |

**`Button/Primary`** (role: action; 12 variants; states Default, Pressed, Loading, Disabled). It is proposed for create, save, archive, restore and edit (`ASM-WEB-059`), although the design does not place a button on either screen (`GAP-WEB-052`).

| State | Proposed token bindings |
|---|---|
| Default | `UI/surface/button/primary`, `UI/text/on-primary` |
| Pressed | none identifiable in the token list (`GAP-WEB-052`) |
| Loading | same as Default |
| Disabled | `UI/surface/disabled`, `UI/text/disabled` |

**`Empty/State`** (role: content; 1 variant). The design places it on `NoteEditorScreen`. Its role there is unstated. It is proposed for the "note not found" state (`ASM-WEB-060`), with `UI/text/secondary` and `UI/icon/subtle`.

**Form-level and page-level messages.**
- No message, toast or banner component exists (`GAP-WEB-004`).
- Proposed bindings for the text: `UI/text/error` for failures and `UI/text/success` for confirmation.
- The proposed message surface is `UI/color/error-subtle` or `UI/color/success-subtle`.

**Not designed** (`GAP-WEB-052`, `GAP-WEB-057`): body multi-line entry, page loading indicator, edit control, archive control, restore control and note metadata layout on `NoteDetailScreen`.
<!-- /SECTION: web-app.notes.components -->
<!-- SECTION: web-app.notes.states | status: DRAFT-UNVERIFIED -->
## States

Failure behaviour for network errors, 5xx and unclassified failures follows `ASM-WEB-011` in the project spec: a user-facing error that keeps inputs and allows retry.

**`/notes/new`**

| State | Behaviour |
|---|---|
| Loading | No page load. While the create request is in flight, `Button/Primary` shows Loading and a second submit is prevented (`ASM-WEB-058`). |
| Empty | Blank form, the initial state. |
| Populated | The user has entered values. |
| Error | 400: inline validation error, with values kept (FR-002). 415 or other failure: form-level error with retry. |

**`/notes/:id/edit`**

| State | Behaviour |
|---|---|
| Loading | The note fetch is in flight. No loading component exists (`GAP-WEB-057`). |
| Empty | No empty-data state on load, because a note has a title and body (BR-001). The user may clear a field while editing, which is a populated form. |
| Populated | The form is filled from the response. |
| Error | Load 404: "note not found" (`ASM-WEB-054`). Load failure: error with retry. Save 404: FR-006. Save 400: FR-007. Save 409: a conflict message, values kept, with an option to reload the note and no merge (`ASM-WEB-057`). Other save failure: form-level error with retry. |

**`/notes/:id`**

| State | Behaviour |
|---|---|
| Loading | The note fetch is in flight (`GAP-WEB-057`). Archive or restore in flight: the control shows Loading. |
| Empty | None. A single note has no empty collection. A note with no tags is not shown, because tag display is `GAP-WEB-054`. |
| Populated | FR-008. The design's `NoteDetailScreen` has only Default and Error variants (`ASM-WEB-060`). |
| Error | 404: "note not found". Other load failure: the design's Error variant, with retry. Archive or restore 404: "note not found". Archive or restore 409: re-fetch and show the resulting state (`ASM-WEB-051`). Other failure: error with retry. The cached note is unchanged (`INV-WEB-051`). |

**Permission-denied.** Not specified on any of the three routes. The backend defines no ownership rule and no 401 or 403 for note operations (`GAP-WEB-009`).

**Partial failure.** Not applicable. This module has no multi-item operation.
<!-- /SECTION: web-app.notes.states -->
<!-- SECTION: web-app.notes.api-integration | status: VERIFIED -->
## API integration

Reference only. Request and response shapes are defined in the contract (`backend-api.md`) and are not restated.

| Operation | Pinned ID | Used by |
|---|---|---|
| Create note (POST) | `API-NOTES-232390` | FR-001, FR-002, FR-003, FR-012 |
| Get note | `API-NOTES-045877` | FR-004, FR-008 |
| Edit note (PATCH) | `API-NOTES-847551` | FR-005, FR-006, FR-007, FR-012 |
| Archive note | `API-NOTES-810640` | FR-009 |
| Unarchive note | `API-NOTES-917869` | FR-010 |

- **Pinning.** The contract IDs carry no `@v` suffix. This run is pinned to `sha256:ceedbd350666`.
- **Documented error statuses used.**
  - Create: 400 and 415.
  - Get: 404.
  - Edit: 400, 404, 409 and 415.
  - Archive and unarchive: 404 and 409.
- **Not used.** `API-NOTES-359413` (tag add) has no control in this module (`GAP-WEB-003`).
<!-- /SECTION: web-app.notes.api-integration -->
<!-- SECTION: web-app.notes.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side |
|---|---|---|
| Title required, non-empty | Not pre-validated. The request is sent and the server's validation error is shown (US-006-02, `ASM-WEB-053`). | Yes. BR-001; `API-NOTES-232390` returns 400. |
| Body required, non-empty | Same as title. | Yes. BR-001; 400. |
| Edit fields, if supplied, must not be empty | Not pre-validated. | Yes. `API-NOTES-847551` returns 400. |
| Maximum title and body length | None. No input states a number (`GAP-WEB-056`). | Backend-defined. US-001-01 mentions a maximum body length but gives no value. |
| Field-level error detail | Display only. | Yes. NFR-011, BR-004. The shape is `GAP-WEB-007`. |
| No timestamps, `id`, `pinned`, tags or attachments in requests | Yes (FR-012, `INV-WEB-052`). The editor offers no file control. | Yes. BR-003, BR-016. |
| Unchanged edit | No request is sent (`ASM-WEB-058`). | Not applicable. |

No input trimming or other transformation is applied to title or body before sending.
<!-- /SECTION: web-app.notes.validation -->
<!-- SECTION: web-app.notes.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No input states a target accessibility standard or level (`GAP-WEB-058`). The behaviours below are proposals (`ASM-WEB-063`).

- **Focus management.**
  - On route change, focus moves to the page heading.
  - After create succeeds, focus lands on the detail heading.
  - After a validation failure, focus moves to the first invalid field.
  - After archive or restore, focus stays on the control or moves to its replacement (restore or archive).
- **Keyboard paths.** Every action is reachable and operable by keyboard in visual order: title, body, save, then edit, archive and restore on the detail screen. The Enter key in the body field inserts a new line and does not submit.
- **Form errors.**
  - Each error message is associated with its field through `aria-describedby`.
  - The invalid field is marked `aria-invalid`.
  - Errors are not conveyed by colour alone, so the Error state uses a text message and an icon (`UI/icon/error`).
- **Live regions.**
  - A polite live region announces save, archive and restore success.
  - An alert region announces failures and the "note not found" message.
  - Loading sets `aria-busy` on the affected region.
- **Disabled and Loading states.** A control in Loading remains focusable. A duplicate submit is ignored.
<!-- /SECTION: web-app.notes.accessibility -->
<!-- SECTION: web-app.notes.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input names locales or copy keys. The only message wording supplied for this module is the phrase "note not found" (US-006-03). Validation, conflict, failure, success and button wording is supplied nowhere (`GAP-WEB-006`).

Unblocked by: a stated locale list and copy deck or key convention, including the date format and timezone for `created_at` (`GAP-WEB-054`).
<!-- /SECTION: web-app.notes.i18n -->
<!-- SECTION: web-app.notes.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode` is `FULL-FROM-BRIEF`. Figma is not connected (`RUN-CONTEXT.inputs.figma_mcp`) and `design-brief.md` supplies no frame IDs. Every reference below is an unverified claim.

| Screen | Frame ID | Variants (from `screens-inventory.md`) | Components listed | Story mapping (from `stories-map.md`) | Verification |
|---|---|---|---|---|---|
| `NoteEditorScreen` | not supplied | Default, Empty, Error, Filled | `Empty/State`, `Input/Text` | US-006-02, US-006-03 | unverified |
| `NoteDetailScreen` | not supplied | Default, Error | none | US-001-03 (a backend story); no EP-006 story | unverified |

- **Variant meaning.** No input says what each variant shows. The proposed mapping is in `ASM-WEB-060`.
- **Archive and restore.** `stories-map.md` maps US-006-06 to `ArchiveScreen`, not to `NoteDetailScreen` (`CON-WEB-051`).
- **Tokens.** The names used are listed in `web-app.notes.components`. No token was verified.
<!-- /SECTION: web-app.notes.design-refs -->
<!-- SECTION: web-app.notes.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are planned. The names match the Verification lines in `web-app.notes.requirements`.

**Component**
- `notes.create.shows_validation_error_inline_without_creating`
- `notes.edit.highlights_invalid_field`
- `notes.edit.404_shows_not_found_and_keeps_cache`
- `notes.edit.409_shows_conflict_and_keeps_values`
- `notes.edit.no_request_when_unchanged`
- `notes.create.prevents_double_submit`
- `notes.detail.edit_control_enabled_when_archived`
- `notes.detail.renders_note`

**E2E**
- `notes.create.submits_and_navigates_to_saved_note`
- `notes.detail.shows_server_created_at_after_create`
- `notes.edit.loads_note_into_form`
- `notes.edit.saves_and_shows_updated_content`
- `notes.detail.archive_updates_state_and_invalidates_search`
- `notes.detail.restore_updates_state_and_invalidates_search`

**Contract** (against the pinned `sha256:ceedbd350666`)
- `notes.contract.requests_contain_only_client_owned_fields`
- `notes.contract.create_response_usable_for_cache_write` (`API-NOTES-232390`)
- `notes.contract.edit_error_statuses_handled` (`API-NOTES-847551`)
- `notes.contract.archive_unarchive_409_reconciled` (`API-NOTES-810640`, `API-NOTES-917869`)

**Accessibility**
- `notes.a11y.focus_moves_to_first_invalid_field`
- `notes.a11y.errors_associated_with_fields`
- `notes.a11y.live_region_announces_save_archive_restore`
- `notes.a11y.keyboard_only_create_edit_archive`

The accessibility target standard is unstated (`GAP-WEB-058`), so these tests assert the behaviours in this document, not conformance to a standard.
<!-- /SECTION: web-app.notes.test-plan -->
<!-- SECTION: web-app.notes.gaps | status: VERIFIED -->
## Gaps, contradictions, assumptions

Open items inherited from the project spec and cited here: `GAP-WEB-001`, `GAP-WEB-003`, `GAP-WEB-004`, `GAP-WEB-006`, `GAP-WEB-007`, `GAP-WEB-008`, `GAP-WEB-009`, `ASM-WEB-001`, `ASM-WEB-005`, `ASM-WEB-006`, `ASM-WEB-011`, `INV-WEB-004`.

### Gaps

| ID | Missing | Degrades | Fallback used | Closing artefact |
|---|---|---|---|---|
| `GAP-WEB-052` | No design frame or component for the save, create, edit, archive and restore controls, and no Pressed-state token. `Button/Primary` is not listed on either screen. | `components`, FR-001, FR-005, FR-009, FR-010, FR-011 | `Button/Primary` reused (`ASM-WEB-059`) | Design frames for both screens showing these controls |
| `GAP-WEB-053` | No multi-line entry variant for the body field. | `components` | None specified | Design frame or component for the body field |
| `GAP-WEB-054` | Which fields `NoteDetailScreen` displays beyond title, body, status and `created_at`, and the date format and timezone. | FR-003, FR-008, `i18n` | Only those four fields are specified | Design frame for the detail screen and a locale statement |
| `GAP-WEB-055` | The design has no outgoing transition from `NoteEditorScreen` or `NoteDetailScreen`, and none for the entry to the editor or the post-archive destination. | `routes`, FR-005, FR-009, FR-011 | `ASM-WEB-052`, `ASM-WEB-061`, `ASM-WEB-062` | Design flows for these screens |
| `GAP-WEB-056` | Maximum title and body length. | `validation` | No client limit | Field limits in the contract or requirements |
| `GAP-WEB-057` | No page-level loading, skeleton or progress component. `Button/Primary` Loading covers submits only. | `states` | Unstyled loading state | Design component |
| `GAP-WEB-058` | No accessibility standard or level in any input. The project `web-app.accessibility` section has no usable content. | `accessibility`, `test-plan` | Behaviours proposed (`ASM-WEB-063`) | A stated standard and level, and how it is verified |

### Contradictions

| ID | Claims and citations | Consequence of each reading | Adopted reading |
|---|---|---|---|
| `CON-WEB-051` | The task assignment lists US-006-02 and US-006-03 as this module's stories. The project `web-app.module-map` also gives `notes` the archive and restore criteria of US-006-06. `stories-map.md` maps US-006-06 to `ArchiveScreen`. | If archive and restore belong to `notes`, FR-009 and FR-010 stand and `NoteDetailScreen` needs those controls. If only `archive` owns them, the controls are `ArchiveScreen` only and those FRs are removed. | The module map (the project spec's statement of ownership). FR-009 and FR-010 are kept, with `NoteDetailScreen` as the host. |
| `CON-WEB-052` | BR-012 and US-003-01 say archive and unarchive are idempotent, with a 200 on a repeat. `API-NOTES-810640` and `API-NOTES-917869` list 409. | Showing the 409 as an error contradicts BR-012. Treating it as success hides a real conflict. | `ASM-WEB-051`: re-fetch and show the resulting state, with no error. |

Neither contradiction is Critical.

### Assumptions

| ID | Assumption |
|---|---|
| `ASM-WEB-051` | A 409 on archive or unarchive is reconciled by re-fetching the note and showing its resulting state, with no error (`CON-WEB-052`). |
| `ASM-WEB-052` | A successful edit keeps the user on `/notes/:id/edit`, with the form refilled from the response (`Q-WEB-051` default). |
| `ASM-WEB-053` | The client does not pre-validate emptiness and relies on the server's 400 (US-006-02 wording). |
| `ASM-WEB-054` | A 404 when loading the note is shown with the same "note not found" state as a save 404. |
| `ASM-WEB-055` | On a save 404 the typed values remain in the form, and the cached note is unchanged. |
| `ASM-WEB-056` | Token bindings are proposals derived from names in `tokens-index.md`. Values are unseen. |
| `ASM-WEB-057` | A 409 on edit shows a conflict message, keeps the typed values, offers a reload and does no merge. |
| `ASM-WEB-058` | Edit sends only changed title or body, and sends nothing when unchanged. A second submit is prevented while a request is in flight. |
| `ASM-WEB-059` | `Button/Primary` is reused for the create, save, archive, restore and edit controls. |
| `ASM-WEB-060` | Editor variants: Empty is a blank create form, Filled is a loaded edit form, Error is a failure, Default is the initial state. `Empty/State` is used for "note not found". Detail Default is the populated note and Error is a load failure. Unverified. |
| `ASM-WEB-061` | After archive or restore the user stays on `/notes/:id` and sees the new state from the response. |
| `ASM-WEB-062` | `NoteDetailScreen` has an edit control that links to `/notes/:id/edit`. |
| `ASM-WEB-063` | The accessibility behaviours in this module are proposals, because no standard is stated. |

### Questions

Both questions are degradable. This run is unattended, so each took its default.

| ID | Question | Class | Default |
|---|---|---|---|
| `Q-WEB-051` | After a successful edit, does the user stay on the editor or go to the note detail? | degradable | Stay on the editor (`ASM-WEB-052`) |
| `Q-WEB-052` | Should leaving the editor with unsaved changes prompt the user? | degradable | No prompt |
<!-- /SECTION: web-app.notes.gaps -->
