---
id: agent-design/web_app/output/F5-flow.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F5-flow.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:design-f5-flow
---

schemaVersion: 1
generated_at: '2026-10-28T18:11:30Z'
run: 17
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:b82c1cc58bf6375891aec3abdc2914c8947fbdc5c31a1eae6822028eb1ea3e31
connections:
- id: C-001
  from:
    screen: ApiScopeDocsScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: all routes are listed
  covers:
  - US-005-01
- id: C-002
  from:
    screen: ApiScopeDocsScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: the scope-compliance suite runs
  covers:
  - US-005-04
- id: C-003
  from:
    screen: NoteEditorScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends a request to add tags to the note
  covers:
  - US-002-01
- id: C-004
  from:
    screen: NoteEditorScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends a request to remove that tag
  covers:
  - US-002-02
