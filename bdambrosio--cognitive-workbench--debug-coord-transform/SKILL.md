---
name: debug-coord-transform
description: Debug tool: Transform coordinates between agent-relative and world-relative Use when this capability is needed.
metadata:
  author: bdambrosio
---

# debug-coord-transform

Debug tool for testing coordinate transformations. Converts between agent-relative and world-relative coordinates.

## Input

- `dx`: float - X coordinate
- `dy`: float - Y coordinate (unchanged, always vertical)
- `dz`: float - Z coordinate
- `yaw`: float - Agent yaw in degrees (default: from current status)
- `direction`: string - "agent-to-world" or "world-to-agent" (default: "agent-to-world")

## Output

Returns uniform return format with:
- `value`: Summary text
- `data`: Dict with:
  - `input`: `{dx, dy, dz, yaw, direction}`
  - `output`: `{dx, dy, dz}` (transformed coordinates)

## Notes

- Hidden from planner catalog (debug/testing only)
- Useful for validating coordinate transformations
- Accessible via direct `{"type": "debug-coord-transform"}` invocation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
