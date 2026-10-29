---
id: agent-developer/web_app/web-app/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/HANDOFFS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:web-app/HANDOFFS.md
---

<!-- SECTION: web-app.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-752470` | `api_contract` | Define the session model: the credential returned by verify-code (type, lifetime, transport), refresh behaviour, a logout operation, and whether notes and search operations require a session. | Sign-in completion, the protected-route rule, session expiry and sign-out cannot be specified without it. | yes | `web-app.routing`, `web-app.auth-flows`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-526592` | `api_contract` | Add operations to list active notes and to list archived-only notes, with pagination. | The notes list on /notes and the ArchiveScreen have no data source in the contract. | yes | `web-app.routing`, `web-app.module-map`, `web-app.state-management`, `web-app.authorization-ui` |
| `HANDOFF-WEBAPP-701819` | `api_contract` | Add operations to list distinct tags and to remove a tag from a note. | The tag filter and TagManagementScreen cannot be built without a tag list or tag removal. | no | `web-app.module-map`, `web-app.authorization-ui`, `web-app.routing` |
| `HANDOFF-WEBAPP-733353` | `api_contract` | Specify retry-after values for 429 and 503 responses, remaining lock time and re-enable rule on 423, and the field-detail shape of 400 errors. | Lockout presentation, resend-limit handling, retry behaviour and inline field errors are undefined without them. | no | `web-app.auth-flows`, `web-app.state-management` |
| `HANDOFF-WEBAPP-850530` | `business_rule` | Provide a clean BR-023 with its Enforcement text filled in, stating which endpoints require a session. | BR-023 contains unfilled template placeholders so it cannot serve as the protected-route rule. | no | `web-app.routing`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-943440` | `screen` | Provide design frames for application chrome (header, navigation, sign-out), message/toast components, error variants of login screens, and the sort control and pin toggle. | The design has only four components and Default variants for login screens, leaving navigation and error states unspecified. | no | `web-app.routing`, `web-app.design-system`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-380598` | `screen` | Provide the type scale, spacing scale, and breakpoints with the layout at each breakpoint. | The responsive section and the design-system scales cannot be written without them. | no | `web-app.design-system`, `web-app.responsive` |
| `HANDOFF-WEBAPP-513896` | `nfr` | State supported browsers and versions, performance targets, and accessibility conformance level for the web app. | These sections are blocked or unproduced because no input gives the targets. | no | `web-app.browser-support`, `web-app.performance`, `web-app.accessibility` |
| `HANDOFF-WEBAPP-427792` | `story` | Decide whether the web app needs stories for ResidencyReportScreen, ScopeDocumentationScreen and TagManagementScreen, and whether sign-in should return the user to the originally requested route. | These screens are in the design but no web story requires them, so they are unrouted. | no | `web-app.overview`, `web-app.routing`, `web-app.module-map` |
<!-- /SECTION: web-app.handoffs -->
