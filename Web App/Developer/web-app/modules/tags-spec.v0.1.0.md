---
id: agent-developer/web_app/web-app/modules/tags-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/tags-SPEC.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/tags-SPEC.md
---

<!-- SECTION: web-app.tags.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: tags
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-003, GAP-WEB-202, CON-WEB-201]
contract_pin: "sha256:ceedbd350666"
scope: []
```

`scope` is empty because the project spec routes no screen for this module (`web-app.routing`, row `TagManagementScreen`: "Not routed"). `spec_mode` follows from the design brief being present and Figma not connected (`RUN-CONTEXT.inputs.figma_mcp`).
<!-- /SECTION: web-app.tags.frontmatter -->
<!-- SECTION: web-app.tags.purpose | status: DERIVED -->
## Purpose

**Intended responsibility.** The `tags` module (design label M2) would own `TagManagementScreen`, where a Team member views and manages the tags in use. Source: the module map in `web-app.module-map`, and `design-brief.md` (`screens-inventory.md`, `TagManagementScreen`, module M2).

**Current state.** The module is a placeholder. No web story requires it and the contract lacks two of its three tag operations (`web-app.module-map`, row `tags`; `GAP-WEB-003`, `GAP-WEB-202`). Requirements, routes and states below are `BLOCKED` for that reason, and nothing has been invented to fill them.

**Not responsible for:**

- **Tag filtering in search.** The `search` module owns choosing tags to filter by (US-006-04) and the `API-SEARCH-580505` call.
- **Archive and restore.** Owned by `notes` and `archive`.
- **Tag validation rules.** These are a backend decision, still open upstream (`backend.coverage`, `GAP-BE-010`).
- **The Residency Report and Scope Documentation screens.** These are separate unspecified modules (`Q-WEB-001`).
<!-- /SECTION: web-app.tags.purpose -->
<!-- SECTION: web-app.tags.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** No `WEB-TAGS-FR-nnn` is written. Every requirement must trace to a story, and none does:

- The EP-006 web stories (US-006-01 to US-006-06) contain no tag add, remove or list behaviour. The only tag behaviour is selecting a tag filter in search, which US-006-04 assigns to the `search` module.
- The stories the design maps to `TagManagementScreen` are US-002-01, US-002-02, US-002-05 and US-003-05 (`stories-map.md`). They are written for API consumers ("via the API"), not for the web app (`CON-WEB-201`).

A requirement written from the design alone would be an invention.

**Unblocked by:** a web story for tag add, remove and list, or a decision to drop the module (`Q-WEB-201`).
<!-- /SECTION: web-app.tags.requirements -->
<!-- SECTION: web-app.tags.routes | status: DERIVED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| none | `TagManagementScreen` | n/a | n/a |

No route is assigned. The project spec lists the screen as "Not routed: no web story, and the contract has no list-tags or remove-tag operation" (`web-app.routing`). The permission model is also undecided (`GAP-WEB-001`, `Q-BE-003`).

**Navigation recorded in the design.** From `TagManagementScreen`: `ON_CLICK` → `SearchScreen` (twice) and `ON_CLICK` → `ArchiveScreen` (`screens-inventory.md`). These are not routed here, because the source screen has no route.
<!-- /SECTION: web-app.tags.routes -->
<!-- SECTION: web-app.tags.components | status: DRAFT-UNVERIFIED -->
## Components

The design maps two components to `TagManagementScreen` (`screens-inventory.md`, `components-inventory.md`). Names, variants and states are unverified claims (`FULL-FROM-BRIEF`).

| Component | Role | Variants | States |
|---|---|---|---|
| `Input/Text` | entry | 5 | Default, Focused, Filled, Error, Disabled |
| `Card/Item` | container | 1 | Default |

**Token bindings: not stated.** No input binds a token to either component, and token values are not supplied. The names in `tokens-index.md` are not assigned to components here (`GAP-WEB-204`). The inventory has no tag chip, tag-removal control, pager or message component (`GAP-WEB-004`, `GAP-WEB-008`).
<!-- /SECTION: web-app.tags.components -->
<!-- SECTION: web-app.tags.states | status: BLOCKED -->
## States

**BLOCKED.** the run did not produce this section for module `tags`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.tags.states -->
<!-- SECTION: web-app.tags.api-integration | status: DERIVED -->
## API integration

| Operation | Pinned ID | Status |
|---|---|---|
| Add one or more tags to a note | `API-NOTES-359413` | exists in the contract |
| Remove a tag from a note | none | absent from the contract (`GAP-WEB-003`) |
| List distinct tags | none | absent from the contract (`GAP-WEB-003`) |

Reference only; no request or response shape is restated. A tag-filtered search is `API-SEARCH-580505`, used by the `search` module.
<!-- /SECTION: web-app.tags.api-integration -->
<!-- SECTION: web-app.tags.validation | status: DRAFT-UNVERIFIED -->
## Validation

**Server-side rule that applies.** Tags are case-insensitive and de-duplicated per note, and tag writes normalise to lowercase (`requirements.md` BR-005).

**Client rules: none specified.** Tag case, allowed characters, length and the maximum per note are unstated (`backend.coverage`, `GAP-BE-010`; `GAP-WEB-204`). The client therefore has no rule to mirror, and the server is the only enforcement stated.
<!-- /SECTION: web-app.tags.validation -->
<!-- SECTION: web-app.tags.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** No accessibility standard or level is stated for the web app, and the project-level `web-app.accessibility` section was not produced. With no route or interaction defined for this module, focus management, keyboard paths, live regions and form errors have nothing to attach to (`GAP-WEB-205`).

**Unblocked by:** a stated standard and level, and a routed `TagManagementScreen`.
<!-- /SECTION: web-app.tags.accessibility -->
<!-- SECTION: web-app.tags.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input supplies copy keys or locales for this module, or for the web app generally (`GAP-WEB-205`).

**Unblocked by:** a locale list and copy for the tag screen.
<!-- /SECTION: web-app.tags.i18n -->
<!-- SECTION: web-app.tags.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

**Mode.** `FULL-FROM-BRIEF`. Everything here comes from `design-brief.md`. Figma is not connected, so nothing is verified.

| Item | Value | Verification |
|---|---|---|
| Screen | `TagManagementScreen` — "Tag Management", module M2 | unverified |
| Screen variants | Default, Error, Filled | unverified |
| Components | `Input/Text`, `Card/Item` | unverified |
| Outgoing transitions | `ON_CLICK` → `SearchScreen` (×2), → `ArchiveScreen` | unverified |
| Stories mapped by the design | US-002-01, US-002-02, US-002-05, US-003-05 (`stories-map.md`) | unverified |
| Frame IDs | none supplied | n/a |
<!-- /SECTION: web-app.tags.design-refs -->
<!-- SECTION: web-app.tags.test-plan | status: BLOCKED -->
## Test plan

**BLOCKED.** Tests verify requirements, and this module has none (`web-app.tags.requirements`). No component, E2E or accessibility test is proposed.

**Contract test that would apply once unblocked:** `API-NOTES-359413`, the only existing tag operation.

**Unblocked by:** requirements for this module.
<!-- /SECTION: web-app.tags.test-plan -->
<!-- SECTION: web-app.tags.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, questions and assumptions

### Inherited from the project spec (referenced, not redefined)

`GAP-WEB-001`, `GAP-WEB-003`, `GAP-WEB-004`, `GAP-WEB-008`, `GAP-WEB-009`, `Q-WEB-001`.

### Gaps

| ID | What is missing | Degrades | Fallback used | Closing artefact |
|---|---|---|---|---|
| `GAP-WEB-202` | No web story requires tag add, remove or list. | `requirements`, `test-plan` | Both are `BLOCKED`. | A web story for tag management, or a decision to drop the module. |
| `GAP-WEB-203` | The design has no loading, empty or permission-denied variant for `TagManagementScreen`, and no frame IDs. | `states`, `design-refs` | Variants are listed as-is, with no mapping. | Design frames or annotations for those states. |
| `GAP-WEB-204` | Tag input rules and component token bindings are not supplied. | `components`, `validation` | No binding and no client rule is asserted. | Tag rules from product (`GAP-BE-010`) and design token assignments. |
| `GAP-WEB-205` | No accessibility standard, copy keys or locales are supplied for this module. | `accessibility`, `i18n` | Both are `BLOCKED`. | A platform or NFR statement, and a copy deck. |

### Contradictions

| ID | Claim A | Claim B | Consequence |
|---|---|---|---|
| `CON-WEB-201` | The project spec says the `tags` module has no web story (`web-app.module-map`). | The design maps US-002-01, US-002-02, US-002-05 and US-003-05 to `TagManagementScreen` (`stories-map.md`). Those stories are written "via the API". | If the design wins, the screen needs requirements and routes. If the stories win, it is out of scope. Non-critical. Reading adopted: the stories; the module stays unspecified. Not resolved. |

### Questions

| ID | Class | Question | Default |
|---|---|---|---|
| `Q-WEB-201` | degradable | Does the web app include a tag management screen? | No: the module stays unspecified (`ASM-WEB-201`). Taken silently, as the run is unattended. |

### Assumptions

| ID | Assumption | What would confirm it |
|---|---|---|
| `ASM-WEB-201` | The module is retained in the module map as a placeholder, with its sections emitted as `BLOCKED` or minimal. | An answer to `Q-WEB-201`. |
<!-- /SECTION: web-app.tags.gaps -->
