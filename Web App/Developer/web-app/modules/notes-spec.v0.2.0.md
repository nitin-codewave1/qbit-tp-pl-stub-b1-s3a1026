---
id: agent-developer/web_app/web-app/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/notes-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-003-04, web_app:US-003-05, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03]
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:web-app/modules/notes-SPEC.md
---

<!-- SECTION: web-app.notes.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: notes
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-002]
contract_pin: "sha256:a6f2ca3343de"
scope: [/notes, /notes/new, /notes/:id, /notes/:id/edit]
```

- **`spec_mode`.** The design brief is present and Figma is not connected. Every screen, component and token name here is an unverified claim. [RUN-CONTEXT `inputs.figma_mcp`; design-brief.md; core 4.5]
- **`blocked_by`.** The notes list has no contract operation (`GAP-WEB-001`) and no design (`GAP-WEB-002`). Everything list-bound is `BLOCKED`. [project spec `web-app.overview`, `web-app.state-management`]
- **`scope`.** These are the routes the project module map gives to `notes`. [project spec `web-app.module-map`, `web-app.routing`]
<!-- /SECTION: web-app.notes.frontmatter -->
<!-- SECTION: web-app.notes.purpose | status: DERIVED -->
## Purpose

**Responsible for.**
- Browsing the active notes list with sort, a live result count and a pin toggle. This part is `BLOCKED`.
- Creating a note.
- Viewing a single note.
- Editing a note, including an archived note.
- Showing server-set timestamps and the server's field-level validation errors.

[project spec `web-app.module-map`; stories.md US-001-01..06, US-003-04, US-003-05, US-006-01..03]

**Not responsible for.**
- **Sign-in, the `401` redirect and credential handling.** Owned by `auth`. [project spec `web-app.auth-flows`]
- **Keyword and tag search, its sort, count and pagination.** Owned by `search`.
- **Archive, restore and the archived-notes view.** Owned by `archive`.
- **Tag add, remove and discovery.** No web story or screen covers them. [`GAP-WEB-003`]
- **Note deletion, attachments, real-time updates and mobile behaviour.** [project spec `web-app.overview` out-of-scope list]
- **Residency, compliance and scope-documentation screens.** [`GAP-WEB-021`]

**CR-130.** The change request adds the login and code-entry screens only. It adds nothing to this module. [feedback.md CR-130; project spec `web-app.overview`]

**Run note.** No blocking questions were raised. The run is unattended and every open point is a `GAP-`, `CON-` or `ASM-`.
<!-- /SECTION: web-app.notes.purpose -->
<!-- SECTION: web-app.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Each requirement is Given/When/Then. Schemas are referenced by pinned ID and never restated (core rule 6).

### Create

**WEB-NOTES-FR-001 — Create and open the saved note.**
- **Given** a title and body entered on `/notes/new`, **when** the user submits, **then** the app calls `API-NOTES-232390` and navigates to `/notes/:id` for the created note.
- **Traces to.** US-006-02, US-001-01.
- **Verification.** (planned) `notes.create.submits-and-navigates`.
- **API.** `API-NOTES-232390`.

**WEB-NOTES-FR-002 — Show the server's validation error on create.**
- **Given** the title is empty, **when** the user submits, **then** the request is sent with no client pre-check. The server's field-level error is rendered inline on each field it names, and no note is created.
- **Traces to.** US-006-02, US-001-01, US-001-05.
- **Verification.** (planned) `notes.create.shows-server-field-errors`.
- **API.** `API-NOTES-232390`.

**WEB-NOTES-FR-003 — Server-set timestamps.**
- **Given** a create or edit succeeds, **when** the response arrives, **then** the UI shows `created_at` and `updated_at` as returned.
- The client never sends or computes either value, including optimistically (`INV-WEB-002`).
- **Traces to.** US-006-02, US-001-06.
- **Verification.** (planned) `notes.timestamps.server-only`.
- **API.** `API-NOTES-232390`, `API-NOTES-847551`.

**WEB-NOTES-FR-004 — No truncation of long input.**
- **Given** a body at the maximum allowed length, **when** the user submits, **then** the client sends and shows the full text without truncating it.
- The maximum is not stated (`GAP-WEB-656`), so the client imposes no limit of its own.
- **Traces to.** US-001-01.
- **Verification.** (planned) `notes.create.no-client-truncation`.
- **API.** `API-NOTES-232390`.

### View

**WEB-NOTES-FR-005 — View a note.**
- **Given** a note exists, **when** `/notes/:id` loads, **then** the app calls `API-NOTES-045877` and shows the note's content and status (`ASM-WEB-455`).
- **Traces to.** US-001-03.
- **Verification.** (planned) `notes.view.shows-note`.
- **API.** `API-NOTES-045877`.

**WEB-NOTES-FR-006 — Note not found on view.**
- **Given** the note id does not exist, **when** `/notes/:id` loads, **then** the app shows a not-found state, not a blank screen.
- **Traces to.** US-001-03, US-006-01 (error rule).
- **Verification.** (planned) `notes.view.not-found-state`.
- **API.** `API-NOTES-045877`.

### Edit

**WEB-NOTES-FR-007 — Open a note for editing.**
- **Given** an existing note, **when** `/notes/:id/edit` loads, **then** the editor is populated from `API-NOTES-045877` (the Filled variant, per project `ASM-WEB-003`).
- **Traces to.** US-006-03.
- **Verification.** (planned) `notes.edit.loads-note`.
- **API.** `API-NOTES-045877`.

**WEB-NOTES-FR-008 — Save an edit.**
- **Given** the user changed the title or body, **when** they save, **then** the app calls `API-NOTES-847551` and shows the updated content.
- The contract has PATCH only, so the stories' "PUT/PATCH" resolves to PATCH.
- Only changed fields are sent (`ASM-WEB-451`). After success the app navigates to `/notes/:id` (`ASM-WEB-454`).
- **Traces to.** US-006-03, US-001-02.
- **Verification.** (planned) `notes.edit.saves-and-shows-update`.
- **API.** `API-NOTES-847551`.

**WEB-NOTES-FR-009 — Note not found on save.**
- **Given** the save returns 404, **when** the response is received, **then** the app shows a "note not found" message and applies no local change (`INV-WEB-062`).
- **Traces to.** US-006-03, US-001-02.
- **Verification.** (planned) `notes.edit.404-no-local-change`.
- **API.** `API-NOTES-847551`.

**WEB-NOTES-FR-010 — Field-level validation on edit.**
- **Given** the save returns a field-level validation error, **when** the response is received, **then** the app highlights each invalid field named and shows its message.
- A field type error cannot arise from the text form, so the case is handled generically.
- **Traces to.** US-006-03, US-001-05.
- **Verification.** (planned) `notes.edit.highlights-invalid-field`.
- **API.** `API-NOTES-847551`.

**WEB-NOTES-FR-011 — Archived notes stay editable.**
- **Given** an archived note, **when** the user edits and saves, **then** the update is applied and the note remains archived.
- Edit controls are never disabled because of archive state (`INV-WEB-003`). The edit request carries no status.
- The tag half of US-003-05 is not covered (`GAP-WEB-003`).
- **Traces to.** US-003-05.
- **Verification.** (planned) `notes.edit.archived-note-editable`.
- **API.** `API-NOTES-847551`.

**WEB-NOTES-FR-012 — No client-side conflict handling.**
- **Given** two edits to one note in quick succession, **when** the responses arrive, **then** the app shows what each response returns.
- It does no merge and no conflict detection. The edit request lists no version field.
- The backend outcome is open. [backend-api.md `GAP-BE-309`]
- **Traces to.** US-001-02.
- **Verification.** (planned) `notes.edit.shows-server-outcome`.
- **API.** `API-NOTES-847551`.

### List — `BLOCKED` on `GAP-WEB-001` and `GAP-WEB-002`

The contract has no operation that lists active notes. FR-013 to FR-018 are written so they can be built once one exists. [project spec `web-app.state-management`]

**WEB-NOTES-FR-013 — Render the active list. `BLOCKED`.**
- **Given** notes exist, **when** `/notes` loads, **then** each note's title and status are rendered. Pinned notes come first, with a pin icon.
- **Traces to.** US-006-01, US-001-04.
- **Verification.** (planned) `notes.list.renders-pinned-first`.
- **API.** none (`GAP-WEB-001`).

**WEB-NOTES-FR-014 — Active notes only by default. `BLOCKED`.**
- The default list shows only active notes. Archived notes are reached through `archive`. [requirements.md BR-011; stories.md US-003-04]
- US-001-04 and US-006-01 say "all notes" (`CON-WEB-070`).
- **Traces to.** US-003-04, US-001-04.
- **Verification.** (planned) `notes.list.excludes-archived`.
- **API.** none (`GAP-WEB-001`).

**WEB-NOTES-FR-015 — Empty and error list. `BLOCKED`.**
- **Given** no notes exist, **then** an empty-state message and a count of 0 are shown, not an error.
- **Given** the list request fails, **then** a user-facing error state is shown, not a blank screen.
- The same applies when every note is archived and the active list is empty.
- **Traces to.** US-006-01, US-001-04, US-003-04.
- **Verification.** (planned) `notes.list.empty-and-error-states`.
- **API.** none (`GAP-WEB-001`).

**WEB-NOTES-FR-016 — Sort. `BLOCKED`.**
- A sort control above the list offers Newest first, Oldest first and Title A-Z, with Newest first selected by default.
- Sorting is client-side and is never sent to the API (`INV-WEB-005`). Pinned notes precede unpinned ones in every order (`INV-WEB-006`).
- The "Newest" basis, collation and tie-breaks are open (`GAP-WEB-008`).
- **Traces to.** US-006-01.
- **Verification.** (planned) `notes.list.sort-client-side-pinned-first`.
- **API.** none.

**WEB-NOTES-FR-017 — Live result count. `BLOCKED`.**
- The count above the list equals the number of notes rendered. It shows 0 when the list is empty.
- It is hidden while loading and recomputed on every change (`INV-WEB-004`).
- **Traces to.** US-006-01.
- **Verification.** (planned) `notes.list.count-equals-rendered`.
- **API.** none.

**WEB-NOTES-FR-018 — Pin toggle on rows. `BLOCKED`.**
- **Given** a row, **when** the user clicks its pin toggle, **then** the app calls `API-NOTES-847551` with only the pinned flag (`INV-WEB-007`). On success the row moves to the top with a pin icon, or returns to its sort position on unpin.
- The client does not reorder rows optimistically.
- The pin toggle has no design (`GAP-WEB-002`). Pin rules for archived notes are open (`GAP-WEB-009`).
- Ownership of this story is `GAP-WEB-655`.
- **Traces to.** US-006-06 (pin clause).
- **Verification.** (planned) `notes.list.pin-toggle-moves-row`.
- **API.** `API-NOTES-847551`.

### Invariants used here

| ID | Statement | Source |
|---|---|---|
| `INV-WEB-002`, `-003`, `-004`, `-005`, `-006`, `-007` | as defined in the project spec | project spec `web-app.state-management` |
| `INV-WEB-062` | A failed mutation (404, validation failure, `401`, network failure) applies no local change. The displayed note and cached data stay as the last server response left them. | DERIVED: US-006-03 (404 case); project spec `web-app.auth-flows` (`401` case) |
<!-- /SECTION: web-app.notes.requirements -->
<!-- SECTION: web-app.notes.routes | status: DRAFT-UNVERIFIED -->
## Routes

Route paths are proposed (`ASM-WEB-001`). Screen names come from the design brief.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes` | Notes list. No design screen. `BLOCKED` (`GAP-WEB-001`, `GAP-WEB-002`) | Authenticated shell | Authenticated Team member |
| `/notes/new` | `NoteEditorScreen` (M1), Empty variant | Authenticated shell | Authenticated Team member |
| `/notes/:id` | Note view. No design screen (`GAP-WEB-002`) | Authenticated shell | Authenticated Team member |
| `/notes/:id/edit` | `NoteEditorScreen` (M1), Filled variant | Authenticated shell | Authenticated Team member |

