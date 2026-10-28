---
id: agent-design/web_app/output/FIGMA-BUILD.md
type: figma_build
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.1.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/FIGMA-BUILD.md
depends_on: []
commit_kind: append
confidence: agent
run_id: 8b6f351e-674c-5e4a-a81d-e4459253444e
okf_id: web_app:design-figma-build
---

# a1 — Figma Build Instructions

**Generated:** 2026-10-28T18:11:30Z · run 17 · version `v1_initial`  
**Ruleset:** 1 · **Inputs:** `sha256:b82c1cc58bf6…`  
**Platform:** web · WCAG-2.1-AA  

> A **view over F1–F6**, written for a builder rather than a reader. It
> contains no fact absent from those files. Do not edit it — edit the
> inputs and regenerate. Where the spec is silent, §9 says so; nothing
> below is inferred to fill a gap.

## 0. Build order

Sections are the execution order, and the dependencies are real:
`UI` aliases `Primitives` by name, `Status Badges` aliases `UI`, an
instance needs its component set, and a frame needs its page.

1. Variable collections
2. Effect styles
3. Text styles
4. Components
5. Icons
6. Pages
7. Screens
8. Prototype connections
9. Blocked — needs a human

---

## 1. Variable collections

### `Primitives`

Modes: `Value` · type `COLOR`

| Variable | Value |
|---|---|
| `Primary/50` | `#EEF6FF` |
| `Primary/100` | `#D9E8FF` |
| `Primary/200` | `#B7D5FF` |
| `Primary/300` | `#85B7FF` |
| `Primary/400` | `#4D8FF7` |
| `Primary/500` | `#2563EB` |
| `Primary/600` | `#1D4AE5` |
| `Primary/700` | `#1C3BD3` |
| `Primary/800` | `#1D37B1` |
| `Primary/900` | `#1F3790` |
| `Error/50` | `#FFF2F1` |
| `Error/100` | `#FFE1DE` |
| `Error/200` | `#FEC4C0` |
| `Error/300` | `#F89A92` |
| `Error/400` | `#EC6059` |
| `Error/500` | `#DC2626` |
| `Error/600` | `#CE0007` |
| `Error/700` | `#B2000B` |
| `Error/800` | `#981116` |
| `Error/900` | `#831A1C` |
| `Success/50` | `#F0FDF4` |
| `Success/100` | `#DAF9E3` |
| `Success/200` | `#B6EEC6` |
| `Success/300` | `#81DF9E` |
| `Success/400` | `#48C570` |
| `Success/500` | `#16A34A` |
| `Success/600` | `#148B3E` |
| `Success/700` | `#197237` |
| `Success/800` | `#1D5F33` |
| `Success/900` | `#1D522F` |
| `Warning/50` | `#FFFBEB` |
| `Warning/100` | `#FEF3C7` |
| `Warning/200` | `#FDE68A` |
| `Warning/300` | `#FCD34D` |
| `Warning/400` | `#FBBF24` |
| `Warning/500` | `#F59E0B` |
| `Warning/600` | `#D97706` |
| `Warning/700` | `#B45309` |
| `Warning/800` | `#92400E` |
| `Warning/900` | `#78350F` |
| `Info/50` | `#ECFEFF` |
| `Info/100` | `#CFFAFE` |
| `Info/200` | `#A5F3FC` |
| `Info/300` | `#67E8F9` |
| `Info/400` | `#22D3EE` |
| `Info/500` | `#06B6D4` |
| `Info/600` | `#0891B2` |
| `Info/700` | `#0E7490` |
| `Info/800` | `#155E75` |
| `Info/900` | `#164E63` |
| `Neutral/0` | `#FFFFFF` |
| `Neutral/50` | `#F9FAFB` |
| `Neutral/100` | `#F3F4F6` |
| `Neutral/200` | `#E5E7EB` |
| `Neutral/300` | `#D1D5DB` |
| `Neutral/400` | `#9CA3AF` |
| `Neutral/500` | `#6B7280` |
| `Neutral/600` | `#4B5563` |
| `Neutral/700` | `#374151` |
| `Neutral/800` | `#1F2937` |
| `Neutral/900` | `#111827` |
| `Dark/50` | `#F8FAFC` |
| `Dark/100` | `#F1F5F9` |
| `Dark/200` | `#E2E8F0` |
| `Dark/300` | `#CBD5E1` |
| `Dark/400` | `#94A3B8` |
| `Dark/500` | `#64748B` |
| `Dark/600` | `#475569` |
| `Dark/700` | `#334155` |
| `Dark/800` | `#1E293B` |
| `Dark/900` | `#0F172A` |

