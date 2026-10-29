---
id: agent-developer/web_app/web-app/modules/login-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/login-SPEC.md
depends_on: [web_app:US-006-05]
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/modules/login-SPEC.md
---

<!-- SECTION: web-app.login.frontmatter | status: DRAFT-UNVERIFIED -->
## Frontmatter

```yaml
id: login
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001, GAP-WEB-006, GAP-WEB-015]
contract_pin: "sha256:ceedbd350666"
scope: ["/login", "/login/code"]
```

- `spec_mode` is `FULL-FROM-BRIEF`. The design brief is present and Figma is not connected (`RUN-CONTEXT.inputs.figma_mcp`). Every screen and token name in this module is an unverified claim.
- The route paths are proposals (`ASM-WEB-001` in WEB-APP-SPEC).
- `blocked_by` refers to project-level items. `GAP-WEB-001` is the session decision, `GAP-WEB-006` is the error copy, and `GAP-WEB-015` is the unfilled placeholders in `requirements.md` BR-023. All three are defined in WEB-APP-SPEC.
<!-- /SECTION: web-app.login.frontmatter -->
<!-- SECTION: web-app.login.purpose | status: DERIVED -->
## Purpose

**Responsible for** (US-006-05; `requirements.md` BR-022, BR-023 Exception):

- The two public sign-in screens, `LoginScreen` and `CodeEntryScreen`.
- Collecting the email address and requesting a one-time code.
- Collecting the code and verifying it.
- Showing a distinct, matching error for each refusal: wrong, expired, already used, resend limit reached and account locked (BR-022).
- Requesting a new code from the code-entry screen.
- Disabling code submission while a lockout is shown (US-006-05).

**Not responsible for:**

- **Session handling.** The credential issued on success, its lifetime, transport, refresh, sign-out, the protected-route rule and the unauthenticated redirect belong to `web-app.auth-flows` and `web-app.routing`. They are `BLOCKED` by `GAP-WEB-001`, `Q-BE-003` and `GAP-WEB-015`.
- **Enforcement of limits.** The 10-minute validity, the resend limit, the lockout and single use are enforced only by `notes_api` (`INV-WEB-002`).
- **Email delivery.**
- **Application chrome**, including a sign-out control (`GAP-WEB-008` in WEB-APP-SPEC).
- **Notes, search, archive and every other module.** This module does not specify where a successful sign-in lands. It cites the project default (`ASM-WEB-009` in WEB-APP-SPEC).
<!-- /SECTION: web-app.login.purpose -->
<!-- SECTION: web-app.login.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Statuses are cited by number only, for example "the 423 response". Behaviour per status follows `web-app.auth-flows`. Nothing here restates the contract's shapes.

**WEB-LOGIN-FR-001: Request a code**
- Given the user is on `/login` and has entered an email address,
- When the user submits,
- Then the web app trims the address (`ASM-WEB-001`), calls `API-AUTH-015365`, and on a 202 response navigates to `/login/code`, holding the email in memory only (`ASM-WEB-001` in WEB-APP-SPEC). It displays no code (`INV-WEB-001`).
- **Traces to.** US-006-05 (criterion 1); BR-023 Exception (callable without a session token).
- **Verification.** `login.e2e.request-code-success` (planned).
- **API.** `API-AUTH-015365`.

**WEB-LOGIN-FR-002: Request-code failures on `/login`**
- Given a submission from `/login`,
- When the email is empty after trimming, then no request is sent and an inline error is shown on the email field (`ASM-WEB-002`).
- When the response is 400, then an inline error is shown on the email field. The field-detail shape is `GAP-WEB-007` in WEB-APP-SPEC.
- When the response is 429, then the resend-limit error is shown, the user stays on `/login`, and the submit control remains enabled (`ASM-WEB-010`).
- When the response is 500 or the request fails at the network level, then a failure state with retry is shown and the entered email is retained (`ASM-WEB-011` in WEB-APP-SPEC).
- **Traces to.** US-006-05 (criterion 3, resend limit; purpose clause "understand why a code was refused"); BR-022.
- **Verification.** `login.component.email-form.errors`, `login.e2e.request-code-resend-limit` (planned).
- **API.** `API-AUTH-015365`.

