---
id: agent-developer/web_app/web-app/modules/note-detail-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/note-detail-SPEC.md
depends_on: [web_app:US-006-06, web_app:US-001-03, web_app:US-002-01, web_app:US-002-02, web_app:US-003-01, web_app:US-003-02]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/modules/note-detail-SPEC.md
---

<!-- SECTION: web-app.note-detail.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: note-detail
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-011, GAP-WEB-007, GAP-WEB-016, GAP-WEB-005]
contract_pin: "sha256:8e424bb2b40a"
scope: ["/notes/:id"]
```

Sources: `web-app.module-map` (module `note-detail`, label M3, screen `NoteDetailScreen`) and `RUN-CONTEXT` (contract pin, Figma MCP not connected).
<!-- /SECTION: web-app.note-detail.frontmatter -->
<!-- SECTION: web-app.note-detail.purpose | status: DERIVED -->
## Purpose

**Responsibility.** `note-detail` owns the single-note screen, `NoteDetailScreen`, at `/notes/:id` (`web-app.routing`, `web-app.module-map`). On it the Team member can:

- view one note in full (US-001-03);
- archive it and restore it (US-006-06, US-003-01, US-003-02);
- add and remove its tags (US-002-01, US-002-02).

Tag controls are specified here under `ASM-WEB-151`.

**Not responsible for**, each owned elsewhere and referenced only:

- **Pin or unpin.** The pin toggle is a list-row action owned by `search` (US-006-06 acceptance criterion 3, `web-app.module-map`).
- **Editing title or body.** Owned by `editor` (`/notes/:id/edit`). Creating a note and the post-create navigation to this screen are also `editor`.
- **Notes list, search, sort, result count, tags list and paging.** Owned by `search`. The "default active list" visibility in US-006-06 is verified there and is `BLOCKED` by `GAP-WEB-002`.
- **Login, session and the protected-route rule.** Owned by `login` and the project-level `web-app.auth-flows` and `web-app.routing`. This module consumes the decision (`GAP-WEB-001`).
- **Residency Report and Scope Documentation screens** (`ASM-WEB-005`).
- **Hard delete.** No input defines a delete operation, and archive is a flag only (`BR-009`).
- **Any backend behaviour.** Needs from the backend are stated in `GAP-WEB-156` and in the project-level gaps.
<!-- /SECTION: web-app.note-detail.purpose -->
<!-- SECTION: web-app.note-detail.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Operation IDs are cited as the contract supplies them, without an `@v` suffix. Error-code names (`NOTE_NOT_FOUND`, `NOTE_ALREADY_ARCHIVED` and others) are the backend's proposals (`ASM-BE-006`), so the web app branches on the error `code` as `web-app.auth-flows` already states.

**WEB-NOTE-DETAIL-FR-001: View a note**
- Given a note exists and the user opens `/notes/:id`, when the screen loads, then the web app requests the note by id and renders its full content: title, body, tags and archived state.
- Traces to. US-001-03.
- Verification. `NoteDetail.renders_full_note` (planned).
- API. `API-NOTES-045877`.

**WEB-NOTE-DETAIL-FR-002: Note not found**
- Given the note does not exist or was deleted, when the screen loads, then the web app shows the note-not-found state and renders no stale content.
- A 400 on this request (malformed id, US-003-06) renders the same state (`ASM-WEB-153`).
- Traces to. US-001-03.
- Verification. `NoteDetail.shows_not_found_on_404_and_400` (planned).
- API. `API-NOTES-045877`.

**WEB-NOTE-DETAIL-FR-003: Load failure other than not found**
- Given the request fails for any reason other than FR-002, when the screen loads, then the web app shows a user-facing error state with a retry action, never a blank screen.
- Traces to. US-001-03. The "error state instead of a blank screen" behaviour is taken from the analogous criterion in US-006-01 (`ASM-WEB-152`).
- Verification. `NoteDetail.shows_error_with_retry` (planned).
- API. `API-NOTES-045877`.

**WEB-NOTE-DETAIL-FR-004: Archive an active note**
- Given an active note is open, when the user clicks archive, then the web app calls the archive operation, shows the note as archived with content unchanged, and the note no longer appears in the default active list.
- Traces to. US-006-06, US-003-01.
- Verification. `NoteDetail.archive_marks_note_archived` (planned) and the E2E test `e2e.archive_removes_note_from_default_results` (planned).
- API. `API-NOTES-810640`.

**WEB-NOTE-DETAIL-FR-005: Archive of an already-archived note**
- Given the note is already archived, when the archive operation is requested, then the web app shows no failure, shows the note as archived and refreshes it.
- Both a 200 (US-003-01) and a 409 (contract, `ASM-BE-004`) are treated as "already archived".
- Traces to. US-003-01, `CON-WEB-002`, `ASM-WEB-008`.
- Verification. `NoteDetail.archive_409_reconciled_as_success` (planned).
- API. `API-NOTES-810640`.

**WEB-NOTE-DETAIL-FR-006: Archive of a missing note**
- Given the note no longer exists, when the user clicks archive, then the web app shows the note-not-found state, applies no local change, and no note is shown as archived.
- Traces to. US-003-01.
- Verification. `NoteDetail.archive_404_shows_not_found` (planned).
- API. `API-NOTES-810640`.

**WEB-NOTE-DETAIL-FR-007: Restore an archived note**
- Given an archived note is viewed, when the user clicks restore, then the web app calls the unarchive operation, shows the note as active with content unchanged, and the note reappears in the active list.
- Traces to. US-006-06, US-003-02.
- Verification. `NoteDetail.restore_marks_note_active` (planned) and the E2E test `e2e.restore_returns_note_to_default_results` (planned).
- API. `API-NOTES-917869`.

**WEB-NOTE-DETAIL-FR-008: Restore of an already-active or missing note**
- Given the note is already active, when restore is requested, then a 200 is shown as the active state with no failure (US-003-02).
- Given the note no longer exists, when restore is requested, then the note-not-found state is shown.
- Traces to. US-003-02.
- Verification. `NoteDetail.restore_idempotent_and_404` (planned).
- API. `API-NOTES-917869`.

**WEB-NOTE-DETAIL-FR-009: Archive state never disables edit or tag controls**
- Given a note is archived, when it is shown, then every tag control and every entry point to editing remains enabled, and archive status is unchanged by a tag operation.
- Traces to. `INV-WEB-004` (project spec), `BR-010`.
- Verification. `NoteDetail.archived_note_keeps_tag_controls_enabled` (planned).
- API. `API-NOTES-359413`, `API-NOTES-150096`.

**WEB-NOTE-DETAIL-FR-010: Add a tag**
- Given an existing note and a tag value entered, when the user submits it, then the web app calls the tag-add operation and shows the note's updated tag list from the response.
- Traces to. US-002-01.
- Verification. `NoteDetail.add_tag_shows_returned_list` (planned).
- API. `API-NOTES-359413`.

**WEB-NOTE-DETAIL-FR-011: Duplicate and mixed-case tags**
- Given the tag already exists on the note, or differs only by case, when the user adds it, then the web app renders exactly the tag list the response returns.
- It does not de-duplicate or re-case locally. Tags are de-duplicated and case-normalised by the server.
- Traces to. US-002-01, `BR-005`.
- Verification. `NoteDetail.duplicate_tag_renders_server_list` (planned).
- API. `API-NOTES-359413`.

**WEB-NOTE-DETAIL-FR-012: Add a tag to a missing note**
- Given the note does not exist, when a tag-add is requested, then the web app shows the note-not-found state and applies no local change.
- Traces to. US-002-01.
- Verification. `NoteDetail.add_tag_404_shows_not_found` (planned).
- API. `API-NOTES-359413`.

**WEB-NOTE-DETAIL-FR-013: Remove a tag**
- Given a note with an existing tag, when the user removes that tag, then the web app calls the tag-remove operation and the tag is no longer shown.
- The note and its other tags are unaffected.
- Traces to. US-002-02, `BR-006`.
- Verification. `NoteDetail.remove_tag_updates_list` (planned).
- API. `API-NOTES-150096`.

**WEB-NOTE-DETAIL-FR-014: Remove a tag that is not on the note**
- Given the tag is not on the note, when removal is requested, then the web app refetches the note and shows its current tag list with no failure state.
- This reconciliation is a default under `CON-WEB-151` and `ASM-WEB-156`. Neither reading of that contradiction is adopted as fact.
- Traces to. US-002-02.
- Verification. `NoteDetail.remove_absent_tag_reconciles` (planned).
- API. `API-NOTES-150096`.

**WEB-NOTE-DETAIL-FR-015: Failed or in-flight mutations**
- Given an archive, restore, tag-add or tag-remove request fails, when the response is received, then the displayed note is unchanged and a user-facing error with a retry action is shown.
- While one is in flight, the triggering control shows its Loading state and a second mutation on the same note cannot be submitted (`ASM-WEB-157`).
- Traces to. `web-app.state-management` cache policy item 4 and `INV-WEB-151`.
- Verification. `NoteDetail.failed_mutation_leaves_state_unchanged` and `NoteDetail.blocks_concurrent_mutation` (planned).
- API. `API-NOTES-810640`, `API-NOTES-917869`, `API-NOTES-359413`, `API-NOTES-150096`.

**WEB-NOTE-DETAIL-FR-016: Cache effects of a successful mutation**
- Given an archive, restore, tag-add or tag-remove succeeds, when the response is received, then the note-by-id cache entry is replaced by the returned note and all cached search results are invalidated.
- Traces to. `web-app.state-management` cache policy items 1 and 2, `BR-011`, US-006-06.
- Verification. `NoteDetail.mutation_replaces_note_and_invalidates_search` (planned).
- API. `API-NOTES-810640`, `API-NOTES-917869`, `API-NOTES-359413`, `API-NOTES-150096`.

**Invariants**
- `INV-WEB-151`: The screen displays the archived state and tag list from the last server response only; it never shows a locally computed value. Source: `web-app.state-management` policy items 1 and 4.
- `INV-WEB-152`: Archive and restore change archive state only; title, body and tags are unchanged by them. Source: `BR-009`, US-003-01, US-003-02.

**Story trace (owned stories)**

| Story | Requirements |
|---|---|
| US-001-03 | FR-001 to FR-003 |
| US-002-01 | FR-010 to FR-012 |
| US-002-02 | FR-013, FR-014 |
| US-003-01 | FR-004 to FR-006 |
| US-003-02 | FR-007, FR-008 |
| US-006-06 | FR-004, FR-007. The pin clause belongs to `search`. |
<!-- /SECTION: web-app.note-detail.requirements -->
<!-- SECTION: web-app.note-detail.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/notes/:id` | `NoteDetailScreen` | app layout (authenticated) | `BLOCKED`: whether notes endpoints require a session is undecided (`GAP-WEB-001`, `Q-BE-003`). Per-note access depends on `Q-BE-002` (`GAP-WEB-011`). |

