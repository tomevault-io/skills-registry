---
name: input-abstraction-layer
description: Build a unified input layer for TV remotes and gamepads. Use when normalizing key codes, long-press, repeat behavior, and Back/Menu semantics across Samsung, LG, Hisense, Xbox, and PS5 environments. Use when this capability is needed.
metadata:
  author: vageez
---

# Input Abstraction Layer

## Overview
Normalize heterogeneous platform key events into one app-level action contract.
Treat platform keycodes as adapter concerns, not feature code concerns.

## Core Contract
Expose canonical actions:
- `NAV_UP`, `NAV_DOWN`, `NAV_LEFT`, `NAV_RIGHT`
- `SELECT`, `BACK`, `MENU`, `PLAY_PAUSE`
- `SEEK_FWD`, `SEEK_BACK`, `PAGE_UP`, `PAGE_DOWN`

## Workflow
1. Create per-platform adapters that map native events to canonical actions.
2. Centralize repeat-rate and long-press policy in one dispatcher.
3. Emit input source metadata (`remote`, `gamepad`, `keyboard`) for telemetry.
4. Gate feature-level handlers behind canonical actions only.
5. Add platform fallback paths for missing keys.

## Reliability Rules
- Debounce duplicate native key bursts before dispatch.
- Ensure Back is idempotent for fast repeat presses.
- Disable key-repeat on destructive actions (sign-out, purchase confirm).
- Log unknown keycodes with platform/build context.

## Validation Checklist
- Same user intent triggers same action on all targets.
- Long-press thresholds are consistent and measurable.
- No double-dispatch during key repeat storms.
- Back/Menu precedence is consistent with product rules.

## References
- Use `references/keymap-matrix.md` as the baseline mapping matrix.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vageez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
