---
id: agent-design/web_app/output/F1-variables.yaml
type: design_tokens
project: f6baaa15-ffcc-4594-b93d-33cd249e7174
version: 0.3.0
author: acd154e9-7d7c-493d-b538-c1e75f4fcbbe
produces: agent-design/web_app/output/F1-variables.yaml
depends_on: []
commit_kind: fork
confidence: agent
run_id: 30365a29-bc04-5530-863e-0419f2b194cd
okf_id: web_app:design-f1-variables
---

schemaVersion: 1
generated_at: '2026-10-29T06:32:23Z'
run: 18
version: v1_initial
ruleset_version: 1
inputs_hash: sha256:759edb805d9280383ace25a3d5be1d2aab5b6b7de0955b2bc09cf593fafb31e1
collections:
  Primitives:
    modes:
    - Value
    variables:
      Primary:
        '50': '#EEF6FF'
        '100': '#D9E8FF'
        '200': '#B7D5FF'
        '300': '#85B7FF'
        '400': '#4D8FF7'
        '500': '#2563EB'
        '600': '#1D4AE5'
        '700': '#1C3BD3'
        '800': '#1D37B1'
        '900': '#1F3790'
      Error:
        '50': '#FFF2F1'
        '100': '#FFE1DE'
        '200': '#FEC4C0'
        '300': '#F89A92'
        '400': '#EC6059'
        '500': '#DC2626'
        '600': '#CE0007'
        '700': '#B2000B'
        '800': '#981116'
        '900': '#831A1C'
      Success:
        '50': '#F0FDF4'
        '100': '#DAF9E3'
        '200': '#B6EEC6'
        '300': '#81DF9E'
        '400': '#48C570'
        '500': '#16A34A'
        '600': '#148B3E'
        '700': '#197237'
        '800': '#1D5F33'
        '900': '#1D522F'
      Warning:
        '50': '#FFFBEB'
        '100': '#FEF3C7'
        '200': '#FDE68A'
        '300': '#FCD34D'
        '400': '#FBBF24'
        '500': '#F59E0B'
        '600': '#D97706'
        '700': '#B45309'
        '800': '#92400E'
        '900': '#78350F'
      Info:
        '50': '#ECFEFF'
        '100': '#CFFAFE'
        '200': '#A5F3FC'
        '300': '#67E8F9'
        '400': '#22D3EE'
        '500': '#06B6D4'
        '600': '#0891B2'
        '700': '#0E7490'
        '800': '#155E75'
        '900': '#164E63'
      Neutral:
        '0': '#FFFFFF'
        '50': '#F9FAFB'
        '100': '#F3F4F6'
        '200': '#E5E7EB'
        '300': '#D1D5DB'
        '400': '#9CA3AF'
        '500': '#6B7280'
        '600': '#4B5563'
        '700': '#374151'
        '800': '#1F2937'
        '900': '#111827'
      Dark:
        '50': '#F8FAFC'
        '100': '#F1F5F9'
        '200': '#E2E8F0'
        '300': '#CBD5E1'
        '400': '#94A3B8'
        '500': '#64748B'
        '600': '#475569'
        '700': '#334155'
        '800': '#1E293B'
        '900': '#0F172A'
  UI:
    modes:
    - Light
    - Dark
    variables:
      Light:
        UI/border/default: Neutral/200
        UI/border/disabled: Neutral/200
        UI/border/error: Error/500
        UI/border/focus: Primary/500
        UI/border/input: Neutral/200
        UI/border/input-focus: Primary/500
        UI/border/strong: Neutral/300
        UI/border/subtle: Neutral/100
        UI/border/success: Success/500
        UI/border/warning: Warning/500
        UI/color/black: Neutral/900
        UI/color/disabled: Neutral/100
        UI/color/error: Error/500
        UI/color/error-subtle: Error/50
        UI/color/hover-destructive: Error/600
        UI/color/hover-ghost: Primary/50
        UI/color/hover-primary: Primary/600
        UI/color/hover-secondary: Neutral/100
        UI/color/hover-success: Success/600
        UI/color/info: Info/500
        UI/color/info-subtle: Info/50
        UI/color/overlay: Neutral/900
        UI/color/primary: Primary/500
        UI/color/primary-hover: Primary/600
        UI/color/primary-subtle: Primary/50
        UI/color/success: Success/500
        UI/color/success-subtle: Success/50
        UI/color/warning: Warning/500
        UI/color/warning-subtle: Warning/50
        UI/color/white: Neutral/0
        UI/icon/active: Primary/500
        UI/icon/default: Neutral/500
        UI/icon/disabled: Neutral/300
        UI/icon/error: Error/500
        UI/icon/info: Info/500
        UI/icon/inverse: Neutral/0
        UI/icon/on-primary: Neutral/0
        UI/icon/subtle: Neutral/400
        UI/icon/success: Success/500
        UI/icon/warning: Warning/500
        UI/surface/button/destructive: Error/500
        UI/surface/button/ghost: Neutral/0
        UI/surface/button/primary: Primary/600
        UI/surface/button/primary-hover: Primary/700
        UI/surface/button/secondary: Neutral/100
        UI/surface/button/success: Success/500
        UI/surface/card: Neutral/0
        UI/surface/default: Neutral/0
        UI/surface/disabled: Neutral/100
        UI/surface/input: Neutral/50
        UI/surface/input-focus: Neutral/0
        UI/surface/overlay: Neutral/900
        UI/surface/secondary: Neutral/50
        UI/surface/tertiary: Neutral/100
        UI/surface/toast/error: Error/50
        UI/surface/toast/info: Info/50
        UI/surface/toast/success: Success/50
        UI/surface/toast/warning: Warning/50
        UI/text/disabled: Neutral/300
        UI/text/error: Error/700
        UI/text/info: Info/700
        UI/text/inverse: Neutral/0
        UI/text/link: Primary/600
        UI/text/link-hover: Primary/700
        UI/text/on-primary: Neutral/0
        UI/text/placeholder: Neutral/500
        UI/text/primary: Neutral/900
        UI/text/secondary: Neutral/600
        UI/text/success: Success/700
        UI/text/tertiary: Neutral/500
        UI/text/warning: Warning/700
      Dark:
        UI/border/default: Dark/700
        UI/border/disabled: Dark/700
        UI/border/error: Error/400
        UI/border/focus: Primary/400
        UI/border/input: Dark/700
        UI/border/input-focus: Primary/400
        UI/border/strong: Dark/600
        UI/border/subtle: Dark/800
        UI/border/success: Success/400
        UI/border/warning: Warning/400
        UI/color/black: Dark/100
        UI/color/disabled: Dark/800
        UI/color/error: Error/400
        UI/color/error-subtle: Error/800
        UI/color/hover-destructive: Error/700
        UI/color/hover-ghost: Primary/900
        UI/color/hover-primary: Primary/700
        UI/color/hover-secondary: Dark/700
        UI/color/hover-success: Success/700
        UI/color/info: Info/400
        UI/color/info-subtle: Info/800
        UI/color/overlay: Neutral/900
        UI/color/primary: Primary/400
        UI/color/primary-hover: Primary/300
        UI/color/primary-subtle: Primary/900
        UI/color/success: Success/400
        UI/color/success-subtle: Success/800
        UI/color/warning: Warning/400
        UI/color/warning-subtle: Warning/800
        UI/color/white: Dark/900
        UI/icon/active: Primary/400
        UI/icon/default: Dark/400
        UI/icon/disabled: Dark/600
        UI/icon/error: Error/400
        UI/icon/info: Info/400
        UI/icon/inverse: Dark/900
        UI/icon/on-primary: Neutral/900
        UI/icon/subtle: Dark/500
        UI/icon/success: Success/400
        UI/icon/warning: Warning/400
        UI/surface/button/destructive: Error/400
        UI/surface/button/ghost: Dark/900
        UI/surface/button/primary: Primary/400
        UI/surface/button/primary-hover: Primary/300
        UI/surface/button/secondary: Dark/700
        UI/surface/button/success: Success/400
        UI/surface/card: Dark/800
        UI/surface/default: Dark/900
        UI/surface/disabled: Dark/800
        UI/surface/input: Dark/800
        UI/surface/input-focus: Dark/900
        UI/surface/overlay: Neutral/900
        UI/surface/secondary: Dark/800
        UI/surface/tertiary: Dark/700
        UI/surface/toast/error: Error/900
        UI/surface/toast/info: Info/900
        UI/surface/toast/success: Success/900
        UI/surface/toast/warning: Warning/900
        UI/text/disabled: Dark/700
        UI/text/error: Error/400
        UI/text/info: Info/400
        UI/text/inverse: Dark/900
        UI/text/link: Primary/400
        UI/text/link-hover: Primary/300
        UI/text/on-primary: Neutral/900
        UI/text/placeholder: Dark/400
        UI/text/primary: Dark/100
        UI/text/secondary: Dark/400
        UI/text/success: Success/400
        UI/text/tertiary: Dark/400
        UI/text/warning: Warning/400
  Spacing:
    modes:
    - Value
    variables:
      Spacing/0: 0px
      Spacing/1: 4px
      Spacing/2: 8px
      Spacing/3: 10px
      Spacing/4: 14px
      Spacing/5: 18px
      Spacing/6: 20px
      Spacing/7: 24px
      Spacing/8: 28px
      Spacing/10: 36px
      Spacing/12: 42px
      Spacing/14: 48px
      Spacing/16: 56px
      Spacing/20: 70px
      Spacing/24: 84px
      Spacing/32: 112px
  Border Radius:
    modes:
    - Value
    variables:
      Border Radius/None: 0px
      Border Radius/XS: 0px
      Border Radius/SM: 2px
      Border Radius/MD: 2px
      Border Radius/LG: 4px
      Border Radius/XL: 4px
      Border Radius/2XL: 6px
      Border Radius/3XL: 8px
      Border Radius/Full: 999px
  Typography:
    modes:
    - Value
    variables:
      Font Family:
        Base: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes;
          max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
      Font Weight:
        Regular: '400'
        Medium: '500'
        Semi Bold: '600'
        Bold: '700'
      Font Size:
        XS: 10px
        SM: 12px
        MD: 14px
        Base: 16px
        LG: 18px
        XL: 20px
        2XL: 24px
        3XL: 28px
        4XL: 32px
      Line Height:
        Tight: 120%
        Snug: 138%
        Normal: 150%
        Relaxed: 163%
  Elevation:
    modes:
    - Value
    variables:
      Elevation/SM:
        y: 1
        blur: 2
        spread: 0
        color: '#000000'
        opacity: 0.05
      Elevation/MD:
        y: 4
        blur: 8
        spread: 0
        color: '#000000'
        opacity: 0.08
      Elevation/LG:
        y: 10
        blur: 20
        spread: 0
        color: '#000000'
        opacity: 0.1
      Elevation/XL:
        y: 20
        blur: 40
        spread: 0
        color: '#000000'
        opacity: 0.12
      Elevation/Scrim:
        y: 0
        blur: 0
        spread: 0
        color: '#000000'
        opacity: 0.4
      Elevation/Sheet:
        y: 0
        blur: 16
        spread: 0
        color: '#000000'
        opacity: 0.16
