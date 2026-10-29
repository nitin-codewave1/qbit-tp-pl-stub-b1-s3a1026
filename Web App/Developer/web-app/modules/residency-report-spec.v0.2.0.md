---
id: agent-developer/web_app/web-app/modules/residency-report-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/residency-report-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/residency-report-SPEC.md
---

<!-- SECTION: web-app.residency-report.frontmatter | status: VERIFIED -->
## Frontmatter

```yaml
id: residency-report
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [Q-WEB-001, GAP-WEB-252, GAP-WEB-253]
contract_pin: "sha256:ceedbd350666"
scope: []   # no route assigned: web-app.routing "ResidencyReportScreen: none"; web-app.module-map "No module spec until Q-WEB-001"
```

Sources: `RUN-CONTEXT` (`contract_pin`, `surface`); `web-app.module-map` (module `residency-report`, design label M4); `web-app.routing`.
<!-- /SECTION: web-app.residency-report.frontmatter -->
<!-- SECTION: web-app.residency-report.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Intended responsibility.** `design-brief.md` (`screens-inventory.md`) lists `ResidencyReportScreen` ("Residency Report", module M4) with variants Default and Error and the component `Button/Primary`. Its title implies it would display EU data-residency results. The title is the only evidence for this, and no input describes the screen's content or behaviour (`ASM-WEB-251`).

**Status of the module.** It is a placeholder.

- No web story requires this screen. Every story in EP-006 concerns notes, search, login or archive (`stories.md`).
- `web-app.overview` places the screen out of scope pending `Q-WEB-001`. The default is "no" (`ASM-WEB-005`). This run is unattended, so that default stands.
- `stories-map.md` maps US-004-01 to US-004-05 to this screen. Those stories belong to EP-004, whose actors are platform operations engineers and compliance auditors. They are written against infrastructure and the API, not the browser (`CON-WEB-251`).

**Not responsible for:**

- Generating, running or enforcing residency checks. That is the backend `residency` module, which is infrastructure tooling (`backend-api.md` module map).
- Notes, search, login and archive screens, which belong to the `notes`, `search`, `archive` and `login` modules.
- Any route, permission or role. None is defined for this surface (`GAP-WEB-253`, `GAP-WEB-255`).
<!-- /SECTION: web-app.residency-report.purpose -->
<!-- SECTION: web-app.residency-report.requirements | status: BLOCKED -->
## Requirements

**BLOCKED.** No functional requirement can be written without inventing one. Core rule 7 requires every requirement to trace to a story or requirement, and none exists for the web surface (`GAP-WEB-252`).

Unblocked by: an answer of yes to `Q-WEB-001`, plus web stories for the screen. The stories must say who uses it (`GAP-WEB-255`) and what it shows. Requirement IDs would then be minted as `WEB-RESIDENCY-REPORT-FR-nnn`.
<!-- /SECTION: web-app.residency-report.requirements -->
<!-- SECTION: web-app.residency-report.routes | status: BLOCKED -->
## Routes

**BLOCKED.** `web-app.routing` assigns no path to `ResidencyReportScreen` and lists the layout and access rule as "n/a, not routed". The layout and required permission are undefined (`GAP-WEB-253`).

Unblocked by: `Q-WEB-001` answered yes, a route path, and a protected-route rule from the backend session decision (`Q-BE-003`, `GAP-WEB-001`).
<!-- /SECTION: web-app.residency-report.routes -->
<!-- SECTION: web-app.residency-report.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `residency-report`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.residency-report.components -->
<!-- SECTION: web-app.residency-report.states | status: BLOCKED -->
## States

**BLOCKED.** There is no route and no requirement to attach states to.

Known evidence only:

- **Design.** The screen has variants Default and Error (`screens-inventory.md`). There is no Empty or Loading variant, and populated is not distinguished from Default (`GAP-WEB-254`).
- **Permission-denied.** Whether the screen is permission-gated is unknown. The backend defines no ownership or role rule (`GAP-WEB-009`, `GAP-WEB-255`). No state is specified.
- **Partial-failure.** No multi-item operation is identified for this module, so no partial-failure state is specified and no gap is raised for it.

Unblocked by: a confirmed route, requirements and design variants for loading, empty and populated.
<!-- /SECTION: web-app.residency-report.states -->
<!-- SECTION: web-app.residency-report.api-integration | status: BLOCKED -->
## API integration

