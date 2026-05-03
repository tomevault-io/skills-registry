---
name: uv-dependency-management
description: Manages dependency lifecycle with uv and requirements export policy. Use when adding, removing, or updating Python dependencies.
metadata:
  author: jeffreysumner
---

# uv Dependency Management

## Steps

1. Add or remove packages with `uv`.
2. Ensure lock state is updated.
3. Export pinned `requirements.txt` with `scripts/export_requirements.ps1`.
4. Run lint/tests before finalizing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeffreysumner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
