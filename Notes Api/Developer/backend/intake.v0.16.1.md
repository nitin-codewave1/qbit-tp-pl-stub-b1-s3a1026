---
id: agent-developer/notes_api/backend/INTAKE.md
type: intake
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.16.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/INTAKE.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: notes_api:backend/INTAKE.md
---

<!-- SECTION: backend.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `design-brief.md` | design_brief | yes | 8,266 bytes, 7 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 486 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `handoffs/HANDOFF-WEBAPP-159759.md` | _none_ | yes | 439 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-653797.md` | _none_ | yes | 529 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-752894.md` | _none_ | yes | 513 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-846910.md` | _none_ | yes | 712 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/needs.json` | _none_ | yes | 2,122 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `platform.md` | platform | yes | 189 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 38,130 bytes, 27 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 23,447 bytes, 36 identifier(s) | declared in INPUTS.yaml |

## Inputs expected

| Role | Status |
|---|---|
| `requirements` | supplied (1 file(s)) |
| `user_stories` | supplied (1 file(s)) |
| `design_brief` | supplied (1 file(s)) |
| `platform` | supplied (1 file(s)) |
| `api_docs` | not supplied — the run degrades, see GAPS.md |
| `feedback` | supplied (1 file(s)) |

## Identifiers

- Distinct identifiers found: **36**
- Of those, user stories: **36**
- With established acceptance detail: **36** (100%, floor 60%)
  - counted when a story carries inline criteria, **or** a `§` reference that resolves to a real requirements section
- Requirements sections available to resolve against: **0**

Roles were declared in `INPUTS.yaml`.
<!-- /SECTION: backend.intake -->