**WEB-LOGIN-FR-003: Submit a code**
- Given the user is on `/login/code` with an email held and has entered a code,
- When the user submits and the code is exactly 6 digits (`ASM-WEB-003`),
- Then the web app calls `API-AUTH-592570` with the held email and the entered code.
- When the code is not exactly 6 digits, then no request is sent and an inline error is shown on the code field.
- **Traces to.** US-006-05 (criterion 1).
- **Verification.** `login.component.code-form.rejects-non-6-digit` (planned).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-004: Wrong code**
- Given a verify request,
- When the response is 401,
- Then the wrong-code error is shown. The code field stays editable, and no remaining-attempts figure is shown because no input supplies one.
- The web app applies no precedence among refusals (`ASM-BE-003` in `backend.coverage`; `INV-WEB-002`).
- **Traces to.** BR-022 ("matching error for each case"); US-006-05 (purpose clause).
- **Verification.** `login.e2e.verify-wrong-code` (planned).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-005: Expired code**
- Given a verify request,
- When the response is 410,
- Then the expired-code error is shown with an option to request a new code. That option is the resend control of FR-008.
- **Traces to.** US-006-05 (criterion 3); BR-022.
- **Verification.** `login.e2e.verify-expired-then-resend` (planned).
- **API.** `API-AUTH-592570`, `API-AUTH-015365`.

**WEB-LOGIN-FR-006: Already-used code**
- Given a verify request,
- When the response is 409,
- Then a distinct already-used-code error is shown. Its copy is unspecified (`GAP-WEB-006` in WEB-APP-SPEC).
- **Traces to.** BR-022 ("matching error for each case").
- **Verification.** `login.component.error-region.per-status` (planned).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-007: Lockout**
- Given a verify request,
- When the response is 423,
- Then the lockout error is shown, stating that the account is locked for 15 minutes, and code submission is disabled (`INV-WEB-005` in WEB-APP-SPEC).
- Lockout is shown only on a 423 response (`CON-WEB-001`, `ASM-WEB-004`).
- The resend control is unchanged by lockout (`Q-WEB-002`).
- No countdown, remaining time or re-enable moment is shown because the contract defines none (`GAP-WEB-005` in WEB-APP-SPEC).
- **Traces to.** US-006-05 (criterion 2); BR-022.
- **Verification.** `login.e2e.verify-lockout`, `login.component.code-form.lockout-disables-submit` (planned).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-008: Request a new code from `/login/code`**
- Given the user is on `/login/code`,
- When the user activates the resend control (`ASM-WEB-006`),
- Then the web app calls `API-AUTH-015365` with the held email.
  - On 202 the user stays on the screen and a confirmation that a new code was requested is shown.
  - On 429 the resend-limit error is shown and no further resend is offered (`ASM-WEB-010`).
  - On 400, 500 or network failure the corresponding error is shown with retry.
- The web app keeps no resend counter and shows no remaining-resends figure (`INV-WEB-002`; `CON-WEB-003` in WEB-APP-SPEC; `CON-BE-004` in `backend.coverage`).
- **Traces to.** US-006-05 (criterion 3); BR-022.
- **Verification.** `login.e2e.resend-then-limit` (planned).
- **API.** `API-AUTH-015365`.

