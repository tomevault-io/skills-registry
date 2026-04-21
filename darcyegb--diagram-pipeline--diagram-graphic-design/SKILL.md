---
name: diagram-graphic-design
description: > Use when this capability is needed.
metadata:
  author: darcyegb
---

# Graphic Design for Diagrams

This skill takes a structural plan (which composition, which channels encode what,
how elements are spatially arranged) and makes it visually excellent. It doesn't
change what's encoded or which composition is used — it governs how the chosen form
is rendered.

The difference between a diagram that communicates and one that communicates
*beautifully* is entirely in this layer: the grid that creates invisible order,
the type system that guides the eye, the color palette that encodes meaning without
decoration, the spacing that makes grouping unambiguous.

## Theoretical Foundations

Four traditions inform this skill. Understanding the *why* behind each rule matters
more than memorizing the rule itself — the theory lets you adapt when the specific
rules don't quite fit.

### Gestalt Perception (Wertheimer, Koffka, Köhler)

The human visual system organizes what it sees automatically, before conscious
thought. Gestalt principles describe these pre-cognitive grouping mechanisms:

**Proximity** — elements near each other are perceived as a group. This is the
strongest grouping signal and the foundation of diagram layout. A 12pt gap between
a title and its explanation says "these belong together." A 36pt gap before the
next card says "new group." The viewer doesn't think about it; they just perceive
the grouping. Implication: spacing IS meaning. Every gap encodes a relationship.

**Similarity** — elements that look alike are perceived as related. Same size, same
color, same shape = same type of thing. Break similarity only to signal a different
functional role — and make the break obvious (not 10.5pt vs 11pt, but 9pt vs 16pt).
Implication: consistency within a type is mandatory. Inconsistency is a signal.

**Closure** — the mind completes incomplete shapes. A card border doesn't need to
be a solid rectangle; three sides and a gap are enough. A connector doesn't need an
explicit arrowhead if the layout implies direction. Implication: you can encode with
less visual weight than you think, because the viewer's mind fills in the rest.

**Figure-ground** — the viewer separates foreground from background. Content
elements (cards, labels, data marks) are figure. Background regions, subtle tints,
and grid structure are ground. When ground becomes too prominent (heavy borders,
saturated background fills), it competes with figure and the diagram feels cluttered.
Implication: background elements should be as quiet as possible.

**Continuity** — the eye follows smooth paths. Aligned edges, consistent baselines,
and linear connectors guide the eye smoothly. Jagged alignment and irregular spacing
break the reading flow. Implication: alignment is not aesthetic preference — it's a
perceptual mechanism for guiding attention.

### Typography (Ellen Lupton, *Thinking with Type*)

Type is not decoration. It's a system of visual signals that encode hierarchy,
grouping, and emphasis.

**Type as hierarchy:** Size, weight, and color work together to signal importance.
The reader should know what's most important, second most important, and least
important from the typography alone — before reading a single word.

**Scale ratios:** Just as musical intervals create harmony because the frequencies
are mathematically related, type sizes create visual rhythm when they follow a
consistent ratio. A Major Third (1.25×) or Minor Third (1.2×) ratio produces
sizes that feel naturally related. Arbitrary sizes feel dissonant.

**The weight system:** A type family's weights (Light, Regular, Medium, Bold) are
designed as a system. Three weights give you three clear hierarchy levels. Adding
a fourth can work if each serves a distinct, non-adjacent function. More than four
creates distinctions the eye can't reliably make at body sizes.

**Spacing as content:** The space between letters (tracking), between lines (leading),
and between text blocks (margins) carries meaning. Tight tracking on uppercase text
crowds the letterforms. Generous leading on multi-line text prevents line-skipping.
These aren't preferences — they're functional requirements with perceptual thresholds.

### Grid Systems (Josef Müller-Brockmann)

The grid is the invisible infrastructure that creates visual order. Viewers never
see the grid, but they perceive its presence as coherence and its absence as chaos.

