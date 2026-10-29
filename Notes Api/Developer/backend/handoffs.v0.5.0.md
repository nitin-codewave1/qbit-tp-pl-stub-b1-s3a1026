---
id: agent-developer/notes_api/backend/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/HANDOFFS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: notes_api:backend/HANDOFFS.md
---

<!-- SECTION: backend.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-NOTESAPI-320429` | `business_rule` | Decide which email addresses may request, verify and receive a login code (BR-030 is garbled and its rationale leaves it open). | The actors and permission rules in the auth model cannot be written without it. | yes | `backend.auth-model`, `backend.domain-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-324925` | `business_rule` | Decide whether notes are owned by one user or shared in a team workspace, and state the access rule per notes and search operation (BR-031 is a placeholder). | Ownership columns, access checks and 401/403 rules for notes and search depend on it. | yes | `backend.auth-model`, `backend.domain-model`, `backend.module-map`, `backend.migration-plan` |
| `HANDOFF-NOTESAPI-813214` | `business_rule` | Decide the session model: the credential verify-code returns (type, lifetime, transport), refresh, logout, and whether notes and search require a session. | The verify-code response and the auth model cannot be completed without it. | yes | `backend.auth-model`, `backend.api-surface`, `backend.module-map`, `backend.migration-plan` |
| `HANDOFF-NOTESAPI-693322` | `business_rule` | Confirm the error body envelope and code names, and give retry-after values for 429 and 503, remaining lock time and re-enable rule on 423, and the field-detail shape of 400 errors. | The error model is proposed only (ASM-BE-006) and these values have no source. | no | `backend.error-model`, `backend.api-surface` |
| `HANDOFF-NOTESAPI-158497` | `story` | Provide a story or decision for operations to list active notes and archived-only notes with pagination. | No input defines a list-notes operation, so US-006-01 and the archived view are uncovered. | no | `backend.api-surface`, `backend.coverage` |
| `HANDOFF-NOTESAPI-467940` | `story` | Provide a story and rules for removing a tag from a note, listing distinct tags, and tag validation (case, charset, length, count). | Tag removal and listing have no acceptance criteria, and the tag rules are unstated. | no | `backend.api-surface`, `backend.domain-model`, `backend.coverage` |

## Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-752470` | `web_app` | Define the session model: the credential returned by verify-code (type, lifetime, transport), refresh behaviour, a logout operation, and whether notes and search operations require a session. | The session model is undecided and no input defines it. It is raised as blocking question Q-BE-003 and needs a human decision. |
| `HANDOFF-WEBAPP-526592` | `web_app` | Add operations to list active notes and to list archived-only notes, with pagination. | No input defines a list-notes operation, and adding one is a scope decision. The handoff file body was not supplied (Q-BE-005, GAP-BE-002). |
| `HANDOFF-WEBAPP-701819` | `web_app` | Add operations to list distinct tags and to remove a tag from a note. | No input has a story for distinct-tag listing or tag removal, and the tag rules are unstated. This needs a story and a decision (Q-BE-006, GAP-BE-003). |
| `HANDOFF-WEBAPP-733353` | `web_app` | Specify retry-after values for 429 and 503 responses, remaining lock time and re-enable rule on 423, and the field-detail shape of 400 errors. | Retry-after values, remaining lock time, the 423 re-enable rule and the 400 detail shape are not stated by any input. This needs an error-model decision (Q-BE-004, GAP-BE-004). Only the 423 status for a lock (BR-033) and the rule that requesting a code is allowed during a lock (BR-032) are answered. |
<!-- /SECTION: backend.handoffs -->
