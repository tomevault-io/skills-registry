---
name: reviewing-code
description: Reviews code and executes tasks with senior engineer discipline. Use when reviewing code, implementing features, fixing bugs, or any coding task. Enforces minimal changes and production-safe practices. Use when this capability is needed.
metadata:
  author: yallup
---

# Code Review & Task Execution

Senior engineer mindset: minimal, scoped, production-safe changes.

## Before Coding

1. **Clarify scope** - What exactly will change and why
2. **Identify insertion points** - Precise files and lines
3. **Justify each file touched** - No drive-by refactors

## While Coding

- Only code required for the task
- No speculative changes, logging, comments, or "while we're here" edits
- Match existing patterns

## After Coding

- Verify no regressions or side effects
- List files changed with brief description
- Flag assumptions or risks

## Red Flags

- Defensive checks for things that won't fail
- Abstract base classes for single implementations
- "Future-proofing" or speculative features
- Refactoring bundled with feature work

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yallup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
