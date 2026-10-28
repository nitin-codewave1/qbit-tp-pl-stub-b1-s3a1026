---
id: agent-developer/web_app/web-app/modules/residency-report-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/residency-report-SPEC.md
depends_on: [web_app:US-004-01, web_app:US-004-02, web_app:US-004-03, web_app:US-004-04, web_app:US-004-05, web_app:US-004-06]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/modules/residency-report-SPEC.md
---

<!-- SECTION: web-app.residency-report.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: residency-report
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [Q-WEB-001, GAP-WEB-202, GAP-WEB-204, GAP-WEB-205, GAP-WEB-206, GAP-WEB-207]
contract_pin: "sha256:8e424bb2b40a"
scope: ["/residency"]
```

- `spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma MCP is not connected (`RUN-CONTEXT.inputs.figma_mcp`).
- The route path `/residency` is a proposal (`ASM-WEB-203`). The project routing assigns no path to this module (`web-app.routing`, `ASM-WEB-005`).
<!-- /SECTION: web-app.residency-report.frontmatter -->
<!-- SECTION: web-app.residency-report.purpose | status: DERIVED -->
## Purpose

**Responsibility.** This module owns design module M6-adjacent screen `ResidencyReportScreen` (module M4, `design-brief.md`). It presents backend-produced EU data-residency results to a signed-in viewer. The results are:

- the per-resource compliance report;
- the launch-gate verdict;
- once the backend supplies them: third-party service residency, per-environment results and audit history.

It owns the stories `US-004-01` to `US-004-06` (`stories.md`, EP-004). The business rules and non-functional requirements behind them are `BR-013` and `BR-014` (all hosting, databases, backups and logs for notes_api, and every third-party dependency, EU-only) and `NFR-007` to `NFR-009` (`requirements.md`).

**Scope basis.**
- The project spec treats this module as unspecified until `Q-WEB-001` is answered (`ASM-WEB-005`, `web-app.module-map`).
- This document is written because the run assigned the stories to the module. It assumes `Q-WEB-001` is answered "in scope" (`ASM-WEB-201`).
- If the answer is "no", discard this document and the `/residency` route.

**Not responsible for:**
- Provisioning resources, blocking writes to non-EU destinations, running the CI residency gate, triggering drift scans, validating backups, or raising alerts. These are backend and infrastructure behaviours (`backend.module-map`, module `residency`).
- Deciding compliance. The web app displays server-supplied results and never computes or overrides a verdict (`INV-WEB-202`).
- The web app's own hosting region and client logging (`GAP-WEB-012`).
- Documenting the API scope (`ScopeDocumentationScreen`, module M5), or any note, search or login behaviour.
- Retaining audit history. The log lives in the backend (`NFR-009`).

**Who the stories name.** Platform operations engineer, compliance auditor and backend developer (`stories.md`). None is a web role in the project spec (`web-app.authorization-ui`, `GAP-WEB-010`). The role that opens this screen is undecided (`GAP-WEB-202`).
<!-- /SECTION: web-app.residency-report.purpose -->
<!-- SECTION: web-app.residency-report.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Every requirement is conditional on `ASM-WEB-201`. IDs follow `WEB-RESIDENCY-REPORT-FR-nnn`. Fields are named only where the screen displays them (core rule 6).

### WEB-RESIDENCY-REPORT-FR-001 — Render the compliance report

- **Given** a viewer permitted to open `/residency` (access rule: `GAP-WEB-202`),
- **When** the screen loads,
- **Then** the web app requests the compliance report and renders every returned resource with its region and its pass/fail status. It also shows the overall `percent_compliant` and the `generated_at` time as supplied.
- **Traces to.** `US-004-02`; `NFR-007`.
- **Verification.** (planned) `residency-report.component.renders-resources-with-region-and-status`.
- **API.** `API-RESIDENCY-913995`.
- Status: `DRAFT-UNVERIFIED` (`ASM-WEB-201`).

### WEB-RESIDENCY-REPORT-FR-002 — Never present an unverified resource as compliant