The path is a proposal (`ASM-WEB-001`). The route and layout come from `web-app.routing`.

**Navigation**
- *Outgoing.* The design records two `ON_CLICK` transitions from `NoteDetailScreen` to `SearchScreen` (`screens-inventory.md`), which is `/notes`. Which controls trigger them is unknown (`GAP-WEB-155`).
- *Incoming.* `search` opens a result here, and `editor` lands here after creating a note (`web-app.module-map`, US-006-02). Both are owned elsewhere.
- *Not specified.* There is no input for navigating from this screen to `/notes/:id/edit` (`GAP-WEB-154`).

**Unauthenticated redirect.** This is the project-level rule in `web-app.routing`. The protected-route rule is `BLOCKED`, so it is not restated here.
<!-- /SECTION: web-app.note-detail.routes -->
<!-- SECTION: web-app.note-detail.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `note-detail`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.note-detail.components -->
<!-- SECTION: web-app.note-detail.states | status: DRAFT-UNVERIFIED -->
## States

The screen has one route, `/notes/:id`. The design's variants are Default, Empty, Error and Filled; their exact meaning is not stated (`ASM-WEB-152`, `GAP-WEB-152`).

| State | Trigger | Presentation |
|---|---|---|
| Loading | Note request in flight | No design frame (`GAP-WEB-152`). No content is shown until the response arrives. |
| Populated | Note returned | Full content per FR-001, with archive and restore control reflecting the returned archived state (FR-004, FR-007). Variant `Filled` (`ASM-WEB-152`). |
| Empty | Note returned with no tags | The tag region shows `Empty/State`; tag add remains available (`ASM-WEB-152`). A note's title and body are never empty (`BR-001`). |
| Error: not found | 404, or 400 on load (FR-002); 404 on any mutation (FR-006, FR-008, FR-012) | Note-not-found message. No stale content and no local change. |
| Error: request failure | Any other load failure (FR-003) | User-facing error with retry. |
| Error: action failure | Failed archive, restore, tag-add or tag-remove (FR-015) | Displayed note unchanged. Error with retry; the triggering control returns from Loading to Default. |
| Field error | Tag-add rejected as invalid (400) | Error state on `Input/Text`, with the server's field-level message (`BR-004`, NFR-011). |
| Permission-denied | Unknown | `BLOCKED`. Whether this route is permission-gated, and whether denial renders "not allowed" or "does not exist", is undecided (`GAP-WEB-001`, `GAP-WEB-011`). It is not specified until the backend decides. |

