---
id: agent-developer/web_app/web-app/modules/residency-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/residency-SPEC.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/residency-SPEC.md
---

<!-- SECTION: web-app.residency.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: residency
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [Q-WEB-002, GAP-WEB-011, GAP-WEB-273, GAP-WEB-274, GAP-WEB-275]
contract_pin: "sha256:9550f0d579d8"
scope: []
```

`scope` is empty because the module owns no routes. The project spec's `web-app.routing` leaves both screens unrouted, and `web-app.module-map` lists the routes owned as "none". Both cite `Q-WEB-002` and `GAP-WEB-011` (`ASM-WEB-267`).

`spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma is `not-connected` (`RUN-CONTEXT.inputs.figma_mcp`).
<!-- /SECTION: web-app.residency.frontmatter -->
<!-- SECTION: web-app.residency.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Owned by this module** (`web-app.module-map`, design label M4):
- `InfrastructureProvisioningScreen` (variants Default, Error)
- `ResidencyReportScreen` (variants Default, Error)

Source: `screens-inventory.md`.

**Intended responsibility.** `stories-map.md` attaches these stories to the two screens:
- `InfrastructureProvisioningScreen`: US-004-01, US-004-03.
- `ResidencyReportScreen`: US-004-02, US-004-04, US-004-05.

Those are infrastructure and compliance stories. Their actors are the platform operations engineer, the backend developer and the compliance auditor (`stories.md`).

**Current status: not served.**
- `web-app.authorization-ui` allows one signed-in role on this surface, the Team member. It excludes the roles above and cites `stories.md` EP-001 to EP-005.
- No EP-006 story requires either screen.
- By the default adopted for `Q-WEB-002`, the screens are not routed, and this module produces no user-facing behaviour (`ASM-WEB-267`).

**Not responsible for:**
- Residency enforcement, scanning, gating or alerting. These are backend behaviours (`backend.module-map`, module `residency`).
- Session, sign-in and the protected-route rule, which belong to `web-app.auth-flows` and `web-app.routing`.
- Navigation chrome. No frame exists for it (`GAP-WEB-005`).
- Every other module's screens.
<!-- /SECTION: web-app.residency.purpose -->
<!-- SECTION: web-app.residency.requirements | status: BLOCKED -->
## Requirements

**Blocked.** No `WEB-RESIDENCY-FR-nnn` is written. Core rule 7 requires every requirement to trace to a story or requirement, and none supplied for this surface does:
- No EP-006 story concerns residency.
- The EP-004 stories describe API or infrastructure behaviour for other actors (`GAP-WEB-011`, `GAP-WEB-273`).
- Inventing requirements from the screen names alone would be inventing, not specifying.

**Unblocked by** one of:
- A web story naming a signed-in role for either screen.
- Confirmation that this surface does not serve these screens. In that case the module is removed from the module map.

Both outcomes are covered by `Q-WEB-002` and `Q-WEB-255`.
<!-- /SECTION: web-app.residency.requirements -->
<!-- SECTION: web-app.residency.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| none | `InfrastructureProvisioningScreen` | n/a | n/a |
| none | `ResidencyReportScreen` | n/a | n/a |

- Neither screen is routed (`web-app.routing`; default adopted for `Q-WEB-002`).
- No path is proposed, because proposing one would imply a decision no input makes.
- The design records no inbound or outgoing transition for either screen (`screens-inventory.md`, outgoing `-`).
- The required permission is undefined. The backend auth model is `BLOCKED` (`backend.auth-model`), and no signed-in role maps to these screens (`web-app.authorization-ui`).
<!-- /SECTION: web-app.residency.routes -->
<!-- SECTION: web-app.residency.components | status: UNKNOWN -->
## Components

**Status:** DRAFT-UNVERIFIED (names from `design-brief.md`; Figma not connected).

| Component | Role | Variants | States | Used on | Token bindings |
|---|---|---|---|---|---|
| `Button/Primary` | action | 12 | Default, Pressed, Loading, Disabled | `ResidencyReportScreen` | not supplied |