- **Given** a returned resource has no region value or no recognised status,
- **When** the report renders,
- **Then** that resource is shown as unverified, never as compliant (`INV-WEB-201`).
- **Traces to.** `US-004-02` (second acceptance criterion: "flagged as unverified rather than assumed compliant").
- **Verification.** (planned) `residency-report.component.unverified-when-status-absent`.
- **API.** `API-RESIDENCY-913995`.
- The contract defines neither an "unverified" status nor the status value set (`GAP-WEB-203`). The rule is therefore fail-closed on the web side (`ASM-WEB-205`).

### WEB-RESIDENCY-REPORT-FR-003 — Third-party service residency (BLOCKED)

- **Given** notes_api depends on a third-party service,
- **When** the viewer reviews the residency view,
- **Then** the service's data-processing location is shown as EU-resident, or the dependency is flagged for remediation.
- **Traces to.** `US-004-04`; `BR-014`.
- **Verification.** (planned) `residency-report.component.third-party-flagged-for-remediation`.
- **API.** none. The contract has no third-party entry (`GAP-WEB-204`).
- Status: `BLOCKED`.

### WEB-RESIDENCY-REPORT-FR-004 — Show the launch-gate verdict

- **Given** the launch-gate request succeeds,
- **When** the screen renders,
- **Then** the verdict and `percent_compliant` are shown. If the verdict is a failure, each `blocking_resources` entry is listed.
- **Traces to.** `US-004-05` (second criterion shows the blocking state; the blocking of the launch itself is a backend and CI outcome, not a web behaviour).
- **Verification.** (planned) `residency-report.component.launch-gate-fail-lists-blocking-resources`.
- **API.** `API-RESIDENCY-395094`.
- The web app displays the verdict as returned (`INV-WEB-202`).
- Status: `DRAFT-UNVERIFIED`.

### WEB-RESIDENCY-REPORT-FR-005 — Per-environment pre-launch result (BLOCKED)

- **Given** the pre-launch check has run across dev, staging and prod,
- **When** the viewer opens the residency view,
- **Then** pass/fail is shown per environment with region details.
- **Traces to.** `US-004-05`.
- **Verification.** (planned) `residency-report.component.per-environment-pass-fail`.
- **API.** none. The launch-gate and report operations carry no environment dimension (`GAP-WEB-205`).
- Status: `BLOCKED`.

### WEB-RESIDENCY-REPORT-FR-006 — Audit history (BLOCKED)

- **Given** multiple residency audits have run,
- **When** the viewer opens the audit history,
- **Then** each past audit's date, scope and result are listed.
- **Given** no audits have run, **then** an empty state is shown rather than an error.
- **Traces to.** `US-004-06`; `NFR-009`.
- **Verification.** (planned) `residency-report.component.history-lists-date-scope-result`.
- **API.** none. The contract exposes only the latest drift scan, not a history (`GAP-WEB-206`).
- Status: `BLOCKED`.

### WEB-RESIDENCY-REPORT-FR-007 — Independent failure handling

- **Given** the report request or the launch-gate request fails with a server error or an unexpected response,
- **When** the screen renders,
- **Then** the affected region shows an error state with a retry action. The other region is unaffected, and the screen is never blank.
- **Traces to.** `US-004-02`, `US-004-05`.
- **Verification.** (planned) `residency-report.component.independent-region-error`.
- **API.** `API-RESIDENCY-913995`, `API-RESIDENCY-395094`.
- The retry control is the one `Button/Primary` on the screen (`ASM-WEB-206`). Independence of the two regions is `ASM-WEB-207`.

### WEB-RESIDENCY-REPORT-FR-008 — No result yet

- **Given** the report or launch-gate request returns not-found, or a report arrives with zero resources,
- **When** the screen renders,
- **Then** an empty state is shown for that region, and no compliance is asserted (`ASM-WEB-202`, `ASM-WEB-208`).
- **Traces to.** `US-004-02`, `US-004-05`.
- **Verification.** (planned) `residency-report.component.not-found-renders-empty`.
- **API.** `API-RESIDENCY-913995`, `API-RESIDENCY-395094`.

### Invariants

- `INV-WEB-201`: A resource is rendered as compliant only when the response explicitly states a pass for it. Absent or unrecognised status or region renders as unverified (`US-004-02`, derived).
- `INV-WEB-202`: The web app never computes, overrides or averages a compliance verdict or percentage. It displays the server-supplied values (derived from `US-004-02` and `US-004-05`).

