---
name: diagram-svg-render
description: > Use when this capability is needed.
metadata:
  author: darcyegb
---

# Skill 4b: diagram-svg-render

**Pipeline Position:** Skill 4 of 4 (alternative to diagram-illustrator-render)
**Upstream Dependencies:** diagram-visual-encoding (Skill 2), diagram-graphic-design (Skill 3)
**Output:** SVG file (.svg), optionally PNG via Python conversion
**Execution Context:** Direct file write — no application required

---

## Input

This skill consumes the same two specifications as diagram-illustrator-render:

### From diagram-graphic-design (Skill 3)
A YAML design specification: grid (artboard dimensions, base unit, margins, gutters),
type system (fonts, scale with sizes/weights/leading), color palette (RGB values),
element styles (stroke weights, corner radii, spacing tokens), and composition rules.

### From diagram-visual-encoding (Skill 2)
A visual encoding plan: composition type, channel assignments, and spatial layout
with positioned elements.

---

## SVG Coordinate System

SVG uses a top-left origin with Y increasing downward — the same mental model as
CSS. This is simpler than Illustrator's bottom-left origin.

```
(0,0) ────────→ X
  │
  │   SVG drawing area
  │
  ↓
  Y
```

The design spec's "from-top" distances translate directly: an element "40pt from top"
goes at `y="40"`. No coordinate flipping needed.

### ViewBox and Dimensions

Set the SVG viewBox to match the artboard dimensions from the design spec:

```xml
<svg xmlns="http://www.w3.org/2000/svg"
     viewBox="0 0 960 330"
     width="960" height="330">
```

The `width`/`height` attributes set the default rendered size. The `viewBox` makes
the SVG scalable — it will render crisply at any size.

---

## Architecture

SVG rendering is simpler than ExtendScript: write markup as a string, save as a file.

### Workflow

```
Read design spec + visual plan
         ↓
Build SVG string (Python or direct string construction)
         ↓
Write .svg file to workspace
         ↓
Copy to user folder (/mnt/Power/ or equivalent)
         ↓
Read the .svg file with Read tool to evaluate
         ↓
Fix markup if needed, rewrite
         ↓
Optionally convert to PNG via Python
```

### File Output

Write the SVG file to the user's workspace folder so they can open it directly:
```
/sessions/vibrant-peaceful-allen/mnt/Power/diagram_name.svg
```

