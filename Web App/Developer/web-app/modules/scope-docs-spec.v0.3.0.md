---
id: agent-developer/web_app/web-app/modules/scope-docs-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/scope-docs-SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/scope-docs-SPEC.md
---

<!-- SECTION: web-app.scope-docs.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: scope-docs
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-011, Q-WEB-002, GAP-WEB-323]
contract_pin: "sha256:9550f0d579d8"
scope: []
```

`scope` is empty because this module owns no routes (`web-app.module-map`, `web-app.routing`: "Not routed", `Q-WEB-002`). `spec_mode` is `FULL-FROM-BRIEF`: `design-brief.md` is present and Figma is not connected, so every design name below is unverified.
<!-- /SECTION: web-app.scope-docs.frontmatter -->
<!-- SECTION: web-app.scope-docs.purpose | status: DRAFT-UNVERIFIED -->
## Purpose

**Owns.** Module `scope-docs`, design label M5, which holds the screen `ScopeDocumentationScreen` (`design-brief.md`, `screens-inventory.md`; `web-app.module-map`).

**What the inputs say about the screen.**
- Its design variants are Default and Error.
- Its listed component is `Button/Primary`.
- It has two design transitions, both `ON_CLICK→SearchScreen`.
- `stories-map.md` attaches US-005-01, US-005-03, US-005-04, US-005-05 and US-005-06 to it.
- Those five stories are EP-005 API-scope stories, and none describes a web user action (`stories.md`).

**Status.**
- No web story requires this screen, so it is not routed and not built.
- This is the project-spec default for `Q-WEB-002`, and the module map withholds a module spec until `Q-WEB-002` is answered.
- This document is therefore a placeholder that records what is known.
- What the screen should show is not stated by any input (`GAP-WEB-324`).
- If the screen were built, it would plausibly display the API's documented out-of-scope list (US-005-05). That is an assumption (`ASM-WEB-317`).

**Not responsible for:**
- Enforcing API scope. The scope-compliance suite, route registry checks and CI gates belong to `notes_api` (US-005-01, US-005-03, US-005-04, `requirements.md` NFR-010).
- Authoring or publishing the API's Scope section. That is backend `contract-docs`.
- Navigation destinations. `SearchScreen` belongs to module `search`.
- Residency screens (`residency` module) and every EP-006 flow.
<!-- /SECTION: web-app.scope-docs.purpose -->
<!-- SECTION: web-app.scope-docs.requirements | status: BLOCKED -->
## Requirements

**Blocked.** No functional requirement can be written without inventing one.
- Every requirement needs a `Traces to.` story (core rule 7).
- The only stories attached to this screen are US-005-01, US-005-03, US-005-04, US-005-05 and US-005-06. They are API-scope stories whose actors are an API tech lead, QA engineer, product owner and backend developer. Their acceptance criteria describe API routes, CI checks and published API documentation, not browser behaviour (`stories.md`).
- No EP-006 story, which is the only web epic, references this screen.

No `WEB-SCOPE-DOCS-FR-nnn` is minted.

Unblocked by: a web story for this screen, or a decision to drop it from the web app (`GAP-WEB-011`, `Q-WEB-002`, `GAP-WEB-323`).
<!-- /SECTION: web-app.scope-docs.requirements -->
<!-- SECTION: web-app.scope-docs.routes | status: BLOCKED -->
## Routes

**Blocked.** No route is assigned (`web-app.routing`: "Not routed (`Q-WEB-002`, `GAP-WEB-011`)").
- No input supplies a path, layout or permission for this screen.
- Proposing a path would be an invention.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| none | `ScopeDocumentationScreen` | none | none |

Unblocked by: `Q-WEB-002` answered "yes", a route table entry, and the session decision (`GAP-WEB-001`) for any permission rule.
<!-- /SECTION: web-app.scope-docs.routes -->
<!-- SECTION: web-app.scope-docs.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `scope-docs`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.scope-docs.components -->
<!-- SECTION: web-app.scope-docs.states | status: BLOCKED -->
## States

**Blocked.** The screen is not routed, and no input defines its content.

| State | Status |
|---|---|
| Loading | Not designed. Blocked: the data source is unconfirmed (`ASM-WEB-317`). |
| Empty | Not designed. Blocked: what "empty" means is unknown (`GAP-WEB-324`). |
| Populated | The design has a Default variant, but its content is not described in any input. Blocked. |
| Error | The design has an Error variant (`screens-inventory.md`). Its content, trigger and message are not described. Blocked. |

- **Permission-denied.** Omitted. It applies only to permission-gated routes, and this module has no route.
- **Partial-failure.** Omitted. It applies only to multi-item operations, and this module has none.

Unblocked by: a routing decision, a screen description, and error-variant frames (`GAP-WEB-324`).
<!-- /SECTION: web-app.scope-docs.states -->
<!-- SECTION: web-app.scope-docs.api-integration | status: DRAFT-UNVERIFIED -->
## Api integration

Reference only. Request and response shapes are in the contract and are not restated.

| Operation | Pinned ID | Status |
|---|---|---|
| Return the Scope section, listing what the API covers and what it excludes | `API-CONTRACTDOCS-206969` | Candidate only. No input says the web app calls it (`ASM-WEB-317`). |

- No other contract operation is claimed for this module.
- The contract IDs carry no `@v` suffix (`web-app.overview`).
- No story requires a web call, so no integration is committed.
<!-- /SECTION: web-app.scope-docs.api-integration -->
<!-- SECTION: web-app.scope-docs.validation | status: DERIVED -->
## Validation

**Client rules.** None.
- The screen's only listed component is `Button/Primary` (`screens-inventory.md`), so there is no user input to validate.
- Derivation: no entry component (`Input/Text`) is listed for the screen.

**Server-side enforcement.** None applies to this module.
<!-- /SECTION: web-app.scope-docs.validation -->
<!-- SECTION: web-app.scope-docs.accessibility | status: BLOCKED -->
## Accessibility

**Blocked.** The project-level accessibility section is itself `BLOCKED`: no standard or conformance level is stated (`web-app.accessibility`, `GAP-WEB-008`).
- No focus, keyboard, live-region or form-error behaviour is specified for this module.
- The screen is not routed and has no known content.

Unblocked by: an accessibility standard (`GAP-WEB-008`) and a screen description (`GAP-WEB-324`).
<!-- /SECTION: web-app.scope-docs.accessibility -->
<!-- SECTION: web-app.scope-docs.i18n | status: BLOCKED -->
## I18n

**Blocked.** No input supplies copy keys, locales or screen text for this module (`GAP-WEB-325`).

Unblocked by: a locale list and the copy for the screen, once its content is defined.
<!-- /SECTION: web-app.scope-docs.i18n -->
<!-- SECTION: web-app.scope-docs.design-refs | status: DRAFT-UNVERIFIED -->
## Design refs

`spec_mode` is `FULL-FROM-BRIEF`. Figma is not connected, so nothing below is verified against Figma.

| Item | Value | Verification |
|---|---|---|
| Screen | `ScopeDocumentationScreen`, title "Scope Documentation", module M5 | Unverified, from `screens-inventory.md` |
| Variants | Default, Error | Unverified |
| Components | `Button/Primary` | Unverified |
| Outgoing transitions | `ON_CLICK→SearchScreen` (twice) | Unverified |
| Frame IDs | none supplied | Not available |
| Token names | none bound to this screen | Not available (`GAP-WEB-326`) |

Stories attached by `stories-map.md`, with no web requirement behind them: US-005-01, US-005-03, US-005-04, US-005-05, US-005-06 (`CON-WEB-304`).
<!-- /SECTION: web-app.scope-docs.design-refs -->
<!-- SECTION: web-app.scope-docs.test-plan | status: BLOCKED -->
## Test plan

**Blocked.** Tests verify requirements, and this module has none (see `web-app.scope-docs.requirements`).
- No component, E2E, contract or accessibility test is proposed.
- Scope-compliance tests for the API surface belong to `notes_api` (US-005-04).

Unblocked by: a requirement for this screen.
<!-- /SECTION: web-app.scope-docs.test-plan -->
<!-- SECTION: web-app.scope-docs.gaps | status: VERIFIED -->
## Gaps

**Gaps**

| ID | Missing | Degrades | Done instead | Closes with |
|---|---|---|---|---|
| `GAP-WEB-323` | A web story that requires `ScopeDocumentationScreen`. The five attached stories are API stories. | `requirements`, `routes`, `test-plan` | Sections marked `BLOCKED`. No FR minted. | A web story, or confirmation the screen is out of scope. |
| `GAP-WEB-324` | A description of what the screen shows, its Loading, Empty and Error behaviour, and its trigger for Error. The design brief gives only its title, variants and component. | `states`, `accessibility`, `components` | States marked `BLOCKED`. | Design frames or a written screen description. |
| `GAP-WEB-325` | Copy keys and locales. | `i18n` | `BLOCKED`. | A locale list and copy. |
| `GAP-WEB-326` | Token bindings for `Button/Primary` on this screen. | `components`, `design-refs` | No binding asserted. | Figma verification or component bodies. |

Related project-level items, which are referenced and not redefined: `GAP-WEB-001`, `GAP-WEB-005`, `GAP-WEB-008`, `GAP-WEB-011`, `Q-WEB-002`.

**Contradictions** (not Critical)

| ID | Claims | Consequence of each reading | Reading used |
|---|---|---|---|
| `CON-WEB-304` | `stories-map.md` attaches US-005-01, US-005-03, US-005-04, US-005-05 and US-005-06 to `ScopeDocumentationScreen`. `stories.md` defines those stories as API and CI behaviour with API-side actors. | If the mapping is right, the screen needs web requirements that no input states. If the stories are right, the screen has no web owner. | The stories. No web requirement is written. |

**Assumptions**

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-317` | If built, the screen would display the API's Scope section (what is covered, what is excluded) from `API-CONTRACTDOCS-206969`, read-only. | A screen description or story. |
| `ASM-WEB-318` | The two design transitions to `SearchScreen` are triggered by `Button/Primary` instances. | Design frames. |
| `ASM-WEB-319` | The module is not routed, matching the project-spec default for `Q-WEB-002`. | `Q-WEB-002` answered. |

**Questions**

| ID | Class | Question | Default adopted |
|---|---|---|---|
| `Q-WEB-305` | degradable | Should the web app display the API's out-of-scope list at all, given no web story asks for it? | No; the module stays unbuilt (`ASM-WEB-319`). |

No question is blocking.
<!-- /SECTION: web-app.scope-docs.gaps -->
