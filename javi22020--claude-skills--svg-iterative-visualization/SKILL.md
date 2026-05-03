---
name: svg-iterative-visualization
description: Render, visualize, and iteratively refine SVG graphics. Use when the user wants to create, explore, or modify any kind of SVG: illustrations, icons, diagrams, data visualizations, generative art, animations, maps, technical drawings, or abstract compositions. Triggers on requests like 'draw', 'visualize', 'create an SVG', 'make a diagram', 'design an icon', or any request for a visual deliverable. Use when this capability is needed.
metadata:
  author: javi22020
---

# SVG Iterative Visualization Skill

This skill guides Claude to **create and autonomously refine SVG graphics through a mandatory minimum of 5 iterations** before presenting a final result. Claude does not wait for user feedback between iterations — it drives the full refinement loop itself, self-critiquing and improving each version, then presents the polished final result with a summary of what evolved.

---

## The Golden Rule

**Never stop at the first draft. Never stop at the second. Run the full self-refinement loop — at least 5 rounds — before presenting anything as a final result.**

Each iteration must make a meaningful, visible improvement. Cosmetic tweaks that change nothing substantial do not count as iterations.

---

## Full Workflow

```
PLAN → v1 (draft) → CRITIQUE → v2 → CRITIQUE → v3 → CRITIQUE → v4 → CRITIQUE → v5 → QUALITY GATE → FINAL PRESENT
         ↑                                                                              ↑
    render + critique each                                               render final + summarize
```

### Phase 1 — Plan (before any code)

Before writing a single line of SVG, commit to a clear direction:

- **Subject & focal point**: What is the center of gravity of the composition?
- **Aesthetic direction**: Pick one extreme and commit — brutally minimal, maximalist, retro-futuristic, organic, geometric, editorial, etc.
- **Palette**: Choose 3–6 colors. Name them. Never use defaults like black-on-white without intention.
- **Complexity level**: Flat icon, layered illustration, or animated piece?
- **Canvas**: What `viewBox` dimensions fit this content best?

Consider 2–3 distinct visual directions. Pick the boldest one. Write a one-sentence creative brief before starting.

---

### Phase 2 — Iteration Loop (minimum 5 rounds, fully autonomous)

Claude runs this loop **without waiting for user input between rounds**. Each round:

#### A. Write/Update SVG
- Round 1: Create the full draft from scratch
- Rounds 2–5+: Read the previous version from disk, apply targeted improvements, save as `_v2`, `_v3`, etc.

#### B. Save and Render
1. Save to `/mnt/user-data/outputs/<name>_vN.svg`
2. Call `present_files` — renders it visually inline
3. Never paste raw SVG XML in the chat

#### C. Self-Critique (required after every single render)

After each render, Claude must **explicitly write out** a scored critique before proceeding to the next iteration. This is not internal reasoning — it must appear in the response so the user can follow the logic:

```
SELF-CRITIQUE — vN
──────────────────────────────────────────────────────
Composition     [1–10]: <score> — <1-sentence reason>
Color & Depth   [1–10]: <score> — <1-sentence reason>
Detail & Polish [1–10]: <score> — <1-sentence reason>
Visual Interest [1–10]: <score> — <1-sentence reason>
Technical Craft [1–10]: <score> — <1-sentence reason>
──────────────────────────────────────────────────────
WEAKEST POINT:        <the single lowest-scoring dimension>
NEXT ITERATION FOCUS: <exactly what will be improved and how>
──────────────────────────────────────────────────────
```

The next iteration **must directly address the weakest point** identified. Skipping the self-critique, or writing a critique but then ignoring it, breaks the quality loop.

#### D. Natural Focus by Round

Each round has a natural focus. Use these as guidance — but always follow the critique's weakest point if it disagrees:

| Round | Natural Focus |
|-------|--------------|
| v1 | Composition skeleton: major shapes, layout, rough palette |
| v2 | Color & depth: gradients, lighting direction, shadows |
| v3 | Detail layer: textures, secondary elements, fine shapes |
| v4 | Polish: hierarchy, spacing, alignment, visual weight |
| v5 | Final touch: animation, glow, typography, micro-details |

If critique says color is weak at v3, fix color at v3 — don't wait for v2's "scheduled" slot.

---

### Phase 3 — Quality Gate (must pass before declaring done)

After round 5, evaluate every item. **If any item fails, run another iteration targeting the failure.**

- [ ] Every major shape has intentional, non-flat fill (gradient, layered opacity, or deliberate solid with clear reasoning)
- [ ] At least one filter effect is used meaningfully (glow, shadow, blur, noise, or texture)
- [ ] The composition fills at least 70% of the canvas — no large accidental voids
- [ ] Visual hierarchy is unambiguous: there is one clear primary element
- [ ] Colors are cohesive — no element looks like it belongs to a different artwork
- [ ] If text is present, it is positioned and styled as a first-class design element
- [ ] No SVG anti-patterns: `viewBox` present, no broken `url(#...)` refs, no orphaned `<defs>`
- [ ] The piece would not be mistaken for a generic AI-generated placeholder

All items pass → Phase 4. Any item fails → one more iteration targeting the failure, then re-check.

---

### Phase 4 — Final Presentation

After the quality gate passes:

1. Present the **final version** with `present_files`
2. Write a short **Evolution Summary** (3–5 sentences) narrating how the piece developed — not a dry changelog, but a story of what decisions were made and why
3. Offer **3 specific directions** for further exploration if the user wants to continue

**Evolution Summary format:**
> "Started with [brief v1 description]. The composition shifted at v[N] when [what changed and why it mattered]. The piece found its character through [key techniques]. The final result achieves [the original aesthetic goal]."

