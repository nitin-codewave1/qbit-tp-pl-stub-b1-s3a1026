---
id: agent-design/web_app/output/F6-pages.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F6-pages.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:design-f6-pages
---

schemaVersion: 1
generated_at: '2026-10-29T01:02:47Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:ea82a5cfe73f6af30397d78d02a2e40732234976d02af5087dbeb98ad2cd3067
pages:
- name: Cover
  kind: cover
  contains: []
  note: Project, platform, ruleset version, and the date this spec was generated.
- name: Foundations
  kind: foundations
  contains:
  - Primitives
  - UI tokens
  - Typography
  - Spacing
  - Radius
  - Elevation
  note: Variable collections. Nothing here is drawn by hand.
- name: Components
  kind: components
  contains:
  - Button/Primary
  - Card/Item
  - Empty/State
  - Input/Text
  note: One component set per frame, variants as a grid.
- name: Screens — M1
  kind: screens
  contains:
  - NoteDetailScreen
  - NoteEditorScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M2
  kind: screens
  contains:
  - SearchScreen
  - TagManagementScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M3
  kind: screens
  contains:
  - ArchiveScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M4
  kind: screens
  contains:
  - ResidencyReportScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M5
  kind: screens
  contains:
  - ScopeDocumentationScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M6
  kind: screens
  contains:
  - CodeEntryScreen
  - LoginScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Flow
  kind: flow
  contains:
  - ArchiveScreen
  - CodeEntryScreen
  - LoginScreen
  - NoteDetailScreen
  - NoteEditorScreen
  - ResidencyReportScreen
  - ScopeDocumentationScreen
  - SearchScreen
  - TagManagementScreen
  note: Prototype connections only — no new frames on this page.