tokens:
  UI/border/default:
    light: Neutral/200
    dark: Dark/700
    light_hex: '#E5E7EB'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/border/disabled:
    light: Neutral/200
    dark: Dark/700
    light_hex: '#E5E7EB'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/border/error:
    light: Error/500
    dark: Error/400
    light_hex: '#DC2626'
    dark_hex: '#EC6059'
    provenance:
      tier: C
      derived_from: B5.error + default rule
      rule: default
      confidence: exact
  UI/border/focus:
    light: Primary/500
    dark: Primary/400
    light_hex: '#2563EB'
    dark_hex: '#4D8FF7'
    provenance:
      tier: C
      derived_from: B5.primary + default rule
      rule: default
      confidence: exact
  UI/border/input:
    light: Neutral/200
    dark: Dark/700
    light_hex: '#E5E7EB'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/border/input-focus:
    light: Primary/500
    dark: Primary/400
    light_hex: '#2563EB'
    dark_hex: '#4D8FF7'
    provenance:
      tier: C
      derived_from: B5.primary + default rule
      rule: default
      confidence: exact
  UI/border/strong:
    light: Neutral/300
    dark: Dark/600
    light_hex: '#D1D5DB'
    dark_hex: '#475569'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/border/subtle:
    light: Neutral/100
    dark: Dark/800
    light_hex: '#F3F4F6'
    dark_hex: '#1E293B'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: A subtle border in dark mode has to be lighter than the surface it divides, not darker. Inversion
        produces a border that disappears into the card.
  UI/border/success:
    light: Success/500
    dark: Success/400
    light_hex: '#16A34A'
    dark_hex: '#48C570'
    provenance:
      tier: C
      derived_from: B5.success + default rule
      rule: default
      confidence: exact
  UI/border/warning:
    light: Warning/500
    dark: Warning/400
    light_hex: '#F59E0B'
    dark_hex: '#FBBF24'
    provenance:
      tier: C
      derived_from: B5.warning + default rule
      rule: default
      confidence: exact
  UI/color/black:
    light: Neutral/900
    dark: Dark/100
    light_hex: '#111827'
    dark_hex: '#F1F5F9'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/color/disabled:
    light: Neutral/100
    dark: Dark/800
    light_hex: '#F3F4F6'
    dark_hex: '#1E293B'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: Disabled fill must read as "inert" rather than "another surface level". The inversion table
        lands it on 700, which is the same value as surface/tertiary and makes a disabled control look
        interactive.
  UI/color/error:
    light: Error/500
    dark: Error/400
    light_hex: '#DC2626'
    dark_hex: '#EC6059'
    provenance:
      tier: C
      derived_from: B5.error + default rule
      rule: default
      confidence: exact
  UI/color/error-subtle:
    light: Error/50
    dark: Error/800
    light_hex: '#FFF2F1'
    dark_hex: '#981116'
    provenance:
      tier: C
      derived_from: B5.error + subtle rule
      rule: subtle
      confidence: exact
  UI/color/hover-destructive:
    light: Error/600
    dark: Error/700
    light_hex: '#CE0007'
    dark_hex: '#B2000B'
    provenance:
      tier: C
      derived_from: B5.error + override rule
      rule: override
      confidence: exact
      note: Same reasoning as color/hover-primary.
  UI/color/hover-ghost:
    light: Primary/50
    dark: Primary/900
    light_hex: '#EEF6FF'
    dark_hex: '#1F3790'
    provenance:
      tier: C
      derived_from: B5.primary + override rule
      rule: override
      confidence: exact
      note: A ghost button's hover is a tint of the page, so it follows the subtle rule rather than the
        default rule.
  UI/color/hover-primary:
    light: Primary/600
    dark: Primary/700
    light_hex: '#1D4AE5'
    dark_hex: '#1C3BD3'
    provenance:
      tier: C
      derived_from: B5.primary + override rule
      rule: override
      confidence: exact
      note: This is the pressed/active fill for a solid primary button, not the resting fill. It goes
        deeper in both modes.
  UI/color/hover-secondary:
    light: Neutral/100
    dark: Dark/700
    light_hex: '#F3F4F6'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/color/hover-success:
    light: Success/600
    dark: Success/700
    light_hex: '#148B3E'
    dark_hex: '#197237'
    provenance:
      tier: C
      derived_from: B5.success + override rule
      rule: override
      confidence: exact
      note: Same reasoning as color/hover-primary.
  UI/color/info:
    light: Info/500
    dark: Info/400
    light_hex: '#06B6D4'
    dark_hex: '#22D3EE'
    provenance:
      tier: C
      derived_from: B5.info + default rule
      rule: default
      confidence: exact
  UI/color/info-subtle:
    light: Info/50
    dark: Info/800
    light_hex: '#ECFEFF'
    dark_hex: '#155E75'
    provenance:
      tier: C
      derived_from: B5.info + subtle rule
      rule: subtle
      confidence: exact
  UI/color/overlay:
    light: Neutral/900
    dark: Neutral/900
    light_hex: '#111827'
    dark_hex: '#111827'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: A scrim is darkness, not a surface. Inverting it would make the modal backdrop lighter than
        the content it is supposed to recede behind.
  UI/color/primary:
    light: Primary/500
    dark: Primary/400
    light_hex: '#2563EB'
    dark_hex: '#4D8FF7'
    provenance:
      tier: C
      derived_from: B5.primary + default rule
      rule: default
      confidence: exact
  UI/color/primary-hover:
    light: Primary/600
    dark: Primary/300
    light_hex: '#1D4AE5'
    dark_hex: '#85B7FF'
    provenance:
      tier: C
      derived_from: B5.primary + override rule
      rule: override
      confidence: exact
      note: Hover has to move AWAY from the resting state in the direction of more contrast. In light
        mode that is darker (600); in dark mode it is lighter (300). The default rule moves both the same
        direction.
  UI/color/primary-subtle:
    light: Primary/50
    dark: Primary/900
    light_hex: '#EEF6FF'
    dark_hex: '#1F3790'
    provenance:
      tier: C
      derived_from: B5.primary + override rule
      rule: override
      confidence: exact
      note: The primary subtle fill sits behind primary-coloured text and chips. At 800 it does not separate
        from surface/secondary in dark mode; the semantic subtles (success/warning/error/info) do not
        have that problem because their hue already distinguishes them.
  UI/color/success:
    light: Success/500
    dark: Success/400
    light_hex: '#16A34A'
    dark_hex: '#48C570'
    provenance:
      tier: C
      derived_from: B5.success + default rule
      rule: default
      confidence: exact
  UI/color/success-subtle:
    light: Success/50
    dark: Success/800
    light_hex: '#F0FDF4'
    dark_hex: '#1D5F33'
    provenance:
      tier: C
      derived_from: B5.success + subtle rule
      rule: subtle
      confidence: exact
  UI/color/warning:
    light: Warning/500
    dark: Warning/400
    light_hex: '#F59E0B'
    dark_hex: '#FBBF24'
    provenance:
      tier: C
      derived_from: B5.warning + default rule
      rule: default
      confidence: exact
  UI/color/warning-subtle:
    light: Warning/50
    dark: Warning/800
    light_hex: '#FFFBEB'
    dark_hex: '#92400E'
    provenance:
      tier: C
      derived_from: B5.warning + subtle rule
      rule: subtle
      confidence: exact
  UI/color/white:
    light: Neutral/0
    dark: Dark/900
    light_hex: '#FFFFFF'
    dark_hex: '#0F172A'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/icon/active:
    light: Primary/500
    dark: Primary/400
    light_hex: '#2563EB'
    dark_hex: '#4D8FF7'
    provenance:
      tier: C
      derived_from: B5.primary + default rule
      rule: default
      confidence: exact
  UI/icon/default:
    light: Neutral/500
    dark: Dark/400
    light_hex: '#6B7280'
    dark_hex: '#94A3B8'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/icon/disabled:
    light: Neutral/300
    dark: Dark/600
    light_hex: '#D1D5DB'
    dark_hex: '#475569'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/icon/error:
    light: Error/500
    dark: Error/400
    light_hex: '#DC2626'
    dark_hex: '#EC6059'
    provenance:
      tier: C
      derived_from: B5.error + default rule
      rule: default
      confidence: exact
  UI/icon/info:
    light: Info/500
    dark: Info/400
    light_hex: '#06B6D4'
    dark_hex: '#22D3EE'
    provenance:
      tier: C
      derived_from: B5.info + default rule
      rule: default
      confidence: exact
  UI/icon/inverse:
    light: Neutral/0
    dark: Dark/900
    light_hex: '#FFFFFF'
    dark_hex: '#0F172A'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/icon/on-primary:
    light: Neutral/0
    dark: Neutral/900
    light_hex: '#FFFFFF'
    dark_hex: '#111827'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: Same reasoning and same measurements as text/on-primary.
  UI/icon/subtle:
    light: Neutral/400
    dark: Dark/500
    light_hex: '#9CA3AF'
    dark_hex: '#64748B'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/icon/success:
    light: Success/500
    dark: Success/400
    light_hex: '#16A34A'
    dark_hex: '#48C570'
    provenance:
      tier: C
      derived_from: B5.success + default rule
      rule: default
      confidence: exact
  UI/icon/warning:
    light: Warning/500
    dark: Warning/400
    light_hex: '#F59E0B'
    dark_hex: '#FBBF24'
    provenance:
      tier: C
      derived_from: B5.warning + default rule
      rule: default
      confidence: exact
  UI/surface/button/destructive:
    light: Error/500
    dark: Error/400
    light_hex: '#DC2626'
    dark_hex: '#EC6059'
    provenance:
      tier: C
      derived_from: B5.error + default rule
      rule: default
      confidence: exact
  UI/surface/button/ghost:
    light: Neutral/0
    dark: Dark/900
    light_hex: '#FFFFFF'
    dark_hex: '#0F172A'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/button/primary:
    light: Primary/600
    dark: Primary/400
    light_hex: '#1D4AE5'
    dark_hex: '#4D8FF7'
    provenance:
      tier: C
      derived_from: B5.primary + default rule
      rule: default
      confidence: exact
  UI/surface/button/primary-hover:
    light: Primary/700
    dark: Primary/300
    light_hex: '#1C3BD3'
    dark_hex: '#85B7FF'
    provenance:
      tier: C
      derived_from: B5.primary + override rule
      rule: override
      confidence: exact
      note: Same reasoning as color/primary-hover — hover moves toward more contrast, which is lighter
        in dark mode and darker in light mode. The default rule resolves every saturated token to 400,
        so a hover state derived by rule would be identical to its own resting state.
  UI/surface/button/secondary:
    light: Neutral/100
    dark: Dark/700
    light_hex: '#F3F4F6'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/button/success:
    light: Success/500
    dark: Success/400
    light_hex: '#16A34A'
    dark_hex: '#48C570'
    provenance:
      tier: C
      derived_from: B5.success + default rule
      rule: default
      confidence: exact
  UI/surface/card:
    light: Neutral/0
    dark: Dark/800
    light_hex: '#FFFFFF'
    dark_hex: '#1E293B'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: 'In light mode a card is white on a grey page. In dark mode the relationship inverts: the
        page is darkest and the card lifts off it. Straight inversion would make card and page the same
        value and lose the elevation entirely.'
  UI/surface/default:
    light: Neutral/0
    dark: Dark/900
    light_hex: '#FFFFFF'
    dark_hex: '#0F172A'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/disabled:
    light: Neutral/100
    dark: Dark/800
    light_hex: '#F3F4F6'
    dark_hex: '#1E293B'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: Matches color/disabled so a disabled control is one flat value.
  UI/surface/input:
    light: Neutral/50
    dark: Dark/800
    light_hex: '#F9FAFB'
    dark_hex: '#1E293B'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/input-focus:
    light: Neutral/0
    dark: Dark/900
    light_hex: '#FFFFFF'
    dark_hex: '#0F172A'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/overlay:
    light: Neutral/900
    dark: Neutral/900
    light_hex: '#111827'
    dark_hex: '#111827'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: Same reasoning as color/overlay — the surface alias of the same scrim.
  UI/surface/secondary:
    light: Neutral/50
    dark: Dark/800
    light_hex: '#F9FAFB'
    dark_hex: '#1E293B'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/tertiary:
    light: Neutral/100
    dark: Dark/700
    light_hex: '#F3F4F6'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/surface/toast/error:
    light: Error/50
    dark: Error/900
    light_hex: '#FFF2F1'
    dark_hex: '#831A1C'
    provenance:
      tier: C
      derived_from: B5.error + toast rule
      rule: toast
      confidence: exact
  UI/surface/toast/info:
    light: Info/50
    dark: Info/900
    light_hex: '#ECFEFF'
    dark_hex: '#164E63'
    provenance:
      tier: C
      derived_from: B5.info + toast rule
      rule: toast
      confidence: exact
  UI/surface/toast/success:
    light: Success/50
    dark: Success/900
    light_hex: '#F0FDF4'
    dark_hex: '#1D522F'
    provenance:
      tier: C
      derived_from: B5.success + toast rule
      rule: toast
      confidence: exact
  UI/surface/toast/warning:
    light: Warning/50
    dark: Warning/900
    light_hex: '#FFFBEB'
    dark_hex: '#78350F'
    provenance:
      tier: C
      derived_from: B5.warning + toast rule
      rule: toast
      confidence: exact
  UI/text/disabled:
    light: Neutral/300
    dark: Dark/700
    light_hex: '#D1D5DB'
    dark_hex: '#334155'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: Disabled text must be visibly inert. Inversion gives 600, which reads as merely secondary.
  UI/text/error:
    light: Error/700
    dark: Error/400
    light_hex: '#B2000B'
    dark_hex: '#EC6059'
    provenance:
      tier: C
      derived_from: B5.error + default rule
      rule: default
      confidence: exact
  UI/text/info:
    light: Info/700
    dark: Info/400
    light_hex: '#0E7490'
    dark_hex: '#22D3EE'
    provenance:
      tier: C
      derived_from: B5.info + default rule
      rule: default
      confidence: exact
  UI/text/inverse:
    light: Neutral/0
    dark: Dark/900
    light_hex: '#FFFFFF'
    dark_hex: '#0F172A'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/text/link:
    light: Primary/600
    dark: Primary/400
    light_hex: '#1D4AE5'
    dark_hex: '#4D8FF7'
    provenance:
      tier: C
      derived_from: B5.primary + default rule
      rule: default
      confidence: exact
  UI/text/link-hover:
    light: Primary/700
    dark: Primary/300
    light_hex: '#1C3BD3'
    dark_hex: '#85B7FF'
    provenance:
      tier: C
      derived_from: B5.primary + override rule
      rule: override
      confidence: exact
      note: Same reasoning as color/primary-hover.
  UI/text/on-primary:
    light: Neutral/0
    dark: Neutral/900
    light_hex: '#FFFFFF'
    dark_hex: '#111827'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: Dark text, not white. The dark-mode primary button fills with primary/400, and white on primary/400
        measures 2.54:1 — a clear AA failure. Near-black on the same fill measures 6.98:1. Light mode
        keeps white on primary/600 at 5.17:1. The label colour therefore flips with the mode; it does
        not stay white in both.
  UI/text/placeholder:
    light: Neutral/500
    dark: Dark/400
    light_hex: '#6B7280'
    dark_hex: '#94A3B8'
    provenance:
      tier: C
      derived_from: B5.neutral + override rule
      rule: override
      confidence: exact
      note: 'Placeholder ought to sit below text/secondary, and in light mode it does. In dark mode AA
        does not leave room: on the dark input surface, step 500 measures 3.07:1 and 600 measures 1.93:1,
        so 400 is the only step that clears 4.5:1 — which is also where secondary lands. The two collide
        in dark mode by necessity. Differentiate placeholder by italic or by an icon affordance rather
        than by reducing its contrast.'
  UI/text/primary:
    light: Neutral/900
    dark: Dark/100
    light_hex: '#111827'
    dark_hex: '#F1F5F9'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/text/secondary:
    light: Neutral/600
    dark: Dark/400
    light_hex: '#4B5563'
    dark_hex: '#94A3B8'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/text/success:
    light: Success/700
    dark: Success/400
    light_hex: '#197237'
    dark_hex: '#48C570'
    provenance:
      tier: C
      derived_from: B5.success + default rule
      rule: default
      confidence: exact
  UI/text/tertiary:
    light: Neutral/500
    dark: Dark/400
    light_hex: '#6B7280'
    dark_hex: '#94A3B8'
    provenance:
      tier: C
      derived_from: B5.neutral + surface rule
      rule: surface
      confidence: exact
  UI/text/warning:
    light: Warning/700
    dark: Warning/400
    light_hex: '#B45309'
    dark_hex: '#FBBF24'
    provenance:
      tier: C
      derived_from: B5.warning + default rule
      rule: default
      confidence: exact
