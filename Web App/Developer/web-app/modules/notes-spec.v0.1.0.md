---
id: agent-developer/web_app/web-app/modules/notes-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/notes-SPEC.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-04, web_app:US-001-05, web_app:US-001-06, web_app:US-003-04, web_app:US-003-05, web_app:US-003-06, web_app:US-006-01, web_app:US-006-02, web_app:US-006-03]
commit_kind: append
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: web_app:web-app/modules/notes-SPEC.md
---

<!-- SECTION: web-app.notes.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: notes
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-002]
contract_pin: "sha256:bd87f6e16dca"
scope: [/notes, /notes/new, /notes/:id, /notes/:id/edit]
```

Sources:
- `spec_mode` follows core 4.5: the design brief is present and Figma is not connected. [RUN-CONTEXT `inputs.figma_mcp`]
- `contract_pin` is taken from `RUN-CONTEXT.upstream.contract_pin`.
- `scope` is the route list owned by `notes`. [web-app.module-map]
<!-- /SECTION: web-app.notes.frontmatter -->
<!-- SECTION: web-app.notes.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The `notes` module owns the browse, create, view and edit experience for notes. [web-app.module-map]
- **Browse (list).** The notes list shows title and status, with pinned notes first and carrying a pin icon. It also has the sort control, the live result count and the per-row pin toggle. [stories.md US-006-01; requirements.md BR-019, BR-020, BR-021]
- **Create.** The editor submits a new note and navigates to the saved note. [stories.md US-006-02]
- **View.** The detail page shows one note. [stories.md US-001-03]
- **Edit.** The editor changes a note's title or body. Edit stays available whether or not the note is archived. [stories.md US-006-03, US-003-05; requirements.md BR-010]
- **Server-owned timestamps.** The UI shows them and never supplies them. [stories.md US-001-06; requirements.md BR-003]

**Not responsible for.** Each of these is referenced, never re-specified here.
- **Sign-in, session expiry, the global 401 rule and credential handling.** The `auth` module and `web-app.auth-flows` own them.
- **Archive and restore actions, and the archived view.** The `archive` module owns them. [web-app.module-map]
  - Its `CON-WEB-001` handling is not repeated here.
  - The notes module only guarantees that edit is never disabled because a note is archived (`INV-WEB-003`).
- **Search, including the search-side sort and count.** The `search` module owns it.
- **Tag add and remove.** No web story or screen exists for them. [`GAP-WEB-004`]
- **Note deletion, attachments and real-time updates.** They are out of scope. [web-app.overview]
- **Backend behaviour.** The backend owns validation rules, concurrency handling, retention and authorisation.

**Stories owned:** US-001-01, US-001-02, US-001-03, US-001-04, US-001-05, US-001-06, US-003-04, US-003-05, US-003-06, US-006-01, US-006-02, US-006-03.

**Blocked core.** The contract has no operation that lists notes. [backend-api.md `backend.api-surface`; `GAP-WEB-001`] Every list-bound behaviour in this module is `BLOCKED` until backend supplies a list operation returning active notes with pinned first.
<!-- /SECTION: web-app.notes.purpose -->
<!-- SECTION: web-app.notes.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Each requirement states Traces to, Verification and API. `(planned)` marks a proposed test name. Contract IDs are cited as supplied, without a version suffix. [`GAP-WEB-022`]

### Create

**WEB-NOTES-FR-001. Submit a new note.**
- Given a title and body are entered in the editor at `/notes/new`, when the user submits, then the app calls the create operation with the entered title and body. On success it navigates to `/notes/:id` for the returned note.
- Traces to: US-006-02, US-001-01; BR-001.
- Verification: `notes.create.submit_navigates_to_saved_note` (planned).
- API: `API-NOTES-232390`.
- Notes:
  - The editor sends no pinned flag and no tags. No control for either exists on the editor. [`GAP-WEB-004`, `GAP-WEB-002`]
  - The client sets no maximum length and never truncates. A body at the server's maximum length is stored in full by the server. [stories.md US-001-01; `GAP-WEB-056`]
  - The client sends exactly what the user entered. It adds no client-side emptiness check of its own (`ASM-WEB-051`).
  - The submit control is disabled while the request is in flight, so one user submit produces one create request (`ASM-WEB-052`).

**WEB-NOTES-FR-002. Create validation errors shown inline.**
- Given the create request is refused for invalid input, for example an empty title, when the response is received, then the app shows the API's validation error inline, next to the affected field. No note is created and the user stays on `/notes/new` with their entries intact.
- Traces to: US-006-02, US-001-05; BR-004, NFR-011.
- Verification: `notes.create.empty_title_shows_inline_server_error` (planned).
- API: `API-NOTES-232390`.
- Notes:
  - Field-level detail is expected from the server on every 4xx validation response. [requirements.md NFR-011]
  - How a detail entry names its field is unconfirmed. [`GAP-WEB-055`] The field mapping is a proposal that depends on the backend error model (`ASM-WEB-008`).
  - When the server refuses without field detail, the app shows a general error on the form.

**WEB-NOTES-FR-003. Create shows server-set timestamps.**
- Given the create request succeeds, when the saved note is shown, then its created and updated times are those returned by the server.
- Traces to: US-006-02, US-001-06; BR-003.
- Verification: `notes.create.created_at_from_server_only` (planned).
- API: `API-NOTES-232390`, `API-NOTES-045877`.
- Notes: the client never sends or computes `created_at` or `updated_at`. [`INV-WEB-002`]

### View

**WEB-NOTES-FR-004. Show a single note.**
- Given a note exists, when the user opens `/notes/:id`, then the app fetches the note and shows its content, status, pinned state and server timestamps. This is `NoteDetailScreen`, Default variant.
- Traces to: US-001-03.
- Verification: `notes.detail.renders_full_note` (planned).
- API: `API-NOTES-045877`.
- Notes:
  - Archived notes are also shown. [backend-api.md `API-NOTES-045877`]
  - The detail page offers an Edit entry that navigates to `/notes/:id/edit` (`ASM-WEB-056`).
  - Archive and restore actions on this page belong to the `archive` module.

**WEB-NOTES-FR-005. Detail not-found and failure state.**
- Given the note does not exist, when `/notes/:id` is opened, then the app shows a not-found state, `NoteDetailScreen` Error variant. When the request fails for any other reason, it shows a user-facing error state, not a blank screen.
- Traces to: US-001-03, US-006-01 (error principle), US-003-06.
- Verification: `notes.detail.not_found_shows_error_variant` (planned).
- API: `API-NOTES-045877`.
- Notes: the note was never deleted by this surface, because no delete operation exists. [backend-api.md `backend.api-surface`] "Does not exist" is the only reading of the second US-001-03 clause that this surface can produce.

**WEB-NOTES-FR-006. Malformed or unknown note id.**
- Given the route id is malformed, for example not in the server's id format, when the detail or edit route loads, then the app shows the same user-facing error state as FR-005. It never shows a blank screen or a raw server error.
- Traces to: US-003-06 (web-side clause), US-001-03.
- Verification: `notes.routes.malformed_id_shows_error_state` (planned).
- API: `API-NOTES-045877`, `API-NOTES-847551`.
- Notes:
  - The client does no id-format validation. The id format is not stated in the contract. [`GAP-WEB-054`]
  - The server-side `400` on a malformed archive or unarchive id belongs to the `archive` module and to backend.

### Edit

**WEB-NOTES-FR-007. Edit and save a note.**
- Given an existing note is opened at `/notes/:id/edit`, when the user changes the title or body and saves, then the app sends a partial edit carrying only the changed fields, and shows the updated content from the response.
- Traces to: US-006-03, US-001-02; BR-006 (via `INV-BE-008`).
- Verification: `notes.edit.patch_carries_only_changed_fields` (planned).
- API: `API-NOTES-045877` (load), `API-NOTES-847551` (save).
- Notes:
  - An edit request never carries a field the user did not change (`INV-WEB-053`).
  - With no changes, Save sends no request (`ASM-WEB-053`).
  - Save is disabled while the request is in flight (`ASM-WEB-052`).
  - On a concurrent edit, the last accepted write wins and no fields are partially corrupted. This is backend behaviour. [stories.md US-001-02] The app shows whatever the server returns and offers no merge or conflict UI. [`GAP-WEB-057`]

**WEB-NOTES-FR-008. Edit not-found.**
- Given the save request reports that the note does not exist, when the response is received, then the app shows a "note not found" message. It does not apply the local changes to the displayed note, and no data is altered.
- Traces to: US-006-03, US-001-02.
- Verification: `notes.edit.save_404_shows_not_found_no_local_apply` (planned).
- API: `API-NOTES-847551`.
- Notes: `INV-WEB-051` applies.

**WEB-NOTES-FR-009. Edit field-level validation.**
- Given the save request returns a field-level validation error, when the response is received, then the app highlights each invalid field. The user's unsaved entries remain in the form.
- Traces to: US-006-03, US-001-05; BR-004, NFR-011.
- Verification: `notes.edit.field_error_highlights_field` (planned).
- API: `API-NOTES-847551`.
- Notes: see the `GAP-WEB-055` note on FR-002.

**WEB-NOTES-FR-010. Edit reflects server updated time.**
- Given a save succeeds, when the response is received, then the shown last-modified time is the server's value.
- Traces to: US-001-06; BR-003.
- Verification: `notes.edit.updated_at_from_server_only` (planned).
- API: `API-NOTES-847551`.
- Notes: `INV-WEB-002` applies.

**WEB-NOTES-FR-011. Edit is available on archived notes.**
- Given a note is archived, when the user opens it for editing and saves, then the app sends the edit and the note remains archived with all fields kept. The edit control is never disabled or hidden because of archive state.
- Traces to: US-003-05; BR-010.
- Verification: `notes.edit.archived_note_editable_status_unchanged` (planned).
- API: `API-NOTES-847551`.
- Notes:
  - `INV-WEB-003` applies.
  - The edit request carries no status field. This is consistent with `INV-WEB-053`.
  - The status shown after save is the one the server returns.

**WEB-NOTES-FR-012. Failed save keeps unsaved entries.**
- Given a save or create fails for any reason, when the failure is shown, then the displayed saved note is unchanged and the form keeps the user's entries (`INV-WEB-051`).
- Traces to: US-006-03, US-006-02.
- Verification: `notes.forms.failure_never_replaces_displayed_note` (planned).
- API: `API-NOTES-232390`, `API-NOTES-847551`.
- Notes: whether unsaved content survives a redirect after a `401` is outside this requirement. [`GAP-WEB-009`]

### List (`BLOCKED` on `GAP-WEB-001`)

**WEB-NOTES-FR-013. Notes list content.**
- Given notes exist, when `/notes` loads, then the app renders each note's title and status. Pinned notes appear first with a pin icon.
- Traces to: US-006-01, US-001-04; BR-021.
- Verification: `notes.list.renders_title_status_pinned_first` (planned).
- API: none. No list operation exists. [`GAP-WEB-001`]
- Notes:
  - The list layout, row structure and pin icon have no design. [`GAP-WEB-002`]
  - Pagination of the list is unspecified. [`GAP-WEB-014`]
  - A row opens `/notes/:id` (`ASM-WEB-055`).

**WEB-NOTES-FR-014. Active-only default.**
- Given active and archived notes exist, when the default list loads, then only active notes are shown. When all notes are archived, the list is empty and is not an error.
- Traces to: US-003-04; BR-011.
- Verification: `notes.list.default_excludes_archived` (planned).
- API: none. [`GAP-WEB-001`]
- Notes: `CON-WEB-051` records that US-001-04 says "all existing notes". The default applied is BR-011.

**WEB-NOTES-FR-015. Empty list.**
- Given no notes exist, or the active list is empty, when `/notes` loads, then the app shows `Empty/State` and a result count of 0. It shows no error.
- Traces to: US-006-01, US-001-04, US-003-04.
- Verification: `notes.list.empty_shows_empty_state_and_zero_count` (planned).
- API: none. [`GAP-WEB-001`]
- Notes: no empty-state copy exists. [`GAP-WEB-012`]

**WEB-NOTES-FR-016. List failure state.**
- Given the list request fails, when `/notes` loads, then the app shows a user-facing error state, not a blank screen.
- Traces to: US-006-01.
- Verification: `notes.list.error_shows_user_facing_state` (planned).
- API: none. [`GAP-WEB-001`]
- Notes: no list error design exists. [`GAP-WEB-002`]

**WEB-NOTES-FR-017. Sort control.**
- Given the list is shown, when the page loads, then a sort control above the list offers Newest first, Oldest first and Title A-Z, with Newest first selected by default.
- Given a different order is selected, when the selection changes, then the list re-sorts locally with no API parameter. Pinned notes still precede unpinned notes in every order.
- Traces to: US-006-01; BR-020, BR-021.
- Verification: `notes.list.sort_options_default_and_pinned_first` (planned).
- API: none. Sorting adds no API parameters. [`INV-WEB-005`, `INV-WEB-006`]
- Notes:
  - Sort basis, collation, tie-breaks and persistence follow `ASM-WEB-004` and `GAP-WEB-006`.
  - The sort control has no design. [`GAP-WEB-002`]
  - `CON-WEB-003`: if the list is paginated, client-side sort orders only the loaded set. The default applied is to sort the loaded set.

**WEB-NOTES-FR-018. Live result count.**
- Given the list is shown, when the displayed set changes, then a count above the list equals the number of notes rendered. It shows `0` for an empty list.
- Traces to: US-006-01; BR-019.
- Verification: `notes.list.count_equals_rendered_notes` (planned).
- API: none.
- Notes:
  - `INV-WEB-004` applies: the count is hidden while loading and recomputed on every change.
  - The count is client-derived (`ASM-WEB-010`).
  - `CON-WEB-002` concerns the search side. On the list, "number rendered" and "number listed" coincide while the list is unpaginated.

**WEB-NOTES-FR-019. Pin toggle on list rows.**
- Given a row in the list, when the user clicks its pin toggle, then the app sends a partial edit carrying only the pinned flag. On success the row moves to the top with a pin icon, or returns to its sort position when unpinned.
- Traces to: US-006-06 (pin clause only; archive and restore belong to the `archive` module), US-006-01; BR-021.
- Verification: `notes.list.pin_toggle_sends_only_pinned_flag` (planned).
- API: `API-NOTES-847551`.
- Notes:
  - `INV-WEB-007` applies. The client does not reorder optimistically. [`GAP-WEB-005`]
  - The toggle and icon have no design. [`GAP-WEB-002`]
  - Behaviour on archived rows is unspecified. [`GAP-WEB-005`]

### Story to requirement map

| Story | Requirements | Status |
|---|---|---|
| US-001-01 | FR-001 | covered |
| US-001-02 | FR-007, FR-008 | covered |
| US-001-03 | FR-004, FR-005 | covered |
| US-001-04 | FR-013, FR-015 | `BLOCKED` (`GAP-WEB-001`) |
| US-001-05 | FR-002, FR-009 | covered; field mapping unconfirmed (`GAP-WEB-055`) |
| US-001-06 | FR-003, FR-010 | covered |
| US-003-04 | FR-014, FR-015 | `BLOCKED` (`GAP-WEB-001`) |
| US-003-05 | FR-011 | covered |
| US-003-06 | FR-005, FR-006 | covered web-side only; the server-side validation is backend-owned |
| US-006-01 | FR-013, FR-015, FR-016, FR-017, FR-018, FR-019 | `BLOCKED` (`GAP-WEB-001`, `GAP-WEB-002`) |
| US-006-02 | FR-001, FR-002, FR-003, FR-012 | covered |
| US-006-03 | FR-007, FR-008, FR-009, FR-012 | covered |
<!-- /SECTION: web-app.notes.requirements -->
<!-- SECTION: web-app.notes.routes | status: DRAFT-UNVERIFIED -->
## Routes

Route paths are proposed. [`ASM-WEB-001`] Screen names and modules come from the design brief. Every route sits in the authenticated shell and requires the Team member role. Unauthenticated callers are redirected as defined in `web-app.routing` (`ASM-WEB-002`).

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes` | notes list: no design screen. `BLOCKED`. [`GAP-WEB-001`, `GAP-WEB-002`] | authenticated shell (not designed) | Team member |
| `/notes/new` | `NoteEditorScreen` (M1), Empty variant for create (`ASM-WEB-003`) | authenticated shell | Team member |
| `/notes/:id` | `NoteDetailScreen` (M1) | authenticated shell | Team member |
| `/notes/:id/edit` | `NoteEditorScreen` (M1), Filled variant for edit (`ASM-WEB-003`) | authenticated shell | Team member |

