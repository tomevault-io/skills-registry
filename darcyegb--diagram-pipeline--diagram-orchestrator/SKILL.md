---
name: diagram-orchestrator
description: > Use when this capability is needed.
metadata:
  author: darcyegb
---

# Diagram Pipeline Orchestrator

Entry point for creating information design diagrams. Routes work through
specialized skills, each handling a distinct layer of the design process.
The first three skills (content → encoding → design) are shared; the final
rendering step branches to either Illustrator or SVG.

## The Pipeline

```
User request (document, concept, data)
        │
        ▼
┌─────────────────────────────┐
│  Skill 1: Content Analysis  │  What to show
│  diagram-content-analysis   │  → Content specification (YAML)
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  Skill 2: Visual Encoding   │  How to encode it
│  diagram-visual-encoding    │  → Visual design plan (YAML)
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  Skill 3: Graphic Design    │  How to make it look right
│  diagram-graphic-design     │  → Design specification (YAML)
└──────────────┬──────────────┘
               │
               ▼
        ┌──────┴──────┐
        │  Renderer?  │
        └──┬───────┬──┘
           │       │
     ┌─────┘       └─────┐
     ▼                    ▼
┌────────────────┐  ┌────────────────┐
│  Skill 4a:     │  │  Skill 4b:     │
│  Illustrator   │  │  SVG Render    │
│  Render        │  │                │
│  → .ai + PNG   │  │  → .svg + PNG  │
└────────────────┘  └────────────────┘
```

Each skill produces a structured YAML specification that the next skill consumes.
The user can approve, modify, or reject the output at each stage.

## Renderer Selection

After Skill 3 completes, choose the renderer:

**Use Illustrator (Skill 4a)** when:
- User explicitly requests Illustrator / .ai output
- User needs editable vector layers with named groups
- User will do further manual editing in Illustrator
- Illustrator is installed and accessible via osascript

**Use SVG (Skill 4b)** when:
- User explicitly requests SVG / "no Illustrator" / "portable"
- Illustrator is not installed or not available
- Output is for web embedding or browser display
- User wants a self-contained file with no app dependencies
- User wants to version-control the diagram (SVG is text-based)

**Default:** If the user doesn't specify, check whether Illustrator is available.
If yes, use Illustrator. If no, use SVG. When in doubt, ask.

## When to Use This Skill

**New diagram from scratch:** User provides source material (document, dataset,
concept description) and wants a diagram. Run the full pipeline.

**Modify an existing diagram:** User identifies a problem. Route to the appropriate
skill based on what layer the problem lives in (see Routing Guide below).

**Explore before committing:** User isn't sure what kind of diagram they want.
Start with Skill 1 to analyze the content, then present options at Skill 2.

## Core Workflow

### Step 1: Assess the Request

Read the user's request and determine:

1. **What source material exists?** Document, dataset, conversation notes, or just
   a concept description? This determines how much Skill 1 needs to do.

2. **How clear is the intent?** "Make a diagram of this chapter" is clear — run the
   pipeline. "Help me visualize something" is ambiguous — interview first.

3. **Are there constraints?** Page size, medium (print/screen/presentation), audience,
   standalone vs. companion to text. Capture these for Skill 1's constraint section.

### Step 2: Run Skill 1 — Content Analysis

Read the `diagram-content-analysis` skill and follow its workflow. Feed it the source
material and constraints.

**Output:** A content specification with story, dimensions, relationships, concepts
(if applicable), written content, and constraints.

**Checkpoint:** Present the content spec to the user. Key questions:
- "Is this the right story? Should the diagram emphasize something different?"
- "Are the priorities right? What's most important to communicate?"
- "Is any important information missing?"

If the user wants changes, iterate within Skill 1. Don't proceed until the content
spec is approved.

### Step 3: Run Skill 2 — Visual Encoding

Read the `diagram-visual-encoding` skill. Feed it the approved content spec.

**Output:** A visual design plan with composition type, channel assignments (which
visual channels encode which data dimensions), dynamic range verification, spatial
layout, and Tufte audit.

