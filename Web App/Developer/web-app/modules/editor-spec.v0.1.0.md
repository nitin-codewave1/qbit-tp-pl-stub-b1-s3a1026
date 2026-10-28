---
id: agent-developer/web_app/web-app/modules/editor-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/editor-SPEC.md
depends_on: [web_app:US-006-02, web_app:US-006-03, web_app:US-001-01, web_app:US-001-02, web_app:US-001-05, web_app:US-001-06, web_app:US-003-05]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/modules/editor-SPEC.md
---

<!-- SECTION: web-app.editor.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: editor
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-011, GAP-WEB-052]
contract_pin: "sha256:8e424bb2b40a"
scope: ["/notes/new", "/notes/:id/edit"]
```

The routes in `scope` are proposals (`ASM-WEB-001`, project spec), so this section is `DRAFT-UNVERIFIED`.
<!-- /SECTION: web-app.editor.frontmatter -->
<!-- SECTION: web-app.editor.purpose | status: DERIVED -->
## Purpose

**Responsible for** (module `editor`, design label M1, `web-app.module-map`):
- Creating a note from a title and body (US-006-02).
- Editing an existing note's title and body (US-006-03).
- Presenting server validation, not-found and failure outcomes for those two operations (US-001-05).
- Handing the saved note to the shared cache and navigating after create. This is the editor's side of US-001-01, US-001-02 and US-001-06.
- Never blocking or altering editing because a note is archived (US-003-05, `BR-010`).

**Derivation.** The module map assigns `API-NOTES-232390` and `API-NOTES-847551` to this module, and lists the web stories as US-006-02 and US-006-03. The remaining owned stories (US-001-01, US-001-02, US-001-05, US-001-06, US-003-05) are API stories whose operations this module consumes (`web-app.coverage`, "Dependency").

**Not responsible for:**
- Rendering the saved note, including displaying `created_at` and `updated_at`, and the archive and restore actions. Those belong to `note-detail` (`API-NOTES-045877`, `API-NOTES-810640`, `API-NOTES-917869`).
- The pin toggle and the notes list, sort and search. Those belong to `search`.
- Tag add and remove controls (`GAP-WEB-015`).
- Login and session handling (`login`; `GAP-WEB-001`).
- Attachments. `BR-016` and `BR-017` exclude them.
- The decision of whether a session is required. That decision is undecided upstream (`Q-BE-003`).
<!-- /SECTION: web-app.editor.purpose -->
<!-- SECTION: web-app.editor.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Each requirement lists **Traces to**, **Verification**, and **API** where one applies. Planned tests are named in `web-app.editor.test-plan`.

**WEB-EDITOR-FR-001: Create form.**
- Given the user opens `/notes/new`, when the page renders, then it shows a blank title field and a blank body field and a submit control.
- Traces to: US-006-02.
- Verification: (planned) `editor.create.rendersBlankForm`.

**WEB-EDITOR-FR-002: Create submission.**
- Given a title and body are entered, when the user submits, then the web app sends the entered title and body with the create operation.
- The request carries no tags, no pinned flag, no `created_at` or `updated_at` and no id.
- Traces to: US-006-02, US-001-01, US-001-06; `BR-003`.
- Verification: (planned) `editor.create.sendsTitleAndBodyOnly`.
- API: `API-NOTES-232390`.

**WEB-EDITOR-FR-003: Create success.**
- Given the create operation succeeds, when the response is received, then the web app writes the returned note into the note-by-id cache and invalidates cached search results.
- The invalidation follows project state-management policy item 2, because a new note changes result sets (`ASM-WEB-060`).
- The web app then navigates to `NoteDetailScreen` (`/notes/:id`) using the id in the response.
- Traces to: US-006-02, US-001-01.
- Verification: (planned) `editor.create.success.navigatesToDetailAndPrimesCache`.
- API: `API-NOTES-232390`.

**WEB-EDITOR-FR-004: Server-set `created_at`.**
- Given the create operation succeeds, when the saved note is shown, then the `created_at` shown is the server value from the response.
- The editor never generates, sends or overrides it (`INV-WEB-051`).
- The display itself is rendered by `note-detail`.
- Traces to: US-006-02, US-001-06; `BR-003`.
- Verification: (planned) `editor.create.success.cachesServerCreatedAtVerbatim`.
- API: `API-NOTES-232390`.

**WEB-EDITOR-FR-005: Create validation failure.**
- Given the title field is empty, when the user submits, then the web app shows the API's validation error inline on that field.
- No note is created, the user stays on `/notes/new`, and entered values are preserved.
- If both fields are invalid, every invalid field is shown (US-001-05).
- Traces to: US-006-02, US-001-01, US-001-05; `BR-001`, `BR-004`, `NFR-011`.
- Verification: (planned) `editor.create.validation.showsServerFieldErrors`.
- API: `API-NOTES-232390`.

**WEB-EDITOR-FR-006: Edit load.**
- Given the user opens `/notes/:id/edit`, when the page loads, then the web app fetches the note and populates the title and body fields with the stored values.
- Traces to: US-006-03.
- Verification: (planned) `editor.edit.loadsAndPopulates`.
- API: `API-NOTES-045877`.

**WEB-EDITOR-FR-007: Edit submission.**
- Given the user changed the title or body, when they save, then the web app sends only the changed fields among title and body (`ASM-WEB-057`).
- The request carries no pinned flag, no archive state, no timestamps and no id in the body.
- The contract has only a partial-edit operation; stories say "PUT/PATCH" (`Q-BE-007`).
- Traces to: US-006-03, US-001-02, US-001-06.
- Verification: (planned) `editor.edit.sendsChangedFieldsOnly`.
- API: `API-NOTES-847551`.

**WEB-EDITOR-FR-008: Edit success.**
- Given the edit operation succeeds, when the response is received, then the web app shows the updated content.
- It writes the returned note into the note-by-id cache and invalidates cached search results, since title and body are searchable (project state-management, item 2).
- The editor remains on `/notes/:id/edit` with the fields repopulated from the response (`ASM-WEB-051`, `Q-WEB-051`).
- Traces to: US-006-03, US-001-02.
- Verification: (planned) `editor.edit.success.showsUpdatedAndInvalidatesSearch`.
- API: `API-NOTES-847551`.

**WEB-EDITOR-FR-009: Edit save, note not found.**
- Given the save request reports the note does not exist, when the response is received, then the web app shows a "note not found" message and applies no local changes.
- The cached note and any displayed stored values are not modified.
- Entered text stays in the form so it is not lost.
- Traces to: US-006-03, US-001-02.
- Verification: (planned) `editor.edit.save404.showsNotFoundAndLeavesCacheUntouched`.
- API: `API-NOTES-847551`.

**WEB-EDITOR-FR-010: Edit load, note not found.**
- Given the user opens `/notes/:id/edit` for an id the API reports as not found, or as an invalid id, when the load completes, then the web app shows the same "note not found" state and no editable form (`ASM-WEB-052`).
- Traces to: US-006-03; US-001-03 (404 outcome); US-003-06 (malformed id outcome).
- Verification: (planned) `editor.edit.load404And400.showsNotFound`.
- API: `API-NOTES-045877`.

**WEB-EDITOR-FR-011: Edit validation failure.**
- Given the save request reports a field-level validation error, when the response is received, then the web app highlights each invalid field and shows the server's message for it.
- Traces to: US-006-03, US-001-05; `NFR-011`.
- Verification: (planned) `editor.edit.validation.highlightsInvalidFields`.
- API: `API-NOTES-847551`.

**WEB-EDITOR-FR-012: Unidentifiable field error.**
- Given a validation response carries a message but the web app cannot tie it to a field, when it is shown, then it appears as a form-level error and no field is highlighted.
- This rule is a fallback, because the detail shape is unspecified (`GAP-WEB-052`).
- Traces to: US-001-05; `NFR-011`.
- Verification: (planned) `editor.validation.unmappedErrorShownFormLevel`.
- API: `API-NOTES-232390`, `API-NOTES-847551`.

**WEB-EDITOR-FR-013: Archived notes remain editable.**
- Given the note being edited is archived, when the user changes and saves it, then the save is offered and sent exactly as for an active note.
- The note's archive state is neither changed nor displayed as changed by the save (`INV-WEB-004`, `INV-WEB-052`).
- The editor contains no archive or restore control.
- Traces to: US-003-05; `BR-010`.
- Verification: (planned) `editor.edit.archivedNote.saveEnabledAndStateUnchanged`.
- API: `API-NOTES-847551`.

**WEB-EDITOR-FR-014: Concurrent edits.**
- Given another write changed the same note since it was loaded, when the user saves, then the web app sends no version or precondition.
- The response of the last accepted write is shown as-is, and the editor does not merge.
- Traces to: US-001-02.
- Verification: (planned) `editor.edit.noPreconditionSent`.
- API: `API-NOTES-847551`.

**WEB-EDITOR-FR-015: No duplicate submission.**
- Given a submit is in flight, when the user triggers submit again, then no second request is sent.
- Derivation: a repeated create would persist a second note, contrary to "reliably persist new notes" (US-001-01).
- Traces to: US-001-01; `NFR-004`.
- Verification: (planned) `editor.submit.inFlightBlocksSecondRequest`.
- API: `API-NOTES-232390`, `API-NOTES-847551`.

**WEB-EDITOR-FR-016: No attachments.**
- Given any editor route, when it renders, then it offers no file or binary control.
- Requests are JSON bodies only.
- Traces to: `BR-016`, `NFR-006`.
- Verification: (planned) `editor.noFileControlRendered`.
- API: `API-NOTES-232390`, `API-NOTES-847551`.

**Owned-story coverage**

| Story | Requirements |
|---|---|
| US-006-02 | FR-001, 002, 003, 004, 005 |
| US-006-03 | FR-006 to 011 |
| US-001-01 | FR-002, 003, 005, 015 |
| US-001-02 | FR-007, 008, 009, 014 |
| US-001-05 | FR-005, 011, 012 |
| US-001-06 | FR-002, 004, 007 |
| US-003-05 | FR-013 |

**Invariants**
- `INV-WEB-051`: The editor never sends `created_at`, `updated_at` or a body id. Timestamps shown always originate from a server response (US-001-06, `BR-002`, `BR-003`).
- `INV-WEB-052`: Saving from the editor never changes a note's archived or pinned state. It sends neither (US-003-05, `BR-010`).
- `INV-WEB-004` (project spec) applies unchanged: edit controls are never disabled because a note is archived.
<!-- /SECTION: web-app.editor.requirements -->
<!-- SECTION: web-app.editor.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes/new` | `NoteEditorScreen` (create) | app layout | Team member (`ASM-WEB-006`). Whether a session is required is `BLOCKED` (`GAP-WEB-001`, `Q-BE-003`). |
| `/notes/:id/edit` | `NoteEditorScreen` (edit) | app layout | Same. Ownership-based denial is unknown (`GAP-WEB-011`, `Q-BE-002`). |

