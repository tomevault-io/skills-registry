---
name: design-guideline
description: DADS (Digital Agency Design System) design rules for consistent, accessible UI. Loaded by the developer subagent via skills field. Use when this capability is needed.
metadata:
  author: khaym
---

# Design Guideline

Design rules based on the Digital Agency Design System (DADS) Foundations.
Rules defined here (contrast ratios, minimum font sizes, etc.) must be followed; deviations require user confirmation.

Reference: https://design.digital.go.jp/dads/foundations/

### Design Decision Priority

1. **This guideline's rules** — Font size minimums, contrast ratios, spacing system, etc. take highest priority
2. **`src/styles/global.css`** — Source of Truth for design token values (colors, sizes, spacing)
3. **Stitch mocks / design comps** — Reference for layout composition and visual direction

Never copy raw values (font sizes, colors, spacing) from Stitch mocks directly. Always cross-reference with this guideline's rules and global.css tokens.
If design intent suggests deviating from this guideline's rules, confirm with the user before implementing.

---

## 1. Color

### Color System

- **Primary**: Brand identity, CTA buttons, priority UI elements
- **Secondary**: Complements primary; adjust lightness within same hue
- **Tertiary**: Opposite lightness direction from secondary
- **Background**: White/black as base. Photos, gradients, etc. as design requires

### Primitive Colors

10 hue families (Blue, Light Blue, Cyan, Green, Lime, Yellow, Orange, Red, Magenta, Purple) + 3 neutrals (White, Black, Gray). Each hue has 13 shades.

### Grayscale Reference

| Token | Usage |
|---|---|
| Gray-420 | 3:1 against white background |
| Gray-536 | 4.5:1 against both white and black backgrounds |
| Gray-600 | 3:1+ against black background |

### Semantic Colors

| Meaning | Color | Usage |
|---|---|---|
| Success | Green family | Success, safety, completion |
| Error | Red family | Failure, danger, constraint |
| Warning | Yellow/Orange family | Warning, prohibition |

### Link Colors

- Default (unvisited): Blue
- Visited: Purple (with slight red for color-blind accessibility)

### Contrast Ratios

DADS baseline values:

- Text / text images: **4.5:1** against background
- Large text (24px+ or 18px+ bold): **3:1** against background
- UI components: **3:1** against adjacent colors
- Decorative text, overlay text on photos, etc.: confirm with user if deviation seems justified

---

## 2. Typography

### Font Family

Define project-specific font families in `global.css`. General guidance:

| Type | DADS Principle | Notes |
|---|---|---|
| Serif (headings) | — | Choose a display font; include Japanese fallback stack |
| Sans-serif (body) | Noto Sans JP | Japanese web fonts are large; system font fallback is acceptable |
| Monospace | Noto Sans Mono | For code content |

**Japanese fallback stack**: `"Yu Gothic Medium", "游ゴシック Medium", YuGothic, "游ゴシック体", "ヒラギノ角ゴ Pro W3", "メイリオ"`

- Include the Japanese fallback in serif variables too, to prevent Japanese text from falling back to generic serif (e.g., MS Mincho)

### Proportional Metrics

- Set `font-feature-settings: "palt"` on `body` to enable proportional alternate spacing for Japanese text
- Reduces unnatural whitespace around punctuation

### Font Weights

DADS defines 2 levels (Normal/Bold). Projects may adopt up to 4 levels for visual contrast:

| Token | Value | Usage |
|---|---|---|
| `--font-weight-light` | 300 | Supporting text, blockquotes |
| `--font-weight-normal` | 400 | Body text, standard content |
| `--font-weight-semibold` | 600 | Labels, CTAs, emphasis |
| `--font-weight-bold` | 700 | Maximum emphasis |

### Font Sizes

| Size | Usage |
|---|---|
| 48-64px | High-impact visual elements |
| 16-45px | Headings and body content |
| 14px | Constrained spaces (footer, etc.) |
| 12-13px | Captions, meta info (use only when design requires) |

### Line Height

| Value | Usage |
|---|---|
| 100% | Single-line UI components |
| 120% | High-density information display |
| 130% | Compact admin/system screens |
| 140% | Large headings |
| 150% | Minimum for standard body text |
| 160% | Standard body text |
| 170-175% | Body text with reduced cognitive load |

### Typography Tokens

Naming: `[Style]-[Size][Weight]-[LineHeight]` (e.g., `Std-17N-170`)

| Style | Usage | Characteristics |
|---|---|---|
| Display (Dsp) | High-impact headers | 48-64px, line-height 140% |
| Standard (Std) | Headings/body | 16-45px, letter-spacing 0-2% |
| Dense (Dns) | Data tables/admin | 14-17px, line-height 120-130% |
| Oneline (Oln) | Single-line UI | 14-17px, line-height 100%, letter-spacing 2% |
| Mono | Code content | 14-17px, line-height 150%, monospace font |

