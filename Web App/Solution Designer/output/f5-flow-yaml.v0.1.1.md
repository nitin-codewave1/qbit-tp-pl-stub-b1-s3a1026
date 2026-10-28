---
id: agent-design/web_app/output/F5-flow.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F5-flow.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:design-f5-flow
---

schemaVersion: 1
generated_at: '2026-10-28T19:29:37Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:85db26965c31f55f10819c2ae5f6223d76e8d42be1c21ad289efd0229f0aec2e
connections:
- id: C-001
  from:
    screen: NoteDetailScreen
    variant: Default
    node: PrimaryAction
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends a request to add tags to the note
  covers:
  - US-002-01
- id: C-002
  from:
    screen: NoteDetailScreen
    variant: Default
    node: PrimaryAction
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends a request to remove that tag
  covers:
  - US-002-02
- id: C-003
  from:
    screen: ScopeDocumentationScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: all routes are listed
  covers:
  - US-005-01
- id: C-004
  from:
    screen: ScopeDocumentationScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: the scope-compliance suite runs
  covers:
  - US-005-04
