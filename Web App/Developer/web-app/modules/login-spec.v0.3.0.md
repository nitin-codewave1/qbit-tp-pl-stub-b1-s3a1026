---
id: agent-developer/web_app/web-app/modules/login-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/login-SPEC.md
depends_on: [web_app:US-006-05]
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/modules/login-SPEC.md
---

<!-- SECTION: web-app.login.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: login
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-001]
contract_pin: "sha256:9550f0d579d8"
scope: [/login, /login/code]
```

Sources:
- Module `login`, design label M6, screens `LoginScreen` and `CodeEntryScreen`, routes `/login` and `/login/code`: project spec `web-app.module-map` and `web-app.routing`. Route paths are proposals (`ASM-WEB-001`).
- `spec_mode`: Figma is `not-connected` and `design-brief.md` is present (core 4.5).
- `blocked_by`: sign-in completion waits on the session decision (`GAP-WEB-001`). Every other part of the module is specified.
<!-- /SECTION: web-app.login.frontmatter -->
<!-- SECTION: web-app.login.purpose | status: DERIVED -->
## Purpose

**Owns** (`web-app.module-map`; story `US-006-05`):
- Email entry on `LoginScreen`, and requesting a one-time code (`API-AUTH-015365`).
- Code entry on `CodeEntryScreen`, and verifying the code (`API-AUTH-592570`).
- Showing the wrong-code, already-used, expired, locked and resend-limit outcomes (`requirements.md` BR-022; `stories.md` US-006-05).
- The resend control on `CodeEntryScreen` (`ASM-WEB-016`).
- Holding the email in memory between the two screens, and the guard that redirects a visit to `/login/code` with no email held (`ASM-WEB-006`).
- Keeping the email and code out of URLs, storage and logs (`INV-WEB-006`).

**Does not own:**
- **Session establishment and its consequences.** This covers the credential returned by verify-code, its lifetime and transport, refresh, expiry mid-flow, logout, the protected-route rule and the 401 redirect on protected routes. All of it is `BLOCKED` (`GAP-WEB-001`, `web-app.auth-flows`, `web-app.routing`).
- **Enforcing the limits.** `notes_api` enforces code expiry, the resend limit and the lockout. This module only displays what the responses say (`requirements.md` BR-022, Enforcement).
- **Application chrome and the notes screens.** These belong to `notes`, `search` and `archive`. `login` depends on none of them.
- **Other roles and surfaces.** The only actor is a Team member (`ASM-WEB-005`).
<!-- /SECTION: web-app.login.purpose -->
<!-- SECTION: web-app.login.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All figures come from `requirements.md` BR-022 and `stories.md` US-006-05, reconciled in `CON-WEB-001`: code valid 10 minutes, 3 resends per 10 minutes, and 5 wrong codes locking the account for 15 minutes. The client never counts them (`INV-WEB-009`).

**WEB-LOGIN-FR-001: Request a code.**
- Traces to. US-006-05 (scenario 1).
- Given the user is on `/login` and has entered an email, when they submit, then the web app calls `API-AUTH-015365`. On 202 it shows `CodeEntryScreen` at `/login/code` and holds the email in memory.
- Verification. (planned) `login.e2e.request-code-opens-code-entry`
- API. `API-AUTH-015365`

**WEB-LOGIN-FR-002: Email entry check.**
- Traces to. US-006-05 (scenario 1).
- Given the email field is empty or whitespace only, when the user submits, then no request is made and a field-level message is shown. Otherwise the value is trimmed and sent as typed. Format validity is left to the server (`ASM-WEB-019`).
- Verification. (planned) `login.component.email-empty-blocks-submit`
- API. none

**WEB-LOGIN-FR-003: Request-code failures on `LoginScreen`.**
- Traces to. US-006-05 (scenario 3); `requirements.md` BR-022.
- Given a request-code response, when the status is:
  - 400, then a field-level error is shown on the email field. The server's detail shape is unavailable, so a generic message is used (`GAP-WEB-004`).
  - 429, then the resend-limit error is shown and the submit control stays enabled (`ASM-WEB-018`).
  - 500 or a network failure, then a retryable error is shown and the typed email is kept (`ASM-WEB-026`).
- Verification. (planned) `login.contract.request-code-status-to-ui`
- API. `API-AUTH-015365`

**WEB-LOGIN-FR-004: Code format gate.**
- Traces to. US-006-05 (scenario 1).
- Given the user is on `/login/code`, when the entry is not exactly 6 digits after whitespace is removed, then no verify request is made and a field-level format message is shown (`ASM-WEB-007`, `ASM-WEB-020`).
- Verification. (planned) `login.component.code-format-gate`
- API. none

**WEB-LOGIN-FR-005: Verify the code; sign-in completion `BLOCKED`.**
- Traces to. US-006-05 (scenario 1).
- Given the entry is exactly 6 digits, when the user submits, then the web app calls `API-AUTH-592570`.
- Given the response is 200, then the held email and the entered code are discarded and the user is treated as signed in.
- `BLOCKED` until `GAP-WEB-001` closes:
  - the credential type, lifetime and transport;
  - what "signed in" means to the app shell;
  - the redirect when the user is already signed in.
- The proposed destination is `/notes` (`ASM-WEB-011`, `Q-WEB-001`).
- Verification. (planned) `login.e2e.verify-success-discards-sensitive-state`. The post-sign-in assertions are not written until the block clears.
- API. `API-AUTH-592570`

**WEB-LOGIN-FR-006: Wrong or malformed code.**
- Traces to. US-006-05 (intent: "understand why a code was refused").
- Given a verify response of 401, then a wrong-code message is shown. Given 400, then a field-level error is shown. In both cases the code field is cleared and focused (`ASM-WEB-022`).
- Verification. (planned) `login.contract.verify-401-400-to-ui`
- API. `API-AUTH-592570`

**WEB-LOGIN-FR-007: Expired code.**
- Traces to. US-006-05 (scenario 3).
- Given a verify response of 410, then an expired-code error is shown with an option to request a new code. The option is the resend control (FR-010).
- Verification. (planned) `login.e2e.expired-code-offers-new-code`
- API. `API-AUTH-592570`

**WEB-LOGIN-FR-008: Already-used code.**
- Traces to. US-006-05 (intent); status per the contract.
- Given a verify response of 409, then an already-used message is shown with the same option to request a new code. The code field is cleared.
- Verification. (planned) `login.contract.verify-409-to-ui`
- API. `API-AUTH-592570`

**WEB-LOGIN-FR-009: Lockout.**
- Traces to. US-006-05 (scenario 2); `requirements.md` BR-022.
- Given a verify response of 423, then a lockout error is shown stating the account is locked for 15 minutes.
- Code submission is disabled while `CodeEntryScreen` stays mounted (`ASM-WEB-017`).
- No countdown is shown, because remaining lock time is not available (`GAP-WEB-004`).
- Verification. (planned) `login.e2e.lockout-disables-submission`
- API. `API-AUTH-592570`

**WEB-LOGIN-FR-010: Resend a code.**
- Traces to. US-006-05 (scenario 3: "an option to request a new code"); `ASM-WEB-016`.
- Given the user is on `/login/code` with the email held, when they activate resend, then the web app calls `API-AUTH-015365` with the held email.
- On 202, the code field and any previous error are cleared, focus moves to the code field, and a "new code sent" notice is shown (`ASM-WEB-021`).
- On 400, 500 or a network failure, a form-level retryable error is shown.
- Verification. (planned) `login.e2e.resend-clears-error-and-notifies`
- API. `API-AUTH-015365`

**WEB-LOGIN-FR-011: Resend limit on `CodeEntryScreen`.**
- Traces to. US-006-05 (scenario 3: "a resend-limit error when no more resends are allowed").
- Given a request-code response of 429 from the resend control, then the resend-limit error is shown and the resend control is disabled while the screen stays mounted (`ASM-WEB-017`).
- The message states no count, because the sources differ (`CON-WEB-002`).
- Verification. (planned) `login.e2e.resend-limit-disables-resend`
- API. `API-AUTH-015365`

**WEB-LOGIN-FR-012: Resend is independent of lockout.**
- Traces to. US-006-05 (scenario 2 disables code submission only); `API-AUTH-015365` (allowed while verification is locked, `Q-WEB-004` default).
- Given the lockout state (FR-009), then the resend control remains in the state its own rules give it.
- Verification. (planned) `login.component.lockout-leaves-resend-enabled`
- API. `API-AUTH-015365`

**WEB-LOGIN-FR-013: Guard for direct visits and reloads.**
- Traces to. US-006-05 (the code-entry screen follows a submitted email).
- Given no email is held in memory, when the user opens or reloads `/login/code`, then they are redirected to `/login` (`ASM-WEB-006`).
- Verification. (planned) `login.e2e.code-entry-without-email-redirects`
- API. none

**WEB-LOGIN-FR-014: One request at a time.**
- Traces to. US-006-05 (the resend limit is consumed by calls).
- Given a login request is pending, then its submit control shows Loading and cannot be activated, and the related input is disabled.
- The resend control and the submit control are not both active at once (`INV-WEB-007`).
- Verification. (planned) `login.component.pending-blocks-duplicate-requests`
- API. `API-AUTH-015365`, `API-AUTH-592570`

**WEB-LOGIN-FR-015: Server failure on verify.**
- Traces to. US-006-05 (scenario 1); the contract lists 500.
- Given a verify response of 500 or a network failure, then a retryable error is shown and the entered code is kept. There is no automatic retry (`ASM-WEB-026`).
- Verification. (planned) `login.contract.verify-500-to-ui`
- API. `API-AUTH-592570`

**WEB-LOGIN-FR-016: No client-side limit state.**
- Traces to. US-006-05; `requirements.md` BR-022 (Enforcement: `notes_api` enforces, the web app displays).
- Given any sequence of attempts, then the web app keeps no attempt counter, resend counter or timer, and learns lock, expiry and resend-limit state only from responses (`INV-WEB-009`, `ASM-WEB-008`).
- Verification. (planned) `login.component.no-client-counters`
- API. none

**WEB-LOGIN-FR-017: Sensitive data handling.**
- Traces to. US-006-05 ("sign in securely"); core section 12.
- Given any login interaction, then the email and code are never placed in a URL, browser storage, log output or analytics event, and a code is never echoed in a message (`INV-WEB-006`).
- Verification. (planned) `login.e2e.no-sensitive-data-in-url-or-storage`
- API. none

### Invariants

- `INV-WEB-006`: The email and code never appear in a URL, browser storage, log or analytics event. The held email lives in memory only and is discarded on success or on leaving the module (`ASM-WEB-006`).
- `INV-WEB-007`: At most one login request is in flight at a time.
- `INV-WEB-008`: A verify request is sent only when the entry is exactly 6 digits (`ASM-WEB-007`).
- `INV-WEB-009`: Lockout, expiry and resend-limit state is derived only from API responses. There is no client timer or counter (`ASM-WEB-008`).

### Story coverage for `US-006-05`

| Scenario | Covered by |
|---|---|
| 1. Email, code entry, verify, sign in | FR-001, FR-002, FR-004, FR-005 (completion `BLOCKED`), FR-013 |
| 2. Lockout | FR-009, FR-012 |
| 3. Expired code and resend limit | FR-007, FR-010, FR-011 |
| Not stated in the story, derived from the contract | FR-003, FR-006, FR-008, FR-014 to FR-017 |
<!-- /SECTION: web-app.login.requirements -->
<!-- SECTION: web-app.login.routes | status: DRAFT-UNVERIFIED -->
## Routes

Paths are proposals (`ASM-WEB-001`). The design lists no outgoing transitions for either screen (`screens-inventory.md`), so navigation below comes from the story and the contract.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/login` | `LoginScreen` | public | none: public so sign-in is reachable without a session (derived). |
| `/login/code` | `CodeEntryScreen` | public | none: public. Reachable only while an email is held in memory, otherwise redirect to `/login` (FR-013). |

