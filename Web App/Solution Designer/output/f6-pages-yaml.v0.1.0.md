---
id: agent-design/web_app/output/F6-pages.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F6-pages.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:design-f6-pages
---

schemaVersion: 1
generated_at: '2026-10-28T18:11:30Z'
run: 17
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:b82c1cc58bf6375891aec3abdc2914c8947fbdc5c31a1eae6822028eb1ea3e31
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
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M3
  kind: screens
  contains:
  - ArchivedNotesScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M4
  kind: screens
  contains:
  - ResidencyReportScreen
  note: One frame per variant, laid out left to right in flow order.
- name: Screens — M5
  kind: screens
  contains:
  - ApiScopeDocsScreen
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
  - ApiScopeDocsScreen
  - ArchivedNotesScreen
  - CodeEntryScreen
  - LoginScreen
  - NoteEditorScreen
  - ResidencyReportScreen
  - SearchScreen
  note: Prototype connections only — no new frames on this page.
