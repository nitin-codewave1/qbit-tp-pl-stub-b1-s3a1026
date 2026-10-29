---
id: agent-developer/notes_api/backend/INTAKE.md
type: intake
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.18.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-developer/notes_api/backend/INTAKE.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: notes_api:backend/INTAKE.md
---

<!-- SECTION: backend.intake | status: VERIFIED -->
## What this run read

| Input | Role(s) | Parsed | Taken from it | How the role was decided |
|---|---|---|---|---|
| `design-brief.md` | design_brief | yes | 8,266 bytes, 7 identifier(s) | declared in INPUTS.yaml |
| `feedback.md` | feedback | yes | 77,525 bytes, 47 identifier(s) | declared in INPUTS.yaml |
| `handoffs/HANDOFF-WEBAPP-263975.md` | _none_ | yes | 477 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-452996.md` | _none_ | yes | 547 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-500592.md` | _none_ | yes | 670 bytes, 0 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/HANDOFF-WEBAPP-732181.md` | _none_ | yes | 552 bytes, 1 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `handoffs/needs.json` | _none_ | yes | 2,191 bytes, 1 identifier(s) | declared in INPUTS.yaml for another surface, or not at all |
| `platform.md` | platform | yes | 189 bytes, 0 identifier(s) | declared in INPUTS.yaml |
| `requirements.md` | requirements | yes | 37,797 bytes, 27 identifier(s) | declared in INPUTS.yaml |
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