### `UI`

Modes: `Light`, `Dark` · type `COLOR`

| Variable | Light | Dark | Resolved |
|---|---|---|---|
| `UI/border/default` | `Neutral/200` | `Dark/700` | #E5E7EB / #334155 |
| `UI/border/disabled` | `Neutral/200` | `Dark/700` | #E5E7EB / #334155 |
| `UI/border/error` | `Error/500` | `Error/400` | #DC2626 / #EC6059 |
| `UI/border/focus` | `Primary/500` | `Primary/400` | #2563EB / #4D8FF7 |
| `UI/border/input` | `Neutral/200` | `Dark/700` | #E5E7EB / #334155 |
| `UI/border/input-focus` | `Primary/500` | `Primary/400` | #2563EB / #4D8FF7 |
| `UI/border/strong` | `Neutral/300` | `Dark/600` | #D1D5DB / #475569 |
| `UI/border/subtle` | `Neutral/100` | `Dark/800` | #F3F4F6 / #1E293B |
| `UI/border/success` | `Success/500` | `Success/400` | #16A34A / #48C570 |
| `UI/border/warning` | `Warning/500` | `Warning/400` | #F59E0B / #FBBF24 |
| `UI/color/black` | `Neutral/900` | `Dark/100` | #111827 / #F1F5F9 |
| `UI/color/disabled` | `Neutral/100` | `Dark/800` | #F3F4F6 / #1E293B |
| `UI/color/error` | `Error/500` | `Error/400` | #DC2626 / #EC6059 |
| `UI/color/error-subtle` | `Error/50` | `Error/800` | #FFF2F1 / #981116 |
| `UI/color/hover-destructive` | `Error/600` | `Error/700` | #CE0007 / #B2000B |
| `UI/color/hover-ghost` | `Primary/50` | `Primary/900` | #EEF6FF / #1F3790 |
| `UI/color/hover-primary` | `Primary/600` | `Primary/700` | #1D4AE5 / #1C3BD3 |
| `UI/color/hover-secondary` | `Neutral/100` | `Dark/700` | #F3F4F6 / #334155 |
| `UI/color/hover-success` | `Success/600` | `Success/700` | #148B3E / #197237 |
| `UI/color/info` | `Info/500` | `Info/400` | #06B6D4 / #22D3EE |
| `UI/color/info-subtle` | `Info/50` | `Info/800` | #ECFEFF / #155E75 |
| `UI/color/overlay` | `Neutral/900` | `Neutral/900` | #111827 / #111827 |
| `UI/color/primary` | `Primary/500` | `Primary/400` | #2563EB / #4D8FF7 |
| `UI/color/primary-hover` | `Primary/600` | `Primary/300` | #1D4AE5 / #85B7FF |
| `UI/color/primary-subtle` | `Primary/50` | `Primary/900` | #EEF6FF / #1F3790 |
| `UI/color/success` | `Success/500` | `Success/400` | #16A34A / #48C570 |
| `UI/color/success-subtle` | `Success/50` | `Success/800` | #F0FDF4 / #1D5F33 |
| `UI/color/warning` | `Warning/500` | `Warning/400` | #F59E0B / #FBBF24 |
| `UI/color/warning-subtle` | `Warning/50` | `Warning/800` | #FFFBEB / #92400E |
| `UI/color/white` | `Neutral/0` | `Dark/900` | #FFFFFF / #0F172A |
| `UI/icon/active` | `Primary/500` | `Primary/400` | #2563EB / #4D8FF7 |
| `UI/icon/default` | `Neutral/500` | `Dark/400` | #6B7280 / #94A3B8 |
| `UI/icon/disabled` | `Neutral/300` | `Dark/600` | #D1D5DB / #475569 |
| `UI/icon/error` | `Error/500` | `Error/400` | #DC2626 / #EC6059 |
| `UI/icon/info` | `Info/500` | `Info/400` | #06B6D4 / #22D3EE |
| `UI/icon/inverse` | `Neutral/0` | `Dark/900` | #FFFFFF / #0F172A |
| `UI/icon/on-primary` | `Neutral/0` | `Neutral/900` | #FFFFFF / #111827 |
| `UI/icon/subtle` | `Neutral/400` | `Dark/500` | #9CA3AF / #64748B |
| `UI/icon/success` | `Success/500` | `Success/400` | #16A34A / #48C570 |
| `UI/icon/warning` | `Warning/500` | `Warning/400` | #F59E0B / #FBBF24 |
| `UI/surface/button/destructive` | `Error/500` | `Error/400` | #DC2626 / #EC6059 |
| `UI/surface/button/ghost` | `Neutral/0` | `Dark/900` | #FFFFFF / #0F172A |
| `UI/surface/button/primary` | `Primary/600` | `Primary/400` | #1D4AE5 / #4D8FF7 |
| `UI/surface/button/primary-hover` | `Primary/700` | `Primary/300` | #1C3BD3 / #85B7FF |
| `UI/surface/button/secondary` | `Neutral/100` | `Dark/700` | #F3F4F6 / #334155 |
| `UI/surface/button/success` | `Success/500` | `Success/400` | #16A34A / #48C570 |
| `UI/surface/card` | `Neutral/0` | `Dark/800` | #FFFFFF / #1E293B |
| `UI/surface/default` | `Neutral/0` | `Dark/900` | #FFFFFF / #0F172A |
| `UI/surface/disabled` | `Neutral/100` | `Dark/800` | #F3F4F6 / #1E293B |
| `UI/surface/input` | `Neutral/50` | `Dark/800` | #F9FAFB / #1E293B |
| `UI/surface/input-focus` | `Neutral/0` | `Dark/900` | #FFFFFF / #0F172A |
| `UI/surface/overlay` | `Neutral/900` | `Neutral/900` | #111827 / #111827 |
| `UI/surface/secondary` | `Neutral/50` | `Dark/800` | #F9FAFB / #1E293B |
| `UI/surface/tertiary` | `Neutral/100` | `Dark/700` | #F3F4F6 / #334155 |
| `UI/surface/toast/error` | `Error/50` | `Error/900` | #FFF2F1 / #831A1C |
| `UI/surface/toast/info` | `Info/50` | `Info/900` | #ECFEFF / #164E63 |
| `UI/surface/toast/success` | `Success/50` | `Success/900` | #F0FDF4 / #1D522F |
| `UI/surface/toast/warning` | `Warning/50` | `Warning/900` | #FFFBEB / #78350F |
| `UI/text/disabled` | `Neutral/300` | `Dark/700` | #D1D5DB / #334155 |
| `UI/text/error` | `Error/700` | `Error/400` | #B2000B / #EC6059 |
| `UI/text/info` | `Info/700` | `Info/400` | #0E7490 / #22D3EE |
| `UI/text/inverse` | `Neutral/0` | `Dark/900` | #FFFFFF / #0F172A |
| `UI/text/link` | `Primary/600` | `Primary/400` | #1D4AE5 / #4D8FF7 |
| `UI/text/link-hover` | `Primary/700` | `Primary/300` | #1C3BD3 / #85B7FF |
| `UI/text/on-primary` | `Neutral/0` | `Neutral/900` | #FFFFFF / #111827 |
| `UI/text/placeholder` | `Neutral/500` | `Dark/400` | #6B7280 / #94A3B8 |
| `UI/text/primary` | `Neutral/900` | `Dark/100` | #111827 / #F1F5F9 |
| `UI/text/secondary` | `Neutral/600` | `Dark/400` | #4B5563 / #94A3B8 |
| `UI/text/success` | `Success/700` | `Success/400` | #197237 / #48C570 |
| `UI/text/tertiary` | `Neutral/500` | `Dark/400` | #6B7280 / #94A3B8 |
| `UI/text/warning` | `Warning/700` | `Warning/400` | #B45309 / #FBBF24 |

