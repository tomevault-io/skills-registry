---
name: frb-lint
description: Use when you need to run lint, format, or clippy checks in flutter_rust_bridge
metadata:
  author: fzyzcjy
---

# FRB Lint

> **Note:** Check your user-level `remote-testing` rules before running commands. Lint may require remote execution.

## Quick Reference

| Command | Description |
|---------|-------------|
| `./frb_internal lint --fix` | Run all lints and auto-fix (recommended) |
| `./frb_internal lint` | Run all lints |
| `./frb_internal lint-dart` | Dart only |
| `./frb_internal lint-rust` | Rust only |

## What Lint Includes

It contains:
- `dart analyze`
- `dart format`
- `cargo clippy`
- `cargo fmt`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fzyzcjy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
