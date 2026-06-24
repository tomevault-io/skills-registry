---
name: rust-tauri-backend
description: Implement or modify VMark's Rust/Tauri backend. Use when adding Tauri commands, menu items, filesystem integration, or platform behaviors in src-tauri. Use when this capability is needed.
metadata:
  author: xiaolai
---

# Rust + Tauri Backend (VMark)

## Overview
Apply VMark backend conventions for Tauri v2 and Rust code.

## Workflow
1) Identify the command or menu integration needed in `src-tauri/src`.
2) Use modern Rust formatting: `format!("{variable}")`.
3) Keep changes scoped; avoid unrelated refactors.
4) If UI interaction is required, wire through `invoke()` or `emit()` properly.
5) Update relevant tests or docs when behavior changes.

## References
- `references/paths.md` for backend entry points.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xiaolai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
