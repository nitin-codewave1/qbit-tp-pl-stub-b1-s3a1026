---
id: agent-design/web_app/okf/screens/noteeditorscreen.md
type: screen
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/okf/screens/noteeditorscreen.md
depends_on: [web_app:US-001-01, web_app:US-001-02, web_app:US-001-03, web_app:US-001-05, web_app:US-001-06, web_app:US-002-01, web_app:US-002-02, web_app:US-003-05, web_app:US-006-02, web_app:US-006-03]
commit_kind: fork
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:NoteEditorScreen
---

---
run: 17
spec_version: v1_initial
inputs_hash: "sha256:b82c1cc58bf6375891aec3abdc2914c8947fbdc5c31a1eae6822028eb1ea3e31"
generator: specgen.okf_lite
id: NoteEditorScreen
type: screen
title: Note Editor
module: M1
parent_ids: [US-001-01, US-001-02, US-001-03, US-001-05, US-001-06, US-002-01, US-002-02, US-003-05, US-006-02, US-006-03]
---

# NoteEditorScreen: Note Editor

Module: `M1`  ·  Variants: Default, Empty, Error, Filled

| components | outgoing |
|---|---|
| Empty/State, Input/Text | ON_CLICK→SearchScreen, ON_CLICK→SearchScreen |

## Stories

- [[US-001-01]]
- [[US-001-02]]
- [[US-001-03]]
- [[US-001-05]]
- [[US-001-06]]
- [[US-002-01]]
- [[US-002-02]]
- [[US-003-05]]
- [[US-006-02]]
- [[US-006-03]]