### `Spacing`

Modes: `Value` · type `FLOAT`

| Variable | Value |
|---|---|
| `Spacing/0` | 0 |
| `Spacing/1` | 4 |
| `Spacing/2` | 8 |
| `Spacing/3` | 10 |
| `Spacing/4` | 14 |
| `Spacing/5` | 18 |
| `Spacing/6` | 20 |
| `Spacing/7` | 24 |
| `Spacing/8` | 28 |
| `Spacing/10` | 36 |
| `Spacing/12` | 42 |
| `Spacing/14` | 48 |
| `Spacing/16` | 56 |
| `Spacing/20` | 70 |
| `Spacing/24` | 84 |
| `Spacing/32` | 112 |

### `Border Radius`

Modes: `Value` · type `FLOAT`

| Variable | Value |
|---|---|
| `Border Radius/None` | 0 |
| `Border Radius/XS` | 0 |
| `Border Radius/SM` | 2 |
| `Border Radius/MD` | 2 |
| `Border Radius/LG` | 4 |
| `Border Radius/XL` | 4 |
| `Border Radius/2XL` | 6 |
| `Border Radius/3XL` | 8 |
| `Border Radius/Full` | 999 |

### `Typography`

Modes: `Value` · type `FLOAT`

| Variable | Value | Type |
|---|---|---|
| `Typography/Font Family/Base` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | STRING |
| `Typography/Font Weight/Regular` | 400 | STRING |
| `Typography/Font Weight/Medium` | 500 | STRING |
| `Typography/Font Weight/Semi Bold` | 600 | STRING |
| `Typography/Font Weight/Bold` | 700 | STRING |
| `Typography/Font Size/XS` | 10 | FLOAT |
| `Typography/Font Size/SM` | 12 | FLOAT |
| `Typography/Font Size/MD` | 14 | FLOAT |
| `Typography/Font Size/Base` | 16 | FLOAT |
| `Typography/Font Size/LG` | 18 | FLOAT |
| `Typography/Font Size/XL` | 20 | FLOAT |
| `Typography/Font Size/2XL` | 24 | FLOAT |
| `Typography/Font Size/3XL` | 28 | FLOAT |
| `Typography/Font Size/4XL` | 32 | FLOAT |
| `Typography/Line Height/Tight` | 120% | STRING |
| `Typography/Line Height/Snug` | 138% | STRING |
| `Typography/Line Height/Normal` | 150% | STRING |
| `Typography/Line Height/Relaxed` | 163% | STRING |