**Navigation**
- `/login` → `/login/code` on a 202 from request-code (FR-001).
- `/login/code` → `/login` when no email is held (FR-013).
- `/login/code` → `/notes` on verify success. This is a proposed destination (`ASM-WEB-011`), and the rest of sign-in is `BLOCKED` (FR-005).

**`BLOCKED` (`GAP-WEB-001`)**
- Behaviour when an already-signed-in user opens either route.
- Redirect-back to a requested route. A return to the originally requested route is the project default no (`Q-WEB-001`).
<!-- /SECTION: web-app.login.routes -->
<!-- SECTION: web-app.login.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.components -->
<!-- SECTION: web-app.login.states | status: DRAFT-UNVERIFIED -->
## States

The design has a Default variant only for both screens (`screens-inventory.md`). Every other state is a proposal, pending error variants and a message component (`GAP-WEB-005`).

Neither route displays a server-held collection. "Empty" therefore means an untouched form, and "populated" means a form with entered values. Message slot names are labels for copy to be supplied (`GAP-WEB-024`).

### `/login` (`LoginScreen`)

| State | Trigger | Presentation | Slot |
|---|---|---|---|
| Empty | first render | empty email field, submit enabled, no message. Design Default. | none |
| Populated | email typed | field Filled | none |
| Loading | request-code pending | submit Loading and inactive, email field Disabled | none |
| Error: email missing | submit with empty email (FR-002) | field Error, field-level message | `email-required` |
| Error: email rejected | 400 | field Error, field-level message | `email-rejected` |
| Error: resend limit | 429 | form-level message, submit stays enabled (`ASM-WEB-018`) | `request-limit` |
| Error: server or network | 500 or network failure | form-level retryable message, email kept | `request-failed` |

