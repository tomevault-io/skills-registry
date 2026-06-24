---
name: arcdeck
description: Convert academic PDF papers into polished, narrative-driven PowerPoint presentations using 13 specialized AI agents orchestrated by Claude Code. Based on Rhetorical Structure Theory (RST) discourse parsing with a multi-agent critique-revise-judge refinement loop. Every slide is visually rich -- text-only slides get programmatic diagrams and charts rendered via PptxGenJS (Node.js). No template PPTX required -- slides are built from scratch with selectable presentation themes and strict layout rules. Use when this capability is needed.
metadata:
  author: RehgLab
---
# ArcDeck: Academic Paper to Presentation Slides

Convert academic PDF papers into polished, narrative-driven PowerPoint presentations using 13 specialized AI agents orchestrated by Claude Code. Based on Rhetorical Structure Theory (RST) discourse parsing with a multi-agent critique-revise-judge refinement loop. Every slide is visually rich -- text-only slides get programmatic diagrams and charts rendered via PptxGenJS (Node.js). No template PPTX required -- slides are built from scratch with selectable presentation themes and strict layout rules.

Agents 10-13 are **Claude Code native** -- Claude Code acts as the LLM directly, following agent specs as instructions with no external API calls needed.

Paper: "Narrative-Driven Paper-to-Slide Generation via ArcDeck"

## Core Principles

1. **Narrative over bullets** -- Slides tell a coherent story guided by RST discourse trees
2. **Commitment-driven** -- A global contract (commitments.md) guides ALL downstream agents
3. **Iterative refinement** -- Narrative critique-revise-judge loop (up to 3 rounds) + aesthetic design critique-refine pass
4. **13 specialized agents** -- Each described in its own `agents/*.md` file (A1-A13)
5. **No text-only slides** -- Every slide has either extracted figures OR programmatic visual elements (diagrams, charts, shapes)
6. **CARP+ design principles** -- Aesthetic pipeline uses Contrast, Alignment, Repetition, Proximity, Content-Visual Alignment, Variety, Information Design
7. **Progressive disclosure** -- Only load agent specs when needed for current phase
8. **Visual variety** -- The model freely chooses from all available PptxGenJS shapes (187+), chart types (10), and diagram layouts to best represent each slide's content

## Invocation

```
/arcdeck <pdf_path> [--audience <type>] [--duration <minutes>] [--template <theme>] [--speaker-notes]
```

Defaults: audience=researchers, duration=20, template=ocean, speaker-notes=off

### Templates (Presentation Themes)

| Template | Style | Title Font | Colors |
|----------|-------|-----------|--------|
| `ocean` | Ocean Gradient — navy/teal/cyan, light content slides | Georgia | `065A82` `1C7293` `0891B2` |
| `minimal` | Clean Minimal — white background, coral accent | Calibri Light | `E63946` `457B9D` `2A9D8F` |
| `dark` | Dark Mode — charcoal backgrounds, violet accent | Trebuchet MS | `7F5AF0` `2CB67D` `FF6B6B` |
| `warm` | Warm Academic — cream background, terracotta accent | Georgia | `C45B3E` `5B8A72` `D4A84B` |

Pass via `--template <name>` or env var `ARCDECK_TEMPLATE=<name>`.

## Phase 0: Setup & User Input

**Gather from user** (via single questionnaire if not provided as args):
- PDF file path (required)
- Target audience: researchers / students / industry / general (default: researchers)
- Duration in minutes (default: 20)
- Speaker notes? yes/no (default: no)

**Environment check:**
- Working directory: create `workspace/` subdirectory for all intermediate artifacts

## Phase 1: PDF Preprocessing (Agents A1-A2)

> Read `agents/01-pdf-preprocessor.md` and `agents/02-asset-extractor.md`

**Goal:** Convert PDF to clean markdown + extract visual assets.

