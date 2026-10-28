---
id: agent-design/web_app/output/F6-pages.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.1
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F6-pages.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 5a1a3a81-7d4d-5162-83a3-f81d5f8196b3
okf_id: web_app:design-f6-pages
---

schemaVersion: 1
generated_at: '2026-10-28T19:29:37Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:85db26965c31f55f10819c2ae5f6223d76e8d42be1c21ad289efd0229f0aec2e
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
  - NoteEditorScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M2
  kind: screens
  contains:
  - SearchScreen
  - TagsListScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M3
  kind: screens
  contains:
  - NoteDetailScreen
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
  - CodeEntryScreen
  - LoginScreen
  - NoteDetailScreen
  - NoteEditorScreen
  - ResidencyReportScreen
  - ScopeDocumentationScreen
  - SearchScreen
  - TagsListScreen
  note: Prototype connections only — no new frames on this page.
