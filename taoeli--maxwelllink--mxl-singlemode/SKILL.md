---
name: mxl-singlemode
description: This skill should be used when users need the MaxwellLink single-mode cavity solver for fast prototyping and regression, in either socket or embedded mode. Use when this capability is needed.
metadata:
  author: taoeli
---

# Single-mode cavity workflows (MaxwellLink)

## Use for fast prototyping
- Use `mxl.SingleModeSimulation` when a 1-mode cavity surrogate is sufficient and Meep is too heavy.

## Configure
- Set `dt_au`, `frequency_au`, `damping_au`, `coupling_strength`, and `coupling_axis`.
- Optional physics knobs used in strong-coupling workflows: `include_dse`.
- Attach molecules in embedded or socket mode (same `Molecule` interface as elsewhere).

## Prefer templates
- Template: `skills/mxl-project-scaffold/assets/templates/singlemode-tls-socket-tcp`

## References
- Recipes: `skills/mxl-singlemode/references/singlemode_run_recipes.md`
- Docs: `docs/source/em_solvers/single_mode_cavity.rst`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/taoeli) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
