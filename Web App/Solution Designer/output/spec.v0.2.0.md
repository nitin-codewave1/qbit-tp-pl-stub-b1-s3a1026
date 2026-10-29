---
id: agent-design/web_app/output/SPEC.md
type: design_spec
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.2.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/SPEC.md
depends_on: []
commit_kind: fork
confidence: agent
run_id: 5bb3fa2c-7733-5f41-a10d-4c162cbe306d
okf_id: web_app:design-spec
---

# a1 — Design Specification

**Generated:** 2026-10-29T01:02:47Z · run 18 · version `v1_initial`  
**Ruleset:** 1 · **Inputs:** `sha256:ea82a5cfe73f…`  
**Platform:** web · WCAG-2.1-AA  

> This document is a **view over F1–F6**. It contains no fact that is not
> in those files. Do not edit it — edit the inputs and regenerate.

1. [Foundations](#1-foundations)
2. [Components](#2-components)
3. [Screens](#3-screens)
4. [Flow](#4-flow)
5. [Stories and coverage](#5-stories-and-coverage)
6. [Figma file structure](#6-figma-file-structure)

---

## 1. Foundations

### Variable collections

| Collection | Modes | Entries |
|---|---|---|
| `Primitives` | Value | 7 |
| `UI` | Light, Dark | 2 |
| `Spacing` | Value | 16 |
| `Border Radius` | Value | 9 |
| `Typography` | Value | 4 |
| `Elevation` | Value | 6 |

#### Primitives

| Family | 50 | 300 | 500 | 700 | 900 |
|---|---|---|---|---|---|
| Primary | `#EEF6FF` | `#85B7FF` | `#2563EB` | `#1C3BD3` | `#1F3790` |
| Error | `#FFF2F1` | `#F89A92` | `#DC2626` | `#B2000B` | `#831A1C` |
| Success | `#F0FDF4` | `#81DF9E` | `#16A34A` | `#197237` | `#1D522F` |
| Warning | `#FFFBEB` | `#FCD34D` | `#F59E0B` | `#B45309` | `#78350F` |
| Info | `#ECFEFF` | `#67E8F9` | `#06B6D4` | `#0E7490` | `#164E63` |
| Neutral | `#F9FAFB` | `#D1D5DB` | `#6B7280` | `#374151` | `#111827` |
| Dark | `#F8FAFC` | `#CBD5E1` | `#64748B` | `#334155` | `#0F172A` |

### Semantic tokens

71 `UI/*` tokens. Components reference these, never a raw hex.

| Token | Light | Dark | Derived from |
|---|---|---|---|
| `UI/border/default` | `Neutral/200` | `Dark/700` | B5.neutral + surface rule |
| `UI/border/disabled` | `Neutral/200` | `Dark/700` | B5.neutral + surface rule |
| `UI/border/error` | `Error/500` | `Error/400` | B5.error + default rule |
| `UI/border/focus` | `Primary/500` | `Primary/400` | B5.primary + default rule |
| `UI/border/input` | `Neutral/200` | `Dark/700` | B5.neutral + surface rule |
| `UI/border/input-focus` | `Primary/500` | `Primary/400` | B5.primary + default rule |
| `UI/border/strong` | `Neutral/300` | `Dark/600` | B5.neutral + surface rule |
| `UI/border/subtle` | `Neutral/100` | `Dark/800` | B5.neutral + override rule |
| `UI/border/success` | `Success/500` | `Success/400` | B5.success + default rule |
| `UI/border/warning` | `Warning/500` | `Warning/400` | B5.warning + default rule |
| `UI/color/black` | `Neutral/900` | `Dark/100` | B5.neutral + surface rule |
| `UI/color/disabled` | `Neutral/100` | `Dark/800` | B5.neutral + override rule |
| `UI/color/error` | `Error/500` | `Error/400` | B5.error + default rule |
| `UI/color/error-subtle` | `Error/50` | `Error/800` | B5.error + subtle rule |
| `UI/color/hover-destructive` | `Error/600` | `Error/700` | B5.error + override rule |
| `UI/color/hover-ghost` | `Primary/50` | `Primary/900` | B5.primary + override rule |
| `UI/color/hover-primary` | `Primary/600` | `Primary/700` | B5.primary + override rule |
| `UI/color/hover-secondary` | `Neutral/100` | `Dark/700` | B5.neutral + surface rule |
| `UI/color/hover-success` | `Success/600` | `Success/700` | B5.success + override rule |
| `UI/color/info` | `Info/500` | `Info/400` | B5.info + default rule |
| `UI/color/info-subtle` | `Info/50` | `Info/800` | B5.info + subtle rule |
| `UI/color/overlay` | `Neutral/900` | `Neutral/900` | B5.neutral + override rule |
| `UI/color/primary` | `Primary/500` | `Primary/400` | B5.primary + default rule |
| `UI/color/primary-hover` | `Primary/600` | `Primary/300` | B5.primary + override rule |
| `UI/color/primary-subtle` | `Primary/50` | `Primary/900` | B5.primary + override rule |
| `UI/color/success` | `Success/500` | `Success/400` | B5.success + default rule |
| `UI/color/success-subtle` | `Success/50` | `Success/800` | B5.success + subtle rule |
| `UI/color/warning` | `Warning/500` | `Warning/400` | B5.warning + default rule |
| `UI/color/warning-subtle` | `Warning/50` | `Warning/800` | B5.warning + subtle rule |
| `UI/color/white` | `Neutral/0` | `Dark/900` | B5.neutral + surface rule |
| `UI/icon/active` | `Primary/500` | `Primary/400` | B5.primary + default rule |
| `UI/icon/default` | `Neutral/500` | `Dark/400` | B5.neutral + surface rule |
| `UI/icon/disabled` | `Neutral/300` | `Dark/600` | B5.neutral + surface rule |
| `UI/icon/error` | `Error/500` | `Error/400` | B5.error + default rule |
| `UI/icon/info` | `Info/500` | `Info/400` | B5.info + default rule |
| `UI/icon/inverse` | `Neutral/0` | `Dark/900` | B5.neutral + surface rule |
| `UI/icon/on-primary` | `Neutral/0` | `Neutral/900` | B5.neutral + override rule |
| `UI/icon/subtle` | `Neutral/400` | `Dark/500` | B5.neutral + surface rule |
| `UI/icon/success` | `Success/500` | `Success/400` | B5.success + default rule |
| `UI/icon/warning` | `Warning/500` | `Warning/400` | B5.warning + default rule |
| `UI/surface/button/destructive` | `Error/500` | `Error/400` | B5.error + default rule |
| `UI/surface/button/ghost` | `Neutral/0` | `Dark/900` | B5.neutral + surface rule |
| `UI/surface/button/primary` | `Primary/600` | `Primary/400` | B5.primary + default rule |
| `UI/surface/button/primary-hover` | `Primary/700` | `Primary/300` | B5.primary + override rule |
| `UI/surface/button/secondary` | `Neutral/100` | `Dark/700` | B5.neutral + surface rule |
| `UI/surface/button/success` | `Success/500` | `Success/400` | B5.success + default rule |
| `UI/surface/card` | `Neutral/0` | `Dark/800` | B5.neutral + override rule |
| `UI/surface/default` | `Neutral/0` | `Dark/900` | B5.neutral + surface rule |
| `UI/surface/disabled` | `Neutral/100` | `Dark/800` | B5.neutral + override rule |
| `UI/surface/input` | `Neutral/50` | `Dark/800` | B5.neutral + surface rule |
| `UI/surface/input-focus` | `Neutral/0` | `Dark/900` | B5.neutral + surface rule |
| `UI/surface/overlay` | `Neutral/900` | `Neutral/900` | B5.neutral + override rule |
| `UI/surface/secondary` | `Neutral/50` | `Dark/800` | B5.neutral + surface rule |
| `UI/surface/tertiary` | `Neutral/100` | `Dark/700` | B5.neutral + surface rule |
| `UI/surface/toast/error` | `Error/50` | `Error/900` | B5.error + toast rule |
| `UI/surface/toast/info` | `Info/50` | `Info/900` | B5.info + toast rule |
| `UI/surface/toast/success` | `Success/50` | `Success/900` | B5.success + toast rule |
| `UI/surface/toast/warning` | `Warning/50` | `Warning/900` | B5.warning + toast rule |
| `UI/text/disabled` | `Neutral/300` | `Dark/700` | B5.neutral + override rule |
| `UI/text/error` | `Error/700` | `Error/400` | B5.error + default rule |
| `UI/text/info` | `Info/700` | `Info/400` | B5.info + default rule |
| `UI/text/inverse` | `Neutral/0` | `Dark/900` | B5.neutral + surface rule |
| `UI/text/link` | `Primary/600` | `Primary/400` | B5.primary + default rule |
| `UI/text/link-hover` | `Primary/700` | `Primary/300` | B5.primary + override rule |
| `UI/text/on-primary` | `Neutral/0` | `Neutral/900` | B5.neutral + override rule |
| `UI/text/placeholder` | `Neutral/500` | `Dark/400` | B5.neutral + override rule |
| `UI/text/primary` | `Neutral/900` | `Dark/100` | B5.neutral + surface rule |
| `UI/text/secondary` | `Neutral/600` | `Dark/400` | B5.neutral + surface rule |
| `UI/text/success` | `Success/700` | `Success/400` | B5.success + default rule |
| `UI/text/tertiary` | `Neutral/500` | `Dark/400` | B5.neutral + surface rule |
| `UI/text/warning` | `Warning/700` | `Warning/400` | B5.warning + default rule |

<details><summary>Why these dark values are overridden</summary>

- **`UI/border/subtle`** — A subtle border in dark mode has to be lighter than the surface it divides, not darker. Inversion produces a border that disappears into the card.
- **`UI/color/disabled`** — Disabled fill must read as "inert" rather than "another surface level". The inversion table lands it on 700, which is the same value as surface/tertiary and makes a disabled control look interactive.
- **`UI/color/hover-destructive`** — Same reasoning as color/hover-primary.
- **`UI/color/hover-ghost`** — A ghost button's hover is a tint of the page, so it follows the subtle rule rather than the default rule.
- **`UI/color/hover-primary`** — This is the pressed/active fill for a solid primary button, not the resting fill. It goes deeper in both modes.
- **`UI/color/hover-success`** — Same reasoning as color/hover-primary.
- **`UI/color/overlay`** — A scrim is darkness, not a surface. Inverting it would make the modal backdrop lighter than the content it is supposed to recede behind.
- **`UI/color/primary-hover`** — Hover has to move AWAY from the resting state in the direction of more contrast. In light mode that is darker (600); in dark mode it is lighter (300). The default rule moves both the same direction.
- **`UI/color/primary-subtle`** — The primary subtle fill sits behind primary-coloured text and chips. At 800 it does not separate from surface/secondary in dark mode; the semantic subtles (success/warning/error/info) do not have that problem because their hue already distinguishes them.
- **`UI/icon/on-primary`** — Same reasoning and same measurements as text/on-primary.
- **`UI/surface/button/primary-hover`** — Same reasoning as color/primary-hover — hover moves toward more contrast, which is lighter in dark mode and darker in light mode. The default rule resolves every saturated token to 400, so a hover state derived by rule would be identical to its own resting state.
- **`UI/surface/card`** — In light mode a card is white on a grey page. In dark mode the relationship inverts: the page is darkest and the card lifts off it. Straight inversion would make card and page the same value and lose the elevation entirely.
- **`UI/surface/disabled`** — Matches color/disabled so a disabled control is one flat value.
- **`UI/surface/overlay`** — Same reasoning as color/overlay — the surface alias of the same scrim.
- **`UI/text/disabled`** — Disabled text must be visibly inert. Inversion gives 600, which reads as merely secondary.
- **`UI/text/link-hover`** — Same reasoning as color/primary-hover.
- **`UI/text/on-primary`** — Dark text, not white. The dark-mode primary button fills with primary/400, and white on primary/400 measures 2.54:1 — a clear AA failure. Near-black on the same fill measures 6.98:1. Light mode keeps white on primary/600 at 5.17:1. The label colour therefore flips with the mode; it does not stay white in both.
- **`UI/text/placeholder`** — Placeholder ought to sit below text/secondary, and in light mode it does. In dark mode AA does not leave room: on the dark input surface, step 500 measures 3.07:1 and 600 measures 1.93:1, so 400 is the only step that clears 4.5:1 — which is also where secondary lands. The two collide in dark mode by necessity. Differentiate placeholder by italic or by an icon affordance rather than by reducing its contrast.

</details>

### Type scales

| Scale | Size | Weight | Line height | Family |
|---|---|---|---|---|
| `heading-xl` | 32px | 700 | 38px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `heading-lg` | 24px | 700 | 29px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `heading-md` | 20px | 600 | 28px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `heading-sm` | 16px | 600 | 22px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `body-lg` | 16px | 400 | 24px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `body-md` | 14px | 400 | 21px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `body-sm` | 12px | 400 | 18px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `label` | 14px | 500 | 21px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `button` | 16px | 600 | 24px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |
| `mono` | 14px | 500 | 21px | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. |

### Component dimensions

Every value below traces to a Tier A/B input. Locking one (R6) makes it human-owned and it is never recomputed.

| Dimension | Value | Derived from |
|---|---|---|
| `app_bar_height` | 48px | B2 density=compact |
| `avatar_lg` | 56px | B2 density=compact |
| `avatar_md` | 36px | B2 density=compact |
| `avatar_sm` | 28px | B2 density=compact |
| `badge_height` | 20px | B2 density=compact |
| `border_weight` | 1px | default stroke |
| `border_weight_focus` | 2px | focus ring |
| `bottom_sheet_handle` | 4px | platform convention |
| `button_height` | 36px | B2 density=compact + A3 tap_target=32px |
| `button_height_lg` | 40px | B2 density=compact + A3 tap_target=32px |
| `button_height_sm` | 32px | B2 density=compact + A3 tap_target=32px |
| `card_gap` | 10px | B2 density=compact |
| `card_padding` | 14px | B2 density=compact |
| `checkbox_size` | 18px | B2 density=compact |
| `chip_height` | 28px | B2 density=compact |
| `divider_weight` | 1px | hairline |
| `fab_size` | 48px | B2 density=compact |
| `icon_lg` | 24px | icon grid |
| `icon_md` | 20px | icon grid |
| `icon_sm` | 16px | icon grid |
| `icon_xl` | 32px | icon grid |
| `input_height` | 38px | B2 density=compact + A3 tap_target=32px |
| `list_row_gap` | 8px | B2 density=compact |
| `list_row_height` | 40px | B2 density=compact + A3 tap_target=32px |
| `modal_max_width` | 480px | A3 ref_width=1440 — centre dialog |
| `modal_padding` | 20px | B2 density=compact |
| `progress_height` | 4px | B2 density=compact |
| `radio_size` | 18px | B2 density=compact |
| `ref_height` | 900px | A3 platform=web |
| `ref_width` | 1440px | A3 platform=web |
| `safe_area_bottom` | 0px | A3 platform=web |
| `safe_area_top` | 0px | A3 platform=web |
| `screen_padding` | 14px | B2 density=compact |
| `search_bar_height` | 38px | B2 density=compact + A3 tap_target=32px |
| `section_gap` | 20px | B2 density=compact |
| `select_height` | 38px | B2 density=compact + A3 tap_target=32px |
| `skeleton_row_height` | 14px | B2 density=compact |
| `switch_height` | 20px | B2 density=compact |
| `switch_width` | 38px | B2 density=compact |
| `tab_bar_height` | 56px | B2 density=compact |
| `tap_target_min` | 32px | A3 platform=web |
| `textarea_min_height` | 76px | B2 density=compact + A3 tap_target=32px |
| `toast_height` | 48px | B2 density=compact |
| `toast_max_width` | 400px | A3 ref_width=1440 |

### Radius

| Step | Value | Usage |
|---|---|---|
| `Border Radius/None` | 0px | no radius |
| `Border Radius/XS` | 0px | subtle |
| `Border Radius/SM` | 2px | badges, chips |
| `Border Radius/MD` | 2px | inputs, small cards |
| `Border Radius/LG` | 4px | cards, modals |
| `Border Radius/XL` | 4px | bottom sheets |
| `Border Radius/2XL` | 6px | large sheets |
| `Border Radius/3XL` | 8px | feature surfaces |
| `Border Radius/Full` | 999px | pills, avatars, FABs |

### Elevation

| Level | Y | Blur | Opacity | Note |
|---|---|---|---|---|
| `Elevation/SM` | 1 | 2 | 0.05 |  |
| `Elevation/MD` | 4 | 8 | 0.08 |  |
| `Elevation/LG` | 10 | 20 | 0.1 |  |
| `Elevation/XL` | 20 | 40 | 0.12 |  |
| `Elevation/Scrim` | 0 | 0 | 0.4 | modal backdrop — a flat fill, not a shadow |
| `Elevation/Sheet` | 0 | 16 | 0.16 | centre dialog — symmetric shadow |


## 2. Components

4 component sets · 19 variants

### `Button/Primary`

**Size**: Small, Medium, Large · **State**: Default, Pressed, Loading, Disabled  
Size: `FILL × 36`  
*pattern library requires Button/Primary*

| Variant | Height | Bindings |
|---|---|---|
| `Size=Small, State=Default` | 32 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Small, State=Pressed` | 32 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Small, State=Loading` | 32 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Small, State=Disabled` | 32 | `bg`→`UI/surface/disabled`, `text`→`UI/text/disabled` |
| `Size=Medium, State=Default` | 36 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Medium, State=Pressed` | 36 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Medium, State=Loading` | 36 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Medium, State=Disabled` | 36 | `bg`→`UI/surface/disabled`, `text`→`UI/text/disabled` |
| `Size=Large, State=Default` | 40 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Large, State=Pressed` | 40 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Large, State=Loading` | 40 | `bg`→`UI/surface/button/primary`, `text`→`UI/text/on-primary` |
| `Size=Large, State=Disabled` | 40 | `bg`→`UI/surface/disabled`, `text`→`UI/text/disabled` |

### `Card/Item`

**State**: Default  
Size: `FILL × HUG`  
*pattern library requires Card/Item*

| Variant | Height | Bindings |
|---|---|---|
| `State=Default` | HUG | `bg`→`UI/surface/card`, `border`→`UI/border/subtle`, `text`→`UI/text/primary` |

### `Empty/State`

**State**: Default  
Size: `FILL × HUG`  
*pattern library requires Empty/State*

| Variant | Height | Bindings |
|---|---|---|
| `State=Default` | HUG | `icon`→`UI/icon/subtle`, `text`→`UI/text/secondary` |

### `Input/Text`

**State**: Default, Focused, Filled, Error, Disabled  
Size: `FILL × 38`  
*pattern library requires Input/Text*

| Variant | Height | Bindings |
|---|---|---|
| `State=Default` | 38 | `bg`→`UI/surface/input`, `border`→`UI/border/input`, `placeholder`→`UI/text/placeholder`, `text`→`UI/text/primary` |
| `State=Focused` | 38 | `bg`→`UI/surface/input`, `border`→`UI/border/input-focus`, `placeholder`→`UI/text/placeholder`, `text`→`UI/text/primary` |
| `State=Filled` | 38 | `bg`→`UI/surface/input`, `border`→`UI/border/input`, `placeholder`→`UI/text/placeholder`, `text`→`UI/text/primary` |
| `State=Error` | 38 | `bg`→`UI/surface/input`, `border`→`UI/border/error`, `helper`→`UI/text/error`, `placeholder`→`UI/text/placeholder`, `text`→`UI/text/primary` |
| `State=Disabled` | 38 | `bg`→`UI/surface/disabled`, `border`→`UI/border/input`, `placeholder`→`UI/text/placeholder`, `text`→`UI/text/disabled` |

### Icons

32 icons.

| Semantic | Icon | Library |
|---|---|---|
| `back` | `arrow-left` | MaterialCommunityIcons |
| `calendar` | `calendar-blank-outline` | MaterialCommunityIcons |
| `check` | `check` | MaterialCommunityIcons |
| `chevron-down` | `chevron-down` | MaterialCommunityIcons |
| `chevron-right` | `chevron-right` | MaterialCommunityIcons |
| `clock` | `clock-outline` | MaterialCommunityIcons |
| `close` | `close` | MaterialCommunityIcons |
| `delete` | `trash-can-outline` | MaterialCommunityIcons |
| `edit` | `pencil` | MaterialCommunityIcons |
| `empty` | `inbox-outline` | MaterialCommunityIcons |
| `error` | `alert-circle-outline` | MaterialCommunityIcons |
| `eye` | `eye-outline` | MaterialCommunityIcons |
| `eye-off` | `eye-off-outline` | MaterialCommunityIcons |
| `filter` | `filter-variant` | MaterialCommunityIcons |
| `forward` | `arrow-right` | MaterialCommunityIcons |
| `home` | `home-outline` | MaterialCommunityIcons |
| `info` | `information-outline` | MaterialCommunityIcons |
| `lock` | `lock-outline` | MaterialCommunityIcons |
| `logout` | `logout` | MaterialCommunityIcons |
| `menu` | `menu` | MaterialCommunityIcons |
| `minus` | `minus` | MaterialCommunityIcons |
| `notification` | `bell-outline` | MaterialCommunityIcons |
| `offline` | `wifi-off` | MaterialCommunityIcons |
| `plus` | `plus` | MaterialCommunityIcons |
| `profile` | `account-circle-outline` | MaterialCommunityIcons |
| `refresh` | `refresh` | MaterialCommunityIcons |
| `search` | `magnify` | MaterialCommunityIcons |
| `settings` | `cog-outline` | MaterialCommunityIcons |
| `share` | `share-variant` | MaterialCommunityIcons |
| `sort` | `sort` | MaterialCommunityIcons |
| `success` | `check-circle-outline` | MaterialCommunityIcons |
| `warning` | `alert-outline` | MaterialCommunityIcons |

## 3. Screens

9 screens · 22 variants

Layout is expressed as **declarative constraints**, not absolute rects — these map 1:1 onto Figma auto-layout.

### 🟢 `ArchiveScreen`

**Archive** · module M3 · frame 1440×900 · bg `UI/surface/default`  
Signal: 75% explicit · 3 transitions · 4 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-003-01`, `US-003-02`, `US-003-06`, `US-006-06`

#### Empty
*the collection is legitimately empty — 1 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  EmptyState  [Empty/State]
    size FILL×FILL
    bind icon→UI/icon/subtle, text→UI/text/secondary
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-003-06`

#### Error
*the request or the input failed — 3 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-003-01`, `US-003-02`, `US-003-06`

#### Filled
*the form is valid and the action is enabled — 2 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-003-01`, `US-003-02`

### 🔴 `CodeEntryScreen`

**Code Entry** · module M6 · frame 1440×900 · bg `UI/surface/default`  
Signal: 0% explicit · 0 transitions · 1 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-006-05`

### 🔴 `LoginScreen`

**Login** · module M6 · frame 1440×900 · bg `UI/surface/default`  
Signal: 0% explicit · 0 transitions · 1 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-006-05`

### 🟢 `NoteDetailScreen`

**Note Detail** · module M1 · frame 1440×900 · bg `UI/surface/default`  
Signal: 100% explicit · 1 transitions · 1 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
```
Stories: `US-001-03`

#### Error
*the request or the input failed — 1 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
```
Stories: `US-001-03`

### 🔴 `NoteEditorScreen`

**Note Editor** · module M1 · frame 1440×900 · bg `UI/surface/default`  
Signal: 0% explicit · 5 transitions · 7 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  TextField  [Input/Text]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/input, text→UI/text/primary
    pattern input.text.single
```
Stories: `US-006-02`, `US-006-03`, `US-001-01`, `US-001-02`, `US-001-05`, `US-001-06`, `US-005-02`

#### Empty
*the collection is legitimately empty — 1 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  EmptyState  [Empty/State]
    size FILL×FILL
    bind icon→UI/icon/subtle, text→UI/text/secondary
```
Stories: `US-001-05`

#### Error
*the request or the input failed — 4 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  TextField  [Input/Text · State=Error]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/error, text→UI/text/primary
    pattern input.text.single
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
```
Stories: `US-001-01`, `US-001-02`, `US-001-05`, `US-005-02`

#### Filled
*the form is valid and the action is enabled — 1 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  TextField  [Input/Text]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/input, text→UI/text/primary
    pattern input.text.single
```
Stories: `US-001-01`

### 🔴 `ResidencyReportScreen`

**Residency Report** · module M4 · frame 1440×900 · bg `UI/surface/default`  
Signal: 20% explicit · 5 transitions · 5 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-004-01`, `US-004-02`, `US-004-03`, `US-004-04`, `US-004-05`

#### Error
*the request or the input failed — 3 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
PrimaryAction  [Button/Primary]
  size FILL×36 · sticky-bottom
  bind bg→UI/surface/button/primary, text→UI/text/on-primary
  pattern action.primary
```
Stories: `US-004-01`, `US-004-04`, `US-004-05`

### 🔴 `ScopeDocumentationScreen`

**Scope Documentation** · module M5 · frame 1440×900 · bg `UI/surface/default`  
Signal: 0% explicit · 5 transitions · 5 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
```
Stories: `US-005-01`, `US-005-03`, `US-005-04`, `US-005-05`, `US-005-06`

#### Error
*the request or the input failed — 3 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
```
Stories: `US-005-01`, `US-005-03`, `US-005-06`

### 🟢 `SearchScreen`

**Search** · module M2 · frame 1440×900 · bg `UI/surface/default`  
Signal: 100% explicit · 3 transitions · 4 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  TextField  [Input/Text]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/input, text→UI/text/primary
    pattern input.text.single
```
Stories: `US-006-04`, `US-002-03`, `US-002-04`, `US-002-06`

#### Empty
*the collection is legitimately empty — 2 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  EmptyState  [Empty/State]
    size FILL×FILL
    bind icon→UI/icon/subtle, text→UI/text/secondary
```
Stories: `US-002-03`, `US-002-06`

#### Error
*the request or the input failed — 2 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  TextField  [Input/Text · State=Error]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/error, text→UI/text/primary
    pattern input.text.single
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
```
Stories: `US-002-03`, `US-002-06`

### 🔴 `TagManagementScreen`

**Tag Management** · module M2 · frame 1440×900 · bg `UI/surface/default`  
Signal: 0% explicit · 3 transitions · 4 stories  

#### Default

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  CardList  [Card/Item]
    size FILL×HUG
    bind bg→UI/surface/card, border→UI/border/subtle, text→UI/text/primary
    pattern list.collection
  TextField  [Input/Text]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/input, text→UI/text/primary
    pattern input.text.single
```
Stories: `US-002-01`, `US-002-02`, `US-002-05`, `US-003-05`

#### Error
*the request or the input failed — 2 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  CardList  [Card/Item]
    size FILL×HUG
    bind bg→UI/surface/card, border→UI/border/subtle, text→UI/text/primary
    pattern list.collection
  TextField  [Input/Text · State=Error]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/error, text→UI/text/primary
    pattern input.text.single
  ErrorMessage
    size FILL×FILL
    bind border→UI/border/error, text→UI/text/error
```
Stories: `US-002-01`, `US-002-02`

#### Filled
*the form is valid and the action is enabled — 2 story(ies)*

```
AppBar
  size FILL×48 · sticky-top
  bind bg→UI/surface/default, icon→UI/icon/default, text→UI/text/primary
ContentStack
  size FILL×FILL · scroll vertical · vertical gap 20
  CardList  [Card/Item]
    size FILL×HUG
    bind bg→UI/surface/card, border→UI/border/subtle, text→UI/text/primary
    pattern list.collection
  TextField  [Input/Text]
    size FILL×38
    bind bg→UI/surface/input, border→UI/border/input, text→UI/text/primary
    pattern input.text.single
```
Stories: `US-002-01`, `US-002-05`

## 4. Flow

5 connections. Every source node exists in its variant's module list — an unresolvable one is a CRITICAL finding.

| ID | From | Node | → | To | Trigger | Condition | Covers |
|---|---|---|---|---|---|---|---|
| `C-001` | `ScopeDocumentationScreen` | `—` | → | `ArchiveScreen` | ON_CLICK | all routes are listed | `US-005-01` |
| `C-002` | `ScopeDocumentationScreen` | `—` | → | `SearchScreen` | ON_CLICK | the scope-compliance suite runs | `US-005-04` |
| `C-003` | `TagManagementScreen` | `—` | → | `SearchScreen` | ON_CLICK | client sends a request to add tags to the note | `US-002-01` |
| `C-004` | `TagManagementScreen` | `—` | → | `SearchScreen` | ON_CLICK | client sends a request to remove that tag | `US-002-02` |
| `C-005` | `TagManagementScreen` | `—` | → | `ArchiveScreen` | ON_CLICK | client sends an edit or tag update request for it | `US-003-05` |

## 5. Stories and coverage

36 active stories · 51 requirements · 41 covered · 0 orphans

### Coverage map

| Requirement | Stories |
|---|---|
| `-` | **none** ⚠ |
| `1` | `US-006-05` |
| `Button/Primary` | **none** ⚠ |
| `Button/Primary, Empty/State, Input/Text` | **none** ⚠ |
| `Card/Item` | **none** ⚠ |
| `Empty/State, Input/Text` | **none** ⚠ |
| `US-001-01` | `US-001-01`, `US-001-02`, `US-001-05`, `US-002-03`, `US-003-01`, `US-003-02`, `US-003-06`, `US-005-01` |
| `US-001-02` | `US-001-01`, `US-001-02`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02`, `US-002-03`, `US-003-01` |
| `US-001-03` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02`, `US-002-03` |
| `US-001-04` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-04`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02` |
| `US-001-05` | `US-001-01`, `US-001-05`, `US-003-06`, `US-005-02` |
| `US-001-06` | `US-001-06` |
| `US-002-01` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02`, `US-002-03` |
| `US-002-02` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02`, `US-002-03` |
| `US-002-03` | `US-002-03`, `US-002-04`, `US-002-06`, `US-005-01`, `US-005-04`, `US-005-06`, `US-006-04` |
| `US-002-04` | `US-002-01`, `US-002-02`, `US-002-03`, `US-002-04`, `US-002-05`, `US-002-06`, `US-005-04` |
| `US-002-05` | `US-002-01`, `US-002-05`, `US-003-03` |
| `US-002-06` | `US-002-02`, `US-002-03`, `US-002-04`, `US-002-06` |
| `US-003-01` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02`, `US-002-03` |
| `US-003-02` | `US-003-01`, `US-003-02`, `US-003-04`, `US-003-05`, `US-003-06`, `US-006-06` |
| `US-003-03` | `US-001-04`, `US-002-01`, `US-002-03`, `US-002-04`, `US-003-01`, `US-003-03`, `US-003-04`, `US-003-05` |
| `US-003-04` | `US-001-04`, `US-002-01`, `US-002-03`, `US-002-04`, `US-003-01`, `US-003-02`, `US-003-03`, `US-003-04` |
| `US-003-05` | `US-001-02`, `US-001-06`, `US-003-01`, `US-003-02`, `US-003-05`, `US-003-06`, `US-005-01`, `US-005-02` |
| `US-003-06` | `US-001-05`, `US-002-06`, `US-003-03`, `US-003-06` |
| `US-004-01` | `US-004-01` |
| `US-004-02` | `US-004-02`, `US-004-04` |
| `US-004-03` | `US-003-05`, `US-004-03` |
| `US-004-04` | `US-004-02`, `US-004-04` |
| `US-004-05` | `US-004-05`, `US-004-06` |
| `US-004-06` | `US-004-06` |
| `US-005-01` | `US-002-03`, `US-003-01`, `US-003-05`, `US-003-06`, `US-005-01`, `US-005-02`, `US-005-04` |
| `US-005-02` | `US-005-02` |
| `US-005-03` | `US-005-03` |
| `US-005-04` | `US-004-02`, `US-004-06`, `US-005-01`, `US-005-02`, `US-005-03`, `US-005-04` |
| `US-005-05` | `US-005-03`, `US-005-05`, `US-005-06` |
| `US-005-06` | `US-005-06` |
| `US-006-01` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-04`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02` |
| `US-006-02` | `US-001-01`, `US-005-01`, `US-005-02`, `US-006-02`, `US-006-03` |
| `US-006-03` | `US-001-01`, `US-001-02`, `US-002-03`, `US-005-02`, `US-006-01`, `US-006-02`, `US-006-03` |
| `US-006-04` | `US-001-01`, `US-002-03`, `US-002-04`, `US-006-02`, `US-006-03`, `US-006-04` |
| `US-006-05` | `US-006-05` |
| `US-006-06` | `US-003-01`, `US-003-02`, `US-003-06`, `US-005-01`, `US-006-01`, `US-006-06` |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-notes_api-agent-ba` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-04`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-03` |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-notes_api-agent-developer` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-04`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-03` |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-notes_api-agent-qa` | `US-001-01`, `US-001-02`, `US-001-03`, `US-001-04`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-03` |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-web_app-agent-ba` | **none** ⚠ |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-web_app-agent-design` | **none** ⚠ |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-web_app-agent-developer` | **none** ⚠ |
| `f6baaa15-ffcc-4594-b93d-33cd249e7174-web_app-agent-qa` | **none** ⚠ |
| `notes_api` | **none** ⚠ |
| `web_app` | `US-006-05`, `US-006-05` |

## 6. Figma file structure

| Page | Kind | Contains | Note |
|---|---|---|---|
| **Cover** | cover | — | Project, platform, ruleset version, and the date this spec was generated. |
| **Foundations** | foundations | Primitives, UI tokens, Typography, Spacing, Radius, Elevation | Variable collections. Nothing here is drawn by hand. |
| **Components** | components | Button/Primary, Card/Item, Empty/State, Input/Text | One component set per frame, variants as a grid. |
| **Screens — M1** | screens | NoteDetailScreen, NoteEditorScreen | One frame per variant, laid out left to right in flow order. |
| **Screens — M2** | screens | SearchScreen, TagManagementScreen | One frame per variant, laid out left to right in flow order. |
| **Screens — M3** | screens | ArchiveScreen | One frame per variant, laid out left to right in flow order. |
| **Screens — M4** | screens | ResidencyReportScreen | One frame per variant, laid out left to right in flow order. |
| **Screens — M5** | screens | ScopeDocumentationScreen | One frame per variant, laid out left to right in flow order. |
| **Screens — M6** | screens | CodeEntryScreen, LoginScreen | One frame per variant, laid out left to right in flow order. |
| **Flow** | flow | ArchiveScreen, CodeEntryScreen, LoginScreen, NoteDetailScreen, NoteEditorScreen, ResidencyReportScreen, … (9) | Prototype connections only — no new frames on this page. |