## 2. Effect styles

Drop shadows. Figma has no shadow variable type, so these are **effect styles**, created separately from §1.

| Style | Y | Blur | Spread | Colour | Opacity |
|---|---|---|---|---|---|
| `Elevation/SM` | 1 | 2 | 0 | `#000000` | 0.05 |
| `Elevation/MD` | 4 | 8 | 0 | `#000000` | 0.08 |
| `Elevation/LG` | 10 | 20 | 0 | `#000000` | 0.1 |
| `Elevation/XL` | 20 | 40 | 0 | `#000000` | 0.12 |
| `Elevation/Scrim` | 0 | 0 | 0 | `#000000` | 0.4 |
| `Elevation/Sheet` | 0 | 16 | 0 | `#000000` | 0.16 |

## 3. Text styles

Named scales. Every `scale` named in §4 and §7 is one of these.

| Style | Family | Size | Weight | Line height |
|---|---|---|---|---|
| `heading-xl` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 32px | 700 | 38px |
| `heading-lg` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 24px | 700 | 29px |
| `heading-md` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 20px | 600 | 28px |
| `heading-sm` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 16px | 600 | 22px |
| `body-lg` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 16px | 400 | 24px |
| `body-md` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 14px | 400 | 21px |
| `body-sm` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 12px | 400 | 18px |
| `label` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 14px | 500 | 21px |
| `button` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 16px | 600 | 24px |
| `mono` | Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. | 14px | 500 | 21px |

## 4. Components

3 component set(s). Each is a Figma **component set** whose variant properties are the axes below.

Layers are what the set's own bindings require: a `text` binding needs a text layer to colour, and that layer is the one §7 addresses by name. Create every layer listed — an instance override naming a layer that does not exist is the one failure this document exists to prevent.

### `Button/Primary`

Role `action` · 12 variant(s)

- **Size** = `Small` | `Medium` | `Large`
- **State** = `Default` | `Pressed` | `Loading` | `Disabled`

**Layers**

| Layer | Node | Bound to | Scale |
|---|---|---|---|
| `_Label` | TEXT | `UI/text/on-primary` | `button` |

