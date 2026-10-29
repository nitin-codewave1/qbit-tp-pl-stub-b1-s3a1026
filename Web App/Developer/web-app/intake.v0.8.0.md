---
id: agent-developer/web_app/web-app/INTAKE.md
type: intake
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.8.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/web_app/web-app/INTAKE.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:web-app/INTAKE.md
---

<!-- SECTION: web-app.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `backend-api.md` | api_docs | yes | 46,199 bytes, 54 identifier(s) | declared in INPUTS.yaml |
| `design-brief.md` | design_brief | yes | 15,151 bytes, 36 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 78,815 bytes, 37 identifier(s) | declared in INPUTS.yaml |
| `handoffs/needs.json` | _none_ | yes | 41 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `platform.md` | platform | yes | 189 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 19,604 bytes, 12 identifier(s) | declared in INPUTS.yaml |
| `stories.md` | user_stories | yes | 21,588 bytes, 36 identifier(s) | declared in INPUTS.yaml |

## Inputs expected

| Role | Status |
|---|---|
| `requirements` | supplied (1 file(s)) |
| `user_stories` | supplied (1 file(s)) |
| `design_brief` | supplied (1 file(s)) |
| `platform` | supplied (1 file(s)) |
| `api_docs` | supplied (1 file(s)) |
| `feedback` | supplied (1 file(s)) |

## Identifiers

- Distinct identifiers found: **36**
- Of those, user stories: **36**
- With established acceptance detail: **36** (100%, floor 60%)
  - counted when a story carries inline criteria, **or** a `§` reference that resolves to a real requirements section
- Requirements sections available to resolve against: **0**

Roles were declared in `INPUTS.yaml`.
<!-- /SECTION: web-app.intake -->
