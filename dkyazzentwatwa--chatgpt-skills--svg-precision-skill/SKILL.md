---
name: svg-precision-skill
description: Generate deterministic SVGs from structured specs with validation and rendering. Use for icons, diagrams, charts, UI mockups, and technical drawings. Use when this capability is needed.
metadata:
  author: dkyazzentwatwa
---

# SVG Precision Skill

Build SVGs from explicit scene specifications, then validate before handing them off.

## Workflow

1. Translate the request into a concrete spec with fixed dimensions and coordinates.
2. Use `references/spec.md` for templates and `references/recipes.md` for stable layout patterns.
3. Build the SVG with `scripts/svg_cli.py build`.
4. Validate with `scripts/svg_cli.py validate`.
5. Render a PNG preview when the user needs a quick visual check.

## Rules

- Set `viewBox`, width, and height explicitly.
- Prefer absolute coordinates and simple shapes.
- Treat text as risky when exact rendering matters.
- Avoid exotic filters unless they are necessary and testable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dkyazzentwatwa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