| Variant | Size | fills | strokes | Radius |
|---|---|---|---|---|
| `Size=Small, State=Default` | FILL×32 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Small, State=Pressed` | FILL×32 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Small, State=Loading` | FILL×32 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Small, State=Disabled` | FILL×32 | `UI/surface/disabled` | `—` | `Border Radius/MD` |
| `Size=Medium, State=Default` | FILL×36 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Medium, State=Pressed` | FILL×36 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Medium, State=Loading` | FILL×36 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Medium, State=Disabled` | FILL×36 | `UI/surface/disabled` | `—` | `Border Radius/MD` |
| `Size=Large, State=Default` | FILL×40 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Large, State=Pressed` | FILL×40 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Large, State=Loading` | FILL×40 | `UI/surface/button/primary` | `—` | `Border Radius/MD` |
| `Size=Large, State=Disabled` | FILL×40 | `UI/surface/disabled` | `—` | `Border Radius/MD` |

### `Empty/State`

Role `content` · 1 variant(s)

- **State** = `Default`

**Layers**

| Layer | Node | Bound to | Scale |
|---|---|---|---|
| `_Label` | TEXT | `UI/text/secondary` | — |
| `_Icon` | VECTOR | `UI/icon/subtle` | — |

| Variant | Size | fills | strokes | Radius |
|---|---|---|---|---|
| `State=Default` | FILL×HUG | `—` | `—` | `Border Radius/MD` |

### `Input/Text`

Role `entry` · 5 variant(s)

- **State** = `Default` | `Focused` | `Filled` | `Error` | `Disabled`

**Layers**

| Layer | Node | Bound to | Scale |
|---|---|---|---|
| `_Label` | TEXT | `UI/text/primary` | — |
| `_Placeholder` | TEXT | `UI/text/placeholder` | — |

| Variant | Size | fills | strokes | Radius |
|---|---|---|---|---|
| `State=Default` | FILL×38 | `UI/surface/input` | `UI/border/input` | `Border Radius/MD` |
| `State=Focused` | FILL×38 | `UI/surface/input` | `UI/border/input-focus` | `Border Radius/MD` |
| `State=Filled` | FILL×38 | `UI/surface/input` | `UI/border/input` | `Border Radius/MD` |
| `State=Error` | FILL×38 | `UI/surface/input` | `UI/border/error` | `Border Radius/MD` |
| `State=Disabled` | FILL×38 | `UI/surface/disabled` | `UI/border/input` | `Border Radius/MD` |

## 5. Icons

32 icon(s). The Figma component name is the key in TitleCase under `Icon/`; the glyph is the named library icon.

| Figma component | Library icon | Library |
|---|---|---|
| `Icon/Back` | `arrow-left` | MaterialCommunityIcons |
| `Icon/Calendar` | `calendar-blank-outline` | MaterialCommunityIcons |
| `Icon/Check` | `check` | MaterialCommunityIcons |
| `Icon/ChevronDown` | `chevron-down` | MaterialCommunityIcons |
| `Icon/ChevronRight` | `chevron-right` | MaterialCommunityIcons |
| `Icon/Clock` | `clock-outline` | MaterialCommunityIcons |
| `Icon/Close` | `close` | MaterialCommunityIcons |
| `Icon/Delete` | `trash-can-outline` | MaterialCommunityIcons |
| `Icon/Edit` | `pencil` | MaterialCommunityIcons |
| `Icon/Empty` | `inbox-outline` | MaterialCommunityIcons |
| `Icon/Error` | `alert-circle-outline` | MaterialCommunityIcons |
| `Icon/Eye` | `eye-outline` | MaterialCommunityIcons |
| `Icon/EyeOff` | `eye-off-outline` | MaterialCommunityIcons |
| `Icon/Filter` | `filter-variant` | MaterialCommunityIcons |
| `Icon/Forward` | `arrow-right` | MaterialCommunityIcons |
| `Icon/Home` | `home-outline` | MaterialCommunityIcons |
| `Icon/Info` | `information-outline` | MaterialCommunityIcons |
| `Icon/Lock` | `lock-outline` | MaterialCommunityIcons |
| `Icon/Logout` | `logout` | MaterialCommunityIcons |
| `Icon/Menu` | `menu` | MaterialCommunityIcons |
| `Icon/Minus` | `minus` | MaterialCommunityIcons |
| `Icon/Notification` | `bell-outline` | MaterialCommunityIcons |
| `Icon/Offline` | `wifi-off` | MaterialCommunityIcons |
| `Icon/Plus` | `plus` | MaterialCommunityIcons |
| `Icon/Profile` | `account-circle-outline` | MaterialCommunityIcons |
| `Icon/Refresh` | `refresh` | MaterialCommunityIcons |
| `Icon/Search` | `magnify` | MaterialCommunityIcons |
| `Icon/Settings` | `cog-outline` | MaterialCommunityIcons |
| `Icon/Share` | `share-variant` | MaterialCommunityIcons |
| `Icon/Sort` | `sort` | MaterialCommunityIcons |
| `Icon/Success` | `check-circle-outline` | MaterialCommunityIcons |
| `Icon/Warning` | `alert-outline` | MaterialCommunityIcons |

