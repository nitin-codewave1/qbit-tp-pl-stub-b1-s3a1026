---
id: agent-developer/notes_api/backend/INTAKE.md
type: intake
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.19.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/INTAKE.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: df4d63c0-0252-56b1-8292-59d2f9d8aa48
okf_id: notes_api:backend/INTAKE.md
---

<!-- SECTION: backend.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `design-brief.md` | design_brief | yes | 8,266 bytes, 7 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 76,310 bytes, 48 identifier(s) | declared in INPUTS.yaml |
| `handoffs/HANDOFF-WEBAPP-526592.md` | _none_ | yes | 504 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-701819.md` | _none_ | yes | 465 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-733353.md` | _none_ | yes | 542 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-752470.md` | _none_ | yes | 604 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/needs.json` | _none_ | yes | 2,068 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `platform.md` | platform | yes | 189 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 37,807 bytes, 27 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 23,459 bytes, 36 identifier(s) | declared in INPUTS.yaml |

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