scales:
  heading-xl:
    size: 32
    weight: 700
    line_height: 38
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → 4XL (32px) + Bold + Tight'
      rule: type-scale
      confidence: exact
      note: hero headings
  heading-lg:
    size: 24
    weight: 700
    line_height: 29
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → 2XL (24px) + Bold + Tight'
      rule: type-scale
      confidence: exact
      note: screen titles
  heading-md:
    size: 20
    weight: 600
    line_height: 28
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → XL (20px) + Semi Bold + Snug'
      rule: type-scale
      confidence: exact
      note: section headers
  heading-sm:
    size: 16
    weight: 600
    line_height: 22
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → Base (16px) + Semi Bold + Snug'
      rule: type-scale
      confidence: exact
      note: card titles
  body-lg:
    size: 16
    weight: 400
    line_height: 24
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → Base (16px) + Regular + Normal'
      rule: type-scale
      confidence: exact
      note: body copy
  body-md:
    size: 14
    weight: 400
    line_height: 21
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → MD (14px) + Regular + Normal'
      rule: type-scale
      confidence: exact
      note: secondary body
  body-sm:
    size: 12
    weight: 400
    line_height: 18
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → SM (12px) + Regular + Normal'
      rule: type-scale
      confidence: exact
      note: captions, timestamps
  label:
    size: 14
    weight: 500
    line_height: 21
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → MD (14px) + Medium + Normal'
      rule: type-scale
      confidence: exact
      note: form labels
  button:
    size: 16
    weight: 600
    line_height: 24
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → Base (16px) + Semi Bold + Normal'
      rule: type-scale
      confidence: exact
      note: all button labels
  mono:
    size: 14
    weight: 500
    line_height: 21
    family: 'Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15 minutes; max
      3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes.'
    provenance:
      tier: B
      derived_from: 'B1 Keep it minimal: follow existing conventions. Lock after 5 wrong codes for 15
        minutes; max 3 resends per 10 minutes; codes are 6 digits; codes expire after 10 minutes. + A3
        base_font=16 → MD (14px) + Medium + Normal'
      rule: type-scale
      confidence: exact
      note: booking references, codes; no mono family supplied, using base