1. **[A1 PDF Preprocessor]** Read the PDF and convert to markdown text using Docling
   - Use Docling's `export_to_markdown()` for structured text extraction (handles multi-column layouts, preserves headings and tables)
   - Clean HTML comments, OCR noise from figure regions, normalize whitespace
   - Save to `workspace/markdown.md`

2. **[A2 Asset Extractor]** Extract figures, tables, and references using Docling
   - Extract all figure images via bounding box cropping from page images
   - Extract tables via `element.get_image(doc)`
   - Post-process: map Docling sequential numbering to paper figure/table numbers
   - Build image metadata JSON: `workspace/images.json`
   - Build table metadata JSON: `workspace/tables.json`
   - Parse references section into citation dict: `workspace/references.json`

## Phase 2: Commitment Building (Agent A3)

> Read `agents/03-commitment-builder.md`

**Goal:** Generate a global contract that guides all downstream agents.

1. Load prompt template: `prompts/outline/commitment_builder_lite.txt`
2. Append paper markdown + talk constraints (duration, audience)
3. System message: `"You are CommitmentBuilder. Output ONLY the Markdown content for commitments.md. Do not wrap in code fences."`
4. Call LLM -> receive markdown output
5. Save to `workspace/commitments.md`
6. Validate: must contain all 5 required section headings (0-4)

## Phase 3: Discourse Parsing (Agent A4)

> Read `agents/04-discourse-parser.md`

**Goal:** Build RST discourse trees for each paper section.

1. Split `workspace/markdown.md` at `## ` headings into sections
2. For each section:
   a. Split into paragraphs (double-newline separated)
   b. Name each paragraph: `{section_key}_{index}` (e.g., `introduction_0`)
   c. Save paragraphs to `workspace/rst/{section_key}/paragraphs.json`
   d. Load prompt: `prompts/outline/section_rst.txt`
   e. Format with `{section_title}`, `{subsections}`, `{paragraph_input}`
   f. System message: `"You are an expert RST discourse parser. Output only valid RS3 XML."`
   g. Call LLM -> receive JSON RST tree
   h. Validate tree structure (see agent spec for rules)
   i. Save to `workspace/rst/{section_key}/section_tree.json`
3. Create aggregate: `workspace/rst/results.json`

## Phase 4: Slide Planning (Agent A5)

> Read `agents/05-slide-planner.md`

**Goal:** Group paragraphs into slides using RST relations as guidance.

For each section:
1. Load RST tree from `workspace/rst/{section_key}/section_tree.json`
2. Load paragraphs from `workspace/rst/{section_key}/paragraphs.json`
3. Load prompt: `prompts/outline/slide_planner.txt`
4. Format with 10 template variables (section data + presentation constraints)
5. System message: `"You are an expert at organizing academic content into presentation slides. Output only valid JSON."`
6. Call LLM -> receive slide groupings JSON
7. Save per-section slides

After all sections: merge into `workspace/merged_slides.json`

## Phase 5: Narrative Refinement Loop (Agents A6 -> A7 -> A8)

> Read `agents/06-narrative-critic.md`, `agents/07-slide-reviser.md`, `agents/08-narrative-judge.md`

**Goal:** Iteratively improve the slide plan through critique-revise-judge cycles.

```
For round = 0 to 2 (max 3 rounds):

  1. [A6 Narrative Critic]
     - Load prompt: prompts/outline/narrative_critic.txt
     - Input: merged_slides + commitments.md + paragraph_snippets
     - Output: critique JSON -> workspace/critique_round_{N}.json

  2. [A7 Slide Reviser]
     - Load prompt: prompts/outline/slide_reviser.txt
     - Input: merged_slides + critique + judge_feedback (from prior round)
     - Output: revised slides JSON -> workspace/revised_slides_round_{N}.json

  3. [A8 Narrative Judge]
     - Load prompt: prompts/outline/narrative_judge.txt
     - Input: revised_slides + commitments.md + critique
     - Output: judgement JSON -> workspace/judgement_round_{N}.json
     - Contains: {decision: "pass"/"revise", score, must_fix[]}

  4. If decision == "pass" OR score >= 7.5 -> break
     Else -> feed judge feedback to next round
```

