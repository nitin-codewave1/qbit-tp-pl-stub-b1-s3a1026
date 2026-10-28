---
id: agent-design/web_app/okf/screens/notedetailscreen.md
type: screen
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.5.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/okf/screens/notedetailscreen.md
depends_on: [web_app:US-001-03, web_app:US-002-01, web_app:US-002-02, web_app:US-003-01, web_app:US-003-02, web_app:US-003-05, web_app:US-003-06]
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:NoteDetailScreen
---

---
run: 18
spec_version: v1_initial
inputs_hash: "sha256:85db26965c31f55f10819c2ae5f6223d76e8d42be1c21ad289efd0229f0aec2e"
generator: specgen.okf_lite
id: NoteDetailScreen
type: screen
title: Note Detail
module: M3
parent_ids: [US-001-03, US-002-01, US-002-02, US-003-01, US-003-02, US-003-05, US-003-06]
---

# NoteDetailScreen: Note Detail

Module: `M3`  ·  Variants: Default, Empty, Error, Filled

| components | outgoing |
|---|---|
| Button/Primary, Empty/State, Input/Text | ON_CLICK→SearchScreen, ON_CLICK→SearchScreen |

## Stories

- [[US-001-03]]
- [[US-002-01]]
- [[US-002-02]]
- [[US-003-01]]
- [[US-003-02]]
- [[US-003-05]]
- [[US-003-06]]
