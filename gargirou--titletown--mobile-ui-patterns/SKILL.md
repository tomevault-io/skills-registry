---
name: mobile-ui-patterns
description: Visual language, design system, and UI conventions for TILETOWN. Use this skill when building or modifying any UI — whether in the HTML prototype or the React Native version. Trigger on any request involving visual design, layout, colours, typography, animations, components, theming, reskinning, or making the game look better. Also use when designing new UI elements like overlays, panels, screens, or buttons. Use when this capability is needed.
metadata:
  author: gargirou
---

# TILETOWN UI Patterns & Design System

## Theme: Aztec Empire

The visual identity is an ancient Mesoamerican empire at its height. The world is lush, golden, and monumental. Think: a jade-inlaid stone temple rising from the jungle, sunlight glinting off obsidian and gold.

**The one thing players remember:** Rich terracotta and jungle greens with Aztec gold accents — like sunlight on a ceremonial mask.

---

## Colour Palette

```
Background / Atmosphere
  ink:           #1A0E05   // deepest background (obsidian dark)
  forest:        #2A1808   // body background (dark jungle)
  forestMid:     #3E2A12   // mid-tone panels
  terrainWrap:   #1E1008   // grid outer ring
  terrainFrame:  #3A2410   // grid border

Surfaces / Panels
  panelBg:  #2A1A0A
  cell bg:  linear-gradient(#B08040 → #C09050) — terracotta earth

Accent — Gold (dominant accent, use sparingly)
  gold:         #D4A017   // borders, labels, sliders
  goldBright:   #F0C030   // numbers, highlights, toasts
  goldDim:      #8A6010   // subtle borders, dividers
  amber:        #E8820A   // warm secondary accent

Text
  cream:        #F5E6C8   // primary text on dark
  creamMid:     #E8D4A4   // secondary text
  textMuted:    #C4A060   // labels, hints, disabled
  greenGlow:    #26A69A   // active/selected items, progress bars (jade teal)
```

**Palette rules:**
- Dark obsidian surfaces everywhere — no light backgrounds except inside modals
- One dominant accent: gold. Use jade teal (`#26A69A`) only for active/progress states.
- Never use generic blue, purple, or neon colours. These break the Aztec theme.
- Jade/teal is acceptable for tile colours (Jade Stone, Sacred Cenote) but not for UI chrome.

---

## Typography

| Role | Font | Size | Weight |
|---|---|---|---|
| Game title / section headers | Almendra | 18–24sp | 700 Bold |
| Labels / UI chrome | Almendra | 9–12sp | 700 Bold, uppercase, letter-spacing |
| Body text / hints | Cardo | 11–13sp | 400 Regular |
| Hints / flavour text | Cardo | 11sp | 400 Italic |
| Numbers / scores | Playfair Display | 16–38sp | 700 Bold |

**Font keys from `constants/theme.js`:**
```js
fonts.titleFamily  = 'Almendra_700Bold'
fonts.headerFamily = 'Almendra_700Bold'
fonts.labelFamily  = 'Almendra_700Bold'
fonts.bodyFamily   = 'Cardo_400Regular'
fonts.bodyItalic   = 'Cardo_400Italic'
fonts.numberFamily = 'PlayfairDisplay_700Bold'
```

**Never use:** Arial, Inter, Roboto, system-ui, or sans-serif fonts. The game has an ancient-world serif personality.

---

## Grid & Cell Design

The grid should feel like a ceremonial plaza carved from stone, inlaid with earth and gold.

```
Grid wrap:
  background: #1E1008 (terrainWrap)
  border: 3px solid #8A6010 (gold-dim)
  border-radius: 14px
  padding: 8px

Grid gap: 4px, colour = #2E4A1A (dark jungle green — reads as ground)

Empty cell:
  background: linear-gradient(#B08040 → #C09050) — terracotta stone
  border-radius: 4–5px
  box-shadow: inset highlights on top/sides, shadow on bottom (3D stone effect)

Occupied cell:
  background = T[type].c (tile colour)
  Same border-radius and inset shadows
  Emoji: drop-shadow(0 1px 2px rgba(0,0,0,.4))
  Label: Almendra, 9px, uppercase

Hover (empty cell only):
  filter: brightness(1.18) saturate(1.1)
  transform: scale(1.07)
```

---

## Panel Design

All sidebar panels, legend, goal cards, spawn panel follow the same template:

```
background: #2A1A0A (panelBg)
border: 1.5px solid #8A6010 (goldDim)
border-radius: 10–11px
box-shadow: 0 6px 24px rgba(0,0,0,.6) + inner highlights

Panel label (top):
  font: Almendra 700
  color: gold (#D4A017)
  letter-spacing: 0.14em
  text-transform: uppercase
```

---

## Button System

Buttons have physical 3D depth — they press down on tap.

```
Brown variant (default):  #6A3818 → #3E2008
Gold variant:   #D4A017 → #9A6C08, color: ink

All buttons:
  font: Almendra 700, uppercase, letter-spacing: .08em
  color: cream (except gold btn = ink)
  border-radius: 8px
  shadowColor: #000, shadowOffset: {0, 3}, shadowOpacity: 0.5
```

---

## Animation Timing

| Event | Duration | Easing |
|---|---|---|
| Cell placed | 160ms | scale from 0.55 + slight rotate, spring to 1 |
| Merge flash | 500ms | brightness 2.2 → 1.4 → 1, scale 1.15 → 1 |
| Score float | 1000ms | opacity 1→0, rise 15% to -70% of cell height |
| Card entrance | 380ms | scale(.72) + translateY(20px) → natural, spring |
| Goal toast in | 420ms | translateY(120px) → 0, spring |
| Progress bar | 400ms | width transition, ease-in-out |
| Score bump | 320ms | scale 1 → 1.4 → 1 |

**Principle:** Merges and placements get the most dramatic animation. UI elements are smooth but quick. Nothing should feel sluggish.

---

## Goal Cards (High Priest's Quests)

```
Default:
  dark panel bg + gold-dim border
  progress bar: jade teal gradient (#009688 → #26A69A) with teal glow

Done state:
  border: gold (#D4A017)
  outer glow: rgba(212,160,23,.4)
  bg shifts slightly lighter
  ✦ symbol top-right in gold
  progress bar: gold gradient (dim → bright) with gold glow
```

---

## High Priest's Quests Section Header

Uses decorative gold hairlines extending from both sides of the title.

---

## Toast Notification

```
background: dark panel
color: gold-bright
font: Almendra 700, uppercase, letter-spacing: .06em
border: 1.5px solid gold-dim
border-radius: 30px (pill shape)
text-shadow: 0 0 12px rgba(212,160,23,.4)
```

---

## Mobile Layout Priorities

1. The grid must fit on screen without scrolling — cell size should use `clamp` or window-dimension-based calc
2. Touch targets minimum 44px (Apple HIG minimum)
3. Sidebar below or beside the grid depending on screen width (flex-wrap)
4. Status text, legend, spawn panel can scroll off-screen — reference info, not gameplay-critical
5. Overlays (shop, game over) use `maxHeight: '92%'` with `ScrollView`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gargirou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
