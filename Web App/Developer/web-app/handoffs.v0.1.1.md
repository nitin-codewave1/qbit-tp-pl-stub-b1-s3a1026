---
id: agent-developer/web_app/web-app/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/HANDOFFS.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:web-app/HANDOFFS.md
---

<!-- SECTION: web-app.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-500592` | `api_contract` | Decide and define the session model: what a successful code verification issues (credential type, lifetime, transport), refresh and logout operations, and whether notes and search endpoints require a session (Q-BE-003). | The protected-route rule, login completion, session expiry and logout cannot be specified without it (GAP-WEB-001). | yes | `web-app.routing`, `web-app.auth-flows`, `web-app.authorization-ui`, `web-app.state-management` |
| `HANDOFF-WEBAPP-732181` | `api_contract` | Add operations to list active notes and to list archived-only notes, since search requires q or a tag and include_archived mixes both states. | The notes list (US-006-01) and archived and active views cannot be fetched without them (GAP-WEB-002). | yes | `web-app.module-map`, `web-app.state-management`, `web-app.coverage` |
| `HANDOFF-WEBAPP-263975` | `api_contract` | Add an operation that lists distinct tags, usable for the tag filter options and the tags list screen. | TagsListScreen and the tag filter options have no data source (GAP-WEB-003). | no | `web-app.routing`, `web-app.module-map`, `web-app.coverage` |
| `HANDOFF-WEBAPP-452996` | `api_contract` | Specify the remaining lock time, the re-enable rule, precedence among simultaneous verify refusals, whether a new code can be requested while locked, and the final error-code registry. | Lockout and refusal UI states cannot be fully defined, and no countdown can be specified (GAP-WEB-009). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-078157` | `screen` | Provide design frames for the notes list, pin toggle, archived view, sort control, result count and pagination control. | The design has no frame for these, so their layout and components cannot be specified (GAP-WEB-004, GAP-WEB-014). | no | `web-app.routing`, `web-app.design-system`, `web-app.coverage` |
| `HANDOFF-WEBAPP-024946` | `nfr` | State the supported browsers and versions, the accessibility standard and level with how it is verified, the web bundle and load budget, and the breakpoints, type scale and spacing scale. | These sections are blocked because no input defines them (GAP-WEB-005 to GAP-WEB-008). | no | `web-app.responsive`, `web-app.browser-support`, `web-app.accessibility`, `web-app.performance`, `web-app.design-system` |
| `HANDOFF-WEBAPP-924046` | `business_rule` | Decide the ownership and permission model, and whether non-owned notes return 401, 403 or not-found (Q-BE-001, Q-BE-002). | The permission-denied state and role capabilities are assumed to be uniform until it is decided (GAP-WEB-011). | no | `web-app.authorization-ui`, `web-app.routing` |
| `HANDOFF-WEBAPP-944632` | `story` | Confirm whether the Residency Report and Scope Documentation screens belong in the web app. If so, supply web stories for them and for tag add and remove controls (Q-WEB-001, GAP-WEB-015). | The design maps these screens and controls, but no web story requires them, so they remain unspecified. | no | `web-app.overview`, `web-app.module-map`, `web-app.coverage` |
<!-- /SECTION: web-app.handoffs -->