Save final outline to `workspace/final_outline.json`

## Phase 6: Visual Asset Processing & Slide Composition (Agents A9-A10)

> Read `agents/09-image-table-filter.md` and `agents/10-slide-composer.md`

**Goal:** Filter relevant visuals, then holistically compose the slide plan.

1. **[A9 Image/Table Filter]**
   - Load prompt: `prompts/pipeline/image_table_filter_agent.yaml`
   - Input: raw_content + images.json + tables.json
   - Output: filtered images/tables (max 5 each) + visual_gap_sections
   - Save to: `workspace/images_filtered.json`, `workspace/tables_filtered.json`

2. **[A10 Slide Composer]** (Claude Code acts as LLM)
   - Read all inputs: final_outline.json, images_filtered.json, tables_filtered.json, commitments.md, rst/*/paragraphs.json, markdown.md
   - Holistic single-pass composition:
     - Match figures to slides by caption relevance
     - Select templates using decision tree (visual count + aspect ratio)
     - **Write slide content using diverse formats** (see Content Format Rules below)
     - **Design visual elements for every T1_TextOnly slide** — freely choose the best visualization type for each slide's content (see Visual Element Catalog below)
   - Enforce variety: no consecutive same template, no consecutive same visual layout, no consecutive same content format
   - Save to: `workspace/slide_plan.json`

### Content Format Rules (IMPORTANT — avoid bullet overuse)

Slides MUST NOT all use bullet points. The renderer supports multiple content formats — use them for visual variety:

| Format | When to Use | JSON |
|--------|-------------|------|
| **Bullets** (2-4 max) | Key points, takeaways, contributions | `"bullets": ["point 1", "point 2"]` |
| **Paragraph** | Narrative explanation, context, motivation | `"paragraph": "Flowing text explaining..."` |
| **Short phrases + dominant VE** | When the visual element IS the content | `"bullets": ["Phrase 1", "Phrase 2"]` (1-2 only) + large `visual_element` |

**Anti-patterns to avoid:**
- Every slide having 4 bullet points (monotonous)
- Bullets that are full sentences (too wordy — keep to <12 words each)
- Bullet text duplicating what the visual element already shows
- All slides using the same content format

**The renderer positions the VE right after bullets** (no wasted gap), capped at 2.0" tall to stay compact and proportional.

## Phase 7: Aesthetic Critique-Refine (Agents A11-A12)

> Read `agents/11-design-critic.md` and `agents/12-design-refiner.md`

**Goal:** Review and polish the slide plan for aesthetic quality using design principles.

1. **[A11 Design Critic]** (Claude Code acts as LLM)
   - Input: slide_plan.json + commitments.md
   - Evaluate using CARP+ design principles:
     - Contrast, Alignment, Repetition, Proximity
     - Content-Visual Alignment (paper-specific labels, real numbers)
     - Variety (template/layout diversity)
     - Information Design (right diagram for right content)
   - Score each criterion (1-10), compute weighted overall score
   - Produce per-slide issues with severity (high/medium/low) and specific fix instructions
   - Save to: `workspace/design_critique.json`
   - Decision: "pass" if score >= 8.0 and no high-severity issues; "refine" otherwise

2. **[A12 Design Refiner]** (Claude Code acts as LLM)
   - Input: slide_plan.json + design_critique.json + markdown.md + rst/*/paragraphs.json
   - Apply all high-severity fixes from critique
   - Enrich visual elements with paper-specific labels and real data
   - Apply LaTeX formatting (only `\textbf{}`, `\textcolor{blue}{}`, `\textcolor{red}{}`)
   - Refine bullets (3-5 per slide, paper terminology)
   - Shorten titles (max 5 words, content-specific)
   - Clean references (keep citations, remove internal refs)
   - Save to: `workspace/slide_plan_refined.json`