spacing:
  Spacing/0:
    value: 0
    provenance:
      tier: B
      derived_from: B2 density=compact · 0 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/1:
    value: 4
    provenance:
      tier: B
      derived_from: B2 density=compact · 1 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/2:
    value: 8
    provenance:
      tier: B
      derived_from: B2 density=compact · 2 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/3:
    value: 10
    provenance:
      tier: B
      derived_from: B2 density=compact · 3 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/4:
    value: 14
    provenance:
      tier: B
      derived_from: B2 density=compact · 4 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
      note: BASE UNIT — standard screen padding
  Spacing/5:
    value: 18
    provenance:
      tier: B
      derived_from: B2 density=compact · 5 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/6:
    value: 20
    provenance:
      tier: B
      derived_from: B2 density=compact · 6 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/7:
    value: 24
    provenance:
      tier: B
      derived_from: B2 density=compact · 7 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/8:
    value: 28
    provenance:
      tier: B
      derived_from: B2 density=compact · 8 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/10:
    value: 36
    provenance:
      tier: B
      derived_from: B2 density=compact · 10 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/12:
    value: 42
    provenance:
      tier: B
      derived_from: B2 density=compact · 12 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/14:
    value: 48
    provenance:
      tier: B
      derived_from: B2 density=compact · 14 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/16:
    value: 56
    provenance:
      tier: B
      derived_from: B2 density=compact · 16 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/20:
    value: 70
    provenance:
      tier: B
      derived_from: B2 density=compact · 20 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/24:
    value: 84
    provenance:
      tier: B
      derived_from: B2 density=compact · 24 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
  Spacing/32:
    value: 112
    provenance:
      tier: B
      derived_from: B2 density=compact · 32 × 4px × 0.875
      rule: spacing-scale
      confidence: exact