The SVG renders in the conversation when read with the Read tool (if it's valid SVG).

---

## Core Workflow

### Phase 1: Translate Specification to SVG Structure

Map the design spec to SVG elements:

| Design Spec | SVG Element |
|---|---|
| Grid artboard | `<svg viewBox>` dimensions |
| Rectangle | `<rect x y width height rx />` |
| Rounded rectangle | `<rect ... rx="4" ry="4" />` |
| Circle | `<circle cx cy r />` |
| Line | `<line x1 y1 x2 y2 />` |
| Arrow | `<line>` with `marker-end` reference |
| Point text | `<text x y>content</text>` |
| Wrapped text | `<foreignObject>` with HTML `<div>` |
| Group | `<g>` with optional `transform` |
| Background | First `<rect>` covering full viewBox |

### Phase 2: Build the SVG

Structure the SVG in layers (same z-order principle as Illustrator — earlier elements
render behind later ones):

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 {W} {H}" width="{W}" height="{H}">
  <defs>
    <!-- Arrow markers, gradients, clip paths -->
  </defs>

  <style>
    /* Font families, common styles */
  </style>

  <!-- Layer 1: Backgrounds -->
  <!-- Layer 2: Structural (grids, lines, arrows) -->
  <!-- Layer 3: Visual content (shapes, data elements) -->
  <!-- Layer 4: Text (always last for z-order) -->
</svg>
```

### Phase 3: Write and Preview

1. Write the SVG string to a `.svg` file
2. Read the file back with the Read tool — SVG files render visually
3. Evaluate against the design spec

### Phase 4: Iterate

Fix any issues in the SVG markup and rewrite. Common fixes:
- Text position adjustments (no precise text measurement in SVG)
- Alignment corrections
- Color value fixes
- Missing elements

### Phase 5: Export (Optional)

Convert to PNG if the user needs a raster image:

```python
# Using cairosvg (pip install cairosvg --break-system-packages)
import cairosvg
cairosvg.svg2png(url='diagram.svg', write_to='diagram.png', scale=2)
```

Or use Puppeteer/browser screenshot if cairosvg is unavailable.

---

## SVG Building Patterns

See `references/svg-patterns.md` for complete, copy-ready SVG patterns for every
common element: rectangles, rounded rects, circles, lines, arrows, text, wrapped
text, grids, cards, and composition-specific patterns.

### Key Differences from Illustrator

**Simpler coordinate system.** Y increases downward. "40pt from top" = `y="40"`.
No `H - fromTop` translation needed.

**Declarative arrows.** SVG has native `<marker>` support — define the arrowhead
once in `<defs>`, reference it with `marker-end="url(#arrow)"`. No manual triangle
calculation.

**CSS-based styling.** Colors, fonts, and strokes can use CSS classes. Define
common styles once in `<style>`, apply via `class` attributes. Keeps markup clean.

**No text measurement.** SVG doesn't expose text metrics before rendering. For
centering text, use `text-anchor="middle"` and `dominant-baseline="central"`.
For width estimation, approximate at 0.55× font size per character for Helvetica.

**Wrapped text via foreignObject.** SVG `<text>` doesn't wrap. For paragraph
text, use `<foreignObject>` with an HTML `<div>` inside. This gives full CSS
text layout (word-wrap, line-height, padding).

**Native opacity and blending.** SVG supports `opacity`, `fill-opacity`, and
`stroke-opacity` attributes directly. No need for separate transparency layers.

---

## Python Helper Approach

For complex diagrams, writing SVG via Python gives you string formatting, loops,
and calculations. Structure the script like this:

```python
#!/usr/bin/env python3
"""Generate SVG diagram from design specification."""

W, H = 960, 330  # From design spec

# Color palette (from design spec)
COLORS = {
    'primary': '#1F2937',
    'secondary': '#6B7280',
    'accent': '#2563EB',
    'border': '#D1D5DB',
    'surface': '#F9FAFB',
    'white': '#FFFFFF',
}

# Font stack
FONT = '"Helvetica Neue", "Helvetica", "Arial", sans-serif'

def svg_header(w, h):
    return f'''<svg xmlns="http://www.w3.org/2000/svg"
     viewBox="0 0 {w} {h}" width="{w}" height="{h}">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5"
            markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="{COLORS['secondary']}" />
    </marker>
  </defs>
  <style>
    text {{ font-family: {FONT}; }}
    .title {{ font-size: 22px; font-weight: 700; fill: {COLORS['primary']}; }}
    .subtitle {{ font-size: 11px; fill: {COLORS['secondary']}; }}
    .label {{ font-size: 10px; font-weight: 500; }}
    .caption {{ font-size: 8px; fill: {COLORS['secondary']}; }}
  </style>'''

def rect(x, y, w, h, fill=None, stroke=None, sw=1, rx=0):
    parts = [f'<rect x="{x}" y="{y}" width="{w}" height="{h}"']
    if rx: parts.append(f'rx="{rx}" ry="{rx}"')
    if fill: parts.append(f'fill="{fill}"')
    else: parts.append('fill="none"')
    if stroke: parts.append(f'stroke="{stroke}" stroke-width="{sw}"')
    else: parts.append('stroke="none"')
    parts.append('/>')
    return ' '.join(parts)

def text(content, x, y, css_class='', extra=''):
    esc = content.replace('&', '&amp;').replace('<', '&lt;')
    cls = f' class="{css_class}"' if css_class else ''
    return f'<text x="{x}" y="{y}"{cls} {extra}>{esc}</text>'

def line(x1, y1, x2, y2, color, sw=1, arrow=False):
    marker = ' marker-end="url(#arrow)"' if arrow else ''
    return f'<line x1="{x1}" y1="{y1}" x2="{x2}" y2="{y2}" stroke="{color}" stroke-width="{sw}"{marker} />'

def circle(cx, cy, r, fill=None, stroke=None, sw=1):
    f = f'fill="{fill}"' if fill else 'fill="none"'
    s = f'stroke="{stroke}" stroke-width="{sw}"' if stroke else 'stroke="none"'
    return f'<circle cx="{cx}" cy="{cy}" r="{r}" {f} {s} />'

# Build the SVG
parts = [svg_header(W, H)]
# ... add layers ...
parts.append('</svg>')

svg = '\n'.join(parts)
with open('/sessions/vibrant-peaceful-allen/mnt/Power/diagram.svg', 'w') as f:
    f.write(svg)
print('SVG written')
```

Run this with `python3 script.py`. The helper functions map 1:1 to the design spec
vocabulary, making translation straightforward.

---

## Evaluation Checklist

After rendering, verify against the design spec using the same 24-rule audit
from diagram-graphic-design:

1. **Grid alignment** — Elements snap to grid positions
2. **Type hierarchy** — Sizes, weights, and colors match the type scale
3. **Color accuracy** — RGB values match the palette exactly
4. **Spacing consistency** — All gaps are multiples of the base unit
5. **Arrow markers** — Arrowheads render and point correctly
6. **Text rendering** — No clipping, correct alignment, proper wrapping
7. **Composition** — Elements follow the spatial layout plan
8. **Z-order** — Backgrounds behind content, text on top
9. **Bounding box containment** — Every element fits inside its container (see below)

### Bounding Box Containment Check

This is the single most common source of SVG rendering bugs. After building the
SVG, verify that **every element's bounding box** (including text) stays within
its parent container's bounds. SVG has no overflow clipping by default — elements
that exceed their container will draw on top of neighbors.

**How to check:** For each container (column background, row card, group box),
compute its bounds `(x, y, x + w, y + h)`, then verify every child element:

1. **Text labels beside shapes.** Estimate text width at 0.55 × font-size ×
   character count. Verify `text_x + text_width < container_right`. If a label
   sits to the right of a bar or shape, the shape width + gap + label width must
   fit within the container width.

2. **Child shapes inside cards.** Compute each shape's bottom edge
   (`y + height` for rects, `cy + r` for circles). Verify it doesn't exceed the
   card's bottom edge. Bars anchored to a baseline should grow upward, not
   downward past the card.

3. **Text overlapping adjacent shapes.** When a text label and a shape share a
   row, check that the text's estimated bounding box doesn't intersect the
   shape's bounding box. Shift one or the other if they collide.

4. **Marker overshoot.** Arrow markers extend beyond the line endpoint by
   `markerWidth` units. Account for this when placing arrows near container edges.

**Formula for text-beside-shape containment:**
```
shape_right = shape_x + shape_width
label_right = shape_right + gap + (char_count × font_size × 0.55)
assert label_right <= container_x + container_width
```

If the assertion fails, reduce the shape width to make room:
```
max_shape_width = container_width - gap - label_width - left_padding
```

Run this check on every container before writing the final SVG.

---

## Common Errors

| Error | Cause | Fix |
|---|---|---|
| Text clipped | `<foreignObject>` too small | Increase width/height |
| Missing xmlns | Forgot namespace | Add `xmlns="http://www.w3.org/2000/svg"` |
| Arrows invisible | No marker definition | Add `<marker>` in `<defs>` |
| Text not centered | Using `x` for centering | Use `text-anchor="middle"` |
| Fonts wrong | System font not available | Use fallback font stack |
| Special chars break SVG | Unescaped `<`, `&` | XML-escape text content |
| foreignObject blank | Missing `xmlns` on HTML | Add `xmlns="http://www.w3.org/1999/xhtml"` |
| Label overflows container | Shape + gap + label > container width | Reduce shape width; compute max from container bounds minus label space |
| Child shape exceeds card | Shape bottom edge below card bottom | Scale shape height to fit; anchor to card bottom and grow upward |
| Text overlaps adjacent shape | Text bbox intersects shape bbox | Shift shape rightward or move label; ensure gap between text right edge and shape left edge |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darcyegb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
