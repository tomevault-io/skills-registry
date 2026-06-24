---
name: backend-type-annotation
description: How to use ty to check type annotation in Python files Use when this capability is needed.
metadata:
  author: jpmolinamatute
---

# Python Type Annotation

We use ty for type annotation checking. There are two ways to run type annotation check:

1. Manually:

   ```bash
   cd ./backend
   uv run ty check
   ```

2. Via script (run from project root), this will also run formatting, lint and tests:

   ```bash
   ./scripts/linting.bash --backend
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpmolinamatute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