## Phase 8: PPTX Assembly (Agent A13)

> Read `agents/13-pptx-builder.md`

**Goal:** Assemble the final PowerPoint with rich visuals from the refined slide plan using PptxGenJS (Node.js).

1. Install dependencies (first run only):
   ```bash
   cd workspace && npm install pptxgenjs
   ```

2. Run `scripts/generate_pptx.js`:
   ```bash
   cd workspace && node generate_pptx.js [template]
   ```
   Templates: `ocean` (default), `minimal`, `dark`, `warm`

   - Loads slide_plan_refined.json (no template PPTX needed)
   - Splits multi-visual slides into separate pages (1 figure per page max)
   - Creates slides: Title -> Content... -> Thank You
   - Section dividers off by default (set `ARCDECK_DIVIDERS=true` to enable)
   - Applies selected template (color palette, fonts, backgrounds)
   - LAYOUT_WIDE (13.33" x 7.5"), layout by aspect ratio
   - Parses LaTeX in bullets (`\textbf{}` -> bold, `\textcolor{}{}` -> colored)
   - Renders visual elements: **19 built-in diagram types + 5 native chart types** (see Visual Element Catalog)
   - Saves to `workspace/output.pptx`

3. Verify output and report summary.

4. (Optional) If user requested speaker notes:
   - Claude Code generates conversational speaker notes per slide
   - Saves to `workspace/speaker_notes.json`

## Phase 9: Delivery

- Report output file location: `workspace/output.pptx`
- Show summary: total slides, sections processed, narrative refinement rounds, design critique score, visual elements rendered
- Offer: regeneration with different settings, individual phase re-runs

## Agent Loading Reference

| Phase | Agent Files to Load |
|-------|-------------------|
| 1 | `agents/01-pdf-preprocessor.md`, `agents/02-asset-extractor.md` |
| 2 | `agents/03-commitment-builder.md` |
| 3 | `agents/04-discourse-parser.md` |
| 4 | `agents/05-slide-planner.md` |
| 5 | `agents/06-narrative-critic.md`, `agents/07-slide-reviser.md`, `agents/08-narrative-judge.md` |
| 6 | `agents/09-image-table-filter.md`, `agents/10-slide-composer.md` |
| 7 | `agents/11-design-critic.md`, `agents/12-design-refiner.md` |
| 8 | `agents/13-pptx-builder.md` |
| On error | `reference/TROUBLESHOOTING.md` |
| Template selection | `reference/SLIDE-TYPES.md` |
| RST help | `reference/RST-RELATIONS.md` |

## Workspace Artifact Map

| Artifact | Producer | Consumer(s) | Format |
|----------|----------|-------------|--------|
| `markdown.md` | A1 | A3, A4, A9, A10, A12 | Plain text |
| `figures/` | A2 | A13 | PNG images |
| `images.json` | A2 | A9 | JSON dict |
| `tables.json` | A2 | A9 | JSON dict |
| `references.json` | A2 | A13 | JSON dict |
| `commitments.md` | A3 | A5, A6, A8, A10, A11 | Markdown |
| `rst/*/section_tree.json` | A4 | A5 | JSON tree |
| `rst/*/paragraphs.json` | A4 | A5, A6, A7, A8, A10, A12 | JSON dict |
| `merged_slides.json` | A5 | A6, A7 | JSON array |
| `critique_round_N.json` | A6 | A7 | JSON |
| `revised_slides_round_N.json` | A7 | A8 | JSON |
| `judgement_round_N.json` | A8 | A7 (next round) | JSON |
| `final_outline.json` | Loop exit | A10 | JSON array |
| `images_filtered.json` | A9 | A10 | JSON dict |
| `tables_filtered.json` | A9 | A10 | JSON dict |
| `slide_plan.json` | A10 | A11, A12 | JSON (with visual_element) |
| `design_critique.json` | A11 | A12 | JSON (scored evaluation) |
| `slide_plan_refined.json` | A12 | A13 | JSON (with visual_element) |
| `output.pptx` | A13 | User | PowerPoint |
| `speaker_notes.json` | A13 (optional) | User | JSON |