### `/login/code` (`CodeEntryScreen`)

| State | Trigger | Presentation | Slot |
|---|---|---|---|
| Empty | first render with email held | empty code field, submit and resend enabled. Design Default. | none |
| Populated | code typed | field Filled | none |
| Loading: verify | verify pending | submit Loading and inactive, code field Disabled, resend inactive | none |
| Loading: resend | resend pending | resend Loading and inactive, submit inactive | none |
| Error: format | submit with an entry that is not 6 digits (FR-004) | field Error, no request sent | `code-format` |
| Error: wrong or rejected | 401 or 400 | field cleared and focused, field-level message | `code-wrong`, `code-rejected` |
| Error: already used | 409 | field cleared, message with the resend option | `code-used` |
| Error: expired | 410 | field cleared, message with the resend option | `code-expired` |
| Error: locked | 423 | message stating the account is locked for 15 minutes. Code field and submit Disabled. Resend unchanged. | `locked` |
| Error: resend limit | 429 from resend | message. Resend Disabled while mounted. | `request-limit` |
| Error: server or network | 500 or network failure | retryable message, code kept | `verify-failed`, `request-failed` |
| Notice: new code sent | 202 from resend | field cleared, focus on the field, plain-text notice | `resend-sent` |
| Guard redirect | no email held | redirect to `/login`, nothing rendered | none |

