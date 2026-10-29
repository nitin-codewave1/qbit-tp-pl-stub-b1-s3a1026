---
id: agent-developer/notes_api/backend/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/HANDOFFS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: notes_api:backend/HANDOFFS.md
---

<!-- SECTION: backend.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-NOTESAPI-067561` | `business_rule` | Provide a clean statement of BR-030: who may request, verify or receive a login code (any email address, or only a known set). | Without it the auth actors and access rules cannot be written (Q-BE-001). | yes | `backend.auth-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-217437` | `business_rule` | Decide whether notes are owned per user or shared in the team workspace, and give the access rule for each note operation (BR-031). | It sets the access predicate on every note read and write and decides whether ownership columns exist (Q-BE-002). | yes | `backend.auth-model`, `backend.domain-model`, `backend.migration-plan` |
| `HANDOFF-NOTESAPI-632796` | `business_rule` | Decide what a successful code verification issues (credential type, lifetime, transport, refresh, logout) and whether note and search endpoints require it. | The verify response and 401/403 behaviour on all other endpoints depend on it (Q-BE-003). | yes | `backend.auth-model`, `backend.api-surface`, `backend.error-model` |
| `HANDOFF-NOTESAPI-413008` | `story` | Provide a story or decision for operations that list active notes (pinned first) and archived-only notes. | Search requires q or a tag, so US-006-01 and the archived view have no backend operation (GAP-BE-005). | no | `backend.api-surface`, `backend.coverage` |
| `HANDOFF-NOTESAPI-998344` | `story` | Provide a story or decision for an operation listing distinct tags, together with tag rules (case, charset, length, maximum per note). | The tag filter and tags list have no data source and tag validation is open (GAP-BE-006, GAP-BE-010). | no | `backend.api-surface`, `backend.coverage` |
| `HANDOFF-NOTESAPI-352541` | `business_rule` | Decide remaining lock time on 423, Retry-After values, counter reset rule, whether verify is rate-limited, and the final error envelope and code names. | Lockout and refusal responses cannot be fully specified (GAP-BE-008, GAP-BE-011). | no | `backend.error-model`, `backend.api-surface` |

## Needs answered here

| Need | From lane | Asked | Answered in | How |
|---|---|---|---|---|
| `HANDOFF-WEBAPP-452996` | `web_app` | Specify the remaining lock time, the re-enable rule, precedence among simultaneous verify refusals, whether a new code can be requested while locked, and the final error-code registry. | `backend.error-model` | Proposes the error-code registry and verify-refusal precedence (lock, used, expired, wrong), the 423 lock status, and that a code may be requested during a lock; remaining lock time and re-enable detail stay open as GAP-BE-008 and GAP-BE-011. |

## Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-500592` | `web_app` | Decide and define the session model: what a successful code verification issues (credential type, lifetime, transport), refresh and logout operations, and whether notes and search endpoints require a session (Q-BE-003). | The session model is Q-BE-003, a blocking human decision. No input decides the credential, lifetime, refresh, logout or whether notes and search require a session. |
| `HANDOFF-WEBAPP-732181` | `web_app` | Add operations to list active notes and to list archived-only notes, since search requires q or a tag and include_archived mixes both states. | No story or decision defines list operations for active or archived-only notes (GAP-BE-005). Adding them would be inventing scope that needs a human decision. |
| `HANDOFF-WEBAPP-263975` | `web_app` | Add an operation that lists distinct tags, usable for the tag filter options and the tags list screen. | No story or decision defines a distinct-tags operation, and tag rules are open (GAP-BE-006, GAP-BE-010). It needs a human decision. |
<!-- /SECTION: backend.handoffs -->
