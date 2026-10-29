---
id: agent-design/web_app/okf/screens/tagmanagementscreen.md
type: screen
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/okf/screens/tagmanagementscreen.md
depends_on: [web_app:US-002-01, web_app:US-002-02, web_app:US-002-05, web_app:US-003-05]
commit_kind: append
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:TagManagementScreen
---

---
run: 18
spec_version: v1_initial
inputs_hash: "sha256:ea82a5cfe73f6af30397d78d02a2e40732234976d02af5087dbeb98ad2cd3067"
generator: specgen.okf_lite
id: TagManagementScreen
type: screen
title: Tag Management
module: M2
parent_ids: [US-002-01, US-002-02, US-002-05, US-003-05]
---

# TagManagementScreen: Tag Management

Module: `M2`  ·  Variants: Default, Error, Filled

| components | outgoing |
|---|---|
| Card/Item, Input/Text | ON_CLICK→SearchScreen, ON_CLICK→SearchScreen, ON_CLICK→ArchiveScreen |

## Stories

- [[US-002-01]]
- [[US-002-02]]
- [[US-002-05]]
- [[US-003-05]]