**Source and status**
- Paths and layout assignment come from `web-app.routing`. The paths are proposals (`ASM-WEB-001`).
- Both routes sit inside the app layout. The content of that layout is undesigned (`GAP-WEB-004`).
- The protected-route and unauthenticated-redirect rules are defined at project level (`web-app.routing`) and are not restated here.

**Navigation**
- The editor's design record shows no outgoing transitions (`screens-inventory.md`).
- After a successful create, the editor navigates to `/notes/:id` (US-006-02).
- How the user enters or leaves the editor otherwise is not designed (`GAP-WEB-054`).
<!-- /SECTION: web-app.editor.routes -->
<!-- SECTION: web-app.editor.components | status: UNKNOWN -->
## Components

The component inventory comes from `components-inventory.md` and `screens-inventory.md`, and is unverified against Figma. `NoteEditorScreen` lists exactly two components: `Empty/State` and `Input/Text`. Token bindings are proposals (`ASM-WEB-056`), because the brief omits component bodies and gives token IDs only.

| Component | Use in this module | Variants and states (from the brief) | Proposed token bindings |
|---|---|---|---|
| `Input/Text` | Title field; body field (see `GAP-WEB-056`) | Default, Focused, Filled, Error, Disabled | Default border `UI/border/input`, surface `UI/surface/input`, placeholder `UI/text/placeholder`. Focused border `UI/border/input-focus`, surface `UI/surface/input-focus`. Error border `UI/border/error`, message `UI/text/error`. Disabled border `UI/border/disabled`, surface `UI/surface/disabled`, text `UI/text/disabled`. |
| `Empty/State` | The "note not found" state on the edit route (`ASM-WEB-055`) | one variant, Default | not determinable from inputs; the surface `UI/surface/default` and text `UI/text/secondary` are candidates only |
| `Button/Primary` | Save and create control (reuse, `ASM-WEB-059`) | 12 variants (not enumerated); states Default, Pressed, Loading, Disabled | Surface `UI/surface/button/primary`, hover `UI/surface/button/primary-hover`, label `UI/text/on-primary`, disabled `UI/surface/disabled` |