**Partial-failure: not applicable.** Every operation on this screen acts on a single note, and tag-add and tag-remove take one tag per request. There is no multi-item operation, so no `GAP-` is raised.

**Offline** is not a state on the web (`web-app` W8).
<!-- /SECTION: web-app.note-detail.states -->
<!-- SECTION: web-app.note-detail.api-integration | status: DRAFT-UNVERIFIED -->
## API integration

Reference only (core rule 6). Request and response definitions live in `backend-api.md` under the pinned contract `sha256:8e424bb2b40a`.

| Operation | Pinned ID | Used by |
|---|---|---|
| View a note | `API-NOTES-045877` | FR-001 to FR-003 |
| Archive | `API-NOTES-810640` | FR-004 to FR-006 |
| Restore (unarchive) | `API-NOTES-917869` | FR-007, FR-008 |
| Add a tag | `API-NOTES-359413` | FR-010 to FR-012 |
| Remove a tag | `API-NOTES-150096` | FR-013, FR-014 |

**Not used by this module.**
- Editing and pin or unpin (`API-NOTES-847551`) belong to `editor` and `search`.
- Create (`API-NOTES-232390`) belongs to `editor`.
- Search (`API-SEARCH-580505`) belongs to `search`.
- The `API-RESIDENCY-*` and `API-CONTRACTDOCS-*` operations are not used by any web requirement.