**Not applicable**
- **Permission-denied.** Both routes are public, so the state is omitted.
- **Partial-failure.** The module has no multi-item operation, so the state is omitted.
<!-- /SECTION: web-app.login.states -->
<!-- SECTION: web-app.login.api-integration | status: VERIFIED -->
## API integration

Reference only. Request and response shapes live in the contract, pinned at `sha256:9550f0d579d8`, and are not restated. The module keys its UI on status code alone, because the error body shape is unavailable (`GAP-WEB-004`).

| User action | Operation | Statuses handled | UI state |
|---|---|---|---|
| Submit email (FR-001) | `API-AUTH-015365` | 202, 400, 429, 500 | `states` tables: `/login` |
| Resend a code (FR-010, FR-011) | `API-AUTH-015365` | 202, 400, 429, 500 | `states` tables: `/login/code` |
| Submit code (FR-005 to FR-009, FR-015) | `API-AUTH-592570` | 200, 400, 401, 409, 410, 423, 500 | `states` tables: `/login/code` |

- The status list per operation is the one the contract publishes. A status outside it is treated as a server failure.
- The 200 from `API-AUTH-592570` defines no credential in the contract, so sign-in completion is `BLOCKED` (`GAP-WEB-001`).
- No other operation is used by this module.
<!-- /SECTION: web-app.login.api-integration -->
<!-- SECTION: web-app.login.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Also enforced server-side |
|---|---|---|
| Email is non-empty after trimming (FR-002) | yes | The contract lists 400 for `API-AUTH-015365`. Which rules produce it is unstated (`GAP-WEB-004`). |
| Email format and case handling | no. Left to the server (`ASM-WEB-019`). | Unstated. The backend assumes email-keyed records with case handling unstated (`ASM-BE-001`). |
| Code is exactly 6 digits after whitespace is removed (FR-004) | yes (`ASM-WEB-007`, `ASM-WEB-020`) | The contract describes a 6-digit numeric code. Its response to a malformed code is not stated beyond the listed 400. |
| Code is correct, unexpired and unused | no | yes: 401, 410 and 409 |
| Resend limit (3 per 10 minutes) | no | yes: 429 (`CON-WEB-002` on wording) |
| Lockout (5 wrong codes, 15 minutes) | no | yes: 423 |

