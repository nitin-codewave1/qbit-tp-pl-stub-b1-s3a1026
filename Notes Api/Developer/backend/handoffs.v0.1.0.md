---
id: agent-developer/notes_api/backend/HANDOFFS.md
type: handoffs
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/HANDOFFS.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: notes_api:backend/HANDOFFS.md
---

<!-- SECTION: backend.handoffs | status: VERIFIED -->
## Needs this lane cannot answer

| Need | From lane | Asked | Why not |
|---|---|---|---|
| `HANDOFF-WEBAPP-200601` | `web_app` | Define a notes list operation returning active (non-archived) notes with pinned first, plus an archived-only list operation. The contract has neither; search cannot substitute because it requires a keyword or tag. | The backend document defines no notes list operation and no archived-only list. Its only read-many operation is GET /notes/search, with optional q, tag and include_archived and a proposed path (GAP-BE-303). It cannot return archived notes alone. Adding list operations is an API surface decision that the inputs do not make, so it needs a human decision. |
| `HANDOFF-WEBAPP-743710` | `web_app` | State what a successful verify-code response returns (body token, cookie or other), its lifetime, and whether any refresh operation exists. | What a successful verify returns is an open blocking question (Q-BE-012). The credential form (body token, cookie or other) and its lifetime (GAP-BE-302) are undecided, and no input mentions a refresh operation. This needs a human decision. |
| `HANDOFF-WEBAPP-163212` | `web_app` | Confirm the HTTP statuses and final machine-readable error codes for verify-code refusals (wrong, expired, used, locked) and request-code resend limit. Also state whether a remaining lock time is returned. | BR-033 fixes the verify refusals at status 415, and the send limit at 415 under BR-028. The machine-readable codes (OTP_CODE_INVALID etc.) are only proposed names (ASM-BE-301), so they cannot be confirmed as final. Which refusal code wins when several apply is open (GAP-BE-601). No input says whether a remaining lock time is returned, so a countdown is not provided. This needs a human decision. |
<!-- /SECTION: backend.handoffs -->