## 6. Pages

Create these pages in order before any frame.

| Page | Kind | Contains |
|---|---|---|
| `Cover` | cover | — |
| `Foundations` | foundations | `Primitives`, `UI tokens`, `Typography`, `Spacing`, `Radius`, `Elevation` |
| `Components` | components | `Button/Primary`, `Empty/State`, `Input/Text` |
| `Screens — M1` | screens | `NoteEditorScreen` |
| `Screens — M2` | screens | `SearchScreen` |
| `Screens — M3` | screens | `ArchivedNotesScreen` |
| `Screens — M4` | screens | `ResidencyReportScreen` |
| `Screens — M5` | screens | `ApiScopeDocsScreen` |
| `Screens — M6` | screens | `CodeEntryScreen`, `LoginScreen` |
| `Flow` | flow | `ApiScopeDocsScreen`, `ArchivedNotesScreen`, `CodeEntryScreen`, `LoginScreen`, `NoteEditorScreen`, `ResidencyReportScreen`, `SearchScreen` |

## 7. Screens

7 screen(s) · 17 variant(s). Coordinates are absolute within the frame and resolve from F3's constraints — every one placed without inventing a value.

### `ApiScopeDocsScreen` — `Default`

Page `Screens — M5` · canvas x=0 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
```

**Build prompt.** Create a 1440×900 frame named `ApiScopeDocsScreen/Default` at canvas (0, 0) on page `Screens — M5`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-005-01`, `US-005-02`, `US-005-03`, `US-005-04`, `US-005-05`, `US-005-06`

### `ApiScopeDocsScreen` — `Error`

Page `Screens — M5` · canvas x=1520 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the request or the input failed — 4 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×880 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  ErrorMessage           14,62    1412×852 normal
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/error`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `ApiScopeDocsScreen/Error` at canvas (1520, 0) on page `Screens — M5`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-005-01`, `US-005-02`, `US-005-03`, `US-005-06`

### `ArchivedNotesScreen` — `Default`

Page `Screens — M3` · canvas x=0 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
```

**Build prompt.** Create a 1440×900 frame named `ArchivedNotesScreen/Default` at canvas (0, 0) on page `Screens — M3`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-003-01`, `US-003-02`, `US-003-03`, `US-003-06`

### `ArchivedNotesScreen` — `Empty`

Page `Screens — M3` · canvas x=1520 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the collection is legitimately empty — 2 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×880 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  EmptyState             14,62    1412×852 normal
    instance of `Empty/State` (State=Default)
    _Label.fills     → `UI/text/secondary`  (TEXT)
    _Icon.fills      → `UI/icon/subtle`  (VECTOR)
```

**Build prompt.** Create a 1440×900 frame named `ArchivedNotesScreen/Empty` at canvas (1520, 0) on page `Screens — M3`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-003-03`, `US-003-06`

### `ArchivedNotesScreen` — `Error`

Page `Screens — M3` · canvas x=3040 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the request or the input failed — 4 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×880 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  ErrorMessage           14,62    1412×852 normal
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/error`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `ArchivedNotesScreen/Error` at canvas (3040, 0) on page `Screens — M3`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-003-01`, `US-003-02`, `US-003-03`, `US-003-06`

### `ArchivedNotesScreen` — `Filled`

Page `Screens — M3` · canvas x=4560 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the form is valid and the action is enabled — 2 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
```

**Build prompt.** Create a 1440×900 frame named `ArchivedNotesScreen/Filled` at canvas (4560, 0) on page `Screens — M3`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-003-01`, `US-003-02`

### `CodeEntryScreen` — `Default`

