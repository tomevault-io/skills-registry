---
name: floor-plan
description: Create architectural floor plans and interior layouts using drawio XML format with building-standard symbols. Best for office layouts, home floor plans, evacuation plans, and seating arrangements. Built on drawio with floorplan-specific stencils. NOT for simple diagrams (use mermaid) or UI wireframes (use wireframe skill). Use when this capability is needed.
metadata:
  author: markdown-viewer
---

# Floor Plan & Layout Generator

**Quick Start:** Define room boundaries with walls → Add doors and windows → Place furniture from stencil library → Add dimensions and labels → Wrap in ` ```drawio ` fence.

> ⚠️ **IMPORTANT:** Always use ` ```drawio ` code fence. NEVER use ` ```xml ` — it will NOT render as a diagram.

## Critical Rules

> 🔗 **This is a drawio-derived skill.** All structure, layout, and edge routing rules inherit from [drawio SKILL.md](../drawio/SKILL.md). Read the base rules first.

**Floor plan-specific additions:**
- Check [stencils/README.md](../drawio/stencils/README.md) for exact furniture stencil names
- Use consistent scale: 1 pixel = 1 cm is a common convention (so a 3m wall = 300px)
- Wall thickness: typically 15-20px for exterior, 10px for interior walls
- Standard door width: 80-90cm (80-90px), window: varies

## Floor Plan Types

| Type | Purpose | Stencil Library | Example |
|------|---------|-----------------|---------|
| Office Layout | Open office, cubicles, meeting rooms | `mxgraph.floorplan.*` (44 symbols) | [office-layout.md](examples/office-layout.md) |
| Home Floor Plan | Residential room layouts | `mxgraph.floorplan.*` | [home-floor-plan.md](examples/home-floor-plan.md) |
| Evacuation Plan | Emergency exit routes, fire safety | Basic shapes + arrows | [evacuation-plan.md](examples/evacuation-plan.md) |
| Seating Plan | Event seating, theater layouts | `mxgraph.floorplan.*` | [seating-plan.md](examples/seating-plan.md) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markdown-viewer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
