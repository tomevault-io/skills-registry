---
name: tufte-viz
description: | Use when this capability is needed.
metadata:
  author: aparente
---

# Tufte Visualization Ideation

Apply Edward Tufte's principles to design clear, honest, high-density data visualizations.

## Workflow

### For new visualizations:

1. **Clarify the data story**
   - What comparisons matter?
   - What's the key insight to communicate?
   - Who's the audience?

2. **Select approach** using Tufte principles:
   - High comparison need → Small multiples
   - Dense data → Consider data tables, sparklines
   - Time-series → Line charts with minimal grid
   - Part-to-whole → Avoid pie charts; prefer bar/table

3. **Design with data-ink in mind**
   - Start minimal, add only what's necessary
   - Every element must earn its ink
   - Default to grayscale; use color purposefully

4. **Apply the Tufte test** (see references/tufte-principles.md)

### For critiquing visualizations:

1. **Check graphical integrity**
   - Calculate lie factor if proportions seem off
   - Verify baselines and scales
   - Look for 3D distortion

2. **Identify chartjunk**
   - Decorative elements
   - Heavy grids
   - Unnecessary 3D effects
   - Moiré patterns

3. **Evaluate data-ink ratio**
   - What can be erased?
   - What's redundant?

4. **Suggest improvements** with specific before/after recommendations

## Key Principles Reference

For detailed principles, read: `references/tufte-principles.md`

**Quick checklist:**
- [ ] Lie Factor ≈ 1.0 (no visual distortion)
- [ ] Maximum data-ink ratio
- [ ] Zero chartjunk
- [ ] Clear labeling
- [ ] Enables comparison
- [ ] Reveals multiple levels of detail
- [ ] Appropriate data density

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aparente) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