**Not in the design for this screen**
- A save or submit control is not listed for `NoteEditorScreen`.
- A multi-line body control is not identified.
- A cancel or back control, and a loading indicator, are not listed.

These are `GAP-WEB-054` and `GAP-WEB-056`. No component, variant or token is invented beyond the reuse in `ASM-WEB-059`.
<!-- /SECTION: web-app.editor.components -->
<!-- SECTION: web-app.editor.states | status: DRAFT-UNVERIFIED -->
## States

Design variants for `NoteEditorScreen` are Default, Empty, Error and Filled (`screens-inventory.md`). The mapping below is an assumption (`ASM-WEB-055`).

| State | `/notes/new` | `/notes/:id/edit` |
|---|---|---|
| Loading | There is no initial fetch. While a submit is in flight, `Button/Primary` shows its Loading state and a second submit is blocked (FR-015). | The note fetch is in flight (FR-006). A loading indicator is required but undesigned (`GAP-WEB-054`). While a save is in flight, as for `/notes/new`. |
| Empty | Blank title and body (design variant Empty; FR-001). | Not applicable on load. `BR-001` means a stored note has a non-empty title and body. A field the user clears is validated on save (FR-011). |
| Populated | User-entered values (variant Filled). | Stored values loaded, then user changes (variant Filled). Archived notes look and behave identically (FR-013). |
| Error | Field-level validation errors inline (FR-005, FR-012). Generic failures (network, 5xx, anything unclassified) are shown as a form-level error, values are retained and retry is allowed (`ASM-WEB-058`). | Load not-found (FR-010) and save not-found (FR-009), each showing "note not found". Field-level validation errors (FR-011). Generic failures as for `/notes/new` (`ASM-WEB-058`). |
| Permission-denied | `BLOCKED` pending `GAP-WEB-011`, `Q-BE-001`, `Q-BE-002`, `GAP-WEB-001`. Whether "not allowed" and "does not exist" render differently is a security decision not made here. | Same. |
| Partial-failure | Not applicable: no multi-item operation (project spec W8: omitted without a gap). | Not applicable. |