### Story coverage (owned stories)

| Story | Covered by | Note |
|---|---|---|
| `US-004-01` | none | Infrastructure provisioning rule. No web actor or UI (`GAP-WEB-010`). `API-RESIDENCY-026899` exists in the contract but is not consumed. |
| `US-004-02` | FR-001, FR-002, FR-007, FR-008 | `GAP-WEB-203` open. |
| `US-004-03` | none | Backend persistence and alerting rule. No web UI (`GAP-WEB-010`). |
| `US-004-04` | FR-003 | `BLOCKED` (`GAP-WEB-204`). |
| `US-004-05` | FR-004, FR-005, FR-007, FR-008 | FR-005 `BLOCKED` (`GAP-WEB-205`). |
| `US-004-06` | FR-006 | `BLOCKED` (`GAP-WEB-206`). |

Story numbering differs between `stories.md` and `backend-api.md` (`CON-WEB-201`). The mapping is made by content (`ASM-WEB-204`).
<!-- /SECTION: web-app.residency-report.requirements -->
<!-- SECTION: web-app.residency-report.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route (proposed) | Page | Layout | Required permission |
|---|---|---|---|
| `/residency` | `ResidencyReportScreen` | app layout (`ASM-WEB-203`) | `BLOCKED` (`GAP-WEB-202`) |

**Notes**
- The path and layout are proposals. The project routing table lists `ResidencyReportScreen` with "none assigned" (`web-app.routing`). That table must be updated if `Q-WEB-001` is answered "in scope".
- **Permission.** Whether the route is gated, and which role may open it, is unknown. The backend permission matrix is `BLOCKED` (`backend.auth-model`; `Q-BE-001`, `Q-BE-002`, `Q-BE-003`), and the compliance-auditor role does not exist on the web surface (`web-app.authorization-ui`).
- **Unauthenticated redirect.** If the app layout is protected, the redirect target is `LoginScreen` (`web-app.routing`).
- **Navigation.** The design records no outgoing transitions from `ResidencyReportScreen` (`screens-inventory.md`). How the screen is reached from other screens is unspecified (`GAP-WEB-013`).
- **History.** No sub-route for audit history is defined. `US-004-06` maps to no screen (`stories-map.md`, `GAP-WEB-207`).
<!-- /SECTION: web-app.residency-report.routes -->
<!-- SECTION: web-app.residency-report.components | status: DRAFT-UNVERIFIED -->
## Components

Source: `components-inventory.md` and `screens-inventory.md`. All names are unverified against Figma.

| Component | Used on this screen | Variants | States | Token bindings |
|---|---|---|---|---|
| `Button/Primary` | yes (`ResidencyReportScreen`) | 12 (individual variants not enumerated in the brief) | Default, Pressed, Loading, Disabled | `BLOCKED` (`GAP-WEB-208`) |

**Use of `Button/Primary`.**
- The brief does not say what the button does.
- It is assumed to be the retry/refresh action (`ASM-WEB-206`).
- Its Loading state is used while a retry is in flight.
- It has no Focused state in the inventory (see `web-app.residency-report.accessibility`).

**Needed by the requirements but absent from the inventory.** None of these are invented here (`GAP-WEB-207`):
- resource list or table;
- pass/fail/unverified status indicator;
- percent-compliant and generated-at display;
- launch-gate verdict display and blocking-resource list;
- environment grouping;
- audit-history list.

`Empty/State` exists in the inventory but the brief does not place it on this screen.

**Token bindings.** The brief lists 71 token IDs but omits component bindings and values (`tokens-index.md`; "bindings are omitted from chain handoff", `components-inventory.md`). No token is bound to a part of any component here (`GAP-WEB-005`, `GAP-WEB-208`).
<!-- /SECTION: web-app.residency-report.components -->
<!-- SECTION: web-app.residency-report.states | status: DRAFT-UNVERIFIED -->
## States

The module has one route, `/residency`. The report region and the launch-gate region have independent states (`ASM-WEB-207`).

