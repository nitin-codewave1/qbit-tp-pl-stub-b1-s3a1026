---
id: agent-developer/web_app/web-app/modules/scope-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/scope-docs-SPEC.md
depends_on: [web_app:US-005-05]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/modules/scope-docs-SPEC.md
---

<!-- SECTION: web-app.scope-docs.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: scope-docs
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [Q-WEB-001, GAP-WEB-001, GAP-WEB-010, GAP-WEB-252]
contract_pin: "sha256:8e424bb2b40a"
scope: ["/scope"]
```

- `spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma MCP is not connected (`RUN-CONTEXT.inputs.figma_mcp`).
- `scope` holds a proposed route, not a supplied one (`ASM-WEB-251`).
- `blocked_by` lists IDs defined in the project spec, plus `GAP-WEB-252` defined in this module.
- This module is written because the dispatch assigns `US-005-05` to it. The project spec's default is that this screen is out of scope (`ASM-WEB-005`, `Q-WEB-001`). See `ASM-WEB-253` and `CON-WEB-251`.
<!-- /SECTION: web-app.scope-docs.frontmatter -->
<!-- SECTION: web-app.scope-docs.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Responsibility.** `ScopeDocumentationScreen` (design label M5, `design-brief.md` screens-inventory) is a read-only screen. It shows the out-of-scope list that the API documentation publishes. It lets a reader see, without calling the API directly, that attachments, mobile-specific endpoints and real-time features are excluded (`US-005-05`, AC-1 and AC-2).

The content comes from the published documentation, `API-CONTRACTDOCS-304307`. The web app does not author, hold or edit it (`INV-WEB-251`).

**Not responsible for:**

- **Other stories the design maps to this screen.** `stories-map.md` also maps `US-005-01`, `US-005-03`, `US-005-04` and `US-005-06` here. The project coverage marks them "Not web", and no module owns them (`GAP-WEB-256`).
- **Endpoint, schema or example browsing.** The same document carries `paths` and `components`. This module does not render them.
- **The data-residency handling note** carried in the same document. It is not required by `US-005-05` (`Q-WEB-251`).
- **The Residency Report screen.** It belongs to the `residency-report` module, which has no spec (`web-app.module-map`).
- **Scope compliance checks and the build gate.** These are CI and backend concerns (`US-005-03`, `US-005-04`).
- **Session, sign-in and authorisation.** These are in `web-app.auth-flows` and `web-app.authorization-ui`.
- **Any UI for attachments, real-time or mobile features.** The web app has none (`BR-016`, `BR-017`, `BR-018`).
<!-- /SECTION: web-app.scope-docs.purpose -->
<!-- SECTION: web-app.scope-docs.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements rest on `ASM-WEB-253` (the module is in scope), and `ASM-WEB-252` (request format, `FR-001`) and `GAP-WEB-252` (Scope section structure, `FR-002` to `FR-004`) as stated per item. The section stays `DRAFT-UNVERIFIED` until `Q-WEB-001` is answered.

**WEB-SCOPE-DOCS-FR-001: Load the published documentation**
- Given a reader opens the scope route, when the screen mounts, then the web app requests the published OpenAPI document once and shows the loading state until the request succeeds or fails.
- Traces to. `US-005-05` (AC-1); `ScopeDocumentationScreen` (`screens-inventory.md`).
- Verification. `scope-docs.component.requests-document-on-mount` (planned).
- API. `API-CONTRACTDOCS-304307`. The request format is `ASM-WEB-252`.

**WEB-SCOPE-DOCS-FR-002: Show the excluded capabilities explicitly**
- Given the document loads and contains Scope content, when the screen renders the Default variant, then attachments, mobile-specific endpoints and real-time features are each shown as an item stated to be out of scope.
- Traces to. `US-005-05` (AC-1); `BR-016`, `BR-017`, `BR-018`.
- Verification. `scope-docs.component.lists-three-excluded-capabilities` (planned).
- API. `API-CONTRACTDOCS-304307`. The Scope content is read from `info`. Its internal structure is unknown (`GAP-WEB-252`).

**WEB-SCOPE-DOCS-FR-003: Show every excluded capability the document lists**
- Given the served Scope content lists an excluded capability beyond the three named, when the screen renders, then that capability is also shown as an item.
- Given a reader checks this screen before proposing a feature, when the capability is excluded, then the reader sees it named as excluded (`US-005-05`, AC-2).
- Traces to. `US-005-05` (AC-2); `INV-WEB-251`.
- Verification. `scope-docs.component.renders-additional-served-items` (planned).
- API. `API-CONTRACTDOCS-304307`.