- **Permission.** There is one role, and no control is hidden by role. A `403` renders the permission-denied state. [project spec `web-app.authorization-ui`]
- **Unauthenticated redirect.** It goes to `/login` with a return target. Behaviour is owned by `auth`. [project spec `web-app.routing`]
- **Matching order.** `/notes/new` must match before `/notes/:id` (`INV-WEB-001`).
- **Flow edges.** `NoteEditorScreen` has two `ON_CLICK` transitions to `SearchScreen`. The trigger elements are unnamed (`GAP-WEB-023`), so they are not implemented here. [design-brief.md screens-inventory]
<!-- /SECTION: web-app.notes.routes -->
<!-- SECTION: web-app.notes.components | status: DRAFT-UNVERIFIED -->
## Components

The design brief omits component bodies (props and bindings), and tokens are listed by ID only. No token binding can be stated, so every binding is `BLOCKED` (`GAP-WEB-017`). [design-brief.md components-inventory, tokens-index]

| Component | Variants and states (brief) | Use in this module | Token bindings |
|---|---|---|---|
| `Input/Text` | 5 variants. States Default, Focused, Filled, Error, Disabled | Title and body fields on `NoteEditorScreen`. Error state for server field errors | `BLOCKED` |
| `Empty/State` | 1 variant. State Default | Empty list on `/notes` (`BLOCKED`) | `BLOCKED` |
| `Button/Primary` | 12 variants. States Default, Pressed, Loading, Disabled | Save and create control (`ASM-WEB-452`) | `BLOCKED` |

