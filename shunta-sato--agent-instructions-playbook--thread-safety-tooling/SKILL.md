---
name: thread-safety-tooling
description: Thread-safety verification toolkit. Use when C/C++ code introduces threads/locks/atomics or when race conditions/deadlocks are plausible. Provides TSan and compile-time thread-safety analysis guidance. Use when this capability is needed.
metadata:
  author: shunta-sato
---

## Purpose

Make concurrency changes verifiable, not hope-driven.

## When to use

Use this skill when touching:
- C/C++ concurrency primitives
- shared mutable state with locks or atomics
- code paths with plausible races or deadlocks

## How to use

0) Open `references/thread-safety-tooling.md`.
1) Choose verification stack (TSan / static thread-safety analysis / stress tests).
   - Quick start helper: `python scripts/init_artifact.py --kind concurrency-matrix --slug <ticket-or-topic>`
2) Provide reproducible commands or CI steps (placeholders if repo differs).
3) Define a minimal stress scenario (what to load; what to assert).
4) Record known limitations and suppression strategy (avoid blanket suppressions).

## Output expectation

- Produce a **Concurrency Verification Matrix**:
  - Tool → What it catches → How to run → Cost/limits

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shunta-sato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