**Checkpoint:** Present the visual plan. Key questions:
- "Does this composition type match what you're imagining?"
- "The primary visual encoding is [X] — does that capture the main message?"

This is where the user might say "I was thinking more of a flowchart" or "Can we
show the volume differences more prominently?" Those are Skill 2 decisions.

### Step 4: Run Skill 3 — Graphic Design

Read the `diagram-graphic-design` skill. Feed it the approved visual plan.

**Output:** A complete design specification with grid system, type scale, color
palette, element styling, and composition rules — all as concrete values
(point sizes, RGB values, spacing in grid units).

**Checkpoint:** The design spec is technical. For most users, present a summary:
- "Using a 12pt grid, Helvetica Neue, blue accent (#2563EB)"
- "Letter-size page, 48pt margins, 2×2 card grid with synthesis card"

Most users will approve this quickly. Designers may want to adjust colors, fonts,
or spacing — those are Skill 3 decisions.

### Step 5: Render the Diagram

Choose the renderer based on the Renderer Selection rules above.

#### Step 5a: Illustrator Renderer

Read the `diagram-illustrator-render` skill. Feed it both the visual plan (Skill 2)
and the design spec (Skill 3).

**Output:** A rendered diagram in Illustrator, exported as PNG for review.

**Evaluation loop:** View the PNG. Check against the design spec. Fix rendering
issues (misalignment, wrong colors, text overflow, MRAP errors). Typically 2-4
iterations.

#### Step 5b: SVG Renderer

Read the `diagram-svg-render` skill. Feed it both the visual plan (Skill 2)
and the design spec (Skill 3).

**Output:** An SVG file, optionally converted to PNG for review.

**Evaluation loop:** Read the SVG file to view it. Check against the design spec.
Fix rendering issues (text clipping, missing markers, foreignObject problems).
Typically 2-3 iterations. SVG is declarative, so iteration is faster — no
app launch or osascript round-trips.

**SVG-specific notes:**
- Coordinate system is top-left origin, Y-down (simpler than Illustrator)
- No text measurement available — approximate at 0.55× font-size per character
- Use `<marker>` for arrows instead of manual arrowhead triangles
- Use `<foreignObject>` for wrapped text
- Convert to PNG via `cairosvg` if the user needs raster output

**Final checkpoint:** Show the user the rendered diagram. If they identify issues,
route to the appropriate skill (see below).

### Step 6: Final Export

Once approved:

**Illustrator path:**
- Export PNG at 200% for digital use
- Save .ai for future editing
- Optionally export PDF for print
- Copy to the workspace folder

**SVG path:**
- Save .svg as the primary deliverable
- Optionally convert to PNG via cairosvg at desired resolution
- Optionally export PDF via cairosvg
- Copy to the workspace folder

## Routing Guide: Where Does This Problem Live?

When the user identifies a problem with a diagram, route to the skill that owns
that layer. The diagnostic question: **what kind of change would fix it?**

### Route to Skill 1 (Content Analysis) if:
- "The story is wrong" / "It should emphasize X instead"
- "This concept is missing" / "That concept isn't important"
- "The definitions don't make sense" / "The examples are wrong"
- "There's too much / too little information"
- "The text on the cards needs rewriting"

**Signal words:** story, message, content, concepts, definitions, examples, text,
what it says, missing information, wrong emphasis.

### Route to Skill 2 (Visual Encoding) if:
- "Wrong type of diagram" / "Should be a flowchart, not cards"
- "Can't see the volume differences" / "The sizes don't work"
- "The flow direction is confusing" / "Reading order is wrong"
- "There's too much visual clutter" / "Not enough data-ink"
- "The comparison doesn't work" / "Can't spot the pattern"

**Signal words:** diagram type, composition, chart type, encoding, channel, flow
direction, reading order, can't see the difference, clutter, pattern.

### Route to Skill 3 (Graphic Design) if:
- "Colors are wrong" / "Too many colors" / "Not enough contrast"
- "Font is too small" / "Typography feels off"
- "Spacing is weird" / "Things aren't aligned"
- "Looks unprofessional" / "Feels cluttered but I can't say why"
- "The hierarchy isn't clear" / "Don't know where to look first"