- **Match order.** `INV-WEB-001`: the static segment `/notes/new` is matched before `/notes/:id`. `/notes/search` and `/notes/archived` also precede `/notes/:id`, but belong to other modules.
- **Navigation edges.** These are proposals.
  - Successful create goes to `/notes/:id`. [stories.md US-006-02]
  - Detail goes to Edit at `/notes/:id/edit` (`ASM-WEB-056`).
  - A list row goes to `/notes/:id` (`ASM-WEB-055`).
- **Permission model.** Only one role signs in to this surface. No per-note permission is specified, because note ownership is unresolved upstream. [backend-api.md `CON-BE-003`; `GAP-WEB-023`]
<!-- /SECTION: web-app.notes.routes -->
<!-- SECTION: web-app.notes.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `notes`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.notes.components -->
<!-- SECTION: web-app.notes.states | status: DRAFT-UNVERIFIED -->
## States

No screen in the design inventory has a loading variant. [`GAP-WEB-013`] Copy for every state is undesigned. [`GAP-WEB-012`]

| Route | Loading | Empty | Populated | Error |
|---|---|---|---|---|
| `/notes` | not designed. The count is hidden while loading (`INV-WEB-004`). | `Empty/State`; count `0` (FR-015) | `BLOCKED` (`GAP-WEB-001`, `GAP-WEB-002`) | user-facing error state, not blank (FR-016); not designed |
| `/notes/new` | none while idle. During submit the control shows an in-flight state (`ASM-WEB-052`). | `NoteEditorScreen` Empty variant: a blank form (`ASM-WEB-003`) | `NoteEditorScreen` Filled variant: the form with entries | `NoteEditorScreen` Error variant: inline field errors (FR-002) |
| `/notes/:id` | not designed (fetch in flight) | not applicable: the note exists or the route is an error | `NoteDetailScreen` Default | `NoteDetailScreen` Error: not found or other failure (FR-005, FR-006) |
| `/notes/:id/edit` | not designed (fetch in flight) | not applicable | `NoteEditorScreen` Filled | `NoteEditorScreen` Error: field errors, "note not found", other failure (FR-008, FR-009) |