**WEB-LOGIN-FR-009: Verify 400**
- Given a verify request,
- When the response is 400,
- Then an inline error is shown on the code field.
- **Traces to.** US-006-05 (purpose clause); `API-AUTH-592570` error responses.
- **Verification.** `login.contract.verify-status-mapping` (planned).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-010: Verify 500 or network failure**
- Given a verify request,
- When the response is 500 or the request fails at the network level,
- Then a failure state with retry is shown and the held email and entered code are retained (`ASM-WEB-011` in WEB-APP-SPEC).
- **Traces to.** US-006-05 (purpose clause); `API-AUTH-592570` error responses.
- **Verification.** `login.component.error-region.per-status` (planned).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-011: Verify success. `BLOCKED` in part**
- Given a verify request,
- When the response is 200,
- Then the web app signs the user in (US-006-05) and navigates to `/notes` (`ASM-WEB-009` in WEB-APP-SPEC).
- **BLOCKED:** what the response issues, how it is held and sent, its lifetime, refresh and sign-out. `API-AUTH-592570` defines no credential (`Q-BE-003`, `GAP-WEB-001`).
- Unblocked by: the backend session-model decision and a clean `requirements.md` BR-023 (`GAP-WEB-015`).
- **Traces to.** US-006-05 (criterion 1).
- **Verification.** `login.e2e.verify-success` (planned; assertions on the credential `BLOCKED`).
- **API.** `API-AUTH-592570`.

**WEB-LOGIN-FR-012: Code-entry precondition**
- Given no email is held in memory,
- When the user opens `/login/code` directly or reloads it,
- Then the web app redirects to `/login` (`ASM-WEB-001` in WEB-APP-SPEC).
- **Traces to.** US-006-05 (criterion 1: the code-entry screen follows email submission).
- **Verification.** `login.e2e.code-entry-redirect` (planned).
- **API.** none.

**Invariants**
- `INV-WEB-001`: The web app never displays a login code other than what the user typed. The request-code response does not reveal one (`API-AUTH-015365`).
- `INV-WEB-002`: Validity, resend limit, lockout and single use are enforced only by `notes_api`. The web app keeps no counter or timer for them and reacts only to responses (BR-022 Enforcement; `web-app.auth-flows`).
<!-- /SECTION: web-app.login.requirements -->
<!-- SECTION: web-app.login.routes | status: DRAFT-UNVERIFIED -->
## Routes

| Route (proposed) | Page | Layout | Required permission |
|---|---|---|---|
| `/login` | `LoginScreen` | public | none. Request-code is callable without a session token (BR-023 Exception). |
| `/login/code` | `CodeEntryScreen` | public | none. Precondition: an email is held in memory, otherwise redirect to `/login` (FR-012). |

- The public layout wraps both routes (`web-app.routing`). The design has no frame for application chrome (`GAP-WEB-008` in WEB-APP-SPEC).
- Behaviour when an already-signed-in visitor opens either route depends on the session model and is `BLOCKED` (`GAP-WEB-001`).
- The paths are proposals (`ASM-WEB-001` in WEB-APP-SPEC).
<!-- /SECTION: web-app.login.routes -->
<!-- SECTION: web-app.login.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.components -->
<!-- SECTION: web-app.login.states | status: DRAFT-UNVERIFIED -->
## States

Both routes are public, so permission-denied does not apply. Neither route has a multi-item operation, so partial-failure does not apply.

**`/login` (`LoginScreen`)**

| State | Presentation |
|---|---|
| Empty | The email field is empty and the submit control is available (FR-002 handles an empty submission). |
| Populated | The email is entered. |
| Loading | A request-code call is in flight. The submit control shows Loading and is not activatable, and the field is not editable (`ASM-WEB-005`). |
| Error | One of the following (FR-002): inline email error (400), resend-limit error (429), failure with retry (500 or network). The email is retained. |

**`/login/code` (`CodeEntryScreen`)**

| State | Presentation |
|---|---|
| Empty | The code field is empty. A hint of 10-minute validity may be shown (BR-022). |
| Populated | A code is entered. |
| Loading | A verify or resend call is in flight, with the same pending behaviour as above. |
| Error: wrong (401) | Wrong-code error (FR-004). |
| Error: expired (410) | Expired-code error with the option to request a new code (FR-005). |
| Error: used (409) | Already-used error (FR-006). |
| Error: invalid input (400, or not 6 digits) | Inline error on the code field (FR-003, FR-009). |
| Error: locked (423) | Lockout error stating the account is locked for 15 minutes, with code submission disabled (FR-007). |
| Error: resend limit (429) | Resend-limit error with the resend control disabled (FR-008, `ASM-WEB-010`). |
| Error: failure | Retryable failure state, with email and code retained (FR-010). |
| Confirmation | After a successful resend, a message that a new code was requested (FR-008). |

