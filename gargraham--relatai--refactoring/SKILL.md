---
name: refactoring
description: Improve code structure and maintainability while strictly preserving existing behavior. Use when this capability is needed.
metadata:
  author: gargraham
---
# Skill: Refactoring

> See also: `AGENT_SYSTEM.md`

## Phase
- Change

## Purpose
- Improve structure while preserving behavior (unless explicitly told otherwise).

## Use When
- “Refactor this”
- “Clean this up”
- “Split this file”
- “Make it more maintainable”

## Inputs to Request (only if unclear)
- What’s the pain (readability/testability/perf)?
- Must behavior remain identical?
- Acceptable blast radius (small/medium/large)?

## Workflow
- State the problem (“regret check”):
  - what’s hard today
  - what will be better after
- Confirm constraints:
  - behavior-preserving by default
- Choose approach:
  - Small: local cleanup
  - Medium: extract modules/helpers
  - Large: phases + SCRATCHPAD.md
- Implement incrementally:
  - avoid mixing new features
- Proof of Life:
  - tests if available, otherwise manual steps

## Output
- **Problem**
- **Approach**
- **Changes made**
- **Proof of Life**
- **Follow-ups** (optional)

## Memory
- Append one line to JOURNAL.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gargraham) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
