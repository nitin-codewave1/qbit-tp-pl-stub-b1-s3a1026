---
id: agent-developer/web_app/web-app/modules/scope-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/scope-docs-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/scope-docs-SPEC.md
---

<!-- SECTION: web-app.scope-docs.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: scope-docs
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [Q-WEB-001, GAP-WEB-302, CON-WEB-301]
contract_pin: "sha256:ceedbd350666"
scope: []
```

- `scope` is empty because no route is assigned to this module. The project spec's `web-app.routing` lists `ScopeDocumentationScreen` as "none … Not routed (`Q-WEB-001`, `ASM-WEB-005`)".
- `spec_mode` is `FULL-FROM-BRIEF`. `design-brief.md` is present and `RUN-CONTEXT.inputs.figma_mcp` is `not-connected` (core 4.5).
- `blocked_by` references `Q-WEB-001` from the project spec. `GAP-WEB-302` and `CON-WEB-301` are minted in this module.
<!-- /SECTION: web-app.scope-docs.frontmatter -->
<!-- SECTION: web-app.scope-docs.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Owns.** Per the project spec's `web-app.module-map`, `scope-docs` is design label M5 and owns one screen, `ScopeDocumentationScreen`. It has no web stories and no routes.

**Current state.** The project spec marks the module as having no spec until `Q-WEB-001` is answered. Its default (`ASM-WEB-005`) is that the screen is not part of the web app. This document applies that default as `ASM-WEB-301`. Under it, the module contains no web behaviour, and every behavioural section below is `BLOCKED` or states that nothing is specified.

**Not responsible for:**
- Enforcing the API scope, namely the 5 endpoint groups, the attachment ban, the real-time ban and the mobile-endpoint ban. These are backend behaviours (`requirements.md` BR-015, BR-016, BR-017, BR-018; NFR-010).
- Publishing, generating or serving the API document or its Scope section. The contract lists `API-CONTRACTDOCS-304307`, but no web requirement uses it.
- `ArchiveScreen` and `SearchScreen`. The design shows transitions to them from `ScopeDocumentationScreen` (`screens-inventory.md`), but they belong to the `archive` and `search` modules and are only referenced here.
- Any backend change. Needs go to the backend lane, not into this document.
<!-- /SECTION: web-app.scope-docs.purpose -->
<!-- SECTION: web-app.scope-docs.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** the run did not produce this section for module `scope-docs`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.scope-docs.requirements -->
<!-- SECTION: web-app.scope-docs.routes | status: BLOCKED -->
## Routes

**BLOCKED.** No route, layout or permission is defined.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| none | `ScopeDocumentationScreen` | none | none |

- No input supplies a path for this screen, and the project spec routes none (`web-app.routing`).
- The protected-route rule is itself `BLOCKED` at project level (`GAP-WEB-001`).

**Unblocked by:** `Q-WEB-001` answered "yes", then a route path and layout in the project spec's `web-app.routing`.
<!-- /SECTION: web-app.scope-docs.routes -->
<!-- SECTION: web-app.scope-docs.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `scope-docs`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.scope-docs.components -->
<!-- SECTION: web-app.scope-docs.states | status: BLOCKED -->
## States

**BLOCKED.** No state can be specified without knowing what the screen shows.

- The design lists two variants for `ScopeDocumentationScreen`, `Default` and `Error` (`screens-inventory.md`).
- There is no input about loading, empty or populated content, nor about the data source. `Default` and `Error` are therefore recorded here as existing, with no meaning attached.
- Permission-denied does not apply, because no route or permission gate exists. Partial-failure does not apply, because no multi-item operation is attributed to this module.

**Unblocked by:** the content definition under `GAP-WEB-303` and a decision on `Q-WEB-001`.
<!-- /SECTION: web-app.scope-docs.states -->
<!-- SECTION: web-app.scope-docs.api-integration | status: BLOCKED -->
## API integration

**BLOCKED.** No operation is bound to this module.

- `API-CONTRACTDOCS-304307` exists in the contract, but no input says the screen calls it. Binding it would be a guess (`GAP-WEB-303`).
- Nothing is restated from the contract (core 6).

**Unblocked by:** a web story or design annotation naming the operation this screen calls.
<!-- /SECTION: web-app.scope-docs.api-integration -->
<!-- SECTION: web-app.scope-docs.validation | status: DRAFT-UNVERIFIED -->
## Validation

No client validation rule is specified.

- `screens-inventory.md` lists no `Input/Text` or other entry component on this screen, and no requirement defines a field.
- No server-side rule is cited.
- This is unverified: it depends on the design brief, which Figma has not checked (`ASM-WEB-301`).
<!-- /SECTION: web-app.scope-docs.validation -->
<!-- SECTION: web-app.scope-docs.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** Focus management, keyboard paths, live regions and form errors depend on content that is not defined (`GAP-WEB-303`).

- The project-level `web-app.accessibility` section carries no content in the supplied project spec, so there is no target standard to inherit (`GAP-WEB-304`).
- The design shows two transitions out of this screen, to `ArchiveScreen` and `SearchScreen` (`screens-inventory.md`). Their keyboard behaviour cannot be specified without a control definition.

**Unblocked by:** a stated accessibility standard and level, plus the content definition.
<!-- /SECTION: web-app.scope-docs.accessibility -->
<!-- SECTION: web-app.scope-docs.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input supplies a locale list or any copy for this screen (`GAP-WEB-303`). No copy keys are invented.

**Unblocked by:** a locale list and the screen's copy, or confirmation that the web app is single-locale.
<!-- /SECTION: web-app.scope-docs.i18n -->
<!-- SECTION: web-app.scope-docs.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Every entry is an unverified claim. Figma MCP is `not-connected`, and `design-brief.md` supplies no frame IDs (`spec_mode: FULL-FROM-BRIEF`).

| Item | Value | Source | Verification |
|---|---|---|---|
| Screen | `ScopeDocumentationScreen`, "Scope Documentation", module M5 | `screens-inventory.md` | unverified |
| Frame ID | none supplied | `design-brief.md` | not available |
| Variants | `Default`, `Error` | `screens-inventory.md` | unverified |
| Components | none | `screens-inventory.md` | unverified |
| Outgoing transitions | `ON_CLICK` → `ArchiveScreen`, `ON_CLICK` → `SearchScreen` | `screens-inventory.md` | unverified |
| Tokens bound | none: token values are not inlined and none are attributed to this screen | `tokens-index.md` | not available |
<!-- /SECTION: web-app.scope-docs.design-refs -->
<!-- SECTION: web-app.scope-docs.test-plan | status: BLOCKED -->
## Test plan

**BLOCKED.** There are no requirements, so there is nothing to verify (core 7). No component, E2E, contract or accessibility test is proposed, because each would need a behaviour to assert.

**Unblocked by:** requirements under `Q-WEB-001` and `GAP-WEB-302`.
<!-- /SECTION: web-app.scope-docs.test-plan -->
<!-- SECTION: web-app.scope-docs.gaps | status: DRAFT-UNVERIFIED -->
## Open items

**Assumptions**

| ID | Statement | What would confirm it |
|---|---|---|
| `ASM-WEB-301` | `ScopeDocumentationScreen` is not part of the web app. This applies the project spec's default for `Q-WEB-001` (`ASM-WEB-005`), and the run is unattended. | Answer to `Q-WEB-001`. |

**Contradictions**

| ID | Severity | Statement | Closing artefact |
|---|---|---|---|
| `CON-WEB-301` | non-critical | `stories-map.md` maps US-005-01, US-005-03, US-005-04, US-005-05 and US-005-06 to `ScopeDocumentationScreen`. The project spec's `web-app.overview` says no web story requires the screen. If the screen is a web screen, those five stories (written for API actors) would need web-level acceptance criteria. If it is not, the design maps stories to a screen outside the web app. The reading adopted is the project spec's: no web requirement (`ASM-WEB-301`). | A decision on `Q-WEB-001`, and web-actor stories if yes. |

**Gaps**

| ID | What is missing | Sections degraded | Fallback used | Closing artefact |
|---|---|---|---|---|
| `GAP-WEB-302` | No web story or requirement says what a user does on `ScopeDocumentationScreen`. | `requirements`, `routes`, `test-plan` | `BLOCKED` | A web story, or confirmation under `Q-WEB-001`. |
| `GAP-WEB-303` | The design gives no components, content, data source or copy for the screen, and no frame ID. | `components`, `states`, `api-integration`, `accessibility`, `i18n` | `BLOCKED`; the two variant names are recorded only | A design frame or annotation for the screen. |
| `GAP-WEB-304` | No accessibility standard or level is available. The project-level `web-app.accessibility` section has no content. | `accessibility` | `BLOCKED` | A stated standard, level and verification method. |

**Questions.** None is minted here. The open question is `Q-WEB-001` from the project spec (degradable, default "no").
<!-- /SECTION: web-app.scope-docs.gaps -->
