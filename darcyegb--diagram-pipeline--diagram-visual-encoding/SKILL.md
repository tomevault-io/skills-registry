---
name: diagram-visual-encoding
description: > Use when this capability is needed.
metadata:
  author: darcyegb
---

# Visual Encoding for Diagrams

This skill bridges content analysis ("what to show") and graphic design ("how to make
it look right"). Its job is to decide the *visual form* — which perceptual channels
encode which data dimensions, which composition pattern organizes them, and how the
spatial layout guides the viewer's eye.

The intellectual foundation comes from three traditions:

**Tufte** — the ethic. Data-ink ratio, graphical integrity, chartjunk avoidance.
Every mark on the diagram should earn its place by encoding data or providing
necessary structure. Decoration is cost, not value.

**Cleveland & McGill** — the science. Their perceptual experiments ranked which
visual channels humans decode most accurately (position > length > angle > area >
color). This ranking determines which channel gets the most important dimension.

**Bertin** — the taxonomy. His Semiology of Graphics identified the complete set
of visual variables (position, size, shape, value, color, orientation, texture) and
which data types each can represent. This gives us the vocabulary for encoding decisions.

## When This Skill Runs

This skill consumes the YAML specification produced by `diagram-content-analysis`
and produces a visual design plan that `diagram-graphic-design` consumes. Think of
it as the structural engineering between the architect (content) and the interior
designer (graphic design).

## Input

The content specification from Skill 1, containing:
- Story (type, message, audience)
- Dimensions (ranked by priority, classified by type)
- Relationships (typed connections)
- Concepts (for educational diagrams — text content)
- Constraints (standalone, medium, page size)

If you don't have a formal spec, you can work from whatever the user provides —
but flag what's missing and what assumptions you're making.

## Core Workflow

### Phase 1: Review the Spec and Identify Encoding Needs

Read the content spec and extract what needs to be visually encoded:

For each **primary and secondary** dimension, note:
- What type it is (quantitative, categorical, relational, ordinal, conceptual)
- What values or range it spans
- How precisely the viewer needs to read it (approximate or exact?)

For **tertiary** dimensions, note whether they'd benefit from subtle encoding
or are better served as annotation text.

For **relationships**, note:
- Are they directional (flow, causation) or undirected (association)?
- Are there many-to-many connections or is the structure mostly tree/chain-like?
- Do relationships carry attributes (labels, weights)?

**Conceptual dimensions** (frameworks, taxonomies, abstract ideas) are special:
they can't be "encoded" in the Cleveland & McGill sense. Instead, the spatial
arrangement itself IS the encoding — proximity means relatedness, position means
sequence or hierarchy, containment means belonging. Note which concepts need spatial
encoding and what their relationships imply about arrangement.

### Phase 2: Map Dimensions to Visual Channels

This is the core decision. Assign each priority dimension to the most effective
visual channel available for its data type.

Read `references/channel-effectiveness.md` for the complete ranking tables.
The key principle: **primary dimension gets the most effective available channel.
Secondary gets the next best that doesn't conflict.**

**Assignment rules:**

1. Two dimensions CANNOT share a channel. Color hue can't encode both "category"
   and "importance." Width can't encode both "volume" and "significance."

2. Channels must be **visually independent** — the viewer must be able to read
   each encoding without the other interfering. Length and position on a common
   scale are independent. Color hue and color luminance are NOT independent (changing
   one affects the perception of the other).

3. Match the channel's **precision** to the dimension's **reading need.** If the
   viewer needs exact comparison, use position on a common scale (most precise).
   If approximate comparison suffices, area or color intensity may work.

4. Respect the channel's **capacity.** Color hue distinguishes ~5-7 categories.
   Shape distinguishes ~4-5. Don't assign a 12-category dimension to color hue.

**For quantitative dimensions**, prefer (most to least accurate):
Position on common scale → Length/width → Angle/slope → Area → Color luminance

**For categorical dimensions**, prefer:
Spatial grouping → Color hue → Shape → Line style

**For relational dimensions**, prefer:
Connection (line/edge) → Containment (nesting) → Proximity

**For ordinal dimensions**, prefer:
Spatial position (left→right, top→bottom) → Color luminance gradient

Write out the assignment explicitly:
```
PRIMARY: [dimension] → [channel] because [reasoning]
SECONDARY: [dimension] → [channel] because [reasoning]
TERTIARY: [dimension] → [channel] or ANNOTATION
```

### Phase 3: Verify Dynamic Range

**This phase catches the most common encoding failure.** A visual channel must
have enough dynamic range *at the diagram's actual scale* to show the differences
that matter.

Read `references/channel-effectiveness.md` Section 3 for the full verification
procedure. The short version:

**For length/width encoding:**
Calculate the pixel width of the smallest and largest values at diagram scale.
The ratio must be ≥ 2:1. If 2M events/day maps to 28pt width and 0.6M maps to
8pt width, that's 3.5:1 — it works. If the ratio drops below 2:1, the encoding
fails and viewers can't distinguish values.

**For area encoding:**
Same calculation but the minimum ratio is ≥ 4:1 because humans perceive area
poorly (Stevens' power law: perceived area ≈ actual area^0.7). A 2:1 area ratio
looks like a 1.6:1 difference. At small diagram scales, area encoding almost
always fails — circles representing $200/mo vs $2,000/mo had radii of 2px vs 7px
in a real iteration. The encoding was invisible.

**For color luminance:**
Maximum 5 distinguishable levels. If the dimension has more than 5 values,
either bin them or switch channels.

**For color hue:**
Maximum 5-7 distinguishable hues. Beyond that, viewers confuse similar colors.

**If dynamic range fails:** Switch to a higher-precision channel (area → length,
color → position) or increase the diagram's physical size to give the channel
more room. Document the failure and the fix.

### Phase 4: Select Composition

With dimension→channel mappings decided, select a composition pattern that
naturally supports those channels.

Read `references/compositions.md` for the full gallery. Selection logic:

**If the primary encoding is...**

Flow + volume → **Sankey / band diagram.** Band width IS the data. Volume
differences visible without reading labels.

Hierarchy + grouping → **Layered architecture.** Vertical layers encode hierarchy.
Background regions encode grouping. Connectors encode flow between layers.

Sequence + decisions → **Flowchart.** Position encodes sequence. Diamond shapes
encode decision points. Branches encode outcomes.

Comparison across categories → **Small multiples.** Same visual repeated for each
category. Direct comparison via spatial alignment.

Two continuous variables → **Scatter plot.** Position on two independent axes.

Part-of-whole proportions → **Stacked bar or waffle chart.** Length/area within
a whole.

Change between two states → **Slope chart.** Angle of connecting line IS the
change. Crossing lines highlight rank reversals.

Network + node sizing → **Bubble network.** BUT verify area dynamic range first —
this composition fails more often than it works at typical diagram scales.

Categorical × categorical → **Matrix / heatmap.** Two spatial axes for categories,
color for the quantitative value.

Transformation over stages → **Shape progression.** Shape morphology encodes data
state (scattered dots → organized rows → grid → chart shapes).

Multiple series over time → **Multi-series line chart.** Position on two axes,
color for series identity. Area fill between lines encodes spread/gap.

Conceptual categories + relationships → **Card-based framework.** Spatial grid
for concepts, arrows for relationships, text layers for progressive disclosure.

**If no single composition fits**, compose elements from multiple patterns. A
layered architecture with embedded Sankey bands. A card framework with embedded
mini-charts. The composition is a container, not a straitjacket.

### Phase 5: Define Spatial Layout

How the composition occupies space. This determines reading order and visual
hierarchy before any graphic design decisions.

**Flow direction:** Most diagrams flow left-to-right (temporal, process) or
top-to-bottom (hierarchical). Choose based on what the primary dimension implies.
Left-to-right suggests process or time. Top-to-bottom suggests hierarchy or
importance. Grid suggests peer comparison.

**Grouping structure:** How elements cluster. Grid (regular spacing for peers),
tree (branching for hierarchy), network (organic for associations), rows/columns
(for sequential stages).

**Reading order:** Where does the eye enter? Where does it exit? For Western
audiences, top-left is the default entry point. The primary dimension should be
readable along the dominant scan path. If the story is "volume decreases across
stages," the volume encoding should be visible in a single left-to-right sweep.

**Density and spacing intent:** Which areas of the diagram should feel dense
(lots of information, close inspection rewarded) versus sparse (breathing room,
transition between sections)? This guides Skill 3's grid decisions without
dictating specific measurements.

**Entry and exit points:** What's the first thing the viewer sees? (Entry.)
What's the last thing — the takeaway? (Exit.) The entry point should present
the story. The exit point should reinforce it or provide the "so what."

### Phase 6: Apply Tufte's Principles as Audit

Before finalizing, audit the design plan against Tufte's core principles:

**Data-ink ratio:** What fraction of the visual marks in this plan encode data
or necessary structure? If you're planning decorative borders, background
gradients, or ornamental elements, challenge each one. The best diagrams have
data-ink ratios approaching 1.0 — almost every mark serves a purpose.

**Graphical integrity:** Does the visual representation match the data
proportionally? If a value doubles, does the visual encoding double? Watch for:
- Area encodings that distort (3D effects, perspective)
- Truncated axes that exaggerate differences
- Broken scales that misrepresent ratios
- Dual axes that create false correlations

**Lie factor:** Size of effect shown in graphic ÷ size of effect in data.
Should be close to 1.0. A lie factor of 2.0 means the graphic exaggerates the
effect by 2×. Common in bubble charts where radius (not area) scales with data.

**Chartjunk audit:** Identify every planned visual element that doesn't encode
data. Grid lines? Usually necessary (keep them subtle). Decorative borders?
Almost never justified. Background tints that don't encode grouping? Remove.
3D effects? Always remove. Drop shadows? Remove unless they encode layering.

**Small multiples consideration:** Could the diagram be more effective as
small multiples? If you're comparing the same structure across 3-8 instances,
small multiples often beat a single complex diagram. Each instance is simpler,
and the viewer's eye makes the comparison automatically.

### Phase 7: Produce the Visual Design Plan

Output a plan with two parts: a human-readable rationale and a structured
specification for Skill 3.

**Human-readable section:**

```
## Composition
[Which pattern and why]

## Channel Assignments
- PRIMARY: [dimension] → [channel] — [why this channel]
- SECONDARY: [dimension] → [channel] — [why this channel]
- ANNOTATIONS: [what appears as text only]

## Dynamic Range Verification
- [channel]: [min value] → [min visual size], [max value] → [max visual size],
  ratio [X:1] — [PASS/FAIL]

## Spatial Layout
- Flow direction: [left-to-right / top-to-bottom / grid]
- Grouping: [structure type]
- Entry point: [what the eye hits first]
- Reading path: [how the eye moves through]

## Tufte Audit
- Data-ink assessment: [what's earned, what's suspect]
- Integrity check: [any distortion risks]
- Chartjunk: [anything to remove]
```

**Structured specification** (YAML, for Skill 3):

```yaml
composition:
  type: sankey | layered | flowchart | small_multiples | slope | matrix |
        shape_progression | timeline | treemap | line_chart | card_framework |
        scatter | custom
  flow_direction: left_to_right | top_to_bottom | grid | radial
  notes: "any composition-specific guidance"

channel_assignments:
  - dimension: "dimension_name"
    channel: position | length | width | angle | area | color_hue |
             color_luminance | shape | connection | containment | proximity |
             spatial_position | line_style
    priority: primary | secondary | tertiary
    precision_needed: exact | approximate | categorical
    dynamic_range:
      min_value: 600000
      max_value: 2000000
      min_visual: "8pt"
      max_visual: "28pt"
      ratio: "3.5:1"
      verdict: pass | fail
      fallback: "alternative channel if fail"

spatial_layout:
  entry_point: "description of what eye hits first"
  reading_path: "how the eye moves through the diagram"
  grouping_structure: grid | tree | network | linear | layered
  density_zones:
    - region: "description"
      intent: dense | sparse | transitional
  element_count:
    primary_nodes: 5
    secondary_nodes: 0
    connections: 4
    annotation_labels: 8

tufte_audit:
  data_ink_assessment: "summary"
  integrity_risks: ["list of potential distortions"]
  chartjunk_removed: ["list of decorative elements eliminated"]
  small_multiples_considered: true | false
  small_multiples_rationale: "why or why not"
```

## Composition Interactions with Skill 3

This skill decides the structural form. Skill 3 decides how to render it.
The boundary:

**Skill 2 decides:**
- Sankey (not flowchart, not layered architecture)
- Band width encodes volume (not color, not position)
- Left-to-right flow (not top-to-bottom)
- 5 stages with 4 transitions
- Direct labeling (not a separate legend)

**Skill 3 decides:**
- Band opacity (75%? 80%?)
- Color palette for bands
- Font family and sizes for labels
- Grid unit and spacing constants
- Label positioning (on the band? adjacent? how many points of clearance?)
- Stroke weights for borders and connectors

There's a gray zone around spatial arrangement — Skill 2 says "grid layout
for 4 concept cards" and Skill 3 decides "2×2 at 240×156pt with 36pt gutters
on a 12pt grid." The split is: Skill 2 owns the topology (how many, what
grouping), Skill 3 owns the geometry (exact dimensions, spacing, alignment).

## References

- **`references/channel-effectiveness.md`** — Cleveland & McGill's full ranking
  tables, Bertin's visual variables, dynamic range verification procedures,
  capacity limits per channel. The primary lookup table for Phase 2.
- **`references/compositions.md`** — Gallery of proven composition patterns:
  what they encode, when they work, when they fail, and design notes. Stripped
  of implementation code (that lives in the rendering skill). The primary lookup
  table for Phase 4.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darcyegb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