dimensions:
  app_bar_height:
    value: 48
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  avatar_lg:
    value: 56
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  avatar_md:
    value: 36
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  avatar_sm:
    value: 28
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  badge_height:
    value: 20
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  border_weight:
    value: 1
    unit: px
    provenance:
      tier: B
      derived_from: default stroke
      rule: dimension
      confidence: exact
  border_weight_focus:
    value: 2
    unit: px
    provenance:
      tier: B
      derived_from: focus ring
      rule: dimension
      confidence: exact
  bottom_sheet_handle:
    value: 4
    unit: px
    provenance:
      tier: B
      derived_from: platform convention
      rule: dimension
      confidence: exact
  button_height:
    value: 36
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  button_height_lg:
    value: 40
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  button_height_sm:
    value: 32
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  card_gap:
    value: 10
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  card_padding:
    value: 14
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  checkbox_size:
    value: 18
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  chip_height:
    value: 28
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  divider_weight:
    value: 1
    unit: px
    provenance:
      tier: B
      derived_from: hairline
      rule: dimension
      confidence: exact
  fab_size:
    value: 48
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  icon_lg:
    value: 24
    unit: px
    provenance:
      tier: B
      derived_from: icon grid
      rule: dimension
      confidence: exact
  icon_md:
    value: 20
    unit: px
    provenance:
      tier: B
      derived_from: icon grid
      rule: dimension
      confidence: exact
  icon_sm:
    value: 16
    unit: px
    provenance:
      tier: B
      derived_from: icon grid
      rule: dimension
      confidence: exact
  icon_xl:
    value: 32
    unit: px
    provenance:
      tier: B
      derived_from: icon grid
      rule: dimension
      confidence: exact
  input_height:
    value: 38
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  list_row_gap:
    value: 8
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  list_row_height:
    value: 40
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  modal_max_width:
    value: 480
    unit: px
    provenance:
      tier: B
      derived_from: A3 ref_width=1440 — centre dialog
      rule: dimension
      confidence: exact
  modal_padding:
    value: 20
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  progress_height:
    value: 4
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  radio_size:
    value: 18
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  ref_height:
    value: 900
    unit: px
    provenance:
      tier: B
      derived_from: A3 platform=web
      rule: dimension
      confidence: exact
  ref_width:
    value: 1440
    unit: px
    provenance:
      tier: B
      derived_from: A3 platform=web
      rule: dimension
      confidence: exact
  safe_area_bottom:
    value: 0
    unit: px
    provenance:
      tier: B
      derived_from: A3 platform=web
      rule: dimension
      confidence: exact
  safe_area_top:
    value: 0
    unit: px
    provenance:
      tier: B
      derived_from: A3 platform=web
      rule: dimension
      confidence: exact
  screen_padding:
    value: 14
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  search_bar_height:
    value: 38
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  section_gap:
    value: 20
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  select_height:
    value: 38
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  skeleton_row_height:
    value: 14
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  switch_height:
    value: 20
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  switch_width:
    value: 38
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  tab_bar_height:
    value: 56
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  tap_target_min:
    value: 32
    unit: px
    provenance:
      tier: B
      derived_from: A3 platform=web
      rule: dimension
      confidence: exact
      note: platform minimum — the critic's quality floor
  textarea_min_height:
    value: 76
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact + A3 tap_target=32px
      rule: dimension
      confidence: exact
  toast_height:
    value: 48
    unit: px
    provenance:
      tier: B
      derived_from: B2 density=compact
      rule: dimension
      confidence: exact
  toast_max_width:
    value: 400
    unit: px
    provenance:
      tier: B
      derived_from: A3 ref_width=1440
      rule: dimension
      confidence: exact