Presentation of the error and loading states beyond the component states in `web-app.editor.components` is undesigned (`GAP-WEB-054`).
<!-- /SECTION: web-app.editor.states -->
<!-- SECTION: web-app.editor.api-integration | status: VERIFIED -->
## API integration

Reference only. Request and response shapes live in the pinned contract and are not restated (core rule 6).

| UI operation | Contract operation |
|---|---|
| Create note (FR-002, FR-003) | `API-NOTES-232390` |
| Load note for editing (FR-006, FR-010) | `API-NOTES-045877` |
| Save note edit (FR-007 to FR-009, FR-011, FR-013, FR-014) | `API-NOTES-847551` |

**Handling notes**
- The web app branches on the outcome class, namely validation failure versus not found, as stated in the stories. These two classes have distinct statuses in the contract, and the backend's registry code names are proposals (`ASM-BE-006`, `GAP-WEB-009`).
- The shape of field-level error detail is not specified, so field mapping is `GAP-WEB-052`.
- No PUT operation exists in the contract. `API-NOTES-847551` is used for edits (`Q-BE-007` default).
- Operations used by other modules (tags, archive, unarchive, search) are not called by this module.
<!-- /SECTION: web-app.editor.api-integration -->
<!-- SECTION: web-app.editor.validation | status: DRAFT-UNVERIFIED -->
## Validation

**Client rules**
1. The web app sends the values exactly as entered and does not trim, normalise or reject them before submit (`CON-WEB-051`, `ASM-WEB-053`).
2. No maximum length, counter or whitespace rule is applied, because none is supplied (`GAP-WEB-053`).
3. On edit, only changed title and body are sent (`ASM-WEB-057`).
4. While a request is in flight, no second request is sent (FR-015).
5. No file or binary control exists, and bodies are JSON only (FR-016).
6. The client never supplies `created_at`, `updated_at` or an id in a body (`INV-WEB-051`).

**Server-side enforcement** (the client displays these results and does not duplicate them)