| State | Report region | Launch-gate region |
|---|---|---|
| Loading | request in flight; no result shown | request in flight; no result shown |
| Empty | not-found (`ASM-WEB-202`) or a response with zero resources (`ASM-WEB-208`). Compliance is not asserted. | not-found (`ASM-WEB-202`) |
| Populated | resources with region and status, `percent_compliant`, `generated_at` (FR-001, FR-002) | verdict, `percent_compliant`, blocking resources on failure (FR-004) |
| Error | server error or unexpected response: error state with retry (FR-007) | same |

**Design coverage.**
- The design defines only **Default** and **Error** variants for `ResidencyReportScreen` (`screens-inventory.md`).
- No frame exists for Loading, Empty or the unverified-resource presentation (`GAP-WEB-207`).

**Permission-denied.**
- This state applies only if the route is permission-gated, which is undecided (`GAP-WEB-202`).
- The choice between a "not allowed" and a "does not exist" rendering is a security decision and is not made here (`web-app.authorization-ui`, `GAP-WEB-011`).
- Status: `BLOCKED`. It is specified once `Q-BE-002` and `Q-BE-003` are decided.

**Partial failure.**
- Not applicable. The module has no multi-item write operation: every consumed operation is a read (`web-app.api-integration`).
- The case of one read failing while the other succeeds is covered by the independent regions above.
- Unverified resources inside a successful report are part of Populated (FR-002), not a failed operation.

**Offline.**
- Not a state on the web unless an input requires it (`W8`). None does.
<!-- /SECTION: web-app.residency-report.states -->
<!-- SECTION: web-app.residency-report.api-integration | status: DERIVED -->
## API integration

References only. Contract IDs carry no `@v` suffix in `backend-api.md`, so they are cited as supplied.

| Operation | API ID | Used by |
|---|---|---|
| Get residency compliance report | `API-RESIDENCY-913995` | FR-001, FR-002, FR-007, FR-008 |
| Get launch gate status | `API-RESIDENCY-395094` | FR-004, FR-007, FR-008 |

**In the contract, consumed by no requirement:**
- `API-RESIDENCY-726277` (latest drift scan). Whether to display it is `Q-WEB-201`; default no.
- `API-RESIDENCY-922826` (run a drift scan).
- `API-RESIDENCY-034853` (CI gate check).
- `API-RESIDENCY-174124` (backup validation).
- `API-RESIDENCY-026899` (provisioning request).

These are infrastructure and CI operations with no web actor (`GAP-WEB-010`). The web app triggers none of them.

**Absent from the contract:**
- third-party service residency (`GAP-WEB-204`);
- a per-environment result (`GAP-WEB-205`);
- an audit-history listing (`GAP-WEB-206`).

**Cache and refresh.**
- Per `web-app.state-management`, there is no time-based staleness.
- Both reads refresh on navigation to the route and on the retry action.
- Neither operation mutates anything, so no invalidation is needed.
- The report carries its own `generated_at`, so freshness is visible to the viewer.

**Error handling.**
- The contract lists not-found and server-error responses for both reads, without stating what not-found means (`ASM-WEB-202`).
- The error envelope and code names are proposals (`ASM-BE-006`). Branching on `code` is preferred to branching on status where the backend publishes them (`GAP-WEB-009`).
<!-- /SECTION: web-app.residency-report.api-integration -->
<!-- SECTION: web-app.residency-report.validation | status: DERIVED -->
## Validation

**Client rules: none.**
- The module has no user input. The only component on the screen is a button (`screens-inventory.md`).
- Both consumed operations take no request fields (`backend-api.md`, `API-RESIDENCY-913995` and `API-RESIDENCY-395094`: "Request: none").

**Server-side enforcement: n/a.** There is nothing for the client to duplicate.

**Response handling (defensive, client-side).** The web app treats server data as untrusted for display purposes:
- An absent or unrecognised status or region renders as unverified (`INV-WEB-201`, FR-002).
- The web app never derives a verdict from other fields (`INV-WEB-202`).

If an audit-history operation later accepts filters (`GAP-WEB-206`), input rules are specified then.
<!-- /SECTION: web-app.residency-report.validation -->
<!-- SECTION: web-app.residency-report.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** No input names an accessibility standard, level or verification method (`GAP-WEB-007`). Focus management, keyboard paths and live regions cannot be written without a target.