radius:
  Border Radius/None:
    value: 0
    provenance:
      tier: B
      derived_from: B3 radius=sharp → None
      rule: radius-personality
      confidence: exact
      note: no radius
  Border Radius/XS:
    value: 0
    provenance:
      tier: B
      derived_from: B3 radius=sharp → XS
      rule: radius-personality
      confidence: exact
      note: subtle
  Border Radius/SM:
    value: 2
    provenance:
      tier: B
      derived_from: B3 radius=sharp → SM
      rule: radius-personality
      confidence: exact
      note: badges, chips
  Border Radius/MD:
    value: 2
    provenance:
      tier: B
      derived_from: B3 radius=sharp → MD
      rule: radius-personality
      confidence: exact
      note: inputs, small cards
  Border Radius/LG:
    value: 4
    provenance:
      tier: B
      derived_from: B3 radius=sharp → LG
      rule: radius-personality
      confidence: exact
      note: cards, modals
  Border Radius/XL:
    value: 4
    provenance:
      tier: B
      derived_from: B3 radius=sharp → XL
      rule: radius-personality
      confidence: exact
      note: bottom sheets
  Border Radius/2XL:
    value: 6
    provenance:
      tier: B
      derived_from: B3 radius=sharp → 2XL
      rule: radius-personality
      confidence: exact
      note: large sheets
  Border Radius/3XL:
    value: 8
    provenance:
      tier: B
      derived_from: B3 radius=sharp → 3XL
      rule: radius-personality
      confidence: exact
      note: feature surfaces
  Border Radius/Full:
    value: 999
    provenance:
      tier: B
      derived_from: B3 radius=sharp → Full
      rule: radius-personality
      confidence: exact
      note: pills, avatars, FABs