- Lockout and the resend-limit state last for the life of the mounted screen. No timer or re-enable is specified (`ASM-WEB-004`; `GAP-WEB-005` in WEB-APP-SPEC).
- The design has only a Default variant for each screen (`GAP-WEB-004` in WEB-APP-SPEC), so these states are described, not drawn.
<!-- /SECTION: web-app.login.states -->
<!-- SECTION: web-app.login.api-integration | status: VERIFIED -->
## API integration

References only. Shapes, types and error tables live in the contract (`sha256:ceedbd350666`, `backend-api.md`).

| Operation | Pinned ID | Used by |
|---|---|---|
| Request a login code | `API-AUTH-015365` | `LoginScreen` submit (FR-001, FR-002); `CodeEntryScreen` resend (FR-005, FR-008) |
| Verify a login code | `API-AUTH-592570` | `CodeEntryScreen` submit (FR-003, FR-004, FR-005, FR-006, FR-007, FR-009, FR-010, FR-011) |

- The contract's IDs carry no `@v` suffix.
- No refresh, sign-out or session operation exists in the contract (`GAP-WEB-001`).
<!-- /SECTION: web-app.login.api-integration -->
<!-- SECTION: web-app.login.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Field | Client rule | Also enforced server-side |
|---|---|---|
| Email | Trimmed (`ASM-WEB-001`); must be non-empty. No format check (`ASM-WEB-002`). | Yes. `API-AUTH-015365` documents a 400 response. The exact rule is not stated by the contract. |
| Code | Exactly 6 numeric digits before any request is sent (`ASM-WEB-003`). The format is stated by `API-AUTH-592570`. | Yes. `API-AUTH-592570` documents a 400 response. Whether a malformed code counts toward the lock is open (`GAP-BE-008` in `backend.coverage`). |

- Server-only rules, never evaluated by the client (`INV-WEB-002`):
  - 10-minute validity;
  - resend limit;
  - 5-wrong-code lockout for 15 minutes;
  - single use.
- Inline errors for 400 responses use the field-detail the server supplies. Its shape is `GAP-WEB-007` in WEB-APP-SPEC.
<!-- /SECTION: web-app.login.validation -->
<!-- SECTION: web-app.login.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No input names an accessibility standard or level, and the project-level accessibility section carries no content. Everything below is a proposal (`ASM-WEB-009`).

- **Focus on arrival.** Focus goes to the email field on `/login` and to the code field on `/login/code`.
- **Focus on failure.**
  - Field-level errors (400, wrong format) move focus to the offending field.
  - Form-level errors (401, 409, 410, 429, 500) leave focus on the code field.
  - On lockout the code field is disabled, so focus moves to the message region.
- **Keyboard.** Enter submits the focused form. Tab order is field, submit, resend. Every control is reachable and operable without a pointer.
- **Live regions.** Every error and the "new code requested" confirmation are announced through a live region. Messages do not auto-dismiss.
- **Form errors.** Each field error is programmatically associated with its field. Error and disabled states are conveyed by text and icon (`UI/icon/error`), never by colour alone. A disabled submit control is accompanied by the lockout message that explains it.
- **Input hints.** The email and code fields should carry autocomplete and input-mode hints appropriate to an email address and a numeric one-time code.
<!-- /SECTION: web-app.login.accessibility -->
<!-- SECTION: web-app.login.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No input names a locale or supplies any copy key. The only supplied wording is the phrase "locked for 15 minutes" (US-006-05). Copy for every refusal is supplied nowhere (`GAP-WEB-006` in WEB-APP-SPEC).

Copy is needed for these states:
- empty email;
- 400 on email;
- resend limit (429);
- request failure;
- code not 6 digits;
- wrong code (401);
- expired code (410);
- already-used code (409);
- lockout (423);
- verify failure;
- "new code requested" confirmation;
- validity hint.