**What the inputs do establish:**
- The screen has no form, so there are no form errors to expose.
- `Button/Primary` has states Default, Pressed, Loading and Disabled, and no Focused state (`components-inventory.md`).
- Status must not rely on colour alone: this is a design decision, not a citation. It is recorded here as a requirement to confirm once a target exists.

**Unblocked by:** `GAP-WEB-007` being closed, plus a Focused state for `Button/Primary` in the design.
<!-- /SECTION: web-app.residency-report.accessibility -->
<!-- SECTION: web-app.residency-report.i18n | status: BLOCKED -->
## i18n

**Blocked.** No copy strings, locales or copy keys are supplied (`GAP-WEB-016`). Keys are not invented here.

**Copy slots the requirements need (unwritten):**
- region/status labels, including pass, fail and unverified;
- empty message;
- error message and retry label;
- launch-gate verdict labels;
- labels for the blocked features once unblocked: third-party flag, environment names, history columns.

**Unblocked by:** a copy deck and a locale list.
<!-- /SECTION: web-app.residency-report.i18n -->
<!-- SECTION: web-app.residency-report.design-refs | status: DRAFT-UNVERIFIED -->
## Design refs

Verification: the Figma MCP is not connected and the design brief is present, so every item is an **unverified claim** (core 4.5, W5).

| Item | Value | Source | Verification |
|---|---|---|---|
| Screen | `ResidencyReportScreen`, title "Residency Report", module M4 | `screens-inventory.md` | unverified |
| Variants | Default, Error | `screens-inventory.md` | unverified |
| Components on screen | `Button/Primary` | `screens-inventory.md` | unverified |
| Outgoing transitions | none | `screens-inventory.md` | unverified |
| Stories mapped | `US-004-01` to `US-004-05` | `stories-map.md` | unverified |
| Story mapped to no screen | `US-004-06` | `stories-map.md` | `GAP-WEB-207` |
| Frame IDs | none supplied | n/a | `GAP-WEB-005` |
| Token values | not supplied | n/a | `GAP-WEB-005`, `GAP-WEB-208` |

**Observations**
- The brief maps `US-004-01` and `US-004-03` to this screen, although neither describes a UI (`GAP-WEB-010`).
- No Loading, Empty or permission-denied variants exist.
<!-- /SECTION: web-app.residency-report.design-refs -->
<!-- SECTION: web-app.residency-report.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are planned. No test tooling is named by any input (`GAP-WEB-211`), so none is named here.

**Component**
- `residency-report.component.renders-resources-with-region-and-status` (FR-001)
- `residency-report.component.unverified-when-status-absent` (FR-002)
- `residency-report.component.never-renders-compliant-without-explicit-pass` (`INV-WEB-201`)
- `residency-report.component.launch-gate-fail-lists-blocking-resources` (FR-004)
- `residency-report.component.verdict-not-recomputed` (`INV-WEB-202`)
- `residency-report.component.independent-region-error` (FR-007)
- `residency-report.component.not-found-renders-empty` (FR-008)
- `residency-report.component.zero-resources-not-asserted-compliant` (FR-008)

**E2E**
- `residency-report.e2e.viewer-opens-report`. Blocked by `GAP-WEB-202` and `Q-WEB-001`.
- `residency-report.e2e.error-then-retry` (FR-007)

**Contract**
- `residency-report.contract.compliance-report-fields-consumed` (`API-RESIDENCY-913995`)
- `residency-report.contract.launch-gate-fields-consumed` (`API-RESIDENCY-395094`)
- `residency-report.contract.unrecognised-status-handled` (`GAP-WEB-203`)

**Blocked tests** (no behaviour to test until the gaps close):
- FR-003 third-party flagging (`GAP-WEB-204`)
- FR-005 per-environment result (`GAP-WEB-205`)
- FR-006 audit history (`GAP-WEB-206`)

**A11y**
- `BLOCKED` by `GAP-WEB-007`.
<!-- /SECTION: web-app.residency-report.test-plan -->
<!-- SECTION: web-app.residency-report.gaps | status: DERIVED -->
## Gaps