- `InfrastructureProvisioningScreen` lists no components (`screens-inventory.md`).
- `Button/Primary` is the only component listed on `ResidencyReportScreen`.
- No component-to-token binding was supplied; component bodies are omitted from the handoff (`components-inventory.md`).
- No binding is proposed. The 71 token IDs are indexed in `tokens-index.md` and are not restated here.
- No component exists for a report table, a status badge or a message region (`GAP-WEB-274`).
<!-- /SECTION: web-app.residency.components -->
<!-- SECTION: web-app.residency.states | status: BLOCKED -->
## States

**Blocked** for the four standard states on each screen, because neither screen is routed (`ASM-WEB-267`).

**What the design supplies** (`screens-inventory.md`):
- `InfrastructureProvisioningScreen`: Default, Error.
- `ResidencyReportScreen`: Default, Error.

No loading, empty or populated variant exists in the design (`GAP-WEB-274`). Writing them would invent design.

**Conditional states:**
- *Permission-denied.* Not specified. There is no permission-gated route, so the state cannot apply.
- *Partial-failure.* Not specified. No multi-item operation is performed from these screens: no user action is specified for either one.

**Unblocked by** the routing decision (`Q-WEB-002`) and design variants for loading, empty and populated states.
<!-- /SECTION: web-app.residency.states -->
<!-- SECTION: web-app.residency.api-integration | status: DRAFT-UNVERIFIED -->
## API integration

No operation is bound, because no behaviour is specified (`ASM-WEB-267`).

**Candidate pairing, not committed** (`ASM-WEB-268`). The pairing rests on the story mapping in `stories-map.md` and the contract summaries in `backend.api-surface`. It would apply only if `Q-WEB-002` is answered "route them".

| Screen | Candidate operation | Basis |
|---|---|---|
| `ResidencyReportScreen` | `API-RESIDENCY-913995` | US-004-02 compliance report |
| `InfrastructureProvisioningScreen` | `API-RESIDENCY-892950` | US-004-01 provisioning check |

**Not bound.** These operations change or trigger state: `API-RESIDENCY-648672`, `API-RESIDENCY-922826`, `API-RESIDENCY-685134` and `API-RESIDENCY-174124`. No input says a browser user may invoke them (`GAP-WEB-275`).

**Absent from the contract.** US-004-06 asks for a historical audit log. No operation lists past audit results (`GAP-WEB-276`).

Shapes and status meanings are not restated here (core rule 6). The contract lists only 400 and 500 for these operations. Meanings for any other status are not available.
<!-- /SECTION: web-app.residency.api-integration -->
<!-- SECTION: web-app.residency.validation | status: BLOCKED -->
## Validation

**Blocked.** No input field is specified for either screen. The design lists no input component on them (`screens-inventory.md`).

The only candidate rule is that the region must be an approved EU region for the provisioning check (`API-RESIDENCY-892950`; US-004-01). The approved list is open (`backend.coverage`, `GAP-BE-011`). A client rule built on it would be a guess.

No client rule is written, and no server-side enforcement claim is made.

**Unblocked by** the routing decision (`Q-WEB-002`), an input design for `InfrastructureProvisioningScreen`, and the approved region list.
<!-- /SECTION: web-app.residency.validation -->
<!-- SECTION: web-app.residency.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.**
- No accessibility standard or level is stated (`web-app.accessibility` is `BLOCKED`; `GAP-WEB-008`).
- Neither screen has a routed flow, form or live data region to which focus, keyboard or live-region rules could attach.

**Unblocked by** the accessibility statement (`GAP-WEB-008`) and the routing decision (`Q-WEB-002`).
<!-- /SECTION: web-app.residency.accessibility -->
<!-- SECTION: web-app.residency.i18n | status: BLOCKED -->
## I18n

**Blocked.** No input supplies copy keys or locales for this module, and none exists for the web app project-wide.

