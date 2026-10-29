---
id: agent-developer/notes_api/backend/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/HANDOFFS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: e1e628bb-b234-5a13-b948-b4689dcc8eab
okf_id: notes_api:backend/HANDOFFS.md
---

<!-- SECTION: backend.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-NOTESAPI-067561` | `business_rule` | Provide a clean statement of BR-030: who may request, verify or receive a login code (any email address, or only a known set). | The auth model cannot define actors or roles without it (Q-BE-001). | yes | `backend.auth-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-501376` | `business_rule` | Decide whether notes are owned per user or shared in the team workspace (BR-031, HANDOFF-NOTESAPI-981573), and give the access rule for each note operation. | The access predicate, the ownership columns (migration 007) and the role matrix depend on it (Q-BE-002). | yes | `backend.auth-model`, `backend.domain-model`, `backend.migration-plan` |
| `HANDOFF-NOTESAPI-345681` | `business_rule` | Decide what a successful code verification issues (credential type, lifetime, transport, refresh and logout) and whether the notes and search endpoints require it. | The verify response, 401/403 applicability on other endpoints and the web_app session handling depend on it (Q-BE-003). | yes | `backend.auth-model`, `backend.api-surface`, `backend.error-model` |
| `HANDOFF-NOTESAPI-418421` | `story` | Supply a story or decision for list operations: active notes (pinned first), archived-only notes, and distinct tags. Search cannot substitute because it needs a keyword or tag. | US-006-01, US-006-05 and the tag filter have no backend data source (GAP-BE-005). | no | `backend.api-surface`, `backend.coverage` |
| `HANDOFF-NOTESAPI-558747` | `business_rule` | Specify the remaining lock time returned on a 423, Retry-After on 429 and 503, and the rejection status for WebSocket or SSE attempts. | The error model cannot give these values without inventing them (GAP-BE-011). | no | `backend.error-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-590636` | `business_rule` | Provide tag rules: case, allowed characters, length and maximum tags per note. | The tag validation in create and add-tag is open (GAP-BE-010). | no | `backend.domain-model`, `backend.api-surface` |

## Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-500592` | `web_app` | Decide and define the session model: what a successful code verification issues (credential type, lifetime, transport), refresh and logout operations, and whether notes and search endpoints require a session (Q-BE-003). | The session model is undecided. It is blocking question Q-BE-003 and needs a human decision, so the backend spec defines no credential, refresh or logout. |
| `HANDOFF-WEBAPP-732181` | `web_app` | Add operations to list active notes and to list archived-only notes, since search requires q or a tag and include_archived mixes both states. | The backend spec has no operation that lists active or archived-only notes (GAP-BE-005). Adding one is a scope decision that needs a story or decision from the product side. |
| `HANDOFF-WEBAPP-263975` | `web_app` | Add an operation that lists distinct tags, usable for the tag filter options and the tags list screen. | The backend spec has no operation that lists distinct tags. No input defines one, and tag rules are also open (GAP-BE-010, GAP-BE-005), so a story or decision is needed first. |
| `HANDOFF-WEBAPP-452996` | `web_app` | Specify the remaining lock time, the re-enable rule, precedence among simultaneous verify refusals, whether a new code can be requested while locked, and the final error-code registry. | Only partly answerable. Request-code is allowed during a lock (BR-032), and verify precedence is proposed in ASM-BE-007. The remaining lock time, the re-enable rule and the final error-code registry are not defined by any input (GAP-BE-011, ASM-BE-001). |
<!-- /SECTION: backend.handoffs -->