elevation:
  Elevation/SM:
    y: 1
    blur: 2
    spread: 0
    opacity: 0.05
    platform_note: null
    provenance:
      tier: A
      derived_from: A3 platform=web → web shadow scale
      rule: elevation
      confidence: exact
      note: cards at rest
  Elevation/MD:
    y: 4
    blur: 8
    spread: 0
    opacity: 0.08
    platform_note: null
    provenance:
      tier: A
      derived_from: A3 platform=web → web shadow scale
      rule: elevation
      confidence: exact
      note: raised cards, dropdowns
  Elevation/LG:
    y: 10
    blur: 20
    spread: 0
    opacity: 0.1
    platform_note: null
    provenance:
      tier: A
      derived_from: A3 platform=web → web shadow scale
      rule: elevation
      confidence: exact
      note: modals, dialogs
  Elevation/XL:
    y: 20
    blur: 40
    spread: 0
    opacity: 0.12
    platform_note: null
    provenance:
      tier: A
      derived_from: A3 platform=web → web shadow scale
      rule: elevation
      confidence: exact
      note: full-screen overlays
  Elevation/Scrim:
    y: 0
    blur: 0
    spread: 0
    opacity: 0.4
    platform_note: modal backdrop — a flat fill, not a shadow
    provenance:
      tier: A
      derived_from: A3 platform=web → centre dialog
      rule: elevation-special
      confidence: exact
      note: 'special case: the shadow scale cannot express a backdrop'
  Elevation/Sheet:
    y: 0
    blur: 16
    spread: 0
    opacity: 0.16
    platform_note: centre dialog — symmetric shadow
    provenance:
      tier: A
      derived_from: A3 platform=web → centre dialog
      rule: elevation-special
      confidence: exact
      note: 'special case: direction is decided by the modal convention'
statuses: {}
