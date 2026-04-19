---
name: brand-design-system
description: > Use when this capability is needed.
metadata:
  author: adi0900
---

# Brand Design System

## Purpose
Enforce visual consistency across all components and pages.
INPUT: None (system is pre-configured).
OUTPUT: All generated code uses these tokens.

## When to Use This Skill
- Every project. Always active.
- Override specific tokens per-project as needed.

## Rules (Non-Negotiable)
1. Never use raw color values. Always reference tokens
2. Never use inline styles for colors or spacing
3. Maximum 2 font families (heading + body)
4. All spacing must use the 8px base unit system
5. Contrast ratio must meet WCAG AA (4.5:1 minimum)

## Design Tokens

### Colors
```
primary:        #B5121C    (Deep Red)
secondary:      #F6C236    (Golden Yellow)
background:     #FAFAFA    (Near White)
surface:        #FFFFFF    (White)
text-primary:   #1A1A1A    (Near Black)
text-secondary: #555555    (Medium Gray)
text-muted:     #888888    (Light Gray)
border:         #E5E5E5    (Border Gray)
success:        #16A34A    (Green)
error:          #DC2626    (Red)
link:           #2563EB    (Blue)
```

### Typography
```
heading: Inter, system-ui, sans-serif
body:    Inter, system-ui, sans-serif
mono:    JetBrains Mono, monospace

Scale (desktop):
  h1:      48px / 1.1 line-height / 700 weight
  h2:      36px / 1.2 / 700
  h3:      28px / 1.3 / 600
  h4:      22px / 1.4 / 600
  body:    16px / 1.6 / 400
  small:   14px / 1.5 / 400
  caption: 12px / 1.4 / 400

Scale (mobile): Reduce each by ~15%
```

### Spacing (8px base)
```
xs:      4px
sm:      8px
md:      16px
lg:      24px
xl:      32px
2xl:     48px
3xl:     64px
4xl:     80px
section: 96px
```

### Border Radius
```
sm:   4px
md:   8px
lg:   12px
xl:   16px
full: 9999px
```

### Shadows
```
sm: 0 1px 2px rgba(0,0,0,0.05)
md: 0 4px 6px rgba(0,0,0,0.07)
lg: 0 10px 15px rgba(0,0,0,0.1)
```

## Checklist (AI Self-Verification)
- [ ] No raw color values in code
- [ ] Only Inter + JetBrains Mono used
- [ ] All spacing divisible by 4 or 8
- [ ] Contrast ratio checked on primary text
- [ ] Font weights limited to 400, 600, 700

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adi0900) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
