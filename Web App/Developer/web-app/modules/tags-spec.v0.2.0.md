---
id: agent-developer/web_app/web-app/modules/tags-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/tags-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/tags-SPEC.md
---

<!-- SECTION: web-app.tags.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: tags
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-003, GAP-WEB-011, GAP-WEB-223]
contract_pin: "sha256:9550f0d579d8"
scope: []
```

- `scope` is empty because this module owns no route. The project spec routes none of its screens (`web-app.routing`, `web-app.module-map`, `GAP-WEB-003`).
- `spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma is not connected (`RUN-CONTEXT.inputs.figma_mcp`).
- `blocked_by` follows from the same inputs:
  - `GAP-WEB-003` is the missing remove-tag and list-tags operations.
  - `GAP-WEB-011` is the absence of a web story backing the screen.
  - `GAP-WEB-223` is the undefined purpose of the design's screen transitions.
<!-- /SECTION: web-app.tags.frontmatter -->
<!-- SECTION: web-app.tags.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Responsibility.** The design brief assigns `NoteTagsScreen` ("Note Tags") to module M2. Its variants are Default, Error and Filled, and it uses `Input/Text` (`design-brief.md`, screens inventory). The module is meant to hold tag management for a note.

**Current state.** No EP-006 story requires this screen, and the project spec leaves it unrouted (`web-app.module-map`, `GAP-WEB-003`, `GAP-WEB-011`). Nothing here is specified as buildable behaviour until a web story and the missing operations exist (`Q-WEB-205`, `ASM-WEB-218`).

**Not responsible for:**
- The search tag filter. US-006-04 and the tag parameter of the search call belong to the `search` module.
- Pin, archive and restore of notes (`search`, `notes`, `archive`).
- Creating notes with initial tags. `NoteEditorScreen` is owned by `notes`.
- Backend tag rules and storage.
- The tag-picking control that feeds the search filter (`GAP-WEB-003`, project spec).
<!-- /SECTION: web-app.tags.purpose -->
<!-- SECTION: web-app.tags.requirements | status: BLOCKED -->
## Requirements

**Blocked. No functional requirement is written.** Every requirement must trace to a story or requirement (core rule 7), and none does:
- `stories-map.md` attaches US-002-01, US-002-02, US-002-05 and US-003-05 to `NoteTagsScreen`, but those stories are written "via the API". Their actors are team members calling the API, not a web screen (`CON-WEB-204`, `GAP-WEB-011`).
- The only web stories touching tags are US-006-04, which belongs to `search`, and none asks for tag management in the browser.
- The server rule that tags are lowercase and de-duplicated per note (`requirements.md` BR-005) is backend behaviour, not a web requirement.

Unblocked by:
1. A web story for tag add, remove or list in the browser (`Q-WEB-205`).
2. Contract operations for remove and list (`GAP-WEB-003`).

No `WEB-TAGS-FR-nnn` ids were minted.
<!-- /SECTION: web-app.tags.requirements -->
<!-- SECTION: web-app.tags.routes | status: DERIVED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| none | `NoteTagsScreen` | n/a | n/a |

- **No route.** `web-app.routing` states: "Not routed (`GAP-WEB-003`)". This module adds none.
- **Permission.** The protected-route rule is itself `BLOCKED` (`GAP-WEB-001`), so no permission can be stated.
- **Design transitions.** The design lists two `ON_CLICK→SearchScreen` transitions out of `NoteTagsScreen` (`screens-inventory.md`). What triggers them is not stated (`GAP-WEB-223`). No navigation is specified from them.
<!-- /SECTION: web-app.tags.routes -->
<!-- SECTION: web-app.tags.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `tags`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.tags.components -->
<!-- SECTION: web-app.tags.states | status: BLOCKED -->
## States

**Blocked.** The screen has no route and no requirement, so loading, empty, populated and error states cannot be specified per route.

What the inputs do give:
- Design variants for `NoteTagsScreen`: Default, Error and Filled. There is no Empty variant and no Loading design (`screens-inventory.md`).
- Meaning of these variants is not stated. Mapping them to states would be a guess.

**Conditional states.**
- *Permission-denied* is conditional on permission-gated routes. This module has no route, so it is omitted, and no gap is raised for that.
- *Partial-failure* is conditional on a multi-item operation. `API-NOTES-359413` accepts a tags array, but the contract defines only 400 and 404 for it and no per-item result. No partial state is asserted.

Unblocked by: a routed screen, a web story, and design frames for loading, empty and error (`GAP-WEB-003`, `GAP-WEB-005`).
<!-- /SECTION: web-app.tags.states -->
<!-- SECTION: web-app.tags.api-integration | status: DERIVED -->
## API integration

Reference only (core rule 6). Request and response shapes are not restated.

| Operation needed | Contract ID | Status |
|---|---|---|
| Add tags to a note | `API-NOTES-359413` | Exists. |
| Read a note's current tags | `API-NOTES-045877` | Exists. |
| Remove a tag from a note | none | Absent (`GAP-WEB-003`). |
| List distinct tags | none | Absent (`GAP-WEB-003`). |

