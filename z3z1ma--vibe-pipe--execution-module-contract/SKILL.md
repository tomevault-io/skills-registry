---
name: execution-module-contract
description: Update src/vibe_piper/execution.py safely in a strict-typed uv repo (keep types/tests/tools in sync). Use when this capability is needed.
metadata:
  author: z3z1ma
---
<!-- BEGIN:compound:skill-managed -->
# Purpose
Make changes to the execution layer without breaking type contracts or downstream call sites.

# When To Use
- You modify `src/vibe_piper/execution.py`.
- You add or change execution-related types in `src/vibe_piper/types.py`.

# Checklist
- Identify the public contract:
  - Entry points/functions/classes that callers import.
  - Any result/plan/config types used across modules.
- Keep types in lockstep:
  - Add/update the corresponding Protocols/type aliases/dataclasses in `src/vibe_piper/types.py`.
  - Prefer `@dataclass(frozen=True)` for value objects.
  - Avoid widening types (e.g., introducing `Any`) in public APIs.
- Error handling hygiene:
  - Use explicit exception types with a clear `msg = "..."; raise ...` pattern.
  - Re-raise with context using `raise ... from e` when converting exceptions.
- Downstream safety sweep:
  - Search the repo for usages of the changed symbols and update call sites.
  - If you rename a public symbol, prefer an explicit rename across the codebase.
- Verification (uv-first):
  - `uv run ruff format src tests`
  - `uv run ruff check src tests`
  - `uv run mypy src`
  - `uv run pytest -m "not integration"`
<!-- END:compound:skill-managed -->

## Manual notes

_This section is preserved when the skill is updated. Put human notes, caveats, and exceptions here._

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z3z1ma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