**Permission-denied.** All four routes are permission-gated, but no distinct state can be specified.
- **Whether a refusal is 403 or 404 is unresolved.** The choice is a security decision that depends on the note access rule, which is blocked upstream. [backend-api.md `backend.auth-model`, `CON-BE-003`; `GAP-WEB-023`]
- **Not-found is specified.** Not-found has its own state (FR-005, FR-008).
- **Permission-denied is unspecified.** A `403` is never handled as a `401`. [web-app.auth-flows]

**Partial-failure.** Not applicable. The notes module has no multi-item operation: create, edit and pin each act on a single note. No `GAP-` is raised.

**Offline.** Not a state on the web unless an input requires it, and none does. [web-app.overview]
<!-- /SECTION: web-app.notes.states -->
<!-- SECTION: web-app.notes.api-integration | status: DRAFT-UNVERIFIED -->
## API integration

References only. Request, response and error shapes are defined in the contract. [backend-api.md `backend.api-surface`]

| Operation | Pinned ID | Used by |
|---|---|---|
| Create note | `API-NOTES-232390` | FR-001, FR-002, FR-003, FR-012 |
| Get note by id | `API-NOTES-045877` | FR-003, FR-004, FR-005, FR-006, FR-007 (load) |
| Partial edit (title, body, pinned) | `API-NOTES-847551` | FR-007 to FR-012, FR-019 |
| List notes | **none in the contract** | FR-013 to FR-018: `BLOCKED`. [`GAP-WEB-001`] |