**WEB-SCOPE-DOCS-FR-004: Distinguish missing Scope content from "nothing excluded"**
- Given the request succeeds but the document has no Scope content, when the screen renders, then it shows the incomplete state (`web-app.scope-docs.states`). It does not show a blank page and does not imply that nothing is out of scope.
- Traces to. `US-005-05` (AC-1); `BR-015` (the scope is defined and bounded).
- Verification. `scope-docs.component.incomplete-document-state` (planned).
- API. `API-CONTRACTDOCS-304307`.

**WEB-SCOPE-DOCS-FR-005: Show the error state on failure**
- Given the request fails (a non-success response such as 406 or 5xx, a network failure, or an invalid document), when the failure is detected, then the screen shows the Error variant with a retry action. No partial or previously cached scope content is shown.
- Traces to. `ScopeDocumentationScreen` Error variant (`screens-inventory.md`); `US-005-05`.
- Verification. `scope-docs.component.error-state-and-retry` (planned).
- API. `API-CONTRACTDOCS-304307` (errors: 406 per the contract).

**WEB-SCOPE-DOCS-FR-006: Navigate to search**
- Given the screen is displayed, when the user activates either of the two navigation controls, then the app navigates to `SearchScreen` (proposed path `/notes`, `web-app.routing`).
- Traces to. Two `ON_CLICK→SearchScreen` transitions from `ScopeDocumentationScreen` (`screens-inventory.md`).
- Verification. `scope-docs.e2e.navigates-to-search` (planned).
- API. none.
- The design does not say which two controls these are (`GAP-WEB-254`).

