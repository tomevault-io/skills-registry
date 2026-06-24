---
name: sounio-codegen-backends
description: Work on Sounio code generation and backends (Cranelift, LLVM, GPU, native ELF backend); use when editing `compiler/src/codegen/`, `compiler/src/backend/`, or backend-adjacent runtime glue. Use when this capability is needed.
metadata:
  author: sounio-lang
---

# Sounio Codegen + Backends

## Overview

Implement and debug lowering/codegen without breaking IR invariants or backend feature-flag boundaries.

## Workflow

### 1) Pick the backend path and feature flags

- Cranelift: `compiler/src/codegen/cranelift.rs` (feature: `jit`)
- MIR→Cranelift: `compiler/src/codegen/mir_cranelift.rs`
- LLVM: `compiler/src/codegen/llvm/` (feature: `llvm`)
- GPU: `compiler/src/codegen/gpu/` (feature: `gpu`)
- Native backend (ELF/linker): `compiler/src/backend/native/`

### 2) Keep IR invariants explicit

- If you change HLIR/MIR shape, validate passes and analyses that assume SSA form.
- Be conservative around effectful ops when doing backend optimizations.

### 3) Add targeted tests

- Prefer Rust tests close to the backend (`compiler/src/**/tests` and `compiler/tests/`).
- GPU tests typically require `--features gpu` (see `.claude/commands/sounio-gpu.md`).

## References

- Codegen map: `references/codegen-navigation.md`
- Feature flags: `references/feature-flags.md`
- Claude command references: `.claude/commands/sounio-gpu.md`, `.claude/commands/sounio-build.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sounio-lang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