**BLOCKED.** No operation is bound, because no requirement uses one.

Candidate only, not bound: `API-RESIDENCY-913995` (the compliance report). The contract also contains other `API-RESIDENCY-*` operations that serve CI and operations needs. Nothing links any of them to a web requirement.

Open points:

- The contract has no operation to list past audits, although US-004-06 describes a historical log (`GAP-WEB-256`).
- Whether residency operations require a session or a role is undecided (`Q-BE-003`, `GAP-WEB-001`).

Unblocked by: web requirements naming the operations, and the backend session decision.
<!-- /SECTION: web-app.residency-report.api-integration -->
<!-- SECTION: web-app.residency-report.validation | status: DERIVED -->
## Validation

No client validation rules are specified. `screens-inventory.md` lists only `Button/Primary` on this screen and no entry component, so no user input is designed. Server-side enforcement is therefore also not applicable here.

If a requirement later adds an input (for example a filter), it needs its own rules and a server-side counterpart.
<!-- /SECTION: web-app.residency-report.validation -->
<!-- SECTION: web-app.residency-report.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** The target standard is undefined for the whole surface (`web-app.accessibility`, `GAP-WEB-007`). No screen content is specified, so focus order, keyboard paths and live regions cannot be written for this module.

Unblocked by: the project-level accessibility standard and a specified screen.
<!-- /SECTION: web-app.residency-report.accessibility -->
<!-- SECTION: web-app.residency-report.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input supplies copy keys or locales for this screen.

Unblocked by: supplied copy and a locale list.
<!-- /SECTION: web-app.residency-report.i18n -->
<!-- SECTION: web-app.residency-report.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

| Screen | Variants | Components | Frame ID | Verification |
|---|---|---|---|---|
| `ResidencyReportScreen` | Default, Error | `Button/Primary` | none supplied | Unverified. `spec_mode` is `FULL-FROM-BRIEF` and Figma MCP is not connected. |

- Source: `design-brief.md` (`screens-inventory.md`, module M4).
- The screen has no outgoing transitions.
- The only evidence is the brief. No frame was inspected, and no token binding or layout is claimed.
<!-- /SECTION: web-app.residency-report.design-refs -->
<!-- SECTION: web-app.residency-report.test-plan | status: BLOCKED -->
## Test plan

**BLOCKED.** There are no requirements to verify (`GAP-WEB-252`). Component, E2E, contract and a11y tests can be named once requirements exist.
<!-- /SECTION: web-app.residency-report.test-plan -->
<!-- SECTION: web-app.residency-report.gaps | status: VERIFIED -->
## Open gaps, contradictions and assumptions

| ID | Item | Closing artefact |
|---|---|---|
| `GAP-WEB-252` | No web story or requirement for `ResidencyReportScreen`. Related: `Q-WEB-001`, `ASM-WEB-005`. | A web story set for the screen, or a decision to drop it. |
| `GAP-WEB-253` | No route, layout or access rule for the screen (`web-app.routing`). | A route decision and the protected-route rule (`Q-BE-003`). |
| `GAP-WEB-254` | Design covers only Default and Error variants. There is no loading, empty or populated variant, no component for a report, and no token bindings. | Design frames and bindings for the screen. |
| `GAP-WEB-255` | The role for this screen is unknown. The compliance auditor, product owner and platform operations engineer are not web roles (`web-app.authorization-ui`). | A decision on who may reach it, and how access is enforced. |
| `GAP-WEB-256` | The contract has no operation to list past residency audits (US-004-06). | A backend operation or a decision that the history stays off the web. |

| ID | Contradiction | Reading adopted |
|---|---|---|
| `CON-WEB-251` | `stories-map.md` maps US-004-01 to US-004-05 to `ResidencyReportScreen`. Those stories describe infrastructure and API behaviour for operations and audit actors, with no web behaviour. | The mapping does not create web requirements. A human decision is needed (`Q-WEB-001`). Non-critical. |

| ID | Assumption | Confirmed by |
|---|---|---|
| `ASM-WEB-251` | The screen's purpose is inferred from its title alone. | A story or design annotation describing it. |

Cited from the project spec and not re-defined here: `Q-WEB-001`, `ASM-WEB-005`, `GAP-WEB-001`, `GAP-WEB-004`, `GAP-WEB-007`, `GAP-WEB-009`, `Q-BE-003`.
<!-- /SECTION: web-app.residency-report.gaps -->