**Error handling.** Handling of the error `code` and status for these operations follows `backend.error-model`, a proposal under `ASM-BE-006`. Several refusals share a status, so the web app branches on `code`.

**Credential and transport.** The credential a call carries is undecided (`GAP-WEB-001`) and is specified nowhere in this module.
<!-- /SECTION: web-app.note-detail.api-integration -->
<!-- SECTION: web-app.note-detail.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side | Source |
|---|---|---|---|
| Tag value is required; an empty value is not submitted | yes | yes (field-level 400) | `API-NOTES-359413` (tag required), `BR-004`, NFR-011 |
| Tag case and duplicates | no client rule; the response list is rendered as returned | yes (lowercase de-duplication) | `BR-005`, US-002-01 |
| Tag length, character set, maximum count | none (`GAP-WEB-157`) | not stated | `GAP-BE-008` |
| Note id in the URL | no format check | yes (malformed id returns 400) | US-003-06, `API-NOTES-045877` |
| Archive and restore preconditions | none; no archived-state check blocks edit or tag | state transitions are idempotent or return a consistent error | `BR-010`, `BR-012`, `INV-WEB-004` |
| Tag removal when the tag is absent | none | outcome unsettled (`CON-WEB-151`) | US-002-02 |

Server field-level errors are shown inline on the field (`BR-004`, NFR-011, US-006-02 and US-006-03 for the pattern).
<!-- /SECTION: web-app.note-detail.validation -->
<!-- SECTION: web-app.note-detail.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input names an accessibility standard, level or verification method (`GAP-WEB-007`). Focus management, keyboard paths, live regions and form-error exposure are specified only once a target exists, as `web-app.accessibility` states.