`INV-WEB-206`: The module never calls an operation that is not in the pinned contract. Remove and list stay unimplemented until the contract defines them.

The error set of `API-NOTES-359413` is in the contract. Retry and detail values are unstated (`GAP-WEB-004`).
<!-- /SECTION: web-app.tags.api-integration -->
<!-- SECTION: web-app.tags.validation | status: DRAFT-UNVERIFIED -->
## Validation

**Client rules: none asserted.** Tag rules (allowed characters, length, maximum count) are unstated by any input. The backend records the same gap (`GAP-BE-003`, cited in `backend-api.md`).

**Enforced server-side, per input:**
- Tags are case-insensitive and de-duplicated per note, normalised to lowercase before persisting (`requirements.md` BR-005).
- Adding a tag already on the note creates no duplicate (`stories.md` US-002-01, as an API behaviour).

The field-detail shape of a 400 is unstated (`GAP-WEB-004`).

Unblocked by: backend tag rules and the error-detail shape.
<!-- /SECTION: web-app.tags.validation -->
<!-- SECTION: web-app.tags.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** The project-level accessibility section is itself `BLOCKED` (`GAP-WEB-008`): no standard or conformance level is stated. Focus management, keyboard paths and live regions for a screen with no route or components beyond `Input/Text` would be invention.

Unblocked by: a stated accessibility standard, and a designed screen.
<!-- /SECTION: web-app.tags.accessibility -->
<!-- SECTION: web-app.tags.i18n | status: BLOCKED -->
## i18n

**Blocked.** No input states locales or copy. No copy keys can be listed for a screen with no requirements (`GAP-WEB-225`).

Unblocked by: a locale list and copy decisions.
<!-- /SECTION: web-app.tags.i18n -->
<!-- SECTION: web-app.tags.design-refs | status: DRAFT-UNVERIFIED -->
## Design refs

`spec_mode` is `FULL-FROM-BRIEF`. Every item below comes from `design-brief.md` and is an unverified claim. No frame IDs were supplied and Figma is not connected.

| Item | Value | Verification |
|---|---|---|
| Screen | `NoteTagsScreen` ("Note Tags") | unverified |
| Module label | M2 | unverified |
| Variants | Default, Error, Filled | unverified |
| Components | `Input/Text` | unverified |
| Outgoing transitions | `ON_CLICK→SearchScreen` (twice) | unverified; purpose undefined (`GAP-WEB-223`) |
| Frame ID | not supplied | n/a |
<!-- /SECTION: web-app.tags.design-refs -->
<!-- SECTION: web-app.tags.test-plan | status: BLOCKED -->
## Test plan

**Blocked.** There are no requirements to verify, so no test is named for one.

Plan once unblocked:
- *Contract:* `API-NOTES-359413` against the pinned contract.
- *Component:* `Input/Text` states on `NoteTagsScreen`.
- *E2E:* tag add and remove through the browser.
- *A11y:* per the standard once stated (`GAP-WEB-008`).

Unblocked by: the web story and the operations named in `web-app.tags.requirements`.
<!-- /SECTION: web-app.tags.test-plan -->
<!-- SECTION: web-app.tags.gaps | status: DRAFT-UNVERIFIED -->
## Gaps

**Inherited from the project spec (cited, not redefined):**
- `GAP-WEB-001`: session model.
- `GAP-WEB-003`: remove-tag and list-tags operations, and no tag-picking control.
- `GAP-WEB-004`: error detail and retry values.
- `GAP-WEB-005`: missing design frames.
- `GAP-WEB-008`: accessibility standard.
- `GAP-WEB-011`: no web story backs the API and infrastructure stories attached to web screens.

**New gaps**

| ID | Missing | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-WEB-223` | What the two `ON_CLICK→SearchScreen` transitions on `NoteTagsScreen` trigger, and how the screen is reached. | `routes`, `states` | Design flow annotations or a web story. |
| `GAP-WEB-224` | Token bindings for `Input/Text` on this screen. | `components` | Design annotations or Figma verification. |
| `GAP-WEB-225` | Locales and copy for any tag UI. | `i18n` | A locale and copy decision. |

**Contradiction (not Critical)**

| ID | Claims | Consequence of each reading | Reading used |
|---|---|---|---|
| `CON-WEB-204` | `stories-map.md` attaches US-002-01, US-002-02, US-002-05 and US-003-05 to `NoteTagsScreen`. `stories.md` writes each of them as an API behaviour ("via the API", "regardless of its archive state"). | Design reading: the screen needs add, remove and list requirements. Story reading: it needs none, because no web story backs it. | Story reading. No requirements are written. |

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-217` | Token bindings for `Input/Text` would come from the border, surface and text tokens named in `tokens-index.md`. No binding is asserted here. | Figma verification. |
| `ASM-WEB-218` | The module stays unrouted and unimplemented until `Q-WEB-205` is answered. | A routing decision. |

**Question**

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-205` | degradable | Should the web app offer tag add, remove and list for a note, and on which route? | No; the module is not routed (`ASM-WEB-218`). |
<!-- /SECTION: web-app.tags.gaps -->