## Visual Element Catalog

Every T1_TextOnly slide MUST have a `visual_element` in the slide plan. The model should freely choose the best type for the content. There is no fixed assignment — pick whatever communicates the content most effectively.

### Shape-Based Diagrams (14 types)

These render as programmatic PptxGenJS shapes below the bullets, capped at 2.0" tall for compact proportions.

| Type | Best For | Visual |
|------|----------|--------|
| `process_flow` | Sequential steps, pipelines, workflows | Horizontal boxes with arrow connectors |
| `callout_cards` | Key concepts, features, components | Side-by-side cards with colored left accent stripe |
| `comparison` | Pros/cons, method A vs B, before/after with tone | Panels with colored headers (green=positive, gray=negative) |
| `cycle` | Iterative processes, feedback loops, recurring steps | Nodes arranged in a circle with center recycling symbol |
| `split_panel` | Two contrasting ideas, dual perspectives | Two large colored panels side by side |
| `stats_callout` | Key numbers, metrics, quantitative results | Big-number boxes with descriptions below |
| `stacked_list` | Ordered items, ranked features, step lists | Horizontal rows stacked vertically with colored left accent |
| `banner_quote` | Key insight, thesis statement, important quote | Large dark banner with accent stripe |
| `timeline` | Chronological events, milestones, project phases | Horizontal line with labeled milestone markers |
| `hierarchy` | Tree structures, taxonomies, parent-child relationships | Root box on top with connector lines to child boxes |
| `matrix` | Categories in a grid, feature comparison grid | 2-column (or 3-column) grid of accent-striped cards |
| `funnel` | Narrowing stages, filtering process, selection | Progressively narrower colored bars top-to-bottom |
| `pyramid` | Layered structure, importance levels, foundations | Stacked layers — bottom widest, top narrowest |
| `before_after` | Transformations, improvements, old vs new | Two panels (gray "before", green "after") with arrow between |

### Chart-Based Visualizations (5 types)

These render as native PptxGenJS charts (`slide.addChart()`). Items need numeric data — the renderer extracts numbers from `item.label` (e.g., "3.67", "95%") or `item.value` field.

| Type | Best For | Visual |
|------|----------|--------|
| `bar_chart` | Comparing quantities across categories, benchmark results | Vertical bar/column chart |
| `pie_chart` | Proportions, market share, distribution | Pie chart with percentage labels |
| `doughnut_chart` | Proportions with central emphasis, completion rates | Doughnut chart with hole |
| `radar_chart` | Multi-dimensional comparison, capability profiles | Spider/radar chart |
| `line_chart` | Trends over time, progression, growth curves | Line chart with data points |

### Visual Element JSON Format

```json
{
  "visual_element": {
    "type": "<type_name>",
    "items": [
      { "label": "Main text", "sublabel": "Description", "tone": "positive" },
      { "label": "3.67", "sublabel": "Average Score", "value": 3.67 }
    ]
  }
}
```

Fields:
- `type` — any type from the tables above
- `items[]` — array of 2-6 items
- `item.label` — primary text (bold, colored) — for charts, can be a number
- `item.sublabel` — secondary description text (gray, smaller)
- `item.tone` — (comparison only) `"positive"`, `"negative"`, or `"neutral"`
- `item.value` — (charts) explicit numeric value (optional — parser tries to extract from label)

### Slide Plan JSON Field Names

The renderer resolves slide titles from `title` (preferred) or `subsection` (legacy). Both work. Required fields per slide:

```json
{
  "template_id": "T1_TextOnly",
  "title": "Slide Title Here",
  "section": "introduction",
  "bullets": ["Point 1", "Point 2"],
  "images": ["image_2.png"],
  "tables": ["table_1.png"],
  "visual_element": null,
  "paragraph": ""
}
```