**What the inputs do establish, without a target:**
- `Input/Text` has Focused and Error states (`components-inventory.md`).
- `Button/Primary` has a Loading and a Disabled state.
- Field-level errors appear inline (`BR-004`).
- Each mutation on this screen produces a visible result: an updated archive state, an updated tag list, or an error.

**Unblocked by:** a requirement naming the standard, level and verification method (`GAP-WEB-007`).
<!-- /SECTION: web-app.note-detail.accessibility -->
<!-- SECTION: web-app.note-detail.i18n | status: BLOCKED -->
## Internationalisation

**Blocked.** No input supplies copy strings, copy keys or locales (`GAP-WEB-016`).

**Messages this module needs once copy exists:**
- note-not-found;
- generic load failure;
- generic action failure;
- archived indicator;
- archive and restore control labels;
- tag-add label and placeholder;
- tag-remove label;
- empty-tags message;
- field-level tag error.

No key or text is invented.

**Unblocked by:** a copy deck with keys and locales.
<!-- /SECTION: web-app.note-detail.i18n -->
<!-- SECTION: web-app.note-detail.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode` is `FULL-FROM-BRIEF`: the design brief is present and Figma MCP is not connected. Every reference below is an unverified claim from `design-brief.md`.

| Reference | Value | Verification |
|---|---|---|
| Screen | `NoteDetailScreen` ("Note Detail", module M3) | Unverified; from `screens-inventory.md` |
| Variants | Default, Empty, Error, Filled | Unverified; semantics undefined (`GAP-WEB-152`) |
| Components on screen | `Button/Primary`, `Empty/State`, `Input/Text` | Unverified |
| Outgoing transitions | `ON_CLICK→SearchScreen` (twice) | Unverified; triggering controls unknown (`GAP-WEB-155`) |
| Stories mapped to this screen | US-001-03, US-002-01, US-002-02, US-003-01, US-003-02 (and US-001-01 and others, owned elsewhere or not web) | Unverified; from `stories-map.md` |
| Frame IDs | None supplied | `GAP-WEB-005` |
| Token names | 71 IDs in the index, none bound to this screen's components | `GAP-WEB-005` |

`stories-map.md` does not map US-006-06 to any screen, so the archive and restore controls have no design frame (`GAP-WEB-153`).
<!-- /SECTION: web-app.note-detail.design-refs -->
<!-- SECTION: web-app.note-detail.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are proposed names, marked `(planned)`.

**Component**
- `NoteDetail.renders_full_note`
- `NoteDetail.shows_not_found_on_404_and_400`
- `NoteDetail.shows_error_with_retry`
- `NoteDetail.archive_marks_note_archived`
- `NoteDetail.archive_409_reconciled_as_success`
- `NoteDetail.archive_404_shows_not_found`
- `NoteDetail.restore_marks_note_active`
- `NoteDetail.restore_idempotent_and_404`
- `NoteDetail.archived_note_keeps_tag_controls_enabled`
- `NoteDetail.add_tag_shows_returned_list`
- `NoteDetail.duplicate_tag_renders_server_list`
- `NoteDetail.add_tag_404_shows_not_found`
- `NoteDetail.remove_tag_updates_list`
- `NoteDetail.remove_absent_tag_reconciles`
- `NoteDetail.failed_mutation_leaves_state_unchanged`
- `NoteDetail.blocks_concurrent_mutation`
- `NoteDetail.mutation_replaces_note_and_invalidates_search`

**E2E**
- `e2e.archive_removes_note_from_default_results`: archive on this screen, then confirm the note is absent from default search results.
- `e2e.restore_returns_note_to_default_results`: restore, then confirm it is present again.
- Both use search with a keyword or tag, because there is no list operation (`GAP-WEB-002`).
- A check against the default active list is `BLOCKED` until `GAP-WEB-002` closes.

**Contract**
- For each operation in the integration section, assert that the fields this screen displays are present.
- Assert the documented success and error statuses for archive, unarchive, tag-add and tag-remove, including the tag-absent case (`CON-WEB-151`).

**Accessibility**
- `BLOCKED` until a target standard exists (`GAP-WEB-007`).
<!-- /SECTION: web-app.note-detail.test-plan -->
<!-- SECTION: web-app.note-detail.gaps | status: DERIVED -->
## Gaps, contradictions, assumptions

Existing project-level items are cited, not re-minted: `GAP-WEB-001`, `GAP-WEB-002`, `GAP-WEB-005`, `GAP-WEB-007`, `GAP-WEB-011`, `GAP-WEB-013`, `GAP-WEB-015`, `GAP-WEB-016`, `CON-WEB-002`, `ASM-WEB-001`, `ASM-WEB-008`, `INV-WEB-004`.

**Contradiction (non-critical; neither side resolved)**
- `CON-WEB-151`: US-002-02 says removing a tag the note does not have returns 415 ("tag not found on note"). The contract lists only 400 and 404 for `API-NOTES-150096`, and no 415.
  - Consequence of reading the story: a tag-absent response is a distinct error that needs its own copy.
  - Consequence of reading the contract: it arrives as a 404 that is indistinguishable from note-not-found by status alone.
  - Default used: reconcile by refetching the note (`ASM-WEB-156`).

**Questions (degradable; defaults taken, unattended)**
- `Q-WEB-151` (degradable): Are tag add and remove controls on `NoteDetailScreen`? Default: yes, per the design mapping (`ASM-WEB-151`).
- `Q-WEB-152` (degradable): After archive or restore, does the user stay on the screen? Default: stay (`ASM-WEB-154`).

**Assumptions**
- `ASM-WEB-151`: This module specifies tag add and remove. The stories are assigned to it and the design maps them to `NoteDetailScreen`, although `web-app.coverage` lists them as "Not web" (`GAP-WEB-015`). Confirmed by a web story.
- `ASM-WEB-152`: Variant meaning is Filled for populated, Empty for a note with no tags, and Error for failure. The load-failure error state follows US-006-01's "error state instead of a blank screen". Confirmed by design annotation.
- `ASM-WEB-153`: A 400 on the note request is shown as not found, because the user cannot correct an id from the URL.
- `ASM-WEB-154`: After archive or restore the user stays on the screen and sees the updated state; no navigation is specified (`Q-WEB-152`).
- `ASM-WEB-155`: Tag entry uses `Input/Text` and archive, restore and tag submit use `Button/Primary`, as the only action and entry components listed for this screen.
- `ASM-WEB-156`: A tag removal that finds the tag already absent is shown as the refreshed tag list, without an error (`CON-WEB-151`).
- `ASM-WEB-157`: Only one mutation on a note is in flight at a time, enforced through the `Button/Primary` Loading state.

**Gaps (each names its closing artefact)**
- `GAP-WEB-152`: Variant semantics (Default, Empty, Error, Filled) and the loading presentation are undefined. Closing artefact: design annotation or Figma access.
- `GAP-WEB-153`: No design components or frames exist for the tag list or chip, tag remove control, archived indicator, restore control, error or toast container, or loading placeholder. No frame maps US-006-06. Closing artefact: design frames and components.
- `GAP-WEB-154`: No entry point from this screen to the edit route. Closing artefact: flow definition (related to `GAP-WEB-013`).
- `GAP-WEB-155`: The two `ON_CLICK→SearchScreen` transitions have no named trigger. Closing artefact: flow definition.
- `GAP-WEB-156`: **Backend must supply** the status and code returned when removing a tag that is not on the note, and acceptance criteria for tag removal (also `GAP-BE-007`). Closing artefact: a contract decision or acceptance criterion.
- `GAP-WEB-157`: No constraint on tag length, character set or count (`GAP-BE-008`), so the client has only a required-value rule. Closing artefact: field constraints.
- `GAP-WEB-158`: Beyond title, body, tags and archived state, whether this screen shows the timestamps or pinned state, and in what format, is not stated. Closing artefact: design frame or requirement.
<!-- /SECTION: web-app.note-detail.gaps -->
