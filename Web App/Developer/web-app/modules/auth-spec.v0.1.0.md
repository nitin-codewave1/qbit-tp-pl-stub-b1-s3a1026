---
id: agent-developer/web_app/web-app/modules/auth-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/auth-SPEC.md
depends_on: [web_app:US-006-05]
commit_kind: append
confidence: agent
run_id: 6a137249-ccab-5a0a-bd2d-8ea991e30fa2
okf_id: web_app:web-app/modules/auth-SPEC.md
---

<!-- SECTION: web-app.auth.frontmatter | status: DERIVED -->
## Frontmatter

```yaml
id: auth
surface: web-app
spec_mode: FULL-FROM-BRIEF
doc_status: DRAFT
blocked_by: [GAP-WEB-008, Q-BE-012]
contract_pin: "sha256:bd87f6e16dca"
scope: [/login, /login/code]
```

- **`spec_mode`.** The design brief is present and Figma is not connected, so every screen name here is an unverified claim. [RUN-CONTEXT `inputs.figma_mcp`; design-brief.md]
- **`blocked_by`.** Credential form, lifetime, storage and attachment are unanswered upstream, so `WEB-AUTH-FR-005` is partly `BLOCKED`. [project spec `web-app.auth-flows`; backend-api.md `Q-BE-012`]
- **`scope`.** The two routes are those the project module map assigns to `auth`. [project spec `web-app.module-map`, `web-app.routing`]
<!-- /SECTION: web-app.auth.frontmatter -->
<!-- SECTION: web-app.auth.purpose | status: DERIVED -->
## Purpose

**Responsible for.** The email one-time-code sign-in on the web: the login screen, the code-entry screen, and the display of every refusal the two sign-in operations can return. These are lockout, expiry, resend limit, wrong code and used code. [stories.md US-006-05; requirements.md BR-022; project spec `web-app.module-map`]

**Numeric limits.** The limits come from backend-api.md because the web-side sentences are corrupted ("6 digits-digit", "5 attempts times", "3 resends times"). The limits are:
- 6 numeric digits;
- a code valid for 10 minutes;
- at most 3 code requests within 10 minutes;
- 5 wrong codes lock the account for 15 minutes.

[backend-api.md `backend.overview`; project spec `web-app.overview`] The web app never enforces them itself (`WEB-AUTH-FR-013`).

**Not responsible for.**
- **Session handling for protected routes.** This covers `401` redirect, return-target handling for other routes, cache discard on a `401`, logout and refresh. Those are project-level auth flows. [project spec `web-app.auth-flows`, `web-app.routing`, `web-app.state-management`] This module only specifies what happens on a successful verification.
- **Credential storage and attachment.** `BLOCKED` (`GAP-WEB-008`, `Q-BE-012`).
- **Existing login methods.** They are unnamed in every input. [backend-api.md `GAP-BE-085`; project spec `GAP-WEB-021`]
- **Eligibility to sign in.** [backend-api.md `Q-BE-003`]
- **Notes, search and archive screens.** Other modules own them.
- **Server-side behaviour.** Limits, expiry, single use, lock and email sending. [backend-api.md `INV-BE-002`, `INV-BE-004` to `INV-BE-006`]
<!-- /SECTION: web-app.auth.purpose -->
<!-- SECTION: web-app.auth.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

Error code names (`OTP_*`, `VALIDATION_ERROR`) are the backend's proposal. They are unconfirmed (`GAP-WEB-010` in the project spec; backend-api.md `ASM-BE-301`). Every requirement that depends on them is `DRAFT-UNVERIFIED`.

### WEB-AUTH-FR-001: Request a code
- **Given** the user is on `/login` and has entered a non-empty email,
- **When** they submit,
- **Then** the app calls the request-code operation, and on success opens `/login/code` with the email held in memory.
- **Traces to.** US-006-05; BR-022.
- **Verification.** `auth_login_submit_requests_code_and_opens_code_entry` (planned).
- **API.** `API-AUTH-707260`.

### WEB-AUTH-FR-002: Empty email
- **Given** the email field is empty,
- **When** the user submits,
- **Then** no request is made and a field-level error is shown on the email field.
- **Traces to.** US-006-05; project spec `ASM-WEB-007` (the client validates non-empty only).
- **Verification.** `auth_login_empty_email_blocks_submit` (planned).
- **API.** none.