The client enforces no maximum length for the email, because no input states one.
<!-- /SECTION: web-app.login.validation -->
<!-- SECTION: web-app.login.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

**Target standard: `BLOCKED`** (`GAP-WEB-008`). The behaviours below are proposals (`ASM-WEB-025`) and each is verified by the a11y tests in `test-plan`.

**Focus management**
- On route load, focus goes to the first field: the email field on `/login`, the code field on `/login/code`.
- On a field-level error, focus moves to that field.
- A cleared field after 401, 409 or 410 receives focus.
- After a successful resend, focus moves to the code field.

**Keyboard paths**
- Enter in either field submits.
- Tab order is field, submit, resend (code screen).
- Every control is reachable and operable by keyboard.

**Live regions**
- Error messages and the lockout message are announced as alerts.
- The resend notice and Loading transitions are announced politely.
- The lockout message is also kept in the page content in reading order, because disabled controls cannot be focused.

**Form errors**
- Field-level messages are programmatically associated with their field.
- An invalid field is marked invalid.
- Error state does not rely on colour alone.

**Input hints (proposal)**
- The code field is numeric-oriented, and browser autofill of one-time codes is allowed (`Q-WEB-006`).
- The email field is an email-type input with autofill allowed.
<!-- /SECTION: web-app.login.accessibility -->
<!-- SECTION: web-app.login.i18n | status: BLOCKED -->
## I18n

**Blocked.** No input supplies a locale list, copy deck or copy keys for any login message (`GAP-WEB-024`).

The message slots that need copy are listed in `states`. The lockout copy must state that the account is locked for 15 minutes (US-006-05). The expired and already-used copy must offer a new code (US-006-05).

Unblocked by: a locale list and copy for every slot.
<!-- /SECTION: web-app.login.i18n -->
<!-- SECTION: web-app.login.design-refs | status: DRAFT-UNVERIFIED -->
## Design refs

`spec_mode` is `FULL-FROM-BRIEF`. Figma is `not-connected`, so every item below is an unverified claim. No frame IDs were supplied.

| Item | Source | Verification |
|---|---|---|
| `LoginScreen`: module M6, variant Default, components listed `Button/Primary`, no outgoing transitions | `screens-inventory.md` | Unverified. No frame ID. |
| `CodeEntryScreen`: module M6, variant Default, components listed `Button/Primary`, no outgoing transitions | `screens-inventory.md` | Unverified. No frame ID. |
| `Button/Primary` (12 variants), `Input/Text` (5 variants) | `components-inventory.md` | Unverified. Variant names not supplied. |
| Tokens named in `components` | `tokens-index.md` | Unverified. IDs only, no values. |
| US-006-05 maps to `CodeEntryScreen` only | `stories-map.md` | `LoginScreen` has no story mapped (`GAP-WEB-023`). |

Not in the design: error variants of either screen, a message component, a resend control frame, and any frame for the lockout, expiry or resend-limit states (`GAP-WEB-005`).
<!-- /SECTION: web-app.login.design-refs -->
<!-- SECTION: web-app.login.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All tests are (planned). Component tests are in-browser unit tests, and E2E tests run against a stub of the pinned contract.

**Component**
- `login.component.email-empty-blocks-submit` (FR-002)
- `login.component.code-format-gate` (FR-004, `INV-WEB-008`)
- `login.component.pending-blocks-duplicate-requests` (FR-014, `INV-WEB-007`)
- `login.component.lockout-leaves-resend-enabled` (FR-012)
- `login.component.no-client-counters` (FR-016, `INV-WEB-009`)

**E2E**
- `login.e2e.request-code-opens-code-entry` (FR-001)
- `login.e2e.verify-success-discards-sensitive-state` (FR-005, partial)
- `login.e2e.expired-code-offers-new-code` (FR-007)
- `login.e2e.lockout-disables-submission` (FR-009)
- `login.e2e.resend-clears-error-and-notifies` (FR-010)
- `login.e2e.resend-limit-disables-resend` (FR-011)
- `login.e2e.code-entry-without-email-redirects` (FR-013)
- `login.e2e.no-sensitive-data-in-url-or-storage` (FR-017, `INV-WEB-006`)

**Contract** (against `sha256:9550f0d579d8`)
- `login.contract.request-code-status-to-ui` (FR-003, FR-010, FR-011)
- `login.contract.verify-401-400-to-ui` (FR-006)
- `login.contract.verify-409-to-ui` (FR-008)
- `login.contract.verify-500-to-ui` (FR-015)
- `login.contract.statuses-match-published-list`: fails if the pin lists a status this module does not handle.

