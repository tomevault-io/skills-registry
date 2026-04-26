---
name: debug-get-voxel-grid
description: Debug tool: Get observed voxel grid with agent-relative coordinates Use when this capability is needed.
metadata:
  author: bdambrosio
---

# debug-get-voxel-grid

Debug tool for testing coordinate system. Returns the observed voxel grid with agent-relative coordinates.

## Input

- `radius`: Optional integer radius (default: 2)

## Output

Returns uniform return format with:
- `value`: Formatted voxel grid text
- `data`: Structured voxel grid dict with:
  - `center`: `{x, y, z, yaw}` (absolute coordinates)
  - `radius`: int
  - `cells`: List of `{dx, dy, dz, solid, block_id, support}` (agent-relative coordinates)

## Notes

- Hidden from planner catalog (debug/testing only)
- Coordinates are agent-relative: dx=right/left, dz=forward/back
- Accessible via direct `{"type": "debug-get-voxel-grid"}` invocation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
