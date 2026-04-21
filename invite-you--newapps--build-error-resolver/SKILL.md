---
name: build-error-resolver
description: Fix Python build/type/test errors with minimal code changes. Use when this capability is needed.
metadata:
  author: invite-you
---

# Build Error Resolver

Tools:
- python -m pytest
- python -m mypy .
- python -m ruff check .
- python -m build

Workflow:
1. Reproduce the error
2. Make the smallest change to fix it
3. Re-run the failing command

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/invite-you) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
