---
id: agent-design/web_app/output/F3-screens.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F3-screens.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:design-f3-screens
---

schemaVersion: 1
generated_at: '2026-10-29T06:32:23Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:759edb805d9280383ace25a3d5be1d2aab5b6b7de0955b2bc09cf593fafb31e1
screens:
- id: ArchivedNotesScreen
  module: M3
  title: Archived Notes
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-003-01
      - US-003-02
      flags: []
    stories:
    - US-003-01
    - US-003-02
    - US-003-03
    - US-003-06
    - US-006-06
    kind: default
  - name: Empty
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: EmptyState
        component: Empty/State
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/secondary
          icon: UI/icon/subtle
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-003-01
      - US-003-02
      flags: []
    stories:
    - US-003-03
    - US-003-06
    kind: state
    note: the collection is legitimately empty — 2 story(ies)
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-003-01
      - US-003-02
      flags: []
    stories:
    - US-003-01
    - US-003-02
    - US-003-03
    - US-003-06
    kind: state
    note: the request or the input failed — 4 story(ies)
  - name: Filled
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-003-01
      - US-003-02
      flags: []
    stories:
    - US-003-01
    - US-003-02
    kind: state
    note: the form is valid and the action is enabled — 2 story(ies)
  stories:
  - US-003-01
  - US-003-02
  - US-003-03
  - US-003-06
  - US-006-06
  signal:
    explicit: 0.2
    transitions: 4
    goal_boundary: 4
    covering_stories: 5
- id: CodeEntryScreen
  module: M6
  title: Code Entry
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-006-05
      flags: []
    stories:
    - US-006-05
    kind: default
  stories:
  - US-006-05
  signal:
    explicit: 0.0
    transitions: 0
    goal_boundary: 1
    covering_stories: 1
- id: InfrastructureProvisioningScreen
  module: M4
  title: Infrastructure Provisioning
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    stories:
    - US-004-01
    - US-004-03
    kind: default
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    stories:
    - US-004-01
    kind: state
    note: the request or the input failed — 1 story(ies)
  stories:
  - US-004-01
  - US-004-03
  signal:
    explicit: 0.0
    transitions: 2
    goal_boundary: 2
    covering_stories: 2
- id: LoginScreen
  module: M6
  title: Login
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-006-05
      flags: []
    stories:
    - US-006-05
    kind: default
  stories:
  - US-006-05
  signal:
    explicit: 0.0
    transitions: 0
    goal_boundary: 1
    covering_stories: 1
- id: NoteEditorScreen
  module: M1
  title: Note Editor
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/input
        overrides: {}
        stories:
        - US-006-02
        - US-006-03
        - US-001-01
        - US-001-02
        - US-001-05
        flags: []
      stories: []
      flags: []
    stories:
    - US-006-02
    - US-006-03
    - US-001-01
    - US-001-02
    - US-001-05
    - US-001-06
    - US-005-02
    kind: default
  - name: Empty
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: EmptyState
        component: Empty/State
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/secondary
          icon: UI/icon/subtle
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    stories:
    - US-001-05
    kind: state
    note: the collection is legitimately empty — 1 story(ies)
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        variant: State=Error
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/error
        overrides: {}
        stories:
        - US-006-02
        - US-006-03
        - US-001-01
        - US-001-02
        - US-001-05
        flags: []
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    stories:
    - US-001-01
    - US-001-02
    - US-001-05
    - US-005-02
    kind: state
    note: the request or the input failed — 4 story(ies)
  - name: Filled
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/input
        overrides: {}
        stories:
        - US-006-02
        - US-006-03
        - US-001-01
        - US-001-02
        - US-001-05
        flags: []
      stories: []
      flags: []
    stories:
    - US-001-01
    kind: state
    note: the form is valid and the action is enabled — 1 story(ies)
  stories:
  - US-006-02
  - US-006-03
  - US-001-01
  - US-001-02
  - US-001-05
  - US-001-06
  - US-005-02
  signal:
    explicit: 0.0
    transitions: 5
    goal_boundary: 7
    covering_stories: 7