| Rule | Source |
|---|---|
| Non-empty title and body at creation | `BR-001`; US-001-01 |
| Field-level detail on 4xx validation responses | `BR-004`, `NFR-011` |
| Malformed field type or empty body reported by field name | US-001-05 |
| Client-supplied timestamps ignored, server time used | US-001-06; `BR-003` |
| Id immutable | `BR-002` |
| Attachment payloads rejected | `BR-016`, `NFR-006` |
| Edit permitted regardless of archive state | `BR-010`; US-003-05 |
| Last accepted write wins on concurrent edits | US-001-02 |

An empty-body or wrong-type request cannot originate from this editor, which always sends text fields. No gap is raised for it.
<!-- /SECTION: web-app.editor.validation -->
<!-- SECTION: web-app.editor.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** The project-level target standard and verification method are absent (`web-app.accessibility`, `GAP-WEB-007`), and the project spec defers focus management, keyboard paths and live regions to module specs "once a target exists". Writing them now would be unsourced invention.

**Established by inputs, without a target**
- Validation errors are shown inline and the invalid field is highlighted (US-006-02, US-006-03).
- `Input/Text` has Focused and Error states (`components-inventory.md`).
- A "note not found" message is shown (US-006-03).

How these are exposed to assistive technology is not stated.

**Unblocked by:** a requirement naming the standard, level and verification method. This section must then specify, for both routes, focus placement after a failed submit, keyboard paths for the form, live-region announcement of errors and success, and the association of errors to fields.
<!-- /SECTION: web-app.editor.accessibility -->
<!-- SECTION: web-app.editor.i18n | status: BLOCKED -->
## i18n

**Blocked.** No copy deck, copy keys or locale list exist (`GAP-WEB-016`).

**Messages this module needs once a deck exists**
- The server field-level validation messages, which are displayed from the API response.
- A "note not found" message (US-006-03, quoted).
- A form-level generic failure message (`ASM-WEB-058`).
- Labels for the title and body fields and the save control, none of which are supplied (`GAP-WEB-054`).

**Unblocked by:** a copy deck and a supported-locale statement.
<!-- /SECTION: web-app.editor.i18n -->
<!-- SECTION: web-app.editor.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma MCP is not connected and a design brief is present, so `spec_mode` is `FULL-FROM-BRIEF`. Everything below is an unverified claim from `design-brief.md`.

| Item | Value | Verification |
|---|---|---|
| Screen | `NoteEditorScreen`, "Note Editor", module M1 | unverified; no frame ID supplied (`GAP-WEB-005`) |
| Variants | Default, Empty, Error, Filled | unverified |
| Components | `Empty/State`, `Input/Text` | unverified |
| Outgoing transitions | none recorded | unverified |
| Stories mapped to this screen | US-001-01, US-001-02, US-001-05, US-001-06, US-005-02, US-006-02, US-006-03 | unverified |
| Tokens | named in `web-app.editor.components` | unverified; values not supplied |

**Observation.** `stories-map.md` maps US-003-05 to `NoteDetailScreen`, not `NoteEditorScreen` (`CON-WEB-052`). The design shows `NoteDetailScreen` using `Input/Text` as well.

**Design coverage.** The design does not show the list, pin toggle, field labels, save control, cancel control or loading indicator (`GAP-WEB-054`, `GAP-WEB-056`).
<!-- /SECTION: web-app.editor.design-refs -->
<!-- SECTION: web-app.editor.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All test names are planned. Each FR's Verification line names its primary test.

**Component**
- `editor.create.rendersBlankForm`
- `editor.edit.loadsAndPopulates`
- `editor.submit.inFlightBlocksSecondRequest`
- `editor.noFileControlRendered`
- `editor.validation.unmappedErrorShownFormLevel`
- `editor.edit.save404.showsNotFoundAndLeavesCacheUntouched`

**End-to-end** (against a stubbed contract)
- `editor.create.success.navigatesToDetailAndPrimesCache`
- `editor.create.validation.showsServerFieldErrors`
- `editor.edit.success.showsUpdatedAndInvalidatesSearch`
- `editor.edit.validation.highlightsInvalidFields`
- `editor.edit.archivedNote.saveEnabledAndStateUnchanged`
- `editor.edit.load404And400.showsNotFound`

**Contract**
- `editor.create.sendsTitleAndBodyOnly`
- `editor.edit.sendsChangedFieldsOnly`
- `editor.edit.noPreconditionSent`
- `editor.create.success.cachesServerCreatedAtVerbatim`
- For each of the three operations, the requests the editor makes validate against the pinned contract (`sha256:8e424bb2b40a`).