**Used by `NoteEditorScreen` in the brief.** The brief lists only `Empty/State` and `Input/Text`, and it does not say whether the body is a multi-line control.

**Undesigned elements (`GAP-WEB-002`):**
- authenticated shell and navigation;
- notes list and rows;
- sort control;
- live result count;
- pin toggle and pin icon;
- note view;
- permission-denied state.

No component is invented for them.
<!-- /SECTION: web-app.notes.components -->
<!-- SECTION: web-app.notes.states | status: DRAFT-UNVERIFIED -->
## States

Screen-level loading is undesigned and copy is not supplied (`GAP-WEB-010`).

| Route | Loading | Empty | Populated | Error |
|---|---|---|---|---|
| `/notes` | Count hidden (`INV-WEB-004`). `BLOCKED` | `Empty/State`, count 0. `BLOCKED` | Rows, count, sort control. `BLOCKED` | User-facing error, no blank screen. `BLOCKED` |
| `/notes/new` | In-flight submit shows `Button/Primary` Loading | Pristine form (Empty variant) | Fields filled, before submit | Inline field errors (Error variant), or a generic try-again error for 5xx and network failure |
| `/notes/:id` | Loading while `API-NOTES-045877` is pending | Not applicable: a note always has content | Note shown | `404` gives a not-found state. Other failures give a user-facing error |
| `/notes/:id/edit` | Loading while the note is fetched | Not applicable | Filled form | `404` gives "note not found" with no local change. Validation gives field highlights. Other failures give a user-facing error |