- id: NoteTagsScreen
  module: M2
  title: Note Tags
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/input
        overrides: {}
        stories:
        - US-002-01
        - US-003-05
        flags: []
      stories: []
      flags: []
    stories:
    - US-002-01
    - US-002-02
    - US-002-05
    - US-003-05
    kind: default
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        variant: State=Error
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/error
        overrides: {}
        stories:
        - US-002-01
        - US-003-05
        flags: []
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    stories:
    - US-002-01
    - US-002-02
    kind: state
    note: the request or the input failed — 2 story(ies)
  - name: Filled
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/input
        overrides: {}
        stories:
        - US-002-01
        - US-003-05
        flags: []
      stories: []
      flags: []
    stories:
    - US-002-01
    - US-002-05
    kind: state
    note: the form is valid and the action is enabled — 2 story(ies)
  stories:
  - US-002-01
  - US-002-02
  - US-002-05
  - US-003-05
  signal:
    explicit: 0.0
    transitions: 3
    goal_boundary: 4
    covering_stories: 4
- id: ResidencyReportScreen
  module: M4
  title: Residency Report
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-004-05
      flags: []
    stories:
    - US-004-02
    - US-004-04
    - US-004-05
    kind: default
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-004-05
      flags: []
    stories:
    - US-004-04
    - US-004-05
    kind: state
    note: the request or the input failed — 2 story(ies)
  stories:
  - US-004-02
  - US-004-04
  - US-004-05
  signal:
    explicit: 0.3333
    transitions: 3
    goal_boundary: 2
    covering_stories: 3
- id: ScopeDocumentationScreen
  module: M5
  title: Scope Documentation
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-005-04
      flags: []
    stories:
    - US-005-01
    - US-005-03
    - US-005-04
    - US-005-05
    - US-005-06
    kind: default
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    - name: PrimaryAction
      component: Button/Primary
      size:
        w: FILL
        h: 36
      position: sticky-bottom
      scroll: none
      margin:
        top: 0
        right: 14
        bottom: 14
        left: 14
      pattern: action.primary
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      overrides:
        _Label: Label
      stories:
      - US-005-04
      flags: []
    stories:
    - US-005-01
    - US-005-03
    - US-005-06
    kind: state
    note: the request or the input failed — 3 story(ies)
  stories:
  - US-005-01
  - US-005-03
  - US-005-04
  - US-005-05
  - US-005-06
  signal:
    explicit: 0.0
    transitions: 5
    goal_boundary: 5
    covering_stories: 5
- id: SearchScreen
  module: M2
  title: Search
  frame:
    size:
      w: 1440
      h: 900
    bg: UI/surface/default
    layout:
      mode: VERTICAL
      gap: 0
      padding:
        top: 0
        right: 0
        bottom: 0
        left: 0
      align: START
    safe_area:
      top: 0
      bottom: 0
  variants:
  - name: Default
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/input
        overrides: {}
        stories:
        - US-006-04
        - US-002-03
        flags: []
      stories: []
      flags: []
    stories:
    - US-006-04
    - US-002-03
    - US-002-04
    - US-002-06
    kind: default
  - name: Empty
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: EmptyState
        component: Empty/State
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/secondary
          icon: UI/icon/subtle
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    stories:
    - US-002-03
    - US-002-06
    kind: state
    note: the collection is legitimately empty — 2 story(ies)
  - name: Error
    modules:
    - name: AppBar
      size:
        w: FILL
        h: 48
      position: sticky-top
      scroll: none
      scale: heading-md
      bindings:
        bg: UI/surface/default
        text: UI/text/primary
        icon: UI/icon/default
      overrides:
        _Header: Header
      stories: []
      flags: []
    - name: ContentStack
      size:
        w: FILL
        h: FILL
      layout:
        mode: VERTICAL
        gap: 20
        padding:
          top: 14
          right: 14
          bottom: 14
          left: 14
        align: START
      position: normal
      scroll: vertical
      bindings: {}
      overrides: {}
      children:
      - name: TextField
        component: Input/Text
        variant: State=Error
        size:
          w: FILL
          h: 38
        position: normal
        scroll: none
        radius: Border Radius/MD
        pattern: input.text.single
        bindings:
          bg: UI/surface/input
          text: UI/text/primary
          border: UI/border/error
        overrides: {}
        stories:
        - US-006-04
        - US-002-03
        flags: []
      - name: ErrorMessage
        size:
          w: FILL
          h: FILL
        position: normal
        scroll: none
        bindings:
          text: UI/text/error
          border: UI/border/error
        overrides: {}
        stories: []
        flags: []
      stories: []
      flags: []
    stories:
    - US-002-03
    - US-002-06
    kind: state
    note: the request or the input failed — 2 story(ies)
  stories:
  - US-006-04
  - US-002-03
  - US-002-04
  - US-002-06
  signal:
    explicit: 1.0
    transitions: 3
    goal_boundary: 0
    covering_stories: 4