Unblocked by: a locale list and the copy for each state above.
<!-- /SECTION: web-app.login.i18n -->
<!-- SECTION: web-app.login.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma is not connected, so nothing below is verified against Figma. No frame IDs were supplied. `design-brief.md` is the only source.

| Screen | Design label | Variants | Components listed | Frame ID | Verification |
|---|---|---|---|---|---|
| `LoginScreen` | M6 | Default | `Button/Primary` | none supplied | unverified |
| `CodeEntryScreen` | M6 | Default | `Button/Primary` | none supplied | unverified |

- Neither screen has outgoing transitions in the design (`screens-inventory.md`).
- `stories-map.md` maps US-006-05 to `CodeEntryScreen`. It maps no story to `LoginScreen`. The mapping to `LoginScreen` here is taken from US-006-05's text and the M6 label.
- Token names are listed in `web-app.login.components`. All are unverified, and their values are not in any input.
- `Input/Text` is not listed for either screen (`GAP-WEB-002`).
<!-- /SECTION: web-app.login.design-refs -->
<!-- SECTION: web-app.login.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are `(planned)`. No test tooling is named by any input.

**Component**
- `login.component.email-form.errors` covers the empty email, 400, 429 and 500 states.
- `login.component.email-form.pending-blocks-resubmit` covers a second activation while a request is in flight.
- `login.component.code-form.rejects-non-6-digit` asserts that no request is sent.
- `login.component.code-form.lockout-disables-submit` covers a 423 response.
- `login.component.error-region.per-status` covers 400, 401, 409, 410, 423, 429 and 500, with one distinct presentation per documented status.

**E2E**
- `login.e2e.request-code-success`
- `login.e2e.request-code-resend-limit`
- `login.e2e.verify-wrong-code`
- `login.e2e.verify-expired-then-resend`
- `login.e2e.verify-lockout`
- `login.e2e.resend-then-limit`
- `login.e2e.code-entry-redirect`
- `login.e2e.verify-success`, with credential assertions `BLOCKED` (FR-011)

**Contract** (against pin `sha256:ceedbd350666`)
- `login.contract.request-status-mapping` and `login.contract.verify-status-mapping` assert that every documented status of each operation maps to exactly one behaviour. An undocumented status falls to the failure state.
- `login.contract.only-two-operations` asserts that the module calls no operation other than the two in `web-app.login.api-integration`.

**Accessibility** (standard unknown, `ASM-WEB-009`)
- `login.a11y.keyboard-only-flow`
- `login.a11y.focus-placement`
- `login.a11y.live-region-announcements`
- `login.a11y.state-not-by-colour-alone`
<!-- /SECTION: web-app.login.test-plan -->
<!-- SECTION: web-app.login.gaps | status: DRAFT-UNVERIFIED -->
## Gaps, contradictions, assumptions and questions

**Numbering note.** The ids reserved for this module (`ASM-`, `CON-`, `GAP-`, `INV-`, `Q-`) overlap some ids already defined in WEB-APP-SPEC. Ids in the tables below are this module's. A project-level id is marked "(WEB-APP-SPEC)" wherever it is cited.

**Gaps**