---

## SVG Technical Reference

### Required structure

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 600" width="800" height="600">
  <defs>
    <!-- ALL gradients, filters, patterns, clip paths — defined before use -->
  </defs>
  <!-- background layer first, then content layers back-to-front -->
</svg>
```

**Hard requirements — violating any of these is a technical failure:**
- `viewBox` is always set — never omit it
- Root `<svg>` has explicit `width` and `height`
- All `<defs>` references are defined before first use
- No remote URLs in `<image href="...">` — fully self-contained
- All `id` attributes are unique within the file

### Capabilities to use freely

| Feature | Use for |
|---------|---------|
| `<linearGradient>`, `<radialGradient>` | Rich fills, depth, light direction |
| `<pattern>` | Textures, repeating motifs, cross-hatching |
| `feGaussianBlur` | Glow effects, soft shadows, depth-of-field blur |
| `feDropShadow` | Quick realistic drop shadows |
| `feTurbulence` | Noise, grain, organic texture, fire/smoke |
| `feBlend`, `feComposite` | Layer blending modes (multiply, overlay, screen) |
| `<clipPath>`, `<mask>` | Complex reveals, shaped compositing |
| `<animate>`, `<animateTransform>` | SMIL-based motion and transforms |
| CSS `@keyframes` in `<style>` | Full CSS animation control |
| `<text>` + typographic attributes | Labels, titles, decorative lettering |

### Palette strategy

Define the palette explicitly at the top of every file so all colors are intentional:

```xml
<style>
  :root {
    --c-bg:      #0f1729;
    --c-primary: #4ecdc4;
    --c-accent:  #ff6b6b;
    --c-mid:     #556cd6;
    --c-light:   #cce8ff;
  }
</style>
```

Use `fill="var(--c-primary)"` or consistent hex values — never scatter arbitrary colors.

---

## Animation Guidelines

One well-timed animation beats five scattered ones. Use animation to **enhance**, not to prove it can be done.

```xml
<!-- Slow orbital rotation (SMIL) -->
<animateTransform attributeName="transform" type="rotate"
  from="0 400 300" to="360 400 300" dur="20s" repeatCount="indefinite"/>

<!-- CSS keyframe pulse -->
<style>
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.45} }
  .pulse { animation: pulse 3s ease-in-out infinite; }
</style>

<!-- Path draw-on reveal -->
<path stroke-dasharray="1000" stroke-dashoffset="1000">
  <animate attributeName="stroke-dashoffset" from="1000" to="0" dur="3s" fill="freeze"/>
</path>
```

---

## Ready-to-Use Patterns

### Radial background
```xml
<radialGradient id="bg" cx="40%" cy="40%" r="70%">
  <stop offset="0%" stop-color="#1a1a2e"/>
  <stop offset="100%" stop-color="#050810"/>
</radialGradient>
<rect width="100%" height="100%" fill="url(#bg)"/>
```

### Glow filter
```xml
<filter id="glow" x="-30%" y="-30%" width="160%" height="160%">
  <feGaussianBlur stdDeviation="6" result="blur"/>
  <feMerge>
    <feMergeNode in="blur"/>
    <feMergeNode in="blur"/>
    <feMergeNode in="SourceGraphic"/>
  </feMerge>
</filter>
```

### Noise/grain overlay
```xml
<filter id="grain">
  <feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/>
  <feColorMatrix type="saturate" values="0"/>
  <feBlend in="SourceGraphic" mode="overlay"/>
  <feComposite in2="SourceGraphic" operator="in"/>
</filter>
<rect width="100%" height="100%" filter="url(#grain)" opacity="0.07"/>
```

### Sphere highlight
```xml
<radialGradient id="highlight" cx="35%" cy="30%" r="50%">
  <stop offset="0%" stop-color="white" stop-opacity="0.3"/>
  <stop offset="100%" stop-color="white" stop-opacity="0"/>
</radialGradient>
```

---

## Design Principles

**Composition**
- Fill 70%+ of the canvas — negative space must be intentional, not accidental
- One dominant element, one or two secondary, supporting details. Never give equal visual weight to everything.
- Asymmetry is more alive than perfect symmetry — use it deliberately

**Color**
- Commit to a strategy: monochromatic + accent, analogous, or complementary pair
- Avoid: random color scatter, all-gray without purpose, generic purple-on-white AI aesthetics
- Dark backgrounds with luminous fills tend to read as more crafted than light backgrounds

**Line and Shape**
- Keep stroke widths consistent within a piece unless variation is intentional
- `stroke-linecap="round"` for organic feel; `"butt"` or `"square"` for precision/technical
- Layering shapes with varying opacity creates depth without visual clutter

**Typography**
- Match font to aesthetic: monospace for technical/retro, serif for editorial, geometric sans for modern/clean
- `text-anchor="middle"` for centered; `"start"` for left-aligned labels
- SVG text does not wrap — handle line breaks with separate `<text>` elements at different `y` positions

---

## What NOT to Do

- **Don't stop before round 5** — quality requires passes, not a single draft
- **Don't skip or internalize the self-critique** — write it out visibly after every render
- **Don't ignore the critique's weakest point** — the next iteration must address it directly
- **Don't paste SVG XML in chat** — always render via `present_files`
- **Don't use placeholder colors** — commit to a real palette from round 1
- **Don't rewrite the entire file for a small change** — be surgical, preserve what works
- **Don't start over unless the user asks** — evolving one piece beats restarting repeatedly
- **Don't use flat fills where depth is achievable** — gradients and opacity layering are almost always better
- **Don't skip version suffixes** — save `_v1`, `_v2`, etc. so the user can go back

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/javi22020) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