### WEB-AUTH-FR-003: Uniform acknowledgement
- **Given** a request-code call succeeds,
- **When** the code-entry screen is shown,
- **Then** nothing on it varies with whether an account exists for the address.
- **Traces to.** US-006-05.
- **Verification.** `auth_code_entry_copy_independent_of_account_existence` (planned).
- **API.** `API-AUTH-707260`. The backend states the acknowledgement is identical for existing and unknown addresses. [backend-api.md `backend.api-surface`]

### WEB-AUTH-FR-004: Submit a code
- **Given** the user is on `/login/code`,
- **When** they enter digits,
- **Then** the field accepts digits only, up to 6, and the app submits to the verify operation only when exactly 6 digits are present, sending the held email and the code.
- **Traces to.** US-006-05; backend-api.md BR-029. The client check is a convenience: the server's format rule is not stated (`GAP-BE-035`).
- **Verification.** `auth_code_entry_accepts_only_six_digits` (planned).
- **API.** `API-AUTH-692907`.

### WEB-AUTH-FR-005: Successful verification
- **Given** the verify call succeeds,
- **When** the response is received,
- **Then** the app discards all cached server state, and goes to the return target if one is present (in-app paths only) and otherwise to `/notes`.
- **Traces to.** US-006-05; project spec `ASM-WEB-002`, `ASM-WEB-006`.
- **Verification.** `auth_verify_success_navigates_to_return_target_or_notes` (planned).
- **API.** `API-AUTH-692907`.
- **`BLOCKED`.** Storing the returned credential and attaching it to later requests cannot be written. The form is unanswered upstream. [backend-api.md `Q-BE-012`; project spec `GAP-WEB-008`]

### WEB-AUTH-FR-006: Wrong code
- **Given** the verify call is refused with `OTP_CODE_INVALID`,
- **When** the response is received,
- **Then** a wrong-code error is shown and the user stays on `/login/code`. No attempts-remaining figure is shown, because no input exposes one.
- **Traces to.** US-006-05; backend-api.md `backend.error-model`.
- **Verification.** `auth_wrong_code_shows_error_without_attempts_left` (planned).
- **API.** `API-AUTH-692907`.

### WEB-AUTH-FR-007: Expired code
- **Given** the verify call is refused with `OTP_CODE_EXPIRED`,
- **When** the response is received,
- **Then** an expired-code error is shown with an option to request a new code.
- **Traces to.** US-006-05; BR-022.
- **Verification.** `auth_expired_code_offers_new_code` (planned).
- **API.** `API-AUTH-692907`.

### WEB-AUTH-FR-008: Used code
- **Given** the verify call is refused with `OTP_CODE_USED`,
- **When** the response is received,
- **Then** it is handled as `WEB-AUTH-FR-007`.
- **Traces to.** US-006-05, which is silent on this case; project spec `ASM-WEB-009`.
- **Verification.** `auth_used_code_handled_as_expired` (planned).
- **API.** `API-AUTH-692907`.

### WEB-AUTH-FR-009: Account locked
- **Given** the verify call is refused with `OTP_ACCOUNT_LOCKED`,
- **When** the response is received,
- **Then** a lockout error states the account is locked for 15 minutes, code submission is disabled, and no request-new-code option is offered. No countdown is shown, because the contract returns no remaining time.
- **Traces to.** US-006-05; BR-022; project spec `ASM-WEB-011`, `GAP-WEB-011`.
- **Verification.** `auth_locked_disables_submit_and_hides_new_code` (planned).
- **API.** `API-AUTH-692907`.

### WEB-AUTH-FR-010: Resend limit
- **Given** the request-code call is refused with `OTP_SEND_LIMIT_EXCEEDED`,
- **When** the response is received,
- **Then** a resend-limit error is shown and no request-new-code option is offered. Raised from `/login`, the user stays on `/login` (`ASM-WEB-002`).
- **Traces to.** US-006-05; BR-022; backend-api.md `INV-BE-006`.
- **Verification.** `auth_send_limit_shows_error_without_new_code_option` (planned).
- **API.** `API-AUTH-707260`.

### WEB-AUTH-FR-011: Request a new code
- **Given** the expired-code option is shown on `/login/code`,
- **When** the user chooses it,
- **Then** the app calls the request-code operation with the held email. On success it stays on `/login/code`, clears the code field and removes the previous error (`ASM-WEB-001`).
- **Traces to.** US-006-05; BR-022.
- **Verification.** `auth_request_new_code_reuses_held_email` (planned).
- **API.** `API-AUTH-707260`.

