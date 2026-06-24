---
name: gemini-forge
description: > Use when this capability is needed.
metadata:
  author: tdimino
---

# Gemini Forge

Generate frontend code with Gemini 3.1 Pro, polish with Claude. Gemini handles structure and scaffolding at $2/MTok; Claude handles craft, aesthetic conviction, and production hardening at $15/MTok.

**Model**: `gemini-3.1-pro-preview` (text-only, 1M context, 64K output)
**Auth**: `GEMINI_API_KEY` env var (same key as nano-banana-pro)

## Quick Draft

Generate React or HTML/CSS from a task description.

```bash
# Simple component
uv run scripts/generate_ui.py "dark dashboard with real-time metrics" --mode react --thinking medium

# Full multi-file app
uv run scripts/generate_ui.py "SaaS pricing page with three tiers" --mode react --thinking high --app

# With design system context
uv run scripts/load_design_system.py --tokens tokens.css --brief brand.md --output context.txt
uv run scripts/generate_ui.py "hero section" --design-context context.txt --thinking high
```

**Thinking levels**: `low` (single components), `medium` (multi-component layouts), `high` (full apps, complex state). See `references/thinking-levels.md`.

## Screenshot-to-Code

Convert a screenshot or mockup to code using the describe-first pipeline. Two API calls prevent layout hallucination—Gemini analyzes first, then generates from the verified spec.

```bash
# Full pipeline: screenshot → spec → code
uv run scripts/screenshot_to_code.py screenshot.png --framework react --output ./output

# Step 1 only: get the spec, inspect it before generating
uv run scripts/screenshot_to_code.py screenshot.png --analyze-only

# Step 2 only: generate from an existing or corrected spec
uv run scripts/screenshot_to_code.py --from-spec spec.md --framework html --output ./output
```

Use `--analyze-only` to inspect the spec. Claude can correct misreadings before passing to generation.

## SVG Animation

Generate or animate SVGs with physics-based reasoning.

```bash
# Animate existing SVG
uv run scripts/generate_svg.py --mode animate logo.svg --description "draw-on stroke, 1.2s"

# Create animated SVG from description
uv run scripts/generate_svg.py --mode create --description "orbiting dots with spring connections"

# Physics simulation
uv run scripts/generate_svg.py --mode physics --description "particle system, 20 nodes, gravity"

# Animated logo
uv run scripts/generate_svg.py --mode logo --description "Minoan double-axe labrys, slow rotation"
```

Output is pure SVG—no external dependencies. Includes `prefers-reduced-motion` fallbacks.

## Polish Workflow

After Gemini generates a draft:

1. Read the generated file
2. Apply `minoan-frontend-design` standards: commit to The One Thing, personality fonts, bold color, OKLCH colors, Tailwind v4, Radix primitives, APCA contrast, semantic HTML, accessibility
4. Write the polished output to the project

Gemini drafts fast. Claude refines with taste.

---
> Source: [tdimino/claude-code-minoan](https://github.com/tdimino/claude-code-minoan) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
