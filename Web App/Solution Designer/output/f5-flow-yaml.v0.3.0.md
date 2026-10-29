---
id: agent-design/web_app/output/F5-flow.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F5-flow.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:design-f5-flow
---

schemaVersion: 1
generated_at: '2026-10-29T06:32:23Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:759edb805d9280383ace25a3d5be1d2aab5b6b7de0955b2bc09cf593fafb31e1
connections:
- id: C-001
  from:
    screen: NoteTagsScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends a request to add tags to the note
  covers:
  - US-002-01
- id: C-002
  from:
    screen: NoteTagsScreen
    variant: Default
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
    node: PrimaryAction
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
    node: PrimaryAction
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: the scope-compliance suite runs
  covers:
  - US-005-04