**Invariants**
- `INV-WEB-251`: The scope content shown is the content served by `API-CONTRACTDOCS-304307` at view time. The web app holds no hard-coded copy of the out-of-scope list.
- `INV-WEB-252`: The screen performs no mutation. Its only network operation is the document read.
<!-- /SECTION: web-app.scope-docs.requirements -->
<!-- SECTION: web-app.scope-docs.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/scope` (proposed, `ASM-WEB-251`) | `ScopeDocumentationScreen` | `BLOCKED` | `BLOCKED` |

- **Path.** No input supplies a path. `web-app.routing` assigns this screen no route until `Q-WEB-001` is answered.
- **Layout and permission.** Whether the route is public or inside the authenticated app layout depends on whether the document endpoint and the app layout require a session. That is undecided (`Q-BE-003`, `GAP-WEB-001`). Unblocked by the decision on `Q-BE-003` or clean `BR-023` text.
- **Entry point.** No input says how a reader reaches this route (`GAP-WEB-013`).
- **Outgoing navigation.** `ScopeDocumentationScreen` links to `SearchScreen`, which is `/notes`. This is the two `ON_CLICK` transitions in `screens-inventory.md`.
<!-- /SECTION: web-app.scope-docs.routes -->
<!-- SECTION: web-app.scope-docs.components | status: BLOCKED -->
## Components

**Blocked.** `screens-inventory.md` lists no component for `ScopeDocumentationScreen`. Its components column is "-", and none of the four inventory components (`Button/Primary`, `Card/Item`, `Empty/State`, `Input/Text`) is assigned to it. No component, variant or token binding is invented here (`GAP-WEB-254`).

**Unblocked by:**
- design frames for `ScopeDocumentationScreen` that show its elements;
- the token value files, or a connected Figma MCP (`GAP-WEB-005`).
<!-- /SECTION: web-app.scope-docs.components -->
<!-- SECTION: web-app.scope-docs.states | status: UNKNOWN -->
## States

States are behavioural. The design supplies only two variants for this screen, Default and Error (`screens-inventory.md`).

| State | Condition | Behaviour | Design variant |
|---|---|---|---|
| Loading | The document request is in flight | A loading indication is shown. No scope content, and no "nothing excluded" message, appears. | none (`GAP-WEB-254`) |
| Populated | 200 with Scope content | The excluded capabilities are shown (`FR-002`, `FR-003`). | Default |
| Incomplete (the empty case) | 200 with no Scope content | A distinct message says scope information is unavailable. It must not read as "no exclusions" (`FR-004`). | none (`GAP-WEB-254`) |
| Error | Non-success response, network failure or unreadable document | An error message with a retry action. No partial content (`FR-005`). | Error |

- **Permission-denied.** Omitted for now. It applies only if the route turns out to be permission-gated, which is undecided (`GAP-WEB-001`, `GAP-WEB-011`). If it is gated, the "not allowed" versus "does not exist" rendering is a security decision made upstream.
- **Partial-failure.** Not applicable. The module has no multi-item operation.
<!-- /SECTION: web-app.scope-docs.states -->
<!-- SECTION: web-app.scope-docs.api-integration | status: VERIFIED -->
## API integration

| Operation | Pinned ID | Used for |
|---|---|---|
| Retrieve the published OpenAPI document (`GET /openapi`) | `API-CONTRACTDOCS-304307` | Loads the Scope content (`FR-001` to `FR-005`) |

- **Contract pin.** Contract `sha256:8e424bb2b40a` (`backend-api.md`). That contract's operation IDs carry no `@v` suffix.
- **Fields displayed.** The screen displays only the Scope content carried in `info`. The schema is defined once, in the contract.
- **Errors.** The contract lists 406 for this operation. No other operation is used.
- **Auth.** Whether this operation requires a session is not stated in the contract (`GAP-WEB-001`).
<!-- /SECTION: web-app.scope-docs.api-integration -->
<!-- SECTION: web-app.scope-docs.validation | status: DRAFT-UNVERIFIED -->
## Validation

The screen has no user input, so there are no client field rules.

| Rule | Client | Also enforced server-side |
|---|---|---|
| The response is a readable document before it is treated as populated | Yes (`FR-004`, `FR-005`) | n/a |
| The request names an acceptable format | Yes (`ASM-WEB-252`) | Yes. An unsupported `Accept` value yields 406 or a default-format fallback with a clear content type (`API-CONTRACTDOCS-304307`, `US-004-01`). |
| The Scope content is present | Yes (`FR-004`) | The contract states the document includes a Scope section (`API-CONTRACTDOCS-304307`) |

`GAP-WEB-255` records the missing media types.
<!-- /SECTION: web-app.scope-docs.validation -->
<!-- SECTION: web-app.scope-docs.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** The project spec names no accessibility standard or level, and says module specs specify focus management, keyboard paths and live regions once a target exists (`web-app.accessibility`, `GAP-WEB-007`).

**Facts that hold now:**
- The screen has no form, so there are no form errors to expose.
- The loading, incomplete and error states each need announcing. How they are exposed to assistive technology is not stated in any input.
- `FR-006` requires both navigation controls to be operable. The input method is not specified.

**Unblocked by:** a requirement naming the target standard and level and the verification method (`GAP-WEB-007`).
<!-- /SECTION: web-app.scope-docs.accessibility -->
<!-- SECTION: web-app.scope-docs.i18n | status: BLOCKED -->
## Internationalisation

**Blocked.** No input defines locales, a copy deck or message keys (`GAP-WEB-016`). No keys are invented here.

**Messages that will need copy:**
- the loading indication;
- the incomplete-scope message;
- the error message and the retry label;
- the labels of the two navigation controls.

The out-of-scope item text itself is served by the API. Its language is a backend matter and is not stated in any input.

**Unblocked by:** a copy deck and a locale list.
<!-- /SECTION: web-app.scope-docs.i18n -->
<!-- SECTION: web-app.scope-docs.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode` is `FULL-FROM-BRIEF`. Every item below is an unverified claim from `design-brief.md`, and none has been checked against Figma.

| Item | Value | Source | Verification |
|---|---|---|---|
| Screen | `ScopeDocumentationScreen` ("Scope Documentation", module M5) | `screens-inventory.md` | Unverified |
| Variants | Default, Error | `screens-inventory.md` | Unverified |
| Components | none listed | `screens-inventory.md` | Unverified |
| Outgoing transitions | `ON_CLICK→SearchScreen` (×2) | `screens-inventory.md` | Unverified |
| Stories mapped to the screen | `US-005-01`, `US-005-03`, `US-005-04`, `US-005-05`, `US-005-06` | `stories-map.md` | Unverified. This module owns `US-005-05` only (`GAP-WEB-256`). |
| Frame IDs | none supplied | n/a | n/a (`GAP-WEB-005`) |
| Token bindings | none supplied for this screen | n/a | n/a |
<!-- /SECTION: web-app.scope-docs.design-refs -->
<!-- SECTION: web-app.scope-docs.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are planned (none exist). Names are proposals.

