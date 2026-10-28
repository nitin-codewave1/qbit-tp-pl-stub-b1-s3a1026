---
id: agent-developer/notes_api/backend/INTAKE.md
type: intake
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.10.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/INTAKE.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 74f30224-6cf6-52b7-b8e8-fac4d2eb580c
okf_id: backend/INTAKE.md
---

<!-- SECTION: backend.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `design-brief.md` | design_brief | yes | 8,377 bytes, 2 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 48,978 bytes, 10 identifier(s) | declared in INPUTS.yaml |
| `platform.md` | platform | yes | 189 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 33,096 bytes, 18 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 23,987 bytes, 6 identifier(s) | declared in INPUTS.yaml |

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

- Distinct identifiers found: **6**
- Of those, user stories: **6**
- With established acceptance detail: **6** (100%, floor 60%)
  - counted when a story carries inline criteria, **or** a `§` reference that resolves to a real requirements section
- Requirements sections available to resolve against: **0**

Roles were declared in `INPUTS.yaml`.
<!-- /SECTION: backend.intake -->