- **Permission-denied.** Every route above is permission-gated by the authenticated session. A `403` renders a permission-denied state distinct from the not-found state.
- Whether the server returns `403` or `404` for a note the user may not access is open (`GAP-WEB-014`). The web shows what the server returns and discloses nothing more.
- **Partial failure.** Omitted on purpose. This module has no multi-item operation: the pin toggle acts on one note and there is no bulk action. [core W8]
- **Session loss.** A `401` ends the session and redirects, as owned by `auth`. [project spec `web-app.auth-flows`]
<!-- /SECTION: web-app.notes.states -->
<!-- SECTION: web-app.notes.api-integration | status: DERIVED -->
## API integration

Reference only. Contract pin `sha256:a6f2ca3343de`. IDs are cited as the contract supplies them, with no `@version` suffix (`GAP-WEB-015`). [backend-api.md `backend.api-surface`]

| Operation | Pinned ID | Used by |
|---|---|---|
| Create note | `API-NOTES-232390` | FR-001..004 |
| Get note by id | `API-NOTES-045877` | FR-005..007 |
| Edit note (partial, including pin) | `API-NOTES-847551` | FR-003, FR-008..012, FR-018 |
| List active notes | none (`GAP-WEB-001`) | FR-013..017 |
| Archive and unarchive | `API-NOTES-810640`, `API-NOTES-917869` | owned by `archive` |
| Tag add and remove | `API-NOTES-359413`, `API-NOTES-150096` | no control (`GAP-WEB-003`) |