### WEB-AUTH-FR-012: Discrimination and 401
- **Given** either sign-in call fails,
- **When** the app decides what to show,
- **Then** it keys on the machine-readable error code and never on HTTP status alone, because status 415 is shared with file-upload rejection. A `401` from either operation shows the local error and never triggers the global redirect to `/login`.
- **Traces to.** US-006-05; project spec `INV-WEB-008`, `INV-WEB-010`.
- **Verification.** `auth_errors_keyed_on_code_not_status`, `auth_verify_401_does_not_redirect` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### WEB-AUTH-FR-013: Server is authoritative
- **Given** any sign-in refusal,
- **When** the app shows it,
- **Then** the app has not counted attempts or resends, and has not computed expiry or lock state from its own clock.
- **Traces to.** US-006-05; project spec `INV-WEB-011`, `CON-WEB-004`.
- **Verification.** `auth_client_holds_no_attempt_or_expiry_counters` (planned).
- **API.** `API-AUTH-692907`.

### WEB-AUTH-FR-014: Email is personal data
- **Given** an email is typed on `/login`,
- **When** it is carried to `/login/code`,
- **Then** it is held in memory only, and never put in a URL, `localStorage` or a client log.
- **Traces to.** US-006-05; project spec `INV-WEB-009`; backend-api.md `ASM-BE-019`.
- **Verification.** `auth_email_never_in_url_or_storage` (planned).
- **API.** none.

### WEB-AUTH-FR-015: Code entry without an email
- **Given** `/login/code` is opened with no email held in memory,
- **When** the route loads,
- **Then** the app redirects to `/login`.
- **Traces to.** US-006-05; project spec `ASM-WEB-005`.
- **Verification.** `auth_code_route_without_email_redirects_to_login` (planned).
- **API.** none.

### WEB-AUTH-FR-016: Field validation errors
- **Given** a sign-in call is refused with `VALIDATION_ERROR`,
- **When** the response is received,
- **Then** field-level errors are shown on the field concerned.
- **Traces to.** US-006-05.
- **Verification.** `auth_validation_error_shown_on_field` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### WEB-AUTH-FR-017: Unclassified failures
- **Given** a network failure or a `5xx`,
- **When** the response or failure is received,
- **Then** a generic error is shown and no state changes.
- **Traces to.** US-006-05.
- **Verification.** `auth_network_or_5xx_shows_generic_error` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.

### WEB-AUTH-FR-018: One request at a time
- **Given** a sign-in request is in flight,
- **When** the user activates the submit or new-code control again,
- **Then** no second request is sent and `Button/Primary` shows its Loading state.
- **Traces to.** US-006-05; BR-022. This is derived: a duplicate request would consume the 3-request limit.
- **Verification.** `auth_no_duplicate_request_in_flight` (planned).
- **API.** `API-AUTH-707260`, `API-AUTH-692907`.
<!-- /SECTION: web-app.auth.requirements -->
<!-- SECTION: web-app.auth.routes | status: DRAFT-UNVERIFIED -->
## Routes