- **The missing list operation.** The permission matrix names "Note: list" but the API surface has no list operation. Search cannot stand in, because search requires a keyword or tag. [requirements.md BR-007; backend-api.md `backend.auth-model`] The web app needs from backend a list operation returning active notes with pinned first, and optionally a way to request archived notes. This is a need for backend, not an invented endpoint.
- **Not used by this module.** Archive and unarchive (`API-NOTES-810640`, `API-NOTES-917869`) belong to `archive`. Tag add and remove (`API-NOTES-359413`, `API-NOTES-150096`) have no web story. [`GAP-WEB-004`] Search (`API-SEARCH-580505`) belongs to `search`.
- **Displayed fields.** The UI displays note title, body, status, pinned state, created and updated times.
- **No sort or count parameters.** Sorting and the live count are client-side. [requirements.md BR-020]
- **Authentication errors.** A `401` from these operations follows the global rule in `web-app.auth-flows`.
<!-- /SECTION: web-app.notes.api-integration -->
<!-- SECTION: web-app.notes.validation | status: DRAFT-UNVERIFIED -->
## Validation

The server is the validation authority. The client shows what the server returns.

| Rule | Client | Also enforced server-side |
|---|---|---|
| Title and body non-empty at creation | no client emptiness check (`ASM-WEB-051`) | yes. [requirements.md BR-001] |
| Malformed requests return field-level errors, never a generic 500 | shows the field error inline (FR-002, FR-009) | yes. [requirements.md BR-004, NFR-011] |
| Attachments or binary payloads rejected | no attachment control exists, so none is ever sent | yes. [requirements.md BR-016, NFR-006] |
| Client-supplied `created_at` and `updated_at` ignored | the client never sends them (`INV-WEB-002`) | yes. [stories.md US-001-06; requirements.md BR-003] |
| Edit never overwrites fields it does not carry | sends only changed fields (`INV-WEB-053`) | yes. [backend-api.md `INV-BE-008`] |
| Maximum title and body length | none enforced client-side | unstated. [`GAP-WEB-056`, backend-api.md `GAP-BE-035`] |
| Route id format | none enforced client-side | unstated for GET and PATCH. [`GAP-WEB-054`] |