Project-level items cited here, not re-minted: `Q-WEB-001`, `GAP-WEB-005`, `GAP-WEB-007`, `GAP-WEB-009`, `GAP-WEB-010`, `GAP-WEB-011`, `GAP-WEB-012`, `GAP-WEB-013`, `GAP-WEB-016`, `ASM-WEB-001`, `ASM-WEB-005`, `ASM-BE-006`. Upstream blockers are `Q-BE-001`, `Q-BE-002` and `Q-BE-003`.

### Contradictions (non-critical; neither side resolved)

- `CON-WEB-201`: Story numbering differs between inputs.
  - `stories.md` places residency in EP-004 (`US-004-01` to `US-004-06`), and `US-001-xx` there are note-CRUD stories.
  - `backend-api.md` cites residency operations against `US-001-01` to `US-001-06` and `API-RESIDENCY-*` summaries.
  - This spec uses `stories.md` as the source of record for story IDs (`ASM-WEB-204`) and maps by content.
  - If the backend numbering is the right one, the traces in this document must be re-pointed.

### Assumptions

- `ASM-WEB-201`: The module is in scope (`Q-WEB-001` answered "yes"). The run assigned `US-004-01` to `US-004-06` to it, though the project spec leaves it unspecified (`ASM-WEB-005`).
- `ASM-WEB-202`: A not-found response on the report or launch-gate read means no result exists yet and is rendered as empty. The contract lists the status without a meaning. Confirm with the backend.
- `ASM-WEB-203`: Route `/residency` in the app layout. Extends `ASM-WEB-001`.
- `ASM-WEB-204`: The stories map to contract operations by content, not by number (`CON-WEB-201`).
- `ASM-WEB-205`: The contract describes a "pass/fail status" without stating values. Any value other than an explicit pass renders as unverified or failing, and absent values render as unverified.
- `ASM-WEB-206`: The single `Button/Primary` is the retry/refresh action.
- `ASM-WEB-207`: The report and launch-gate reads are independent. One failing does not blank the other.
- `ASM-WEB-208`: A successful response with zero resources renders as empty, never as a compliant result.

### Questions

- `Q-WEB-201` (degradable): Should the latest drift scan (`API-RESIDENCY-726277`) appear on this screen?
  - **Default:** no, because no web requirement uses it.
  - Without it, `US-004-06` has no web source of history.

### Gaps (each names its closing artefact)

- `GAP-WEB-202`: Who may open `/residency`, and whether the residency operations require a session, are unknown. The compliance-auditor role does not exist on the web surface. **Backend must supply** the access rule and the decision behind `Q-BE-003`. Degrades routes and states (permission-denied). Closing artefact: a role and permission statement.
- `GAP-WEB-203`: The contract defines no "unverified" status and no status value set. **Backend must supply** the status values and how an undetermined region is reported (`US-004-02`). Closing artefact: contract additions.
- `GAP-WEB-204`: No contract entry for third-party service residency or a remediation flag (`US-004-04`). **Backend must supply** a representation, or confirm that third parties appear as resources in the report. Closing artefact: contract operation or field.
- `GAP-WEB-205`: No per-environment dimension (dev, staging, prod) on the report or launch gate (`US-004-05`). **Backend must supply** per-environment results with region details. Closing artefact: contract additions.
- `GAP-WEB-206`: No audit-history operation. Only the latest drift scan exists, and "latest scan" is not equated here with an audit run (`US-004-06`, `NFR-009`). **Backend must supply** a history listing with date, scope and result. Closing artefact: a contract operation.
- `GAP-WEB-207`: Design has no frames for Loading, Empty, permission-denied, the unverified presentation, the resource list, the verdict, the environment grouping or the history. `US-004-06` maps to no screen. Closing artefact: design frames.
- `GAP-WEB-208`: Token bindings for `Button/Primary`, and bindings for the undesigned parts, are not supplied. Closing artefact: component binding files.
- `GAP-WEB-209`: Ordering, grouping and size limits of the resource list are unspecified, and so is whether failing resources are surfaced first. Closing artefact: a behaviour statement and design.
- `GAP-WEB-211`: Test tooling and frameworks are unnamed. Closing artefact: a platform statement.
<!-- /SECTION: web-app.residency-report.gaps -->
