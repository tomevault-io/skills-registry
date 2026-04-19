---
name: flatten-repo
description: Umbrella skill for flatten-repo when tasks span architecture, tests, quality gates, and release readiness together. Use when this capability is needed.
metadata:
  author: guillaume-lombardo
---

# Flatten Repo

## Overview

Use this skill for cross-cutting requests. For focused tasks, prefer dedicated skills:

- `flatten-repo-architecture` for module/package design and metadata wiring.
- `flatten-repo-testing` for pytest layout and marker behavior.
- `flatten-repo-quality` for pre-commit, Ruff, ty, secret scanning, and build/twine checks.

## Workflow

1. Split the request by concern (architecture, tests, quality).
2. Apply only the minimal needed dedicated skills.
3. For publication requests, explicitly validate metadata consistency, build artifacts, and twine checks.
4. Verify each concern independently, then run a final end-to-end check.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guillaume-lombardo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