- **Invalid submit.** An invalid submit leaves the user on the form with entries intact (`INV-WEB-051`).
- **Duplicate submit.** Submit controls are disabled while a request is in flight (`ASM-WEB-052`).
- **No-change edit.** Saving an edit with no changes sends no request (`ASM-WEB-053`).
<!-- /SECTION: web-app.notes.validation -->
<!-- SECTION: web-app.notes.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

**Stated by inputs.**
- A validation error is shown inline, next to the field. [stories.md US-006-02]
- The invalid field is highlighted. [stories.md US-006-03]

**Not specified.** No input provides any of the following. [`GAP-WEB-018`]
- A target standard or a verification method.
- Focus management after navigation, after a failed submit, or after a pin change.
- Keyboard paths for the editor, the sort control and the pin toggle.
- Live-region announcement of the result count, the sort change, the pin state, or form errors.
- Whether highlighting is conveyed by more than colour.

These stay unspecified until the accessibility target and announcement behaviour arrive from the product or design source. No standard, role or ARIA behaviour is assumed here.
<!-- /SECTION: web-app.notes.accessibility -->
<!-- SECTION: web-app.notes.i18n | status: BLOCKED -->
## i18n

**Blocked.** No copy deck, copy key set or locale list is supplied. [`GAP-WEB-012`] No key or string is minted here.

