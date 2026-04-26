---
name: cogames-variant-debug
description: Debug CoGames mission or variant regressions by comparing branch vs main. Use when behavior changes or a `cogames play` command regresses. Use when this capability is needed.
metadata:
  author: metta-ai
---

# CoGames Variant Debug

## Workflow
- Reproduce with the provided `uv run cogames play` command.
- Locate variant definitions, map setup, and reward/assembler logic.
- Diff against origin/main to isolate the regression.
- Propose a minimal fix and a verification command.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
