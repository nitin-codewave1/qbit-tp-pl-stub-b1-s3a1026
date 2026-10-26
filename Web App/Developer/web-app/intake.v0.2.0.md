---
id: agent-developer/web_app/web-app/INTAKE.md
type: intake
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/INTAKE.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: bd40d909-f5d3-5811-94e2-22764694e8a7
okf_id: web-app/INTAKE.md
---

<!-- SECTION: web-app.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `backend-api.md` | api_docs | yes | 25,346 bytes, 25 identifier(s) | declared in INPUTS.yaml |
| `design-brief.md` | design_brief | yes | 8,744 bytes, 6 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 46,779 bytes, 11 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 16,671 bytes, 12 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 20,314 bytes, 6 identifier(s) | declared in INPUTS.yaml |

## Inputs expected

| Role | Status |
|---|---|
| `requirements` | supplied (1 file(s)) |
| `user_stories` | supplied (1 file(s)) |
| `design_brief` | supplied (1 file(s)) |
| `platform` | not supplied — the run degrades, see GAPS.md |
| `api_docs` | supplied (1 file(s)) |
| `feedback` | supplied (1 file(s)) |

## Identifiers

- Distinct identifiers found: **6**
- Of those, user stories: **6**
- With established acceptance detail: **6** (100%, floor 60%)
  - counted when a story carries inline criteria, **or** a `§` reference that resolves to a real requirements section
- Requirements sections available to resolve against: **0**

Roles were declared in `INPUTS.yaml`.
<!-- /SECTION: web-app.intake -->