Page `Screens — M6` · canvas x=0 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
PrimaryAction          14,850   1412×36 sticky-bottom
  instance of `Button/Primary` (Size=Medium, State=Default)  (resting state)
  margin 0,14,14,14
  fills            → `UI/surface/button/primary`
  _Label.fills     → `UI/text/on-primary`  (TEXT)
  set text _Label = "Label"  ⚠ placeholder
```

**Build prompt.** Create a 1440×900 frame named `CodeEntryScreen/Default` at canvas (0, 0) on page `Screens — M6`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Pin `PrimaryAction` to the bottom, above the safe area. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-006-05`

### `LoginScreen` — `Default`

Page `Screens — M6` · canvas x=1520 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
PrimaryAction          14,850   1412×36 sticky-bottom
  instance of `Button/Primary` (Size=Medium, State=Default)  (resting state)
  margin 0,14,14,14
  fills            → `UI/surface/button/primary`
  _Label.fills     → `UI/text/on-primary`  (TEXT)
  set text _Label = "Label"  ⚠ placeholder
```

**Build prompt.** Create a 1440×900 frame named `LoginScreen/Default` at canvas (1520, 0) on page `Screens — M6`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Pin `PrimaryAction` to the bottom, above the safe area. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-006-05`

### `NoteEditorScreen` — `Default`

Page `Screens — M1` · canvas x=0 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×66 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  TextField              14,62    1412×38 normal
    instance of `Input/Text` (State=Default)  (resting state)
    radius  → `Border Radius/MD`
    fills            → `UI/surface/input`
    strokes          → `UI/border/input`
    _Label.fills     → `UI/text/primary`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `NoteEditorScreen/Default` at canvas (0, 0) on page `Screens — M1`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-001-01`, `US-001-02`, `US-001-03`, `US-001-05`, `US-001-06`, `US-002-01`, `US-002-02`, `US-003-05`, `US-006-02`, `US-006-03`

### `NoteEditorScreen` — `Empty`

Page `Screens — M1` · canvas x=1520 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the collection is legitimately empty — 1 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×880 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  EmptyState             14,62    1412×852 normal
    instance of `Empty/State` (State=Default)
    _Label.fills     → `UI/text/secondary`  (TEXT)
    _Icon.fills      → `UI/icon/subtle`  (VECTOR)
```

**Build prompt.** Create a 1440×900 frame named `NoteEditorScreen/Empty` at canvas (1520, 0) on page `Screens — M1`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-001-05`

### `NoteEditorScreen` — `Error`

Page `Screens — M1` · canvas x=3040 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the request or the input failed — 6 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×938 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  TextField              14,62    1412×38 normal
    instance of `Input/Text` (State=Error)
    radius  → `Border Radius/MD`
    fills            → `UI/surface/input`
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/primary`  (TEXT)
  ErrorMessage           14,120   1412×852 normal
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/error`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `NoteEditorScreen/Error` at canvas (3040, 0) on page `Screens — M1`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-001-01`, `US-001-02`, `US-001-03`, `US-001-05`, `US-002-01`, `US-002-02`

### `NoteEditorScreen` — `Filled`

Page `Screens — M1` · canvas x=4560 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the form is valid and the action is enabled — 2 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×66 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  TextField              14,62    1412×38 normal
    instance of `Input/Text` (State=Default)  (resting state)
    radius  → `Border Radius/MD`
    fills            → `UI/surface/input`
    strokes          → `UI/border/input`
    _Label.fills     → `UI/text/primary`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `NoteEditorScreen/Filled` at canvas (4560, 0) on page `Screens — M1`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-001-01`, `US-002-01`

### `ResidencyReportScreen` — `Default`

Page `Screens — M4` · canvas x=0 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
```

**Build prompt.** Create a 1440×900 frame named `ResidencyReportScreen/Default` at canvas (0, 0) on page `Screens — M4`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-004-01`, `US-004-02`, `US-004-03`, `US-004-04`, `US-004-05`

### `ResidencyReportScreen` — `Error`

Page `Screens — M4` · canvas x=1520 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the request or the input failed — 3 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×880 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  ErrorMessage           14,62    1412×852 normal
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/error`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `ResidencyReportScreen/Error` at canvas (1520, 0) on page `Screens — M4`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-004-01`, `US-004-04`, `US-004-05`

### `SearchScreen` — `Default`

Page `Screens — M2` · canvas x=0 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×66 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  TextField              14,62    1412×38 normal
    instance of `Input/Text` (State=Default)  (resting state)
    radius  → `Border Radius/MD`
    fills            → `UI/surface/input`
    strokes          → `UI/border/input`
    _Label.fills     → `UI/text/primary`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `SearchScreen/Default` at canvas (0, 0) on page `Screens — M2`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-002-03`, `US-002-04`, `US-002-06`, `US-006-04`