**Unblocked by** a locale list and copy source, if these screens are routed.
<!-- /SECTION: web-app.residency.i18n -->
<!-- SECTION: web-app.residency.design-refs | status: DRAFT-UNVERIFIED -->
## Design refs

| Screen | Frame ID | Variants named | Verification |
|---|---|---|---|
| `InfrastructureProvisioningScreen` | not supplied | Default, Error | Unverified: Figma `not-connected`, brief only |
| `ResidencyReportScreen` | not supplied | Default, Error | Unverified: Figma `not-connected`, brief only |

- Source: `design-brief.md`, `screens-inventory.md`.
- The brief carries names and counts. It supplies no frame IDs.
- Every name here is an unverified claim (spec_mode rule 4.5).
<!-- /SECTION: web-app.residency.design-refs -->
<!-- SECTION: web-app.residency.test-plan | status: BLOCKED -->
## Test plan

**Blocked.** There is no requirement to verify (`web-app.residency.requirements`) and no route to exercise.

When unblocked, the plan would add four kinds of test:
- **Component tests** for each screen variant.
- **E2E tests** for the routed flow.
- **Contract tests** against the pinned operations in the API integration section.
- **Accessibility tests** against the standard stated for `GAP-WEB-008`.

Test names are not proposed yet, since there is no requirement to name them after.

**Unblocked by** `Q-WEB-002` and `Q-WEB-255`.
<!-- /SECTION: web-app.residency.test-plan -->
<!-- SECTION: web-app.residency.gaps | status: DRAFT-UNVERIFIED -->
## Gaps

**Inherited from the project spec (cited, not re-minted):**
- `Q-WEB-002`: whether `InfrastructureProvisioningScreen`, `ResidencyReportScreen` and `ScopeDocumentationScreen` are routed. Default adopted: not routed.
- `GAP-WEB-005`: no frames for application chrome, messages, or error variants.
- `GAP-WEB-008`: accessibility standard.
- `GAP-WEB-011`: EP-001 to EP-005 stories are API or infrastructure stories, with no web stories behind these screens.

**Minted here**

| ID | Missing | Degrades | Done instead | Closing artefact |
|---|---|---|---|---|
| `GAP-WEB-273` | A web story or requirement for either screen. The mapped stories' actors (platform operations engineer, compliance auditor) are not roles that sign in on this surface. | requirements, routes, states, test-plan | Requirements `BLOCKED`; screens unrouted | A web story naming a signed-in role, or a decision that this surface does not serve them |
| `GAP-WEB-274` | Design variants beyond Default and Error (loading, empty, populated) and any component for report content, status or messages. `InfrastructureProvisioningScreen` lists no components. | components, states | Inventory facts only; no binding | Design frames for each state |
| `GAP-WEB-275` | An authorization decision for the residency operations. Several are state-changing triggers, and the backend auth model is `BLOCKED`. This needs a backend answer on who may call them, and a web role. | routes, api-integration | Trigger operations unbound | Backend decisions on `Q-BE-001` to `Q-BE-003` plus a web role definition |
| `GAP-WEB-276` | A contract operation for the historical audit log (US-004-06). It is mapped to no screen in `stories-map.md`. | api-integration | Not bound | A backend operation listing past audit results, and a screen mapping |

**Questions** (unattended: degradable defaults adopted; none blocking)

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-255` | degradable | Should any signed-in role, other than the Team member, see these screens? | None; module stays unrouted |

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-267` | The module is not routed, per the default for `Q-WEB-002`; it has no navigation, requirement or behaviour. | `Q-WEB-002` answered |
| `ASM-WEB-268` | The candidate operation pairing in the API integration section follows the story mapping and the contract summaries. It is not a binding. | A design flow plus the routing decision |

**Contradictions.** None were found. Directions checked:
- story↔design: the design has screens with no supporting web story. That is a gap (`GAP-WEB-011`, `GAP-WEB-273`), not a contradiction.
- story↔requirement: no story or requirement for this module conflicts with another.
- requirement↔requirement: none relevant.
- design↔design: counts and inventories agree.
<!-- /SECTION: web-app.residency.gaps -->
