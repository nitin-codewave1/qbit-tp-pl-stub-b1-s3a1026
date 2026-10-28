---
id: agent-developer/web_app/web-app/modules/login-SPEC.md
type: module_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/modules/login-SPEC.md
depends_on: [web_app:US-006-05]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
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
contract_pin: "sha256:8e424bb2b40a"
scope: [/login, /login/code]
```

`spec_mode` is `FULL-FROM-BRIEF`. `design-brief.md` is present and Figma MCP is not connected (`RUN-CONTEXT.inputs.figma_mcp`). Every screen and token name in this module is an unverified claim (4.5, `web-app.design-system`).

`GAP-WEB-001` (project spec) is the one blocker. It covers session issuance after a successful verify and is expanded in `web-app.login.requirements` (`WEB-LOGIN-FR-004`).
<!-- /SECTION: web-app.login.frontmatter -->
<!-- SECTION: web-app.login.purpose | status: DERIVED -->
## Purpose

**Responsibility.** This module lets a Team member sign in from the browser with an emailed one-time code (US-006-05; module map, `web-app.module-map`, row `login`, label M6). It owns:

- `LoginScreen`: the email entry and request of a code.
- `CodeEntryScreen`: the code entry, verification and resend.
- Presenting the refusal states the backend reports: wrong, expired, already-used, resend-limit and account-locked (`BR-022`, US-006-05).

**Not responsible for:**

- The limits themselves (10-minute validity, 3 resends per 10 minutes, 5 wrong codes, 15-minute lock). The backend enforces them (`BR-022` Enforcement: "notes_api enforces the limits on request-code and verify-code; the web app displays the returned errors").
- Issuing, storing, refreshing or ending a session. This is undecided (`Q-BE-003`, project-spec `GAP-WEB-001`), and the session mechanism is owned by `web-app.auth-flows`.
- The protected-route rule and the unauthenticated redirect into this module (`web-app.routing`).
- The destination after sign-in beyond the proposal in `web-app.routing`. The notes list belongs to the `search` module.
- Notes, search, editor and detail behaviour, and the `ResidencyReportScreen` and `ScopeDocumentationScreen` screens.
- Backend behaviour. Needs from the backend are recorded as project-spec `GAP-WEB-001` and `GAP-WEB-009`.
<!-- /SECTION: web-app.login.purpose -->
<!-- SECTION: web-app.login.requirements | status: DRAFT-UNVERIFIED -->
## Requirements

All requirements trace to US-006-05 unless stated. Error names such as `LOGIN_CODE_INVALID` are the backend's proposed registry values (`backend.error-model`, `ASM-BE-006`), not final contract values. Per `backend.error-model`, the web app branches on the error `code`, never on the HTTP status (`INV-WEB-002`).

**WEB-LOGIN-FR-001: Request a code**
- Given the user is on `LoginScreen` and has entered an email address,
- When they submit,
- Then the web app calls `API-AUTH-015365`, and on success shows `CodeEntryScreen` while holding the submitted email.
- Traces to: US-006-05 (first Given); `BR-023`.
- API: `API-AUTH-015365`.
- Verification: `login.request-code.navigates-to-code-entry` (planned).

**WEB-LOGIN-FR-002: Never display a code**
- Given a code request succeeded,
- When any login screen renders,
- Then no login code is shown to the user. The response message never contains the code.
- Traces to: US-006-05; `API-AUTH-015365` response description.
- API: `API-AUTH-015365`.
- Verification: `login.request-code.never-renders-code` (planned).

**WEB-LOGIN-FR-003: Verify a code**
- Given the user is on `CodeEntryScreen` with a held email and has entered a 6-digit code,
- When they submit,
- Then the web app calls `API-AUTH-592570` with that email and code.
- Traces to: US-006-05 (first Given); `BR-023` ("codes are 6 digits").
- API: `API-AUTH-592570`.
- Verification: `login.verify-code.calls-verify` (planned).

**WEB-LOGIN-FR-004: Sign in on success (partly `BLOCKED`)**
- Given `API-AUTH-592570` reports the code verified,
- When the response is received,
- Then the web app signs the user in and moves to the notes list (`ASM-WEB-009` and `Q-WEB-002` in the project spec).
- The signing-in mechanism is `BLOCKED`. The credential type, lifetime, transport, refresh and logout are undecided (`Q-BE-003`, project-spec `GAP-WEB-001`).
- Unblocked by: the answer to `Q-BE-003`, or clean `BR-023` text.
- Traces to: US-006-05 ("signs the user in").
- API: `API-AUTH-592570`.
- Verification: `login.verify-code.signs-in-and-navigates` (planned; assertable only after `GAP-WEB-001`).

**WEB-LOGIN-FR-005: Wrong code**
- Given the user submits a wrong code,
- When the backend refuses it with the wrong-code error,
- Then the web app shows a distinct wrong-code error and keeps the user on `CodeEntryScreen`.
- Traces to: `BR-022` ("the web app shows the matching error for each case"); US-006-05.
- API: `API-AUTH-592570`.
- Verification: `login.verify-code.wrong-code-error` (planned).

**WEB-LOGIN-FR-006: Account locked**
- Given the user has entered a wrong code 5 times,
- When the backend reports the account is locked,
- Then the web app shows a lockout error stating the account is locked for 15 minutes and disables code submission (`INV-WEB-001`).
- No countdown or re-enable rule is specified. The contract defines no remaining-lock-time field (project-spec `GAP-WEB-009`).
- Traces to: US-006-05 (second Given); `BR-022`.
- API: `API-AUTH-592570`.
- Verification: `login.verify-code.locked-disables-submit` (planned).

**WEB-LOGIN-FR-007: Expired code**
- Given the code was issued more than 10 minutes ago,
- When the user submits it and the backend reports it expired,
- Then the web app shows an expired-code error with an option to request a new code.
- Traces to: US-006-05 (third Given); `BR-022`.
- API: `API-AUTH-592570`, `API-AUTH-015365` (for the new code).
- Verification: `login.verify-code.expired-offers-resend` (planned).

**WEB-LOGIN-FR-008: Already-used code**
- Given a code was already consumed,
- When the backend reports it already used,
- Then the web app shows a distinct already-used error. The copy is unspecified (project-spec `GAP-WEB-016`).
- Traces to: `BR-022` ("matching error for each case"); `backend.error-model`.
- API: `API-AUTH-592570`.
- Verification: `login.verify-code.used-code-error` (planned).

**WEB-LOGIN-FR-009: Request a new code (resend)**
- Given the user is on `CodeEntryScreen` and not locked (`ASM-WEB-004`),
- When they choose to request a new code,
- Then the web app calls `API-AUTH-015365` with the held email and stays on `CodeEntryScreen`.
- Traces to: US-006-05 (third Given: "option to request a new code").
- API: `API-AUTH-015365`.
- Verification: `login.resend.calls-request-code` (planned).

**WEB-LOGIN-FR-010: Resend limit**
- Given the resend limit of 3 per 10 minutes has been reached,
- When the user asks for a new code and the backend reports the limit,
- Then the web app shows a resend-limit error and offers no further resend.
- The web app shows no remaining-resends counter, because the limit is counted by the backend as requests including the first (`CON-WEB-001`).
- Traces to: US-006-05 (third Given); `BR-022`.
- API: `API-AUTH-015365`.
- Verification: `login.resend.limit-reached-error` (planned).

**WEB-LOGIN-FR-011: Guard the code-entry route**
- Given no email is held (direct visit, reload or lost state),
- When the user opens `/login/code`,
- Then the web app redirects to `/login`.
- Traces to: US-006-05 (the code-entry screen follows the email submit); `web-app.routing`.
- Verification: `login.routing.code-entry-requires-email` (planned).

**WEB-LOGIN-FR-012: Validation errors from the backend**
- Given the backend returns a 400 for an email or code,
- When the response is received,
- Then the web app shows the error against the offending field and does not navigate.
- Traces to: US-006-05; `NFR-011` (field-level detail on validation errors).
- API: `API-AUTH-015365`, `API-AUTH-592570`.
- Verification: `login.validation.backend-400-inline` (planned).

**Invariants**

- `INV-WEB-001`: Code submission is disabled while the account is locked (US-006-05).
- `INV-WEB-002`: The web app distinguishes login refusals by the error `code`, never by HTTP status alone. Several refusals share status 415 (`backend.error-model`).
- `INV-WEB-003`: A login code is never displayed in any state (`API-AUTH-015365`).

Everything not listed above (generic failure, network loss, unlisted statuses) has no story. See `GAP-WEB-004` and `ASM-WEB-006`.
<!-- /SECTION: web-app.login.requirements -->
<!-- SECTION: web-app.login.routes | status: DRAFT-UNVERIFIED -->
## Routes

Paths are proposals (project-spec `ASM-WEB-001`). Screen names come from `design-brief.md`.

| Route | Page | Layout | Required permission |
|---|---|---|---|
| `/login` | `LoginScreen` | public | none (`BR-023` exception: request-code and verify-code need no session token) |
| `/login/code` | `CodeEntryScreen` | public | none. Reachable only with a held email; otherwise redirect to `/login` (`WEB-LOGIN-FR-011`) |

Both routes are public, so no permission-denied state exists for this module. Whether an already-signed-in user visiting these routes is redirected is unspecified and depends on the session decision (`GAP-WEB-001`).

The destination after sign-in and the return-to-route behaviour are project-level (`web-app.routing`, project-spec `GAP-WEB-013`).
<!-- /SECTION: web-app.login.routes -->
<!-- SECTION: web-app.login.components | status: UNKNOWN -->
<!-- status line corrected below -->
<!-- /SECTION: web-app.login.components -->
<!-- SECTION: web-app.login.states | status: BLOCKED -->
## States

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.states -->
<!-- SECTION: web-app.login.api-integration | status: BLOCKED -->
## Api integration

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.api-integration -->
<!-- SECTION: web-app.login.validation | status: BLOCKED -->
## Validation

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.validation -->
<!-- SECTION: web-app.login.accessibility | status: BLOCKED -->
## Accessibility

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.accessibility -->
<!-- SECTION: web-app.login.i18n | status: BLOCKED -->
## I18n

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.i18n -->
<!-- SECTION: web-app.login.design-refs | status: BLOCKED -->
## Design refs

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.design-refs -->
<!-- SECTION: web-app.login.test-plan | status: BLOCKED -->
## Test plan

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.test-plan -->
<!-- SECTION: web-app.login.gaps | status: BLOCKED -->
## Gaps

**BLOCKED.** the run did not produce this section for module `login`. Nothing has been written in its place.

This section is required by the surface's template and was not produced. It is emitted blocked rather than omitted: a missing section is invisible, a blocked one is a work item.
<!-- /SECTION: web-app.login.gaps -->
