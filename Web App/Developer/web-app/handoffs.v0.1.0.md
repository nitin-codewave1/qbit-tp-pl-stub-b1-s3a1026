---
id: agent-developer/web_app/web-app/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/HANDOFFS.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:web-app/HANDOFFS.md
---

<!-- SECTION: web-app.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-WEBAPP-846910` | `api_contract` | Add contract operations that list active notes (pinned first, paginated or not) and list archived notes only. Search cannot substitute because it requires a keyword or tag and include_archived mixes active and archived notes. | The notes list, live count, sort, pin toggle on rows and the archived view cannot be built without a list operation (GAP-WEB-001). | yes | `web-app.routing`, `web-app.state-management`, `web-app.authorization-ui`, `web-app.performance`, `web-app.coverage` |
| `HANDOFF-WEBAPP-653797` | `api_contract` | State what a successful API-AUTH-692907 verification returns (body token, cookie or other), its lifetime, and whether any refresh operation exists. | Credential storage and attachment to requests cannot be specified without it (GAP-WEB-004, Q-BE-012). | yes | `web-app.auth-flows`, `web-app.coverage` |
| `HANDOFF-WEBAPP-229710` | `business_rule` | Decide whether archiving an already-archived note and unarchiving an active note returns success idempotently (stories US-003-01/02) or a state-conflict error (requirements BR-012), and state the status and error code. | Client handling of archive/restore on a note already in the target state is BLOCKED on this critical contradiction (CON-WEB-001). | yes | `web-app.overview`, `web-app.state-management`, `web-app.coverage` |
| `HANDOFF-WEBAPP-752894` | `api_contract` | Publish the final error-code registry and the precedence when several verify refusals apply at once (invalid, expired, used, locked), since all share one status. | Error display discriminates on code names that are only proposals (GAP-WEB-005, GAP-BE-601). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-159759` | `api_contract` | Return remaining lock time (or a stated re-enable rule) on the OTP_ACCOUNT_LOCKED error. | The lockout error cannot show a countdown or know when submission re-enables (GAP-WEB-006). | no | `web-app.auth-flows` |
| `HANDOFF-WEBAPP-385244` | `screen` | Provide frames for the authenticated shell and navigation, notes list, note view, sort control, live count, pin toggle/icon, tag filter, permission-denied state, search pagination, loading states, and LoginScreen/CodeEntryScreen error variants with resend control. | None of these elements is designed; the brief has only three components (GAP-WEB-002, GAP-WEB-007, GAP-WEB-011). | no | `web-app.routing`, `web-app.state-management`, `web-app.authorization-ui`, `web-app.auth-flows`, `web-app.design-system` |
| `HANDOFF-WEBAPP-777968` | `business_rule` | Define the sort rule (basis of Newest, collation, tie-breaks, persistence), pin rules (optimistic behaviour, pinning archived notes), and whether client sort or server relevance governs paginated search. | Sort and pin behaviour is applied on assumptions (GAP-WEB-008, GAP-WEB-009, CON-WEB-003). | no | `web-app.state-management` |
| `HANDOFF-WEBAPP-903114` | `business_rule` | State who may sign in, whether notes are per-user or shared, whether an inaccessible note returns 403 or 404, and the sign-out/session invalidation rule. | Authorization UI and sign-out cannot be specified (GAP-WEB-014, GAP-WEB-012). | no | `web-app.authorization-ui`, `web-app.auth-flows` |
| `HANDOFF-WEBAPP-589476` | `nfr` | Supply a supported-browser matrix, an accessibility target standard with verification method, a web bundle/first-load budget, and responsive breakpoints. | These sections are blocked or unbudgeted without them (GAP-WEB-018, GAP-WEB-019, GAP-WEB-020). | no | `web-app.browser-support`, `web-app.accessibility`, `web-app.performance`, `web-app.responsive` |
| `HANDOFF-WEBAPP-172260` | `handoff-brief` | Decide which surface owns EP-004 and EP-005 stories and the ResidencyReportScreen and ApiScopeDocsScreen, and whether the web serves tag add/remove and multi-tag filtering. | The web spec excludes these screens and stories pending ownership (GAP-WEB-021, GAP-WEB-003, GAP-WEB-024). | no | `web-app.overview`, `web-app.routing`, `web-app.module-map`, `web-app.coverage` |
<!-- /SECTION: web-app.handoffs -->
