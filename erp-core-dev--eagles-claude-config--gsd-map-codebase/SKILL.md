---
name: gsd-map-codebase
description: Generate a codebase map for GSD context injection Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# GSD Codebase Map

Generate a concise codebase map optimized for injecting into GSD sub-agent contexts.

## What To Do

1. Scan directory structure (Glob)
2. Identify key files (controllers, services, models, tests)
3. Count files per directory
4. Generate compressed map (< 2K tokens) for sub-agent injection
5. Output to .planning/CODEBASE-MAP.md

## Arguments
- `--depth=<n>`: Directory depth (default: 2)
- `--focus=<dir>`: Focus on specific directory

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
