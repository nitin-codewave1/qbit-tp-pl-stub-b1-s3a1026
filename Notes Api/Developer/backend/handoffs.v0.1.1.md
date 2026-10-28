---
id: agent-developer/notes_api/backend/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/HANDOFFS.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: notes_api:backend/HANDOFFS.md
---

<!-- SECTION: backend.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-NOTESAPI-067561` | `business_rule` | Provide a clean statement of BR-030: who may request, verify or receive a login code (any email address, or only a known set). | The permission matrix in the auth model cannot be written. The source text of BR-030 is garbled and the rule is marked open (Q-BE-001). | yes | `backend.auth-model`, `backend.coverage` |
| `HANDOFF-NOTESAPI-499880` | `business_rule` | Decide whether notes are owned per user or shared by the team workspace, and give the access rule for each note read and write (BR-031). | The note ownership columns, migration 005 and the per-request access check depend on it (Q-BE-002). | yes | `backend.auth-model`, `backend.domain-model`, `backend.migration-plan` |
| `HANDOFF-NOTESAPI-162512` | `business_rule` | State what a successful code verification issues (credential type, lifetime, transport, refresh) and whether note and search endpoints require it. | The verify response and the 401/403 applicability cannot be specified without it (Q-BE-003, GAP-BE-004). | yes | `backend.auth-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-832585` | `story` | Add a story for listing active notes (pinned first) and for listing archived notes only. | No backend story or endpoint lists notes, although BR-019, US-006-01 and US-006-05 assume one (GAP-BE-005, GAP-BE-006). | no | `backend.api-surface`, `backend.coverage` |

## Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-159759` | `web_app` | Return remaining lock time (or a stated re-enable rule) on the OTP_ACCOUNT_LOCKED error. | The spec gives a 15-minute lock and the ACCOUNT_LOCKED code, but defines no remaining-time field or re-enable rule on the error. The registry names are proposals, so this needs a decision and is not answered in this document. |
| `HANDOFF-WEBAPP-653797` | `web_app` | State what a successful API-AUTH-692907 verification returns (body token, cookie or other), its lifetime, and whether any refresh operation exists. | What a successful verify issues, its lifetime and any refresh are undecided (Q-BE-003, GAP-BE-004). That needs a human decision, so auth-model is BLOCKED. |
| `HANDOFF-WEBAPP-752894` | `web_app` | Publish the final error-code registry and the precedence when several verify refusals apply at once (invalid, expired, used, locked), since all share one status. | The error registry is only proposed (ASM-BE-006). No input sets a precedence when several verify refusals apply, and several share status 415. Needs a human decision. |
| `HANDOFF-WEBAPP-846910` | `web_app` | Add contract operations that list active notes (pinned first, paginated or not) and list archived notes only. Search cannot substitute because it requires a keyword or tag and include_archived mixes active and archived notes. | No input defines a list-notes or archived-only list operation (GAP-BE-005, GAP-BE-006). Adding one needs a new story or a human decision. The spec does not invent endpoints. |
<!-- /SECTION: backend.handoffs -->
