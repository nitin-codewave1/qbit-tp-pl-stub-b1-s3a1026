---
id: agent-design/web_app/output/F5-flow.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F5-flow.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:design-f5-flow
---

schemaVersion: 1
generated_at: '2026-10-29T01:02:47Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:ea82a5cfe73f6af30397d78d02a2e40732234976d02af5087dbeb98ad2cd3067
connections:
- id: C-001
  from:
    screen: ScopeDocumentationScreen
    variant: Default
  to:
    screen: ArchiveScreen
    variant: Default
  trigger: ON_CLICK
  condition: all routes are listed
  covers:
  - US-005-01
- id: C-002
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
- id: C-003
  from:
    screen: TagManagementScreen
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
    screen: TagManagementScreen
    variant: Default
  to:
    screen: SearchScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends a request to remove that tag
  covers:
  - US-002-02
- id: C-005
  from:
    screen: TagManagementScreen
    variant: Default
  to:
    screen: ArchiveScreen
    variant: Default
  trigger: ON_CLICK
  condition: client sends an edit or tag update request for it
  covers:
  - US-003-05