Paths are proposed in the project spec (`ASM-WEB-001` there). Screens and modules come from the design brief.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/login` | `LoginScreen` (M6) | public layout | none (unauthenticated) |
| `/login/code` | `CodeEntryScreen` (M6) | public layout | none; requires an email held in memory, else redirect to `/login` (`WEB-AUTH-FR-015`) |

- **Return target.** After a successful verification, go to the return target if present, otherwise to `/notes`. [project spec `web-app.routing`]
- **Authenticated user on these routes.** Unspecified (`GAP-WEB-003`).
<!-- /SECTION: web-app.auth.routes -->
<!-- SECTION: web-app.auth.components | status: UNKNOWN -->
<!-- status placeholder replaced below -->
<!-- /SECTION: web-app.auth.components -->
<!-- SECTION: web-app.auth.states | status: DRAFT-UNVERIFIED -->
## States

Neither screen lists a collection, so **empty** here means the pristine form and **populated** means a filled form. Both routes are public, so **permission-denied** does not apply and is omitted. There is no multi-item operation, so **partial-failure** does not apply and is omitted.

### `/login` (`LoginScreen`)

| State | Behaviour |
|---|---|
| Empty | Pristine form. This is the only designed variant (Default). |
| Populated | Email entered, submit available. |
| Loading | A request-code call is in flight. `Button/Primary` shows Loading and no second request is sent (`WEB-AUTH-FR-018`). |
| Error | Empty email (`FR-002`), validation (`FR-016`), resend limit (`FR-010`) or generic failure (`FR-017`). Undesigned (`GAP-WEB-007` in the project spec). |

### `/login/code` (`CodeEntryScreen`)

| State | Behaviour |
|---|---|
| Empty | Pristine code field. This is the only designed variant (Default). |
| Populated | Digits entered. Submission is available at exactly 6 digits (`FR-004`). |
| Loading | A verify call or a new-code request is in flight (`FR-018`). |
| Error: wrong | `OTP_CODE_INVALID` (`FR-006`). |
| Error: expired or used | Error with the request-new-code option (`FR-007`, `FR-008`). |
| Error: locked | Lockout error, submission disabled, no new-code option (`FR-009`). The disabled state persists until the user leaves the screen. Reloading loses the held email and returns the user to `/login` (`FR-015`). The re-enable moment is unspecified (`GAP-WEB-011` in the project spec). |
| Error: resend limit | Resend-limit error, no new-code option (`FR-010`). |
| Error: validation or generic | `FR-016`, `FR-017`. |
| Redirect | No email in memory goes to `/login` (`FR-015`). |

Only a Default variant is designed for each screen, so every error and loading state above is undesigned. [design-brief.md screens-inventory; project spec `GAP-WEB-007`, `GAP-WEB-013`]
<!-- /SECTION: web-app.auth.states -->
<!-- SECTION: web-app.auth.api-integration | status: DRAFT-UNVERIFIED -->
## API integration

Reference only. Schemas, fields and error envelopes are defined in the contract. [backend-api.md `backend.api-surface`, `backend.error-model`]

| Operation | Pinned ID | Used by |
|---|---|---|
| Request a one-time code | `API-AUTH-707260` | `WEB-AUTH-FR-001`, `-003`, `-010`, `-011`, `-012`, `-016`, `-017`, `-018` |
| Verify a one-time code | `API-AUTH-692907` | `WEB-AUTH-FR-004` to `-009`, `-012`, `-013`, `-016`, `-017`, `-018` |

- **Pin.** `sha256:bd87f6e16dca`. The contract IDs carry no `@version` suffix (project spec `GAP-WEB-022`).
- **Optional response fields.** The request-code response fields `message` and `expires_at` are backend proposals. This module does not depend on them. [backend-api.md `API-AUTH-707260`]
- **Credential in the verify response.** Its form is unresolved (`Q-BE-012`).
- **Statuses.** The statuses of the verify refusals are not stated in any input (`GAP-BE-038`, `GAP-BE-053`), so the web app keys on the error code (`WEB-AUTH-FR-012`).
<!-- /SECTION: web-app.auth.api-integration -->
<!-- SECTION: web-app.auth.validation | status: DRAFT-UNVERIFIED -->
## Validation

| Rule | Client | Server |
|---|---|---|
| Email non-empty | Yes (`FR-002`) | Format rule not stated (`GAP-BE-035`) |
| Email typed value | Sent as typed; no re-casing or transformation | Compared in normalised form (`ASM-BE-015`) |
| Code is 6 numeric digits | Digits only, up to 6, submit at exactly 6 (`FR-004`) | A 6-digit numeric code is issued (BR-029 in backend-api.md); validation rule not stated (`GAP-BE-035`) |
| Code valid for 10 minutes | No (`FR-013`) | Yes (`INV-BE-004`) |
| Single use | No | Yes (`INV-BE-002`) |
| At most 3 code requests in 10 minutes | No | Yes (`INV-BE-006`) |
| 5 wrong codes lock for 15 minutes | No | Yes (`INV-BE-005`) |

Field-level messages returned by the server are displayed on the field concerned (`FR-016`).
<!-- /SECTION: web-app.auth.validation -->
<!-- SECTION: web-app.auth.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** No input states an accessibility target standard or a verification method. [project spec `web-app.accessibility`, `GAP-WEB-018`]

**Decisions this module needs from the target:**
- Where focus goes on each error, on arriving at `/login/code`, and on becoming locked.
- How the error and lockout messages are announced.
- How the disabled submission is conveyed.
- Keyboard paths for submit and request-new-code.
- How field errors are associated with fields.

**Unblocks with:** an accessibility target and announcement behaviour covering the points above.
<!-- /SECTION: web-app.auth.accessibility -->
<!-- SECTION: web-app.auth.i18n | status: BLOCKED -->
## Internationalisation

**BLOCKED.** No copy deck or locale list is supplied. [project spec `GAP-WEB-012`]

**Messages needing copy:**
- email-empty and validation errors;
- wrong code;
- expired code, and the new-code option label;
- lockout, which states 15 minutes;
- resend limit;
- generic failure.

**Unblocks with:** a copy deck with locales.
<!-- /SECTION: web-app.auth.i18n -->
<!-- SECTION: web-app.auth.design-refs | status: DRAFT-UNVERIFIED -->
## Design references

Figma is not connected. Every reference below is **unverified**, taken from design-brief.md.

| Screen | Module | Variants | Components | Outgoing flow |
|---|---|---|---|---|
| `LoginScreen` | M6 | Default | `Button/Primary` | none recorded |
| `CodeEntryScreen` | M6 | Default | `Button/Primary` | none recorded |

- **Story mapping.** The stories map lists `CodeEntryScreen` for US-006-05. [design-brief.md stories-map]
- **Frame IDs.** None supplied, only screen identifiers.
- **Transition.** The `LoginScreen` to `CodeEntryScreen` transition is not recorded in the design flows. It comes from the story.
- **Token bindings.** None supplied (`GAP-WEB-015` in the project spec).
<!-- /SECTION: web-app.auth.design-refs -->
<!-- SECTION: web-app.auth.test-plan | status: DRAFT-UNVERIFIED -->
## Test plan

All names are planned.

**Component.**
- Each `WEB-AUTH-FR-nnn` has a named test, listed in `web-app.auth.requirements`.
- Each state in `web-app.auth.states` is rendered once.

**End to end.**
- `auth_e2e_request_verify_signin_to_notes`
- `auth_e2e_return_target_after_signin`
- `auth_e2e_five_wrong_codes_lockout`
- `auth_e2e_expired_code_new_code`
- `auth_e2e_send_limit`

These need a backend double that returns the contract's error codes.

**Contract.**
- Check both operations against pin `sha256:bd87f6e16dca`.
- `auth_contract_error_code_registry_mapping`
- `auth_contract_status_415_shared_discrimination`

**Accessibility.** `BLOCKED` until a target exists (`GAP-WEB-018` in the project spec).
<!-- /SECTION: web-app.auth.test-plan -->
<!-- SECTION: web-app.auth.gaps | status: DERIVED -->
## Gaps, contradictions and assumptions

**Id collision to review.** The numbers reserved for this module (`ASM-` from 001, `GAP-` from 002) overlap numbers already defined in the project spec. Items below are marked *(module)*. The same id with *(project spec)* is a different item.

**New in this module:**
- **`GAP-WEB-002` *(module)*.** Paste and autofill behaviour of the code field is not specified. Closing artefact: an input behaviour rule from design or product.
- **`GAP-WEB-003` *(module)*.** What an already-authenticated user sees on `/login` or `/login/code` is not specified. Closing artefact: a routing rule.
- **`GAP-WEB-004` *(module)*.** No control to correct a mistyped email exists on `CodeEntryScreen`, so a code may be sent to a wrong address. Closing artefact: a design for it, or a decision that none is needed.
- **`ASM-WEB-001` *(module)*.** Request-new-code reuses the held email and stays on `/login/code`. Confirm with the story owner.
- **`ASM-WEB-002` *(module)*.** On a resend-limit refusal from `/login`, the user stays on `/login`. Confirm with the story owner.

**Cited from the project spec** (all *(project spec)*):
- **Gaps:**
  - `GAP-WEB-007`: error variants, `Input/Text` and resend control.
  - `GAP-WEB-008`: credential.
  - `GAP-WEB-010`: statuses and code names.
  - `GAP-WEB-011`: lock time.
  - `GAP-WEB-012`: copy.
  - `GAP-WEB-013`: loading.
  - `GAP-WEB-015`: tokens.
  - `GAP-WEB-018`: accessibility.
  - `GAP-WEB-021`: existing login and eligibility.
  - `GAP-WEB-022`: versions.
- **Contradiction:** `CON-WEB-004` (resends versus requests).
- **Assumptions:** `ASM-WEB-002`, `ASM-WEB-005`, `ASM-WEB-006`, `ASM-WEB-007`, `ASM-WEB-009`, `ASM-WEB-011`.
- **Invariants:** `INV-WEB-008`, `INV-WEB-009`, `INV-WEB-010`, `INV-WEB-011`.
<!-- /SECTION: web-app.auth.gaps -->