### Rules

- Body text: 16px minimum (supporting text is exempt)
- Never apply italic to Japanese text (synthetic slant degrades readability)
- Maintain consistent typography application across the site

---

## 3. Icons

### 4 Placement Specs

| Type | Position | Description |
|---|---|---|
| Front icon | Start of block-level box | CSS block-level box start |
| Lead icon | Start of line box | Part of text sequence or flexbox parallel |
| Tail icon | End of line box | Part of text sequence or flexbox parallel |
| End icon | End of block-level box | CSS block-level box end |

### Rules

- Lead and tail icons are part of the text sequence by default
- Use flexbox parallel placement when layout structure requires it
- Meaningful icons: provide alt text or aria-label. Decorative icons: `aria-hidden="true"`

---

## 4. Layout

### Breakpoints

| Device | Viewport |
|---|---|
| Mobile / Tablet | < 768px |
| Desktop | >= 768px |

### Grid System

- **12-column** grid as standard
- **Gutter**: 2x body font size
- **Column**: Integer multiple of body font size
- **Margin**: Space between grid edge and page edge

### Column Layout Patterns

| Pattern | Examples |
|---|---|
| 1-column | Full grid width |
| 2-column | 9:3, 3:9, 8:4, 4:8, 6:6 |
| 3-column | 3:6:3, 4:4:4 |
| 4-column | Equal (3 columns each) |
| Offset | Content-focused placement |

### Rules

- Consistent layouts improve learning efficiency and user experience
- Left menu + 12-column configurations are supported
- Side-by-side grid children must stretch to equal height: use `display: flex; flex-direction: column` on grid cells and `flex: 1` on the child component root

---

## 5. Link Text

### Interaction States

| State | Style |
|---|---|
| Default | Blue text + underline |
| Hover | Lighter blue, thicker underline. **No size change** |
| Focus | Black outline (4px), yellow background, yellow ring |
| Active | Orange text + underline |
| Visited | Magenta / red-purple |

### Underline

- Underline offset: `underline-offset: calc(3/16 * 1rem)`
- Icon areas excluded from underline but included in clickable zone

### External Links & File Types

- New tab links: Add icon at link end with alt text indicating new tab
- File links: Include format and size in link text (e.g., "PDF: 100KB")

### Rules

- Never change font size or weight on hover (causes layout shift)
- Use combination of underline, color, and icons for link distinction

---

## 6. Spacing

### Base Unit

- Standard base unit: **8 CSS px**
- Define 3-5 scale variations in the style guide

### Scale Example (3 levels)

| Scale | Value | Multiplier |
|---|---|---|
| Small | 8px | 1x |
| Medium | 24px | 3x |
| Large | 64px | 8x |

### Padding vs Margin

- **Padding**: Internal spacing. Expands element box size
- **Margin**: External spacing between elements. Note vertical margin collapsing

### Design Principles

- **Consistency**: Same spacing for similar elements
- **Modular scale ratio**: Maintain proportional relationships
- **Importance-based**: Larger for primary content, smaller for supporting content
- **Relationship expression**: Small gap = related content, large gap = different groups
- **Responsive**: Scale appropriately with viewport size

---

## 7. Corner Shapes

### 5 Border Radius Levels

| Style | Square | Rectangle | Description |
|---|---|---|---|
| None | 0px | 0px | Sharp corners |
| Small | 8px | 8px | Subtle rounding |
| Medium | 16px | 12px | Moderate rounding |
| Large | 32px | 16px | Strong rounding |
| Full | 50% of size | 50% of height | Circle/ellipse |

### Rules

- Smaller elements appear more rounded; adjust visually by size
- Cards/buttons with different aspect ratios may need individual adjustment
- Use radius changes to visually emphasize specific elements
- Apply radius to specific corners based on component usage

---

## 8. Elevation

### Level Structure

| Level | Description |
|---|---|
| Level 0 | Default. Flat on background surface |
| Level 1-8 | Progressive visual depth |

### Visual Representation

- **Drop shadow**: 8 shadow styles (Style 1-8) for pseudo-3D effect
- **Overlay shade**: Transparent background for modal dialogs; blocks interaction with elements below

### Elevation Principles

| Principle | Description |
|---|---|
| High difference | Use in spacious UI. For emphasis and hover effects |
| Low difference | For dense information layouts. Minimize visual noise |
| Hover state | Reserve at least **+1 level** above resting state |
| Modal overlay | At least **+2 levels** above background content |

### Overlay Shade Rules

- Position at the highest existing component level
- Reset elevation baseline to 0 above the shade
- Components on shade recalculate elevation from new baseline
- Non-clickable; must contain a close trigger

### Note

Drop shadows cannot ensure contrast ratios. Use borders or background color differences when boundary clarity is needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khaym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