**Error handling.** Behaviour is chosen by the error `code`, using the backend's proposed names (`ASM-WEB-008`). [backend-api.md `backend.error-model`]
<!-- /SECTION: web-app.notes.api-integration -->
<!-- SECTION: web-app.notes.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Server | Source |
|---|---|---|---|
| Title and body are non-empty at creation | No pre-check. The request is sent and the server error is shown inline | Enforced | stories.md US-006-02; requirements.md BR-001 |
| Field-level detail on validation failures | Render it on the named fields | Provided on 100% of 4xx validation responses | requirements.md NFR-011; backend-api.md `backend.error-model` |
| `created_at` and `updated_at` are server-set | Never sent | Client values ignored | stories.md US-001-06; requirements.md BR-003 |
| Maximum title and body length | None, so no truncation | Unstated | `GAP-WEB-656`; backend-api.md `GAP-BE-035` |
| Whitespace-only title or body | No rule | Unstated | backend-api.md `GAP-BE-035` |
| Attachments or binary data | No file control exists | Rejected | requirements.md BR-016 |
| Invalid data type for a field | Not reachable from the text form | Rejected with 400 | stories.md US-001-05 |

The editor shows the server's messages and does not duplicate server validation.
<!-- /SECTION: web-app.notes.validation -->
<!-- SECTION: web-app.notes.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No target standard or verification method is supplied (`GAP-WEB-019`). The behaviours below are proposals, not conformance claims (`ASM-WEB-453`). The one behaviour a story names is highlighting the invalid field. [stories.md US-006-03]

- **Focus management.**
  - After a validation failure, focus moves to the first invalid field.
  - After navigation to `/notes/:id`, focus moves to the page heading.
  - After a pin change, focus stays on the toggle.
- **Keyboard paths.**
  - Every control is reachable and operable by keyboard.
  - The editor can be submitted from the keyboard.
  - Sort options and the pin toggle are standard controls.
- **Live regions.**
  - Save success, "note not found", the list error state and the result count changes are announced politely.
  - The count announcement respects `INV-WEB-004`.
- **Form errors.**
  - Each error message is programmatically associated with its field.
  - An invalid field is conveyed by more than colour.
  - Error states use the `Input/Text` Error state.
- **Pin toggle.** It exposes its on or off state, not only an icon.
<!-- /SECTION: web-app.notes.accessibility -->
<!-- SECTION: web-app.notes.i18n | status: BLOCKED -->
## Internationalisation

**Blocked.** No input supplies copy keys, copy text or a locale list. Empty-state and error copy is not supplied. [`GAP-WEB-010`] The display format of `created_at` and `updated_at` is also unstated (`GAP-WEB-657`).