| ID | Statement | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-WEB-002` | The design lists only `Button/Primary` for `LoginScreen` and `CodeEntryScreen`. Nothing binds the email field or the code field to a component, and which `Button/Primary` variants apply is not stated. | `components`, `design-refs` | Design frames or an inventory update for the email and code controls and the button variants. |
| `GAP-WEB-003` | No story or design transition lets a user return from `/login/code` to correct a mistyped email. This module specifies none (`Q-WEB-001`). | `routes`, `states` | A story or design transition. |

**Contradictions**

| ID | Claims | Reading adopted | Consequence of each side |
|---|---|---|---|
| `CON-WEB-001` | US-006-05 describes lockout after the 5th wrong code. The backend returns 401 for the 5th wrong code and applies the lock from the next attempt (`ASM-BE-004` in `backend.coverage`; `backend.error-model`). | Show lockout only when the verify response is 423. The story itself says "when the verify-code endpoint reports the account is locked". | Adopted: the UI shows a wrong-code error on the 5th attempt and lockout on the 6th. The alternative, counting 5 locally, would diverge from the server counter, which is per email and resets on a successful verify. Non-critical: it changes which message shows on one attempt. |

**Assumptions**

| ID | Assumption | What would confirm it |
|---|---|---|
| `ASM-WEB-001` | The email is trimmed client-side and otherwise sent as typed. The backend compares case-insensitively (`backend.coverage`, `ASM-BE-001`). | The product owner. |
| `ASM-WEB-002` | The client checks only that the email is non-empty. Format validity is left to the server's 400. | A validation rule from product. |
| `ASM-WEB-003` | The client sends a verify request only when the code is exactly 6 digits. Story text "6 digits-digit" is garbled and the contract states a 6-digit numeric code. A blocked attempt makes no call, so it cannot count toward the lock. | A backend decision on malformed codes (`GAP-BE-008`). |
| `ASM-WEB-004` | Lockout is derived only from a 423 response. The web app keeps no attempt counter or timer. Submission stays disabled while the screen is mounted, and a fresh visit recomputes the state from a new response. | Backend remaining-lock-time and re-enable rules (`GAP-WEB-005` in WEB-APP-SPEC). |
| `ASM-WEB-005` | While a request is pending, the submit control shows Loading and is not activatable, and the related field is not editable. This avoids duplicate requests that would consume the resend limit. | Design annotation. |
| `ASM-WEB-006` | `CodeEntryScreen` has a resend control, required by US-006-05 ("an option to request a new code"). It reuses `Button/Primary`, the only action component. | A design frame. |
| `ASM-WEB-007` | Token bindings are proposals derived from names in `tokens-index.md`. Values are unseen. | Figma verification. |
| `ASM-WEB-008` | Messages render as plain text in a message region, because no message component exists. Field-level for 400 and format failures, form-level for the rest. | Design frames with a message component. |
| `ASM-WEB-009` | The accessibility behaviours are proposals because no input names a standard. | A stated standard and level. |
| `ASM-WEB-010` | On a 429 from `/login` the submit control stays enabled, because the server decides when the window ends. On `/login/code` the resend control is disabled after a 429, because US-006-05 says "no more resends" are offered. | A retry-after or re-enable rule (`GAP-WEB-005` in WEB-APP-SPEC). |

**Questions** (all degradable, answered by default because the run is unattended)

| ID | Question | Default taken |
|---|---|---|
| `Q-WEB-001` | Should `CodeEntryScreen` let the user go back and correct the email? | No control is specified (`GAP-WEB-003`). |
| `Q-WEB-002` | Does lockout also disable the resend control? | No. US-006-05 disables only code submission, so the resend control is unchanged. |

**Project-level items this module depends on** (defined in WEB-APP-SPEC and the backend; cited, not re-minted)

| ID | Statement | Needed from |
|---|---|---|
| `GAP-WEB-001` | Session model: what a successful verify issues, its lifetime and transport, refresh, sign-out, and whether notes and search require it (`Q-BE-003`). | Backend. |
| `GAP-WEB-005` | Remaining lock time, retry-after, re-enable rule, and whether lockout presentation survives a reload. | Backend (`GAP-BE-008`, `GAP-BE-011` in `backend.coverage`). |
| `GAP-WEB-006` | Copy for every refusal state. | Product. |
| `GAP-WEB-007` | Field-detail shape of 400 responses. | Backend. |
| `GAP-WEB-015` | BR-023 Enforcement text holds unfilled placeholders. | Business-rule owner. |
| `CON-WEB-003` | The resend count: BR-022 says "3 resends", the backend counts 3 requests in total (`CON-BE-004`). The web app shows no remaining-resends figure. | Product. |
<!-- /SECTION: web-app.login.gaps -->