**Signal words:** colors, fonts, spacing, alignment, grid, typography, hierarchy,
professional, polish, clean up, looks off, feels wrong.

### Route to Skill 4a (Illustrator Render) if:
- "Text is behind the boxes" / "Elements are overlapping"
- "The export is blurry" / "Wrong file format"
- "Illustrator threw an error" / "Script didn't work"
- "Positions are off by a few points" / "Rounding errors"
- "Need to re-export at different size"

**Signal words:** rendering, Illustrator, error, export, z-order, overlap, blurry,
positions, script, JSX, MRAP.

### Route to Skill 4b (SVG Render) if:
- "Text is clipped" / "Text overflows the box"
- "Arrows aren't showing" / "Markers are missing"
- "The foreignObject isn't rendering" / "HTML inside SVG is blank"
- "SVG looks different in different browsers"
- "Need PNG instead of SVG" / "Convert to raster"

**Signal words:** SVG, markup, viewBox, marker, foreignObject, text clipping,
xmlns, browser rendering, cairosvg, convert to PNG.

### Ambiguous Cases

Sometimes the user says something like "it doesn't look right" without being specific.
Ask a diagnostic question:

- "Is the *information* wrong (what it shows), or does the *presentation* feel off
  (how it looks)?"
- If information → Skill 1 or 2
- If presentation → Skill 3 or 4

- "Can you see the main message in the first 3 seconds?"
- If no → probably Skill 2 (encoding not working)
- If yes but it's ugly → Skill 3 (graphic design)

## Adaptive Autonomy

**When intent is clear** (user provides a document and says "make a one-page diagram
of the key concepts"): Run the pipeline, present checkpoints briefly, move quickly.
Propose rather than interview.

**When intent is ambiguous** (user says "help me visualize this"): Interview at
Skill 1 to understand what they want to communicate. Present composition options at
Skill 2. Move to rendering only after the design direction is confirmed.

**When iterating** (user has seen a render and wants changes): Identify the layer,
route directly to that skill, and re-render. Don't re-run upstream skills unless the
change cascades (e.g., changing the story in Skill 1 requires re-running 2, 3, and 4).

## Cascade Rules

Changes at an upstream skill may invalidate downstream work:

| Change at | Must re-run |
|-----------|-------------|
| Skill 1 (content) | Skills 2, 3, 4a/4b |
| Skill 2 (encoding) | Skills 3, 4a/4b |
| Skill 3 (design) | Skill 4a or 4b only |
| Skill 4a/4b (render) | Nothing — just fix and re-render |
| Switch renderer | Skill 4a ↔ 4b only (design spec is shared) |

In practice, most iterations after the first render are Skill 3 or 4 changes that
don't cascade. Content and encoding changes are rarer but more expensive.
Switching between Illustrator and SVG is cheap — the design spec feeds both
renderers, so only the rendering step re-runs.

## Quick Start

For the common case — user provides a document and wants a diagram:

1. Read the document
2. Run Skill 1: extract story, dimensions, concepts, write content
3. Present content spec → get approval
4. Run Skill 2: select composition, map channels, verify dynamic range
5. Present visual plan → get approval (brief)
6. Run Skill 3: design grid, type, color, elements
7. Summarize design choices → get approval (usually quick)
8. Choose renderer: Illustrator (4a) or SVG (4b) — see Renderer Selection
9. Run chosen renderer: write JSX/SVG, execute/save, export PNG
10. View render → evaluate → fix → iterate (2-4 cycles)
11. Present final diagram → get approval
12. Export and deliver

## References

Each skill has its own reference documents. Read them when that skill runs:

- **Skill 1:** `references/content-writing.md`, `references/output-format.md`
- **Skill 2:** `references/channel-effectiveness.md`, `references/compositions.md`
- **Skill 3:** `references/design-rules.md`
- **Skill 4a:** `references/extendscript-api.md`, `references/composition-implementations.md`
- **Skill 4b:** `references/svg-patterns.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darcyegb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