**Unblocked by:**
- a copy deck for this module's messages;
- the supported locales;
- a timestamp display rule.
<!-- /SECTION: web-app.notes.i18n -->
<!-- SECTION: web-app.notes.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode: FULL-FROM-BRIEF`. Figma MCP is not connected. The brief names screens and components but supplies **no frame IDs**, so no frame ID is recorded. Every name is an unverified claim. [design-brief.md; core 4.5]

| Design element | Brief reference | Status |
|---|---|---|
| `NoteEditorScreen` (M1) | Variants Default, Empty, Error, Filled. Components `Empty/State`, `Input/Text` | Unverified. Frame ID not supplied. Mapping of Empty to create and Filled to edit is project `ASM-WEB-003` |
| Stories mapped to it | US-001-01, 02, 03, 05, 06; US-003-05; US-006-02, US-006-03 (stories-map) | Unverified. US-001-04, US-003-04 and US-006-01 map to no screen |
| Notes list, note view, shell, sort control, count, pin | none | No design (`GAP-WEB-002`) |
| Tokens | Token IDs in the tokens-index | Unverified. Values and bindings not supplied (`GAP-WEB-017`) |
<!-- /SECTION: web-app.notes.design-refs -->
<!-- SECTION: web-app.notes.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are proposed `(planned)`. No input names a test framework (`GAP-WEB-658`), so the plan is tool-agnostic.

**Component**
- `notes.create.shows-server-field-errors`
- `notes.create.no-client-truncation`
- `notes.edit.highlights-invalid-field`
- `notes.edit.archived-note-editable`
- `notes.view.not-found-state`
- (blocked) `notes.list.sort-client-side-pinned-first`
- (blocked) `notes.list.count-equals-rendered`
- (blocked) `notes.list.empty-and-error-states`

**End-to-end**
- `notes.create.submits-and-navigates`
- `notes.edit.saves-and-shows-update`
- `notes.edit.404-no-local-change`
- `notes.timestamps.server-only` (asserts no `created_at` or `updated_at` in any request)
- (blocked) `notes.list.pin-toggle-moves-row`
- (blocked) `notes.list.excludes-archived`

**Contract**
- Assert the app uses `API-NOTES-232390`, `API-NOTES-045877` and `API-NOTES-847551` against pin `sha256:a6f2ca3343de`.
- Assert that a pin change sends only the pinned flag (`INV-WEB-007`).
- Assert that no list operation exists, so the blocked tests stay blocked until the contract adds one (`GAP-WEB-001`).

**Accessibility**
- Check the proposals in `web-app.notes.accessibility`.
- The target standard is `BLOCKED` on `GAP-WEB-019`.
<!-- /SECTION: web-app.notes.test-plan -->
<!-- SECTION: web-app.notes.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Defined in the project spec and not restated** (`web-app.overview`):
- `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-003`, `GAP-WEB-008`, `GAP-WEB-009`, `GAP-WEB-010`, `GAP-WEB-014`, `GAP-WEB-015`, `GAP-WEB-017`, `GAP-WEB-019`, `GAP-WEB-021`, `GAP-WEB-023`
- `ASM-WEB-001`, `ASM-WEB-003`, `ASM-WEB-008`

**Minted in this module:**

| ID | Kind | Statement and closing artefact |
|---|---|---|
| `CON-WEB-070` | non-critical | US-001-04 and US-006-01 say the list shows "all notes". BR-011 and US-003-04 say the default list shows active notes only. Reading used: BR-011 and US-003-04. The archived view is owned by `archive`. Closing artefact: confirmation from the story owner. |
| `GAP-WEB-655` | gap | The module map assigns the pin toggle to `notes`, and the owned-story list omits US-006-06. FR-018 is written under the module map. Closing artefact: confirmation of which module owns the pin clause. |
| `GAP-WEB-656` | gap | The maximum length of title and body is not stated, although US-001-01 refers to a "maximum allowed length". Closing artefact: a backend-stated limit. |
| `GAP-WEB-657` | gap | The display format, locale and timezone of `created_at` and `updated_at` are not stated. Closing artefact: a design or copy rule. |
| `GAP-WEB-658` | gap | No test framework or tooling is named. Closing artefact: a stated test stack. |
| `GAP-WEB-659` | gap | Leaving the editor with unsaved changes, and any cancel or discard control, is not specified. Closing artefact: a design or behaviour rule. |
| `INV-WEB-062` | invariant | A failed mutation applies no local change. See `web-app.notes.requirements`. |
| `ASM-WEB-451` | assumption | An edit sends only the fields the user changed, which backend `INV-BE-008` permits. Confirm with the backend owner. |
| `ASM-WEB-452` | assumption | The save and create control uses `Button/Primary`, the only action component in the inventory. The brief does not list it on `NoteEditorScreen`. Confirm by inspecting the frames. |
| `ASM-WEB-453` | assumption | The accessibility behaviours are proposals pending a target standard (`GAP-WEB-019`). |
| `ASM-WEB-454` | assumption | After a successful edit the app navigates to `/notes/:id` and shows the updated content. US-006-03 says only "shows the updated content". Confirm with the design. |
| `ASM-WEB-455` | assumption | The note view shows the title, body, status, tags, a pin indicator and the two timestamps. The contract returns these. Confirm with a note-view design. |

**Contradiction checks performed for this module:**
- **Story to design.** None found beyond the three stories with no screen (`GAP-WEB-002`).
- **Story to requirement.** `CON-WEB-070` found.
- **Requirement to requirement.** None found.
- **Design to design.** None found.

**Questions.** None raised. This run is unattended.
<!-- /SECTION: web-app.notes.gaps -->