**Component**
- `scope-docs.component.requests-document-on-mount` (`FR-001`)
- `scope-docs.component.lists-three-excluded-capabilities` (`FR-002`)
- `scope-docs.component.renders-additional-served-items` (`FR-003`)
- `scope-docs.component.incomplete-document-state` (`FR-004`)
- `scope-docs.component.error-state-and-retry` (`FR-005`)
- `scope-docs.component.shows-no-hardcoded-scope-copy` (`INV-WEB-251`): serve a document with different items and assert that exactly those are shown.

**E2E**
- `scope-docs.e2e.populated-view`
- `scope-docs.e2e.navigates-to-search` (`FR-006`)

**Contract**
- `scope-docs.contract.document-has-scope-content`: against `API-CONTRACTDOCS-304307`, assert the served document names attachments, mobile and real-time as excluded.
- `scope-docs.contract.unsupported-accept-yields-406-or-fallback`: the contract allows either outcome (`US-004-01`).
- Both depend on `GAP-WEB-252` and `GAP-WEB-255` for exact assertions.

**Accessibility**
- `scope-docs.a11y.automated-scan` is planned, but its standard and level are `BLOCKED` (`GAP-WEB-007`).
<!-- /SECTION: web-app.scope-docs.test-plan -->
<!-- SECTION: web-app.scope-docs.gaps | status: UNKNOWN -->
## Gaps, contradictions, assumptions and questions

**Contradictions (non-critical; neither side resolved)**
- `CON-WEB-251`
  - Claim A: the design maps `US-005-05` to `ScopeDocumentationScreen` (`stories-map.md`).
  - Claim B: the project coverage marks `US-005-05` "Not web". No EP-006 story requires the screen, and the default is out of scope (`web-app.coverage`, `ASM-WEB-005`).
  - If A is chosen: this module is built as specified here.
  - If B is chosen: this module is dropped, and the stakeholder reference stays in the API documentation only.
  - Reading used: A, because the dispatch assigns the story to this module (`ASM-WEB-253`).

**Questions**
- `Q-WEB-251` (degradable): Should the screen also show the data-residency handling note carried in the same document? Default: no, because `US-005-05` does not ask for it. Consequence of yes: an extra rendered block and an extra test.
- Related, already raised: `Q-WEB-001` (is the screen in the web app at all) and `Q-BE-003` (does the endpoint require a session).

**Assumptions**
- `ASM-WEB-251`: The route path is `/scope`. No input supplies it. It would be confirmed by a route table in the design or requirements.
- `ASM-WEB-252`: The web app requests the document in JSON via the `Accept` header. The contract names the header but lists no media types (`GAP-WEB-255`).
- `ASM-WEB-253`: The module is specified as in scope because the dispatch assigns `US-005-05` to it. It is confirmed only by an answer to `Q-WEB-001`.

**Gaps**
- `GAP-WEB-252`: The structure of the Scope content within `info` is not defined: how the excluded capabilities are keyed, ordered or described. **Backend must supply** a structure for it. Closing artefact: a contract revision describing the Scope section.
- `GAP-WEB-253`: The actor of `US-005-05` is the product owner. `web-app.authorization-ui` lists only Team member, and no web role is defined for a product owner or for stakeholders. Closing artefact: a role decision with the answer to `Q-WEB-001`.
- `GAP-WEB-254`: The design has no loading or empty variant, no component for this screen, and no labels for the two navigation controls. Closing artefact: design frames.
- `GAP-WEB-255`: The media types and format of `API-CONTRACTDOCS-304307` are not stated. **Backend must supply** them. Closing artefact: a contract revision.
- `GAP-WEB-256`: `US-005-01`, `US-005-03`, `US-005-04` and `US-005-06` are mapped to this screen by the design but owned by no module. Closing artefact: an ownership decision with the answer to `Q-WEB-001`.
- Already open in the project spec and affecting this module: `GAP-WEB-001`, `GAP-WEB-005`, `GAP-WEB-007`, `GAP-WEB-010`, `GAP-WEB-011`, `GAP-WEB-013`, `GAP-WEB-016`.
<!-- /SECTION: web-app.scope-docs.gaps -->
