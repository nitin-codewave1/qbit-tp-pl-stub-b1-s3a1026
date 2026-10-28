---
id: agent-design/web_app/output/F2-components.yaml
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F2-components.yaml
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:design-f2-components
---

schemaVersion: 1
generated_at: '2026-10-28T18:11:30Z'
run: 17
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:b82c1cc58bf6375891aec3abdc2914c8947fbdc5c31a1eae6822028eb1ea3e31
components:
  Button/Primary:
    name: Button/Primary
    role: action
    props:
    - name: Size
      values:
      - Small
      - Medium
      - Large
    - name: State
      values:
      - Default
      - Pressed
      - Loading
      - Disabled
    variants:
    - key: Size=Small, State=Default
      props:
        Size: Small
        State: Default
      size:
        w: FILL
        h: 32
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Small, State=Pressed
      props:
        Size: Small
        State: Pressed
      size:
        w: FILL
        h: 32
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Small, State=Loading
      props:
        Size: Small
        State: Loading
      size:
        w: FILL
        h: 32
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Small, State=Disabled
      props:
        Size: Small
        State: Disabled
      size:
        w: FILL
        h: 32
      bindings:
        bg: UI/surface/disabled
        text: UI/text/disabled
      radius: Border Radius/MD
    - key: Size=Medium, State=Default
      props:
        Size: Medium
        State: Default
      size:
        w: FILL
        h: 36
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Medium, State=Pressed
      props:
        Size: Medium
        State: Pressed
      size:
        w: FILL
        h: 36
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Medium, State=Loading
      props:
        Size: Medium
        State: Loading
      size:
        w: FILL
        h: 36
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Medium, State=Disabled
      props:
        Size: Medium
        State: Disabled
      size:
        w: FILL
        h: 36
      bindings:
        bg: UI/surface/disabled
        text: UI/text/disabled
      radius: Border Radius/MD
    - key: Size=Large, State=Default
      props:
        Size: Large
        State: Default
      size:
        w: FILL
        h: 40
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Large, State=Pressed
      props:
        Size: Large
        State: Pressed
      size:
        w: FILL
        h: 40
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Large, State=Loading
      props:
        Size: Large
        State: Loading
      size:
        w: FILL
        h: 40
      bindings:
        bg: UI/surface/button/primary
        text: UI/text/on-primary
      radius: Border Radius/MD
    - key: Size=Large, State=Disabled
      props:
        Size: Large
        State: Disabled
      size:
        w: FILL
        h: 40
      bindings:
        bg: UI/surface/disabled
        text: UI/text/disabled
      radius: Border Radius/MD
    size:
      w: FILL
      h: 36
    bindings: {}
    icon_slots: []
    states:
    - Default
    - Pressed
    - Loading
    - Disabled
    provenance:
      tier: C
      derived_from: pattern library requires Button/Primary
      rule: component-inventory
      confidence: exact
  Empty/State:
    name: Empty/State
    role: content
    props:
    - name: State
      values:
      - Default
    variants:
    - key: State=Default
      props:
        State: Default
      size:
        w: FILL
        h: HUG
      bindings:
        text: UI/text/secondary
        icon: UI/icon/subtle
      radius: Border Radius/MD
    size:
      w: FILL
      h: HUG
    bindings: {}
    icon_slots: []
    states:
    - Default
    provenance:
      tier: C
      derived_from: pattern library requires Empty/State
      rule: component-inventory
      confidence: exact
  Input/Text:
    name: Input/Text
    role: entry
    props:
    - name: State
      values:
      - Default
      - Focused
      - Filled
      - Error
      - Disabled
    variants:
    - key: State=Default
      props:
        State: Default
      size:
        w: FILL
        h: 38
      bindings:
        bg: UI/surface/input
        text: UI/text/primary
        placeholder: UI/text/placeholder
        border: UI/border/input
      radius: Border Radius/MD
    - key: State=Focused
      props:
        State: Focused
      size:
        w: FILL
        h: 38
      bindings:
        bg: UI/surface/input
        text: UI/text/primary
        placeholder: UI/text/placeholder
        border: UI/border/input-focus
      radius: Border Radius/MD
    - key: State=Filled
      props:
        State: Filled
      size:
        w: FILL
        h: 38
      bindings:
        bg: UI/surface/input
        text: UI/text/primary
        placeholder: UI/text/placeholder
        border: UI/border/input
      radius: Border Radius/MD
    - key: State=Error
      props:
        State: Error
      size:
        w: FILL
        h: 38
      bindings:
        bg: UI/surface/input
        text: UI/text/primary
        placeholder: UI/text/placeholder
        border: UI/border/error
        helper: UI/text/error
      radius: Border Radius/MD
    - key: State=Disabled
      props:
        State: Disabled
      size:
        w: FILL
        h: 38
      bindings:
        bg: UI/surface/disabled
        text: UI/text/disabled
        placeholder: UI/text/placeholder
        border: UI/border/input
      radius: Border Radius/MD
    size:
      w: FILL
      h: 38
    bindings: {}
    icon_slots: []
    states:
    - Default
    - Focused
    - Filled
    - Error
    - Disabled
    provenance:
      tier: C
      derived_from: pattern library requires Input/Text
      rule: component-inventory
      confidence: exact
icons:
  back:
    name: arrow-left
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  calendar:
    name: calendar-blank-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  check:
    name: check
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  chevron-down:
    name: chevron-down
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  chevron-right:
    name: chevron-right
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  clock:
    name: clock-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  close:
    name: close
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  delete:
    name: trash-can-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  edit:
    name: pencil
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  empty:
    name: inbox-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  error:
    name: alert-circle-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  eye:
    name: eye-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  eye-off:
    name: eye-off-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  filter:
    name: filter-variant
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  forward:
    name: arrow-right
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  home:
    name: home-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  info:
    name: information-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  lock:
    name: lock-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  logout:
    name: logout
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  menu:
    name: menu
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  minus:
    name: minus
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  notification:
    name: bell-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  offline:
    name: wifi-off
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  plus:
    name: plus
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  profile:
    name: account-circle-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  refresh:
    name: refresh
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  search:
    name: magnify
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  settings:
    name: cog-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  share:
    name: share-variant
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  sort:
    name: sort
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  success:
    name: check-circle-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
  warning:
    name: alert-outline
    library: MaterialCommunityIcons
    non_obvious: false
    rationale: null
