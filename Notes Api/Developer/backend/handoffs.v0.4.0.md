---
id: agent-developer/notes_api/backend/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.4.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/HANDOFFS.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
okf_id: notes_api:backend/HANDOFFS.md
---

<!-- SECTION: backend.handoffs | status: VERIFIED -->
## Needs this lane raised

| Need | Kind | What is needed | Why | Blocking | Waiting sections |
|---|---|---|---|---|---|
| `HANDOFF-NOTESAPI-969269` | `business_rule` | Restate BR-030 cleanly: may any email address request, verify and receive a login code, or only a known set of addresses? | BR-030 is garbled and its rationale says the rule stays open. The actors and permission matrix in the auth model cannot be written without it. | yes | `backend.auth-model`, `backend.domain-model`, `backend.module-map` |
| `HANDOFF-NOTESAPI-690778` | `business_rule` | Decide note ownership (per-user or shared team workspace) and the access rule for each note and search operation, replacing the placeholder text of BR-031. | Ownership columns, the access predicate and the notes/search to auth dependency cannot be specified until this is decided. | yes | `backend.auth-model`, `backend.domain-model`, `backend.migration-plan`, `backend.module-map` |
| `HANDOFF-NOTESAPI-193009` | `business_rule` | Define the session model: the credential returned by a successful verify-code (type, lifetime, transport), refresh behaviour, logout, and whether notes and search operations require a session. | The verify-code response, 401/403 handling for protected endpoints and the auth model depend on it. | yes | `backend.auth-model`, `backend.api-surface`, `backend.error-model` |
| `HANDOFF-NOTESAPI-206942` | `story` | Add a story or rule for listing active notes and archived-only notes with pagination, and for listing distinct tags and removing a tag from a note. | Search needs q or tag, so no operation lists notes or tags. US-006-01 and US-006-04 are uncovered in the coverage section. | no | `backend.api-surface`, `backend.coverage` |
| `HANDOFF-NOTESAPI-872672` | `business_rule` | Specify retry hints and values for 429 and 503, remaining lock time and the re-enable rule on 423, wrong-code counter reset, window type, expiry boundary, and the final error envelope and code names. | The error model carries these as open points (GAP-BE-008, GAP-BE-011) and cannot state them without a decision. | no | `backend.error-model`, `backend.nfr` |
| `HANDOFF-NOTESAPI-284535` | `nfr` | Supply the NFR-017 and NFR-019 text without garbling, plus encryption at rest and in transit, backup and log retention, and latency and availability targets for the login operations. | The data lifecycle and NFR sections cannot assert these figures from the inputs. | no | `backend.data-lifecycle`, `backend.nfr`, `backend.observability` |

## Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-752470` | `web_app` | Define the session model: the credential returned by verify-code (type, lifetime, transport), refresh behaviour, a logout operation, and whether notes and search operations require a session. | The session model is undecided. No input defines a credential, lifetime, transport, refresh, logout or whether notes and search need a session (Q-BE-003, blocking). The decision is outside this lane. |
| `HANDOFF-WEBAPP-526592` | `web_app` | Add operations to list active notes and to list archived-only notes, with pagination. | No input defines an operation that lists active or archived-only notes, and search requires q or tag (GAP-BE-005). Adding one needs a story or a human decision, so this lane does not invent it. |
| `HANDOFF-WEBAPP-701819` | `web_app` | Add operations to list distinct tags and to remove a tag from a note. | No input defines distinct-tag listing or tag removal, and the tag rules are open (GAP-BE-006, GAP-BE-010). Adding them needs a story or a human decision, so this lane does not invent them. |
| `HANDOFF-WEBAPP-733353` | `web_app` | Specify retry-after values for 429 and 503 responses, remaining lock time and re-enable rule on 423, and the field-detail shape of 400 errors. | The retry-after values, the remaining lock time, the 423 re-enable rule and the 400 field-detail shape are not in any input (GAP-BE-008, GAP-BE-011). The error model keeps them open and does not invent them. |
<!-- /SECTION: backend.handoffs -->