**Accessibility**
- `login.a11y.focus-moves-on-error-and-load`
- `login.a11y.keyboard-submit-and-tab-order`
- `login.a11y.alerts-announced-and-fields-associated`

The conformance level for the a11y tests is `BLOCKED` (`GAP-WEB-008`). No test is written for post-sign-in behaviour until `GAP-WEB-001` closes.
<!-- /SECTION: web-app.login.test-plan -->
<!-- SECTION: web-app.login.gaps | status: DERIVED -->
## Gaps

Contradiction directions checked: story↔design, story↔requirement, requirement↔requirement, design↔design. No new contradiction was found. Figures follow `CON-WEB-001`. The resend wording follows `CON-WEB-002` by stating no count. No contradiction touches a data shape, state machine, authorisation rule or money/PII path, so none is Critical.

### Defined here

| ID | Missing | Degrades | Done instead | Closes with |
|---|---|---|---|---|
| `GAP-WEB-023` | Variant names and mapping for `Button/Primary` (12) and `Input/Text` (5), a Pressed token, and a design mapping of `LoginScreen` to US-006-05. | `components`, `design-refs` | Bindings by state only, marked proposals. | Design annotations or Figma verification. |
| `GAP-WEB-024` | Locale list, copy deck and copy keys for every login message. | `i18n` | Section `BLOCKED`. Slots listed in `states`. | A copy deck and locale list. |

| ID | Question | Class | Default |
|---|---|---|---|
| `Q-WEB-005` | Should `CodeEntryScreen` show the address the code was sent to, masked? | degradable | No. |
| `Q-WEB-006` | Should browser autofill of one-time codes be enabled on the code field? | degradable | Yes (`ASM-WEB-025`). |

| ID | Assumption | Would confirm |
|---|---|---|
| `ASM-WEB-017` | Lockout (423) and resend-limit (429 on `/login/code`) disablement last while `CodeEntryScreen` stays mounted. It is not stored. A fresh visit recomputes it from a new response. | Backend remaining-lock-time and re-enable rules (`GAP-WEB-004`). |
| `ASM-WEB-018` | On `/login`, a 429 keeps the submit control enabled, because the server decides when the window ends. | A retry-after or re-enable rule. |
| `ASM-WEB-019` | The email is trimmed and otherwise sent as typed. The field starts empty on each visit to `/login`. Format and case handling are the server's. | Backend decision on case handling (`ASM-BE-001`). |
| `ASM-WEB-020` | Whitespace in the code entry is removed before the 6-digit check. A non-conforming entry shows a field-level format message and makes no call. | A backend ruling on malformed codes. |
| `ASM-WEB-021` | After a successful resend: code field and error cleared, focus on the field, plain-text "new code sent" notice. | Design or product. |
| `ASM-WEB-022` | After 401, 400, 409 or 410 the code field is cleared and focused. | Design or product. |
| `ASM-WEB-023` | Messages are plain text in a message region, because no message component exists. Field-level for 400 and format failures, form-level for the rest. | Design frames with a message component. |
| `ASM-WEB-024` | Token bindings are proposals from token names. Values are unseen. | Figma verification. |
| `ASM-WEB-025` | The accessibility behaviours and input hints are proposals, because no standard is stated. | A stated standard and level (`GAP-WEB-008`). |
| `ASM-WEB-026` | On 500 or a network failure, inputs are kept and the user retries by resubmitting. There is no automatic retry. | An error-handling decision. |

### Referenced from the project spec

- **Gaps.** `GAP-WEB-001` (session), `GAP-WEB-004` (error detail), `GAP-WEB-005` (design frames), `GAP-WEB-006` (type and spacing scale), `GAP-WEB-008` (accessibility standard).
- **Contradictions.** `CON-WEB-001`, `CON-WEB-002`.
- **Assumptions.** `ASM-WEB-001`, `ASM-WEB-005`, `ASM-WEB-006`, `ASM-WEB-007`, `ASM-WEB-008`, `ASM-WEB-011`, `ASM-WEB-015`, `ASM-WEB-016`.
- **Questions.** `Q-WEB-001`, `Q-WEB-003`, `Q-WEB-004`.
<!-- /SECTION: web-app.login.gaps -->