**Copy slots the stories require** (text itself unsupplied):
- An empty-state message for the notes list. [stories.md US-006-01]
- A user-facing list error. [stories.md US-006-01]
- A "note not found" message. [stories.md US-006-03]
- Inline field validation display. [stories.md US-006-02, US-006-03]

**Unblocks with:** a copy deck covering these slots, and the supported locales.
<!-- /SECTION: web-app.notes.i18n -->
<!-- SECTION: web-app.notes.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode: FULL-FROM-BRIEF`. Figma is not connected, so every reference is an unverified claim from the brief. [RUN-CONTEXT `inputs.figma_mcp`] The brief supplies screen names and variants but no frame IDs. No frame ID is stated here. [design-brief.md]

| Screen | Module | Variants (per brief) | Components (per brief) | Stories mapped (per stories-map) | Verification |
|---|---|---|---|---|---|
| `NoteEditorScreen` | M1 | Default, Empty, Error, Filled | `Empty/State`, `Input/Text` | US-001-01, US-001-02, US-001-05, US-001-06, US-003-05, US-006-02, US-006-03 | unverified |
| `NoteDetailScreen` | M1 | Default, Error | none | US-001-03 | unverified |
| notes list | none | none | none | US-006-01 | no design exists. [`GAP-WEB-002`] |

- **No screen mapping for three stories.** US-001-04, US-003-04 and US-003-06 are mapped to no screen in the stories-map.
- **Tokens.** The brief lists identifiers only. No value or binding is supplied, none is asserted here, and no visual value is hard-coded. [`GAP-WEB-015`]
- **Variant meaning.** Use of the Empty variant for create and the Filled variant for edit is an assumption. [`ASM-WEB-003`] The meaning of the Default variant on `NoteEditorScreen` is unstated.
<!-- /SECTION: web-app.notes.design-refs -->
<!-- SECTION: web-app.notes.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All test names are `(planned)` proposals. Per-requirement test names are in `web-app.notes.requirements`.

**Component tests.**
- Editor: entries preserved on failure (FR-012); inline error placement; Save disabled while in flight (`ASM-WEB-052`).
- Edit form: only changed fields carried (`INV-WEB-053`); no request when nothing changed (`ASM-WEB-053`).
- Detail: all fields rendered; Error variant for not found.
- List components: sort order, pinned-first and count recompute (FR-017, FR-018). Blocked until the list exists.

**End-to-end tests.**
- `notes.e2e.create_then_view_saved_note` (planned): FR-001, FR-003.
- `notes.e2e.edit_title_then_see_update` (planned): FR-007, FR-010.
- `notes.e2e.edit_archived_note_stays_archived` (planned): FR-011.
- `notes.e2e.edit_deleted_route_shows_not_found` (planned): FR-008.
- `notes.e2e.list_pin_sort_count` (planned): FR-013 to FR-019. `BLOCKED` on `GAP-WEB-001`.

**Contract tests** against the pinned contract.
- `notes.contract.create_response_shape` (planned): `API-NOTES-232390`.
- `notes.contract.get_by_id_shape` (planned): `API-NOTES-045877`.
- `notes.contract.patch_partial_semantics` (planned): `API-NOTES-847551`. Confirms an unsent field is never overwritten (`INV-BE-008`).
- `notes.contract.error_details_field_naming` (planned): confirms how a detail entry names its field. [`GAP-WEB-055`]
- `notes.contract.malformed_id_status` (planned): [`GAP-WEB-054`].

**Accessibility tests.** Not definable: no target standard exists. [`GAP-WEB-018`] Placeholder: `notes.a11y.inline_errors_programmatically_associated` (planned) is proposed only once a target is supplied.
<!-- /SECTION: web-app.notes.test-plan -->
<!-- SECTION: web-app.notes.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

Project-level items are cited, not redefined. Items below are minted in this module's reserved block.

**Contradiction checks.** All four directions were run.
- **Story to design:** no direct contradiction. Missing designs are gaps.
- **Story to requirement:** `CON-WEB-051` (below).
- **Requirement to requirement:** `CON-WEB-005` (rule numbering differs between inputs), cited.
- **Design to design:** none found.

No Q- was minted. Every open decision has a stated default.

### Project-level items that affect this module

`GAP-WEB-001` (no list operation), `GAP-WEB-002` (no list, shell, sort, count or pin design), `GAP-WEB-004`, `GAP-WEB-005`, `GAP-WEB-006`, `GAP-WEB-009`, `GAP-WEB-012`, `GAP-WEB-013`, `GAP-WEB-014`, `GAP-WEB-015`, `GAP-WEB-018`, `GAP-WEB-022`, `GAP-WEB-023`, `CON-WEB-002`, `CON-WEB-003`, `CON-WEB-005`, `ASM-WEB-001`, `ASM-WEB-002`, `ASM-WEB-003`, `ASM-WEB-004`, `ASM-WEB-008`, `ASM-WEB-010`.

### Contradiction minted here

- **`CON-WEB-051` (non-critical).** US-001-04 says `GET /notes` returns a list of all existing notes. US-003-04 and BR-011 say the default list returns only active notes and excludes archived ones. [stories.md; requirements.md BR-011]
  - If "all" holds, archived notes appear in the default list.
  - If BR-011 holds, they appear only in the archived view.
  - Default applied: BR-011, the reading with the strongest citation. FR-014 depends on it.
  - Not resolved here.

### Gaps minted here

- **`GAP-WEB-052`.** `NoteEditorScreen` has no Save or Cancel control and no multi-line body control. Only `Input/Text` and `Empty/State` are mapped. Closing artefact: an updated editor frame.
- **`GAP-WEB-053`.** `NoteDetailScreen` has no component mapping and no layout for status, pinned state and timestamps. The Edit entry is also undesigned. Closing artefact: an updated detail frame.
- **`GAP-WEB-054`.** The contract lists no `400` for get-by-id or edit with a malformed id, and states no id format. US-003-06 expects a descriptive error. Closing artefact: the backend error model for these operations.
- **`GAP-WEB-055`.** The shape of a field-level error detail entry is unstated, so mapping to form fields is unconfirmed. [backend-api.md `ASM-BE-300`] Closing artefact: the backend error envelope.
- **`GAP-WEB-056`.** Maximum title and body lengths, and character rules, are unstated, although US-001-01 refers to a maximum body length. Closing artefact: the backend validation rules (`GAP-BE-035`).
- **`GAP-WEB-057`.** The edit request carries no version or precondition, so a stale overwrite cannot be detected or shown. US-001-02 only states that the last accepted write wins. Closing artefact: a rule or story on concurrent-edit UX, or a contract field.
- **`GAP-WEB-058`.** There is no rule for leaving the editor with unsaved changes. Default: no navigation guard. Closing artefact: a story or rule.

### Assumptions minted here

- **`ASM-WEB-051`.** The client does no emptiness check of its own on create. It submits and shows the API's validation error, as US-006-02 states. Confirm with the story owner.
- **`ASM-WEB-052`.** Submit and Save controls are disabled while a request is in flight, so one user action sends one request. `Button/Primary` has Loading and Disabled states. [design-brief.md] Confirm with the design owner.
- **`ASM-WEB-053`.** Edit sends only changed fields, and Save with no changes sends no request. Confirm with the story owner.
- **`ASM-WEB-055`.** A list row opens `/notes/:id`. No input states how a row is activated. Confirm with the design.
- **`ASM-WEB-056`.** `NoteDetailScreen` offers an Edit entry to `/notes/:id/edit`. No input states how edit is reached. Confirm with the design.
- **`ASM-WEB-057`.** Save and Create reuse `Button/Primary`, although the brief maps it only to login screens. Confirm with the design.
- **`ASM-WEB-058`.** The title field uses `Input/Text`, which is mapped to `NoteEditorScreen`. Whether the body does is unknown. Confirm with the design.

### Invariants minted here

- **`INV-WEB-051`.** A failed create or edit never replaces the displayed saved note with the unsaved values, and the form keeps the user's entries. [stories.md US-006-02, US-006-03]
- **`INV-WEB-053`.** An edit request never carries a field the user did not change. Derived from US-003-05 ("no data fields are lost") and `INV-BE-008`.
<!-- /SECTION: web-app.notes.gaps -->