### `SearchScreen` — `Empty`

Page `Screens — M2` · canvas x=1520 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the collection is legitimately empty — 2 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×880 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  EmptyState             14,62    1412×852 normal
    instance of `Empty/State` (State=Default)
    _Label.fills     → `UI/text/secondary`  (TEXT)
    _Icon.fills      → `UI/icon/subtle`  (VECTOR)
```

**Build prompt.** Create a 1440×900 frame named `SearchScreen/Empty` at canvas (1520, 0) on page `Screens — M2`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-002-03`, `US-002-06`

### `SearchScreen` — `Error`

Page `Screens — M2` · canvas x=3040 y=0 · frame 1440×900 · safe area 0/0  
fills → `UI/surface/default` · auto-layout VERTICAL, gap 0

_the request or the input failed — 2 story(ies)_

```
AppBar                 0,0      1440×48 sticky-top
  fills            → `UI/surface/default`
  _Header.fills    → `UI/text/primary`  (TEXT, scale heading-md)
  _Icon.fills      → `UI/icon/default`  (VECTOR)
  set text _Header = "Header"  ⚠ placeholder
ContentStack           0,48     1440×938 normal · scroll vertical
  auto-layout VERTICAL · gap 20 · padding 14,14,14,14 · align START
  TextField              14,62    1412×38 normal
    instance of `Input/Text` (State=Error)
    radius  → `Border Radius/MD`
    fills            → `UI/surface/input`
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/primary`  (TEXT)
  ErrorMessage           14,120   1412×852 normal
    strokes          → `UI/border/error`
    _Label.fills     → `UI/text/error`  (TEXT)
```

**Build prompt.** Create a 1440×900 frame named `SearchScreen/Error` at canvas (3040, 0) on page `Screens — M2`, fill bound to `UI/surface/default`, auto-layout VERTICAL. Safe area: 0px top, 0px bottom. Pin `AppBar` to the top, below the safe area. Place `ContentStack` in the flowing band between the pinned bands. Sizes, offsets and bindings are exactly as listed above.

Covers: `US-002-03`, `US-002-06`

## 8. Prototype connections

2 connection(s) · 2 exact duplicate(s) dropped.

| ID | From | Source | To | Trigger |
|---|---|---|---|---|
| `C-001` | `ApiScopeDocsScreen/Default` | the frame | `SearchScreen/Default` | ON_CLICK |
| `C-003` | `NoteEditorScreen/Default` | the frame | `SearchScreen/Default` | ON_CLICK |

## 9. Blocked — needs a human

> 5 instance(s) took the resting state `State=Default` because the screen does not say which state it is in. Marked `(resting state)` in §7.

21 item(s). Each is a fact F1–F6 do not
carry. Build the rest; these need an input change, landed with
`python apply/apply_patch.py <patch> --approved-by <you>`.

| Kind | Subject | Why |
|---|---|---|
| `placeholder_copy` | `ApiScopeDocsScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ApiScopeDocsScreen/Error · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ArchivedNotesScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ArchivedNotesScreen/Empty · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ArchivedNotesScreen/Error · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ArchivedNotesScreen/Filled · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `CodeEntryScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `CodeEntryScreen/Default · _Label` | the string is still the slot name ("Label") |
| `placeholder_copy` | `LoginScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `LoginScreen/Default · _Label` | the string is still the slot name ("Label") |
| `placeholder_copy` | `NoteEditorScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `NoteEditorScreen/Empty · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `NoteEditorScreen/Error · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `NoteEditorScreen/Filled · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ResidencyReportScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `ResidencyReportScreen/Error · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `SearchScreen/Default · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `SearchScreen/Empty · _Header` | the string is still the slot name ("Header") |
| `placeholder_copy` | `SearchScreen/Error · _Header` | the string is still the slot name ("Header") |
| `frame_level_reaction` | `C-001` | no source node in F5 — the reaction attaches to the whole frame rather than an element |
| `frame_level_reaction` | `C-003` | no source node in F5 — the reaction attaches to the whole frame rather than an element |
