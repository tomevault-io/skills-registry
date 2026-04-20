---
name: industrial-ui
description: Dark mode first, monospace, brutalist UI design patterns for dashboards, terminals, and data-dense interfaces Use when this capability is needed.
metadata:
  author: procdexeh
---

# Industrial UI Design

Design philosophy for dark mode, data-dense interfaces.

## The Harkonnen Principles

1. **Dark mode is the default** - Light mode is an afterthought
2. **Monospace is truth** - Fixed-width fonts for data, alignment, precision
3. **Density over whitespace** - Information-rich, not empty
4. **Brutalist clarity** - No decoration without purpose
5. **Function is beauty** - If it works perfectly, it looks perfect

## Color System

```
Background layers:
  base:    #0a0a0a -> #121212 -> #1a1a1a -> #242424
  
Accent hierarchy:
  primary: Electric/cyan for primary actions
  warning: Amber/orange for alerts
  danger:  Red, desaturated for errors
  success: Muted green, not neon
  
Text:
  primary:   #e0e0e0 (not pure white - reduces eye strain)
  secondary: #888888
  tertiary:  #555555
```

## Typography

```
Code/Data:    JetBrains Mono, Berkeley Mono, IBM Plex Mono, monospace
UI Labels:    Inter, system-ui (only where mono hurts readability)
Sizing:       12px base, 1.5 line-height for dense data
```

## Spacing

```
Base unit: 4px
Components: 8px padding minimum
Density: Tight but not cramped - breathing room, not lounging room
```

## Component Patterns

### Data Tables
- Monospace for all numeric data
- Subtle row striping (#ffffff05 alternating)
- Sticky headers
- Right-align numbers, left-align text
- Compact row height (32-36px)

### Terminal/Console
- True black background (#000000 or #0a0a0a)
- Scanline subtle texture optional
- Cursor: block, blinking
- Selection: inverted or high-contrast highlight

### Cards/Panels
- Minimal border-radius (2-4px max, or 0)
- Border: 1px solid #333 or subtle gradient
- No drop shadows - use borders and background shifts

### Buttons
- Rectangular or minimal radius
- Uppercase labels in tight tracking
- Clear state changes: hover, active, disabled
- Ghost buttons for secondary actions

### Forms
- Underline inputs over boxed
- Labels above, not floating
- Monospace for technical inputs
- Validation inline, not toast

## Anti-Patterns

**Never:**
- Pure white backgrounds
- Rounded "pill" buttons in data UIs
- Excessive whitespace masquerading as "clean"
- Colorful gradients without purpose
- Light mode defaults
- Variable-width fonts in data tables
- Decorative icons that don't convey information

## Accessibility

Dark mode done right:
- 4.5:1 contrast ratio minimum for text
- Don't rely on color alone for state
- Focus indicators must be visible
- Respect prefers-reduced-motion
- Test at different brightness levels

---

*"The slow blade penetrates the shield. The clear interface penetrates confusion."*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/procdexeh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
