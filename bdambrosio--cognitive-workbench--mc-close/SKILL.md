---
name: mc-close
description: Close currently open UI. Returns success/failure Use when this capability is needed.
metadata:
  author: bdambrosio
---

# Minecraft Close Tool

Closes currently open UI interface. Returns success/failure status.

## Purpose

UI management for closing opened interfaces like crafting tables, chests, and furnaces.

## Input

- `value`: Ignored

## Output

Returns uniform_return format with:
- `value`: Text summary (success/failure message)
- `data`: Structured data dict (machine-readable). Key fields:
  - `success`: Boolean

## Behavior & Performance

- Closes currently open UI
- Fails if no UI is open
- Returns immediately after closing

## Guidelines

- Use after completing operations in opened UI
- Fails silently if no UI is open
- Use `mc-open` to open UI interfaces

## Usage Examples

Close open UI:
```json
{"type":"mc-close","out":"$close"}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
