---
id: agent-developer/web_app/web-app/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/HANDOFFS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/HANDOFFS.md
---

<!-- SECTION: web-app.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-552660` | `api_contract` | Define the session model: what a successful verify-code response gives the browser (credential type, lifetime, transport), refresh and logout operations, and which notes and search endpoints require a session. Also supply a clean BR-023 without template placeholders. | The protected-route rule, sign-in completion, session expiry and sign-out are BLOCKED without it. | yes | `web-app.routing`, `web-app.auth-flows`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-460580` | `api_contract` | Add operations to list active notes and to list archived-only notes, with pagination, and without requiring a keyword or tag. | US-006-01 and the archived view in US-006-06 need list data sources. The contract has none, and search requires q or tags. | yes | `web-app.routing`, `web-app.state-management`, `web-app.module-map`, `web-app.coverage` |
| `HANDOFF-WEBAPP-863088` | `api_contract` | Add a remove-tag operation and a list-distinct-tags operation, and supply a web story saying how the user picks tags for the filter. | NoteTagsScreen cannot be routed and the US-006-04 tag-picking control cannot be specified. | no | `web-app.module-map`, `web-app.routing`, `web-app.coverage` |
| `HANDOFF-WEBAPP-457990` | `api_contract` | State the error model: retry-after for 429 and 503, remaining lock time on 423, the 423 re-enable rule, and the field-detail shape of 400. | The UI keys on status code only and shows no countdown or field-level detail. | no | `web-app.auth-flows`, `web-app.state-management` |
| `HANDOFF-WEBAPP-712561` | `screen` | Supply design frames for application chrome (header, navigation, sign-out), message and toast components, error variants of LoginScreen and CodeEntryScreen, the sort control, the pin toggle, a not-found page, and an input component on the login screens. | These parts exist only as proposals marked ASM-WEB-015 and ASM-WEB-016. | no | `web-app.routing`, `web-app.design-system`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-854794` | `screen` | State the type scale, spacing scale, breakpoints and the layout at each breakpoint. | The responsive section is BLOCKED and the design-system scales are BLOCKED. | no | `web-app.design-system`, `web-app.responsive` |
| `HANDOFF-WEBAPP-593210` | `nfr` | List the supported browsers and versions for the web app. | The browser-support section is BLOCKED. | no | `web-app.browser-support` |
| `HANDOFF-WEBAPP-223037` | `nfr` | State the accessibility standard and conformance level for the web app. | The accessibility section is BLOCKED. | no | `web-app.accessibility` |
| `HANDOFF-WEBAPP-467885` | `nfr` | State web performance targets (load time) and a bundle budget. | The performance section is BLOCKED. NFR-001 to NFR-012 are API-level. | no | `web-app.performance` |
| `HANDOFF-WEBAPP-329761` | `screen` | Supply a design frame for note detail, or decide which screen US-006-01 maps to and where US-006-02 navigates to the saved note. | US-006-01 maps to no screen and the design has no note-detail screen. SearchScreen and NoteEditorScreen are assumed. | no | `web-app.routing`, `web-app.module-map` |
| `HANDOFF-WEBAPP-121536` | `story` | Supply web stories for the screens InfrastructureProvisioningScreen, ResidencyReportScreen, ScopeDocumentationScreen and NoteTagsScreen, or confirm that they are out of scope for the web app. | 30 stories in EP-001 to EP-005 are API or infrastructure stories, so these screens have no web story and are not routed. | no | `web-app.coverage`, `web-app.module-map`, `web-app.routing` |
| `HANDOFF-WEBAPP-856732` | `business_rule` | Decide how sorting and the live count behave when results span more than one page, since sorting is client-side and results are paginated. | Sort and count currently apply only to the notes loaded into the list (ASM-WEB-010). | no | `web-app.state-management` |
| `HANDOFF-WEBAPP-838983` | `business_rule` | Decide who may sign in, whether notes are owned per user, and whether another person's note shows as not found or forbidden (Q-BE-001, Q-BE-002). | Ownership-dependent behaviour and the not-allowed versus does-not-exist display are BLOCKED. | no | `web-app.authorization-ui` |
<!-- /SECTION: web-app.handoffs -->