**Accessibility:** `BLOCKED`, pending `GAP-WEB-007`. Tests are defined once a standard exists.
<!-- /SECTION: web-app.editor.test-plan -->
<!-- SECTION: web-app.editor.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

Project-level items cited by this module: `GAP-WEB-001`, `GAP-WEB-004`, `GAP-WEB-005`, `GAP-WEB-007`, `GAP-WEB-009`, `GAP-WEB-011`, `GAP-WEB-015`, `GAP-WEB-016`, `ASM-WEB-001`, `ASM-WEB-006`, `Q-BE-001`, `Q-BE-002`, `Q-BE-003`, `Q-BE-007`, `GAP-BE-008`.

**Contradictions (non-critical; neither side resolved)**
- `CON-WEB-051`: US-006-02 and US-006-03 say the web app shows "the API's validation error" for an empty title, which implies submit-through. A client pre-check would block the request and show a client message instead.
  - Consequence of submit-through: a request is spent on obvious emptiness, and whitespace-only handling depends on the server.
  - Consequence of a pre-check: the message shown is not the API's, and the field rules would be duplicated and could drift.
  - Default: submit-through (the story text is the most specific; `ASM-WEB-053`).
- `CON-WEB-052`: `stories-map.md` places US-003-05 (edit or tag an archived note) on `NoteDetailScreen`. The project routing and module map place note editing on this module's routes.
  - Consequence of following the design: edit of archived notes lives on the detail screen.
  - Consequence of following the module map: it lives on `/notes/:id/edit`.
  - Default: the editor route (`web-app.routing`, `web-app.module-map`, US-006-03).

**Questions (degradable; unattended defaults taken)**
- `Q-WEB-051`: After a successful edit, does the user stay on the editor or go to the note detail? Default: stay and show the updated values (`ASM-WEB-051`).
- `Q-WEB-052`: Should leaving a form with unsaved changes prompt the user? Default: no prompt, since no input requires one.

**Assumptions**
- `ASM-WEB-051`: A successful edit keeps the user on `/notes/:id/edit` with the form repopulated from the response.
- `ASM-WEB-052`: An invalid-id and a not-found response on load are both shown as "note not found".
- `ASM-WEB-053`: The client does not pre-validate emptiness and relies on the server (`CON-WEB-051`).
- `ASM-WEB-054`: The editor detects no concurrent change to a note, consistent with last-write-wins (US-001-02, `ASM-BE-005`).
- `ASM-WEB-055`: Design variants map as Default = initial render, Empty = blank create form, Filled = populated form, Error = error presentation. `Empty/State` is the not-found presentation.
- `ASM-WEB-056`: Token bindings are proposals derived from token names in `tokens-index.md`; values are unseen.
- `ASM-WEB-057`: Edit sends only changed title and body. With no change, no request is sent. PATCH is used for edit.
- `ASM-WEB-058`: Network, 5xx and unclassified failures show a form-level error, retain values and allow retry.
- `ASM-WEB-059`: `Button/Primary` is reused for the save and create control, and the body uses a multi-line entry control.
- `ASM-WEB-060`: A successful create invalidates cached search results, because it changes membership in result sets.

**Gaps** (each names the artefact that closes it)
- `GAP-WEB-052`: The shape of field-level error detail is unspecified, in particular how an entry identifies its field.
  - **Backend must supply:** the validation error envelope and field identifiers for the create and edit operations.
  - Without it, field highlighting falls back to a form-level error (FR-012).
  - Closing artefact: contract additions.
- `GAP-WEB-053`: Title and body maximum lengths and the whitespace-only rule are unstated (`GAP-BE-008`, US-001-01). The editor applies none.
  - Closing artefact: field constraints.
- `GAP-WEB-054`: No editor-specific layout exists: field order, labels, save and cancel controls, loading indicator, saved confirmation, and how the user enters and leaves the editor.
  - Closing artefact: design frames.
- `GAP-WEB-055`: Whether the editor indicates that a note is archived is undefined.
  - Closing artefact: a design decision or story.
- `GAP-WEB-056`: `NoteEditorScreen` lists no button component and no multi-line body control.
  - Closing artefact: design frames or a confirmed reuse decision (`ASM-WEB-059`).
<!-- /SECTION: web-app.editor.gaps -->