- `images` and `tables` — arrays of filenames (not paths). Files must exist in `workspace/figures/`.
- `template_id` routing: `T7_Title` (skipped, auto-generated), `T8_Conclusion` (dark bg), `T1_TextOnly` (VE support), all others → image slide layout.
- `section` names can be any string — used for grouping only (not displayed unless dividers enabled).

### Selection Guidelines (for Agents A10/A12)

Choose the visual element type that **best matches the content structure**:

- **Sequential process?** → `process_flow`, `timeline`, `funnel`
- **Comparison/contrast?** → `comparison`, `before_after`, `split_panel`
- **Key numbers/metrics?** → `stats_callout`, `bar_chart`, `pie_chart`
- **Hierarchical/tree?** → `hierarchy`, `pyramid`
- **Iterative/cyclic?** → `cycle`
- **Feature list/grid?** → `callout_cards`, `matrix`, `stacked_list`
- **Single key insight?** → `banner_quote`
- **Multi-dimensional comparison?** → `radar_chart`
- **Trend/progression data?** → `line_chart`, `bar_chart`
- **Proportions/distribution?** → `pie_chart`, `doughnut_chart`

**Variety rule**: Never use the same visual element type on consecutive slides.

### PptxGenJS Capabilities Reference

The PPTX renderer (`generate_pptx.js`) uses PptxGenJS v4.0.1 which provides:

- **187+ shape types**: rectangles, rounded rectangles, arrows (right, left, notched, curved, bent, striped, swoosh), chevrons, pentagons, hexagons, octagons, stars, flowchart shapes (process, decision, terminator, connector, document), callout shapes, math symbols, gears, cubes, cans, funnels, lightning bolts, clouds, braces, brackets, arcs, and more
- **10 native chart types**: area, bar, bar3D, bubble, doughnut, line, pie, radar, scatter, bubble3D
- **Rich formatting**: gradient fills, shadows, line styles (solid, dash, dashDot), arrow endpoints, rotation, custom geometry paths (bezier curves)
- **Tables**: full cell-level formatting with colspan/rowspan, auto-paging
- **Slide masters**: reusable layout definitions with placeholders

If a new visual element type is needed that doesn't exist in the catalog above, the renderer can be extended by adding a new function that composes PptxGenJS shapes, charts, or tables. The model should specify the type name and items, and the renderer handles the visual.

## Pipeline Scripts

| Script | Purpose | Phase |
|--------|---------|-------|
| `scripts/extract_docling.py` | PDF figure/table extraction using Docling DocumentConverter | Phase 1 (A2) |
| `scripts/postprocess_figures.py` | Map Docling sequential numbering to paper figure/table numbers | Phase 1 (A2) |
| `scripts/generate_pptx.js` | PPTX assembly from slide_plan_refined.json via PptxGenJS (Node.js) | Phase 8 (A13) |

**Legacy scripts** (kept for reference, superseded by `generate_pptx.js`):
- `scripts/render_pptx.py` -- python-pptx template-based renderer
- `scripts/enhance_visuals.py` -- python-pptx visual element drawing
- `scripts/run_visual_pipeline.py` -- combined render+enhance pipeline

### Scripts Usage

```bash
# Phase 1: Extract figures from PDF
python scripts/extract_docling.py <pdf_path> workspace/figures/

# Phase 1: Post-process figure numbering
python scripts/postprocess_figures.py workspace/

# Phase 8: Generate PPTX (install pptxgenjs first if needed)
cd workspace && npm install pptxgenjs
cd workspace && node generate_pptx.js              # default ocean template
cd workspace && node generate_pptx.js minimal       # clean minimal theme
cd workspace && node generate_pptx.js dark           # dark mode theme
cd workspace && node generate_pptx.js warm           # warm academic theme
# Or via env var:
ARCDECK_TEMPLATE=dark node generate_pptx.js
```

---
> Source: [RehgLab/ArcDeck](https://github.com/RehgLab/ArcDeck) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
