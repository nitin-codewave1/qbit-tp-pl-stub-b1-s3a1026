---
id: agent-developer/web_app/web-app/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/auth-SPEC.md
depends_on: [web_app:US-006-05]
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:web-app/modules/auth-SPEC.md
---

<!-- SECTION: web-app.auth.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: auth
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-004, Q-BE-012]
contract_pin: "sha256:a6f2ca3343de"
scope: [/login, /login/code]
```

- **`spec_mode`.** The design brief is present and Figma is not connected. Every screen, component and token name in this module is an unverified claim taken from design-brief.md. [RUN-CONTEXT `inputs.figma_mcp`; design-brief.md]
- **`blocked_by`.** Credential form, lifetime, storage and attachment are unanswered upstream. `WEB-AUTH-FR-004` and `WEB-AUTH-FR-012` are therefore partly `BLOCKED`. [project spec `web-app.auth-flows`; `GAP-WEB-004`; backend-api.md `Q-BE-012`]
- **`scope`.** These are the two routes the project module map gives to `auth`. [project spec `web-app.module-map`, `web-app.routing`]
<!-- /SECTION: web-app.auth.frontmatter -->
<!-- SECTION: web-app.auth.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The email one-time-code sign-in on the web, and the handling of session loss:
- the login screen (`LoginScreen`, M6) and the code-entry screen (`CodeEntryScreen`, M6);
- the display of every refusal the two sign-in operations can return: wrong code, expired code, used code, lockout and resend limit;
- consuming the return target after sign-in;
- the response to a `401` from a protected operation, which ends the session and redirects to `/login`.

[stories.md US-006-05; requirements.md BR-022; project spec `web-app.module-map`, `web-app.auth-flows`]

**Owns story.** `US-006-05`.

**Numeric limits.** US-006-05 and BR-022 carry injected clauses ("6 digits-digit", "5 attempts times", "3 resends times"). Limits are taken only where backend-api.md states the same value: 6-digit numeric code, 10-minute expiry, 3 code requests per 10 minutes, 5 wrong codes, 15-minute lock. [backend-api.md `backend.overview`; project spec `web-app.overview`]

**Not responsible for:**
- **Notes, search, archive and pin behaviour.** These belong to the `notes`, `search` and `archive` modules. [project spec `web-app.module-map`]
- **Route guarding for protected routes.** The protected-route rule is project-level (`web-app.routing`). This module only supplies the `/login` destination and consumes the return target.
- **Cache invalidation policy.** This module triggers the discard of cached server state on sign-in and on a session-ending `401`, but the policy belongs to `web-app.state-management` (`ASM-WEB-006`).
- **Other sign-in methods.** The existing method is unnamed in every input. [`GAP-WEB-016`]
- **Sign-out.** No operation, story or frame exists. [`GAP-WEB-012`]
- **Backend rules.** Rate limiting, locking, code generation and expiry are enforced by the backend. The web only displays what the server returns (`INV-WEB-011`).
<!-- /SECTION: web-app.auth.purpose -->
<!-- SECTION: web-app.auth.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Error codes named below are the backend's proposed registry (`ASM-WEB-008`, `GAP-WEB-005`). Verification names are proposals; no test tooling is named (`GAP-WEB-609`).

**`WEB-AUTH-FR-001` — Request a code.**
- **Given** the user is on `LoginScreen` and has entered an email address,
- **When** they submit,
- **Then** the app calls `API-AUTH-707260` with that email, shows an in-flight state, and takes no other action until the response arrives.
- **Traces to.** US-006-05 (scenario 1); BR-022.
- **Verification.** `login_submit_calls_request_code` (planned).
- **API.** `API-AUTH-707260`.

**`WEB-AUTH-FR-002` — Advance to code entry.**
- **Given** the request-code call succeeded (`ASM-WEB-401`),
- **When** the response is received,
- **Then** the app opens `CodeEntryScreen` and holds the email in memory only (`INV-WEB-009`). The copy and behaviour are identical whether or not an account exists for the address (`INV-WEB-014`).
- **Traces to.** US-006-05 (scenario 1).
- **Verification.** `login_success_opens_code_entry_with_email_in_memory` (planned).
- **API.** `API-AUTH-707260`.

**`WEB-AUTH-FR-003` — Enter and verify a code.**
- **Given** the user is on `CodeEntryScreen`,
- **When** they type in the code field,
- **Then** the field accepts digits only, up to 6 characters. On submit with exactly 6 digits, the app calls `API-AUTH-692907` with the held email and the entered code (`ASM-WEB-409`). With fewer than 6 digits, the app shows a field-level error and makes no call.
- **Traces to.** US-006-05 (scenario 1); BR-022.
- **Verification.** `code_entry_digits_only_max_six`, `code_entry_submit_calls_verify` (planned).
- **API.** `API-AUTH-692907`.

**`WEB-AUTH-FR-004` — Sign in on success.**
- **Given** the verify call succeeded (`ASM-WEB-401`),
- **When** the response is received,
- **Then** the app stores the credential, discards cached server state (`ASM-WEB-006`), clears the held email and code from memory (`ASM-WEB-410`), and navigates to the return target if one exists, otherwise to `/notes`. The return target accepts in-app paths only (`ASM-WEB-002`).
- **Credential storage: `BLOCKED`.** Where the credential is kept, and how it is attached to later requests, depends on what verification returns, which is unanswered (`Q-BE-012`, `GAP-WEB-004`). No storage location is assumed.
- **Traces to.** US-006-05 (scenario 1).
- **Verification.** `verify_success_navigates_to_return_target_or_notes` (planned); credential-handling tests are `BLOCKED`.
- **API.** `API-AUTH-692907`.

**`WEB-AUTH-FR-005` — Wrong code.**
- **Given** the user submitted a code,
- **When** the server refuses it with `OTP_CODE_INVALID`,
- **Then** the app shows a wrong-code error and stays on `CodeEntryScreen`. It does not show attempts remaining, because no input exposes them.
- **Traces to.** US-006-05; BR-022.
- **Verification.** `verify_wrong_code_shows_error_and_stays` (planned).
- **API.** `API-AUTH-692907`.

**`WEB-AUTH-FR-006` — Expired or used code.**
- **Given** the user submitted a code,
- **When** the server refuses it with `OTP_CODE_EXPIRED`, or with `OTP_CODE_USED` (`ASM-WEB-009`),
- **Then** the app shows an expired-code error with an option to request a new code. The option behaves per `WEB-AUTH-FR-009`.
- **Traces to.** US-006-05 (scenario 3); BR-022.
- **Verification.** `verify_expired_or_used_offers_new_code` (planned).
- **API.** `API-AUTH-692907`.

**`WEB-AUTH-FR-007` — Account locked.**
- **Given** the user submitted a code,
- **When** the server refuses it with `OTP_ACCOUNT_LOCKED`,
- **Then** the app shows a lockout error stating the account is locked for 15 minutes, and disables code submission. It offers no request-new-code option (`ASM-WEB-011`) and no countdown, because the contract returns no remaining lock time (`GAP-WEB-006`). The disabled state is cleared only when the user leaves `CodeEntryScreen` (`ASM-WEB-411`), never by a client timer (`INV-WEB-011`).
- **Traces to.** US-006-05 (scenario 2); BR-022.
- **Verification.** `verify_locked_shows_15_minute_error_and_disables_submit` (planned).
- **API.** `API-AUTH-692907`.

**`WEB-AUTH-FR-008` — Resend limit.**
- **Given** the user requested a code, from `LoginScreen` or from `CodeEntryScreen`,
- **When** the server refuses it with `OTP_RATE_LIMITED`,
- **Then** the app shows a resend-limit error on the screen where the request was made and offers no request-new-code option. The client never counts requests itself (`INV-WEB-011`; `CON-WEB-004`).
- **Traces to.** US-006-05 (scenario 3); BR-022.
- **Verification.** `send_rate_limited_shows_resend_limit_error` (planned).
- **API.** `API-AUTH-707260`.

**`WEB-AUTH-FR-009` — Request a new code.**
- **Given** `CodeEntryScreen` shows an error that offers a new code (`WEB-AUTH-FR-006`),
- **When** the user activates the option,
- **Then** the app calls `API-AUTH-707260` with the held email, stays on `CodeEntryScreen`, clears the code field, and shows a confirmation that a new code was requested (`ASM-WEB-405`). Refusals follow `WEB-AUTH-FR-008`.
- **Traces to.** US-006-05 (scenario 3).
- **Verification.** `code_entry_request_new_code_calls_send_and_clears_field` (planned).
- **API.** `API-AUTH-707260`.

**`WEB-AUTH-FR-010` — Invalid input and other failures.**
- **Given** either sign-in call is made,
- **When** the server returns `VALIDATION_FAILED`,
- **Then** the app shows field-level errors on the entry field.
- **When** the call fails with a network failure or a server error,
- **Then** the app shows a generic try-again error and keeps the entered values. Behaviour when the email transport fails is not stated by any input.
- **Traces to.** US-006-05; requirements.md NFR-011.
- **Verification.** `sign_in_validation_error_marks_field`, `sign_in_server_error_shows_generic_error` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

**`WEB-AUTH-FR-011` — A `401` from a sign-in operation is local.**
- **Given** either sign-in call is made,
- **When** it returns `401`,
- **Then** the app shows the local error and never triggers the session-expiry redirect (`INV-WEB-008`). A `401` from verify may be a wrong-code refusal (backend-api.md `GAP-BE-053`).
- **Traces to.** US-006-05.
- **Verification.** `sign_in_401_does_not_redirect_to_login` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

**`WEB-AUTH-FR-012` — Session loss and return target.**
- **Given** the user is on a protected route,
- **When** any protected operation returns `401`,
- **Then** the app ends the session, discards cached server state (`ASM-WEB-006`), applies no local result of the failed mutation, and redirects to `/login`, keeping the requested in-app path as the return target.
- **Credential clearing: `BLOCKED`** on `Q-BE-012` and `GAP-WEB-004`. No refresh operation exists in the contract, so the user signs in again by code. Whether unsaved editor content survives is unspecified (`GAP-WEB-013`).
- **Traces to.** US-006-05 (project spec `web-app.auth-flows`, session expiry).
- **Verification.** `protected_401_redirects_to_login_with_return_target` (planned).
- **API.** none; the `401` comes from any protected operation.

**`WEB-AUTH-FR-013` — Code-entry precondition.**
- **Given** `/login/code` is opened with no email held in memory,
- **When** the route is entered,
- **Then** the app redirects to `/login`.
- **Traces to.** US-006-05; project spec `web-app.routing` (`ASM-WEB-005`).
- **Verification.** `code_entry_without_email_redirects_to_login` (planned).
- **API.** none.

**`WEB-AUTH-FR-014` — Already signed in.**
- **Given** the user is already signed in,
- **When** they open `/login` or `/login/code`,
- **Then** the app redirects to `/notes` (`ASM-WEB-407`).
- **How "signed in" is determined depends on the credential, so the check is `BLOCKED`** on `GAP-WEB-004` (`GAP-WEB-607`).
- **Traces to.** US-006-05 (derived from the routing rule; no input states this behaviour).
- **Verification.** (planned) `signed_in_user_is_redirected_from_login`; `BLOCKED`.
- **API.** none.

**Story coverage (`US-006-05`).**

| Scenario | Requirements |
|---|---|
| 1. Request code, enter valid code, signed in | `WEB-AUTH-FR-001`, `-002`, `-003`, `-004` |
| 2. Wrong code 5 times, locked, 15 minutes, submission disabled | `WEB-AUTH-FR-005`, `-007` |
| 3. Expired code, or resend limit reached | `WEB-AUTH-FR-006`, `-008`, `-009` |

**Invariants this module adds** (existing `INV-WEB-008`, `INV-WEB-009` and `INV-WEB-011` apply unchanged):
- **`INV-WEB-012`.** The one-time code is never placed in a URL, `localStorage`, `sessionStorage` or a client log, and is never exemplified with a real-looking value. It is held only in the field's component state and cleared on leaving the screen.
- **`INV-WEB-013`.** At most one sign-in request is in flight at a time. While one is outstanding, every submit and request-new-code control is disabled (`ASM-WEB-406`).
- **`INV-WEB-014`.** No copy, status display or timing difference implies whether an email address has an account. [backend-api.md `ASM-BE-400`]
<!-- /SECTION: web-app.auth.requirements -->
<!-- SECTION: web-app.auth.routes | status: DRAFT-UNVERIFIED -->
## Routes

Route paths are proposed (`ASM-WEB-001`). Screen names come from the design brief. [design-brief.md screens-inventory]

| Route | Page | Layout | Required permission | Precondition |
|---|---|---|---|---|
| `/login` | `LoginScreen` (M6) | Public layout | none (unauthenticated) | A signed-in user is redirected to `/notes` (`WEB-AUTH-FR-014`, `ASM-WEB-407`) |
| `/login/code` | `CodeEntryScreen` (M6) | Public layout | none (unauthenticated) | An email held in memory, otherwise redirect to `/login` (`WEB-AUTH-FR-013`) |

**Transitions:**
- `/login` → `/login/code`: on a successful request-code call.
- `/login/code` → `/login`: on a control to change the email (`ASM-WEB-404`; control undesigned, `GAP-WEB-007`).
- `/login/code` → return target or `/notes`: on a successful verify call.
- any protected route → `/login`: on session loss, carrying the return target (`WEB-AUTH-FR-012`).

**Layout.** Both routes use the public layout assumed in the project spec. No shell, header or navigation component is designed. [project spec `web-app.routing`; `GAP-WEB-002`]

**Permission-gated routes.** None in this module. Both routes are public, so no permission-denied state is specified here and no `GAP-` is raised for its omission.
<!-- /SECTION: web-app.auth.routes -->
<!-- SECTION: web-app.auth.components | status: BLOCKED -->
## Components

**BLOCKED.** the run did not produce this section for module `auth`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.auth.components -->
<!-- SECTION: web-app.auth.states | status: DRAFT-UNVERIFIED -->
## States

The brief supplies one variant (`Default`) for each screen. Every error, loading and confirmation state below is undesigned (`GAP-WEB-007`). They are derived from the requirements. Screen-level loading is also undesigned (`GAP-WEB-010`).

**`/login` (`LoginScreen`):**

| State | When | Behaviour |
|---|---|---|
| Empty (Default) | email field empty | `Button/Primary` Default; `Input/Text` Default |
| Populated | email typed | `Input/Text` Filled; submit enabled |
| Loading | request-code in flight | `Button/Primary` Loading; field disabled (`INV-WEB-013`) |
| Error: empty email | submit with empty field | `Input/Text` Error; no call (`ASM-WEB-007`) |
| Error: `VALIDATION_FAILED` | server rejects input | field-level error on the email field |
| Error: `OTP_RATE_LIMITED` | resend limit reached | resend-limit error; no request-new-code option |
| Error: generic | network or server failure | generic try-again error; entered email kept |

**`/login/code` (`CodeEntryScreen`):**

| State | When | Behaviour |
|---|---|---|
| Empty (Default) | code field empty | submit and request-new-code available as applicable |
| Populated | digits typed | `Input/Text` Filled |
| Loading | verify or request-new-code in flight | `Button/Primary` Loading; controls disabled (`INV-WEB-013`) |
| Confirmation | new code requested | confirmation shown; code field cleared (`ASM-WEB-405`) |
| Error: short code | fewer than 6 digits on submit | `Input/Text` Error; no call (`ASM-WEB-409`) |
| Error: `OTP_CODE_INVALID` | wrong code | wrong-code error; stay on screen |
| Error: `OTP_CODE_EXPIRED`, `OTP_CODE_USED` | expired or used code | expired-code error with request-new-code option |
| Error: `OTP_ACCOUNT_LOCKED` | account locked | lockout error stating 15 minutes; submission disabled; no request-new-code; no countdown (`ASM-WEB-411`) |
| Error: `OTP_RATE_LIMITED` | resend limit reached | resend-limit error; no request-new-code option |
| Error: `VALIDATION_FAILED` | server rejects input | field-level error on the code field |
| Error: generic | network or server failure | generic try-again error; entered values kept |
| Redirect | no email held | redirect to `/login`; nothing rendered (`WEB-AUTH-FR-013`) |

**Permission-denied.** Not applicable: both routes are public.

**Partial failure.** Not applicable: this module has no multi-item operation.

**Not stated by any input:**
- how long the lockout state persists after a reload: the server decides on the next verify;
- whether any state survives navigation away: nothing does (`INV-WEB-009`, `INV-WEB-012`).
<!-- /SECTION: web-app.auth.states -->
<!-- SECTION: web-app.auth.api-integration | status: DRAFT-UNVERIFIED -->
## API integration

Reference only. Request and response shapes, field types and error envelopes are defined in the contract and are not restated here. IDs are cited as the contract supplies them, with no `@version` suffix (`GAP-WEB-015`). Contract pin `sha256:a6f2ca3343de`. [backend-api.md `backend.api-surface`]

| Operation | Pinned ID | Used by | Refusals handled |
|---|---|---|---|
| Request a one-time code | `API-AUTH-707260` | `WEB-AUTH-FR-001`, `-008`, `-009`, `-010`, `-011` | `OTP_RATE_LIMITED`, `VALIDATION_FAILED`, `401`, generic |
| Verify a one-time code | `API-AUTH-692907` | `WEB-AUTH-FR-003`, `-004`, `-005`, `-006`, `-007`, `-010`, `-011` | `OTP_CODE_INVALID`, `OTP_CODE_EXPIRED`, `OTP_CODE_USED`, `OTP_ACCOUNT_LOCKED`, `VALIDATION_FAILED`, `401`, generic |

**Notes:**
- Error handling discriminates on the machine-readable `code`, never on HTTP status alone, because several refusals share one status (`INV-WEB-010`; backend-api.md `backend.error-model`).
- The code names are the backend's proposal (`ASM-WEB-008`, `GAP-WEB-005`). Which code wins when several verify refusals apply at once is open (backend-api.md `GAP-BE-601`).
- The success status and body of both operations are not stated (backend-api.md `GAP-BE-604`). The web treats any success status as success (`ASM-WEB-401`, `GAP-WEB-605`).
- What verification returns as a credential is unanswered (`Q-BE-012`, `GAP-WEB-004`).
- No refresh, sign-out or lock-status operation exists in the contract (`GAP-WEB-012`, `GAP-WEB-006`).
<!-- /SECTION: web-app.auth.api-integration -->
<!-- SECTION: web-app.auth.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Field | Client rule | Also enforced server-side |
|---|---|---|
| Email | Non-empty only. Format is not checked on the client (`ASM-WEB-007`) | Not stated: no email format rule is supplied (backend-api.md `GAP-BE-035`). The operation documents `VALIDATION_FAILED`, so a rejection is displayed field-level (`WEB-AUTH-FR-010`) |
| Code | Digits only, maximum 6 characters; exactly 6 digits required to call the API (`ASM-WEB-409`) | Code format is stated as 6 numeric digits [backend-api.md `backend.overview`]; whether a malformed code returns `VALIDATION_FAILED` or a wrong-code refusal is not stated (`GAP-BE-035`) |
| Code correctness, expiry, use count | none: never computed on the client (`INV-WEB-011`) | Yes: server only. [requirements.md BR-022; backend-api.md `backend.overview`] |
| Attempt count, resend count, lock state | none: never computed on the client (`INV-WEB-011`) | Yes: server only |

**Display of server validation errors.** Field-level errors are shown on the field they name. [requirements.md NFR-011] The error response's per-field detail structure is not restated here; it is defined in the contract.
<!-- /SECTION: web-app.auth.validation -->
<!-- SECTION: web-app.auth.accessibility | status: DRAFT-UNVERIFIED -->
## Accessibility

No accessibility target standard or verification method is supplied (`GAP-WEB-019`). The behaviours below are not cited from any input. They are proposals (`ASM-WEB-402`) to be confirmed against a target.

- **Labels.** Each field has a programmatically associated visible label.
- **Form errors.** A field-level error is programmatically associated with its field and visible next to it. Error state is not conveyed by colour alone.
- **Live regions.** Non-field errors (wrong code, expired, lockout, resend limit, generic) and the new-code confirmation are announced through a polite or assertive live region. Lockout is announced as an error.
- **Focus on arrival.** On entering `/login` or `/login/code`, focus moves to the page heading or the first field.
- **Focus on error.** After a refusal, focus returns to the field (or the first invalid field) so the user can correct it, without losing the entered value.
- **Focus when submission is disabled.** After lockout, focus does not move to a disabled control.
- **Keyboard.** Both forms are operable by keyboard alone: Tab order follows visual order, Enter submits, and the request-new-code and change-email controls are reachable.
- **Time limits.** The client runs no countdown and no timer (`INV-WEB-011`), so no client time limit applies.
- **Code field.** A single field with a numeric input hint and a one-time-code autofill hint, if the design confirms a single field (`ASM-WEB-403`).

Focus-visible and contrast behaviour depend on tokens that carry no values in the inputs (`GAP-WEB-017`) and cannot be specified.
<!-- /SECTION: web-app.auth.accessibility -->
<!-- SECTION: web-app.auth.i18n | status: BLOCKED -->
## i18n

**Blocked.** No input lists locales or supplies copy keys or a copy deck for any language. [`GAP-WEB-606`; `GAP-WEB-010`]

**Copy cases that will need a key once a scheme is chosen.** The cases are listed without inventing key names:
- email-required error, and short-code error;
- wrong-code error;
- expired-code error, with the request-new-code option label;
- lockout error, which must state the fixed 15 minutes [stories.md US-006-05];
- resend-limit error;
- new-code confirmation;
- generic try-again error;
- field labels, button labels, page headings;
- the change-email control.

No copy may imply whether an account exists for an address (`INV-WEB-014`).

**Unblocked by:** a locale list and a copy deck, or a stated decision that English is the only locale.
<!-- /SECTION: web-app.auth.i18n -->
<!-- SECTION: web-app.auth.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

`spec_mode: FULL-FROM-BRIEF`. Figma is not connected, so every name here is unverified. [RUN-CONTEXT `inputs.figma_mcp`] The brief supplies screen names as identifiers and no Figma frame node IDs (`GAP-WEB-608`).

| Reference | Source | Verification |
|---|---|---|
| `LoginScreen`: module M6, variant Default, component `Button/Primary` | design-brief.md screens-inventory | Unverified claim |
| `CodeEntryScreen`: module M6, variant Default, component `Button/Primary` | design-brief.md screens-inventory | Unverified claim |
| `Button/Primary`, `Input/Text` (components) | design-brief.md components-inventory | Unverified claim |
| Story link: US-006-05 → `CodeEntryScreen` | design-brief.md stories-map | Unverified claim. The map links US-006-05 to `CodeEntryScreen` only, not to `LoginScreen` |
| Tokens named in `web-app.auth.components` | design-brief.md tokens-index | Names unverified; values and component bindings not supplied |

**Not designed** (`GAP-WEB-007`): error variants of either screen, the `Input/Text` mapping, the request-new-code control, the change-email control, the confirmation, and screen-level loading.
<!-- /SECTION: web-app.auth.design-refs -->
<!-- SECTION: web-app.auth.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are proposed, and no test framework, runner or E2E tool is named by any input (`GAP-WEB-609`). Tests are described by behaviour only.

**Component tests:**
- `login_submit_calls_request_code`, `login_empty_email_blocks_call`
- `code_entry_digits_only_max_six`, `code_entry_short_code_shows_field_error`
- `code_entry_submit_calls_verify`
- `verify_wrong_code_shows_error_and_stays`
- `verify_expired_or_used_offers_new_code`
- `verify_locked_shows_15_minute_error_and_disables_submit` (no countdown, no re-enable on a timer)
- `send_rate_limited_shows_resend_limit_error` (on both screens)
- `sign_in_validation_error_marks_field`, `sign_in_server_error_shows_generic_error`
- `sign_in_controls_disabled_while_in_flight` (`INV-WEB-013`)
- `error_copy_never_implies_account_existence` (`INV-WEB-014`)

**E2E:**
- `email_code_sign_in_happy_path` to the return target and to `/notes`
- `locked_account_flow` (five wrong codes, lockout shown)
- `expired_code_request_new_code_flow`
- `resend_limit_flow`
- `direct_open_of_login_code_redirects_to_login`
- `protected_route_401_redirects_to_login_and_restores_return_target`

Credential persistence tests are `BLOCKED` (`GAP-WEB-004`).

**Contract:**
- Verify the client's handling of each refusal against the pinned operations `API-AUTH-707260` and `API-AUTH-692907` at pin `sha256:a6f2ca3343de`.
- Fail if the error `code` registry changes (`ASM-WEB-008`).
- Fail if the success status or body changes (`GAP-WEB-605`).
- Assert that no other operation is called by this module.

**Privacy:**
- Assert that the email and code never appear in a URL, browser storage or client logs (`INV-WEB-009`, `INV-WEB-012`). Test data uses masked or clearly synthetic values only.

**Accessibility:**
- Automated checks of labels, error association and live-region announcement on both screens.
- Manual keyboard-only run of the sign-in flow.
- The target standard is open (`GAP-WEB-019`).
<!-- /SECTION: web-app.auth.test-plan -->
<!-- SECTION: web-app.auth.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

Items defined in the project spec are referenced here, not restated. This module mints the items marked **new**.

**Project-spec items this module depends on:**
- `GAP-WEB-004` (credential), `GAP-WEB-005` (final error codes), `GAP-WEB-006` (lock time), `GAP-WEB-007` (undesigned screens), `GAP-WEB-010` (copy), `GAP-WEB-012` (sign-out), `GAP-WEB-013` (unsaved content), `GAP-WEB-015` (versioned IDs), `GAP-WEB-016` (existing login method), `GAP-WEB-017` (token values), `GAP-WEB-019` (accessibility target).
- `CON-WEB-004` (resend wording).
- `ASM-WEB-001`, `-002`, `-005`, `-006`, `-007`, `-008`, `-009`, `-011`.
- Upstream: `Q-BE-012`, `GAP-BE-035`, `GAP-BE-053`, `GAP-BE-601`, `GAP-BE-604`.

**Contradiction check.** Story to design, story to requirement, requirement to requirement and design to design were checked for this module's scope. No new `CON-` was found beyond `CON-WEB-004`.

**New gaps:**

| ID | Statement | Degrades | Closing artefact |
|---|---|---|---|
| `GAP-WEB-605` | The success status and body of the request-code and verify operations are not stated (backend-api.md `GAP-BE-604`). | `WEB-AUTH-FR-002`, `-004` | Backend states status and body for both operations |
| `GAP-WEB-606` | No locale list, copy keys or copy deck for any language. | `web-app.auth.i18n` (`BLOCKED`) | A locale list and a copy deck |
| `GAP-WEB-607` | Behaviour for an already-signed-in user opening `/login` or `/login/code`, and how signed-in is determined, are not stated. | `WEB-AUTH-FR-014` (`BLOCKED`) | An auth routing rule, after the credential decision (`GAP-WEB-004`) |
| `GAP-WEB-608` | The brief supplies screen names but no Figma frame node IDs. | `web-app.auth.design-refs` | Figma MCP connected, or a brief with frame IDs |
| `GAP-WEB-609` | No unit, component, E2E or contract test tooling is named. | `web-app.auth.test-plan` | A test tooling decision |

**New assumptions:**

| ID | Statement | Confirm with |
|---|---|---|
| `ASM-WEB-401` | Any success status from the request-code and verify operations means success. | Backend statement of status and body (`GAP-WEB-605`) |
| `ASM-WEB-402` | The accessibility behaviours in `web-app.auth.accessibility` (labels, error association, live regions, focus handling, keyboard paths) are proposals. | A stated accessibility target (`GAP-WEB-019`) |
| `ASM-WEB-403` | The code is entered in a single `Input/Text` field, not segmented boxes, and `Input/Text` is the email field too. | The frames |
| `ASM-WEB-404` | `CodeEntryScreen` offers a control to return to `/login` and change the email. | The frames |
| `ASM-WEB-405` | Request-new-code re-calls the request-code operation with the held email, stays on `CodeEntryScreen`, clears the code field and shows a confirmation. | The frames and a behaviour rule |
| `ASM-WEB-406` | All submit and request-new-code controls are disabled while any sign-in request is in flight. | A behaviour rule |
| `ASM-WEB-407` | A signed-in user opening a sign-in route is redirected to `/notes`. | An auth routing rule (`GAP-WEB-607`) |
| `ASM-WEB-408` | Token bindings for `Button/Primary` and `Input/Text` are proposed by token name. | The frames or token bindings in the design system |
| `ASM-WEB-409` | The client calls verify only when exactly 6 digits are entered, and otherwise shows a field-level error. | A validation rule (`GAP-BE-035`) |
| `ASM-WEB-410` | The held email and code are cleared from memory after a successful sign-in. | A data-protection policy (`GAP-BE-017`) |
| `ASM-WEB-411` | The lockout disabled state is cleared only when the user leaves `CodeEntryScreen`, never by a timer. | A lockout design and a remaining-lock-time field (`GAP-WEB-006`) |
<!-- /SECTION: web-app.auth.gaps -->