**The base unit:** Every spatial measurement in a diagram — margins, gutters, padding,
element dimensions — should be a multiple of a single base unit (typically 8pt or 12pt).
This constraint produces visual harmony because all proportions are related. Off-grid
placement by a few points here and there registers as sloppiness even though nobody can
articulate why.

**Margins and gutters:** Margins (page-edge breathing room) should be at least as
generous as gutters (inter-element spacing). When gutters exceed margins, content feels
like it's spilling off the page.

**Content density:** Content should fill 70-85% of the available area. Below 70% feels
empty. Above 85% feels cramped. For fixed page sizes, work backward from dimensions to
determine the maximum content rectangle.

**The grid serves the content, not the reverse.** When content doesn't fit the grid
cleanly (an odd number of cards, a diagram that's wider than it is tall), adjust the
grid — don't distort the content. Müller-Brockmann's grids are starting points, not
prisons.

### Color Theory (Josef Albers, Johannes Itten)

Color in information design is functional, not decorative. Every color decision should
answer: "What information does this color communicate?"

**Albers' interaction of color:** Colors don't exist in isolation — they change in the
presence of their neighbors. A gray square on a white background looks darker than the
same gray on a black background. Implication: test colors in context, not in swatches.

**Value (lightness) before hue:** In Itten's framework, value does more work than hue.
Dark-on-light creates hierarchy. Light text on dark backgrounds draws attention.
Value contrast is what makes text readable (WCAG 4.5:1 minimum). Hue is for
categorization; value is for hierarchy.

**The 60-30-10 rule:** 60% dominant (white/near-white background), 30% secondary
(grays for text, borders, structural elements), 10% accent (one saturated hue for
emphasis). When accent exceeds ~10%, it stops functioning as emphasis — it becomes
just another color. Restraint is what makes the accent powerful.

**Simultaneous contrast:** A blue accent strip next to a gray card will make the gray
appear slightly warm (yellowish). A warm orange next to neutral gray will make the gray
appear cool (bluish). This matters when grays are used for hierarchy — the "same gray"
can look different depending on what's next to it. Verify by checking colors in their
actual layout context.

## Core Workflow

### Phase 1: Establish the Grid

Given the composition and element count from Skill 2, design the grid system.

**Step 1: Choose the base unit.** 8pt for compact diagrams (many elements, screen
display). 12pt for spacious diagrams (fewer elements, print, presentations). The
base unit determines everything else.

**Step 2: Define margins and gutters.**
```
Margins (page edge): 3-4 × base unit minimum
Gutters (between elements): 2-3 × base unit
Internal padding (within elements): 1-2 × base unit
```
Rule: margins ≥ gutters ≥ internal padding. This creates three spatial levels
that encode containment hierarchy.

**Step 3: Calculate element dimensions.** Work from the page/artboard size inward:
```
Available width = artboard width - 2 × margin
Element width = (available width - (cols-1) × gutter) / cols
Element height = derived from content needs (text lines, internal spacing)
```
Round element dimensions to the nearest base unit.

**Step 4: Verify content density.** Calculate total content area / artboard area.
Should be 0.70-0.85. If too sparse, reduce margins or increase element size. If
too dense, add margins or reduce content (go back to Skill 1 and cut more).

**Step 5: Check Gestalt proximity ratios.** Intra-group spacing (within a card or
node) should be ≤ half of inter-group spacing (between cards or nodes). This
ensures proximity-based grouping reads correctly.

Output: A grid specification with base unit, margins, gutters, element dimensions,
all as multiples of the base unit.

### Phase 2: Design the Type System

**Step 1: Choose the type family.** For diagrams, sans-serif families with clear
weight differentiation work best. Helvetica Neue, Inter, and system sans-serifs
are reliable. One family only — introducing a second family is rarely justified
and creates cognitive overhead.

**Step 2: Define the scale.** Pick a ratio and derive all sizes from the base:

```
Ratio: 1.22× (Minor Third) — good for compact diagrams
Ratio: 1.25× (Major Third) — good for spacious diagrams
Ratio: 1.33× (Perfect Fourth) — good for presentations

Example (base 9pt, ratio 1.22×):
  9pt   body text, annotations, levers
  11pt  card titles, section labels (9 × 1.22)
  13pt  (usually skipped — reserved for emphasis if needed)
  18pt  page title (9 × 2, or approximately 9 × 1.22³)
```

Every text element must use a size from this scale. No exceptions.

**Step 3: Assign weights to hierarchy levels.**

```
Level 1: [largest size] + Bold      → page title, entry point
Level 2: [medium size] + Bold       → section/card titles
Level 3: [base size] + Regular      → body text, explanations
Level 3b: [base size] + Medium      → insights, emphasis (accent color)
Level 4: [base size] + Regular      → secondary text (mid-gray)
```

Maximum 3-4 levels. Each level must be visually distinct from adjacent levels
at their rendered size. If two levels look the same when rendered, merge them.

**Step 4: Set spacing.**
- Leading (line spacing): ≥ 1.3× font size. For 9pt text, ≥ 12pt leading.
- Tracking for ALL-CAPS: +50 to +120 (uppercase letterforms need room)
- Tracking for body text: 0 (default)
- Line length for multi-line text: 45-75 characters (optimal: 66)

Output: Type specification with family, scale ratio, sizes, weights, leading,
tracking, and the hierarchy level each combination serves.

### Phase 3: Design the Color Palette

**Step 1: Determine functional color roles.** Every color in the diagram must
have a job:

```
Background:     white or near-white (rgb 255,255,255 or 249,250,251)
Primary text:   dark gray (rgb 31,41,55)    — contrast ≥14:1 on white
Secondary text: mid-gray (rgb 107,114,128)  — contrast ≥4.5:1 on white
Accent:         one saturated hue           — contrast ≥4.5:1 on white
Structure:      light gray for borders, rules, gridlines
Grouping tint:  very light tint for background regions (if needed)
```

**Two text grays only.** Primary dark for text that matters. Mid-gray for
secondary text. A third gray creates distinctions the eye can't reliably
perceive at body sizes.

**Step 2: Choose the accent hue.** The accent does double duty: it signals
emphasis AND encodes one categorical dimension (if Skill 2 assigned color
hue to a dimension). If color hue encodes a category, the accent hue is
that encoding. If not, choose a hue that contrasts with the gray palette
without being aggressive. Blues and teals are safe defaults.

**Step 3: Verify contrast ratios.** WCAG AA minimum:
```
Normal text (< 14pt bold, < 18pt regular):  ≥ 4.5:1
Large text (≥ 14pt bold, ≥ 18pt regular):   ≥ 3:1
```

Check every text color against its background. Common failure: mid-gray
text on a tinted background drops below 4.5:1. Common fix: darken the text
or lighten the background.

**Step 4: Check the 60-30-10 distribution.** Visually estimate: is the accent
(saturated hue) ≤ 10% of visual area? If it appears on backgrounds, borders,
or body text, it's overused and no longer functions as emphasis.

**Step 5: Verify grayscale readability.** Convert the palette to grayscale. If
any two elements that should be distinguishable become indistinguishable, add
a non-color differentiator (position, shape, weight, or text label).

Output: Color palette with RGB values, functional role for each color, contrast
ratios verified, 60-30-10 distribution confirmed.

### Phase 4: Define Element Styling

**Step 1: Stroke weight hierarchy.** Maximum 4 levels, each serving a distinct
function:

```
0.25-0.5pt    structural rules (separators, gridlines)
0.5-1pt       containment borders (card outlines, region boundaries)
1-2pt         relational connectors (arrows, flow lines)
2-3pt         emphasis elements (accent lines, pull-quote bars)
```

**Step 2: Corner radius.** If using rounded rectangles, one radius value for all
elements. Typically 4-6pt. Consistency matters more than the specific radius.

**Step 3: Label positioning rules.** Labels within 4pt of their parent element.
Arrow labels on the arrow line, not floating in gutters. The Gestalt proximity
principle: if a label is equidistant from two elements, it belongs to neither.

**Step 4: Consistency audit.** Same-type elements (cards, nodes, connectors) must
have identical treatment — same dimensions, same stroke weight, same internal
spacing, same type treatment. Break similarity only to signal a different
functional role, and make the break obvious (change 2+ attributes, not just one).

Output: Element styling spec with stroke weights, corner radii, label rules,
and consistency requirements.

### Phase 5: Compose and Audit

**Step 1: Assemble the full specification.** Combine grid, type, color, and
element styling into a complete design spec.

**Step 2: Check hierarchy alignment.** Size, weight, color, and position should
all agree about what's most important. If the largest text is at the bottom,
the boldest text is a subtitle, and the accent color highlights a footnote,
the hierarchy signals conflict and the viewer's eye bounces.

**Step 3: Entry point test.** Squint at the planned layout (or imagine it at
25% zoom). Exactly one element should dominate. If two compete, strengthen the
winner or weaken the competitor.

**Step 4: Tufte's data-ink audit (inherited from Skill 2).** Review every planned
visual element: does it encode data, provide necessary structure, or earn its
place through grouping/hierarchy? If not, remove it.

**Step 5: Symmetry decision.** Per section of the diagram, choose symmetrical
(equal treatment = peer status) or asymmetrical (unequal treatment = hierarchy)
balance deliberately. Don't mix within a section.

Read `references/design-rules.md` for the full 24-rule audit checklist.

### Phase 6: Produce the Design Specification

Output the complete specification that the rendering skill consumes.

**Structured specification (YAML):**

```yaml
grid:
  base_unit: 12
  margins: { left: 48, right: 48, top: 48, bottom: 48 }
  gutters: { horizontal: 36, vertical: 24 }
  internal_padding: { horizontal: 12, vertical: 8 }
  artboard: { width: 612, height: 792 }  # letter = 612×792pt
  content_density: 0.78

type:
  family: "HelveticaNeue"
  scale_ratio: 1.22
  levels:
    - level: 1
      size: 18
      weight: Bold
      tracking: 0
      role: "page title"
    - level: 2
      size: 11
      weight: Bold
      tracking: 0
      role: "card/section titles"
    - level: 3
      size: 9
      weight: Regular
      tracking: 0
      role: "body text, explanations"
    - level: 3b
      size: 9
      weight: Medium
      color_override: accent
      role: "insights, emphasis"
    - level: 4
      size: 9
      weight: Regular
      color_override: secondary
      role: "levers, annotations, footer"
  leading_ratio: 1.33
  uppercase_tracking: 80
  max_line_characters: 65

color:
  background: [255, 255, 255]
  primary_text: [31, 41, 55]
  secondary_text: [107, 114, 128]
  accent: [37, 99, 235]
  structure_border: [209, 213, 219]
  structure_rule: [229, 231, 235]
  grouping_tint: [243, 244, 246]
  contrast_verified: true
  grayscale_verified: true

elements:
  stroke_weights:
    structural: 0.5
    containment: 0.75
    connectors: 1.5
    emphasis: 2.5
  corner_radius: 4
  label_max_distance: 4  # pt from parent element
  accent_strip:
    concept_width: 2
    synthesis_width: 4

composition_rules:
  hierarchy_levels: 4
  entry_point: "title, top-left"
  reading_path: "title → first card → arrow → second card → ..."
  balance: "symmetrical for concept grid, asymmetrical for synthesis card"
  data_ink_notes: "no decorative borders, no non-functional tints"
```

## References

- **`references/design-rules.md`** — The full 24-rule audit checklist organized
  by Grid & Layout, Typography, Color, Visual Elements, and Composition. Each
  rule includes the principle, the perceptual reason, and how to verify compliance.
  Run through this after every render.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darcyegb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
