---
name: sounio-stdlib-dev
description: Develop the Sounio standard library (`stdlib/`): add/edit modules, keep APIs consistent, and update `STDLIB_REFERENCE.md`; use when working on stdlib `.sio` code or stdlib docs/tests. Use when this capability is needed.
metadata:
  author: sounio-lang
---

# Sounio Stdlib Dev

## Overview

Make focused, idiomatic changes to the Sounio standard library and keep the repo’s stdlib reference accurate.

## Workflow

### 1) Find the right module and baseline

- Start from `STDLIB_REFERENCE.md` to understand intended module status and scope.
- Mirror patterns from the module’s existing files before introducing new idioms.

### 2) Keep Sounio syntax consistent

- Prefer canonical syntax rules from `CLAUDE.md` and `docs/LLM_PROGRAMMING_GUIDE.md`.
- If the compiler does not support a feature yet, avoid using it in stdlib unless it is explicitly “parser-only”.

### 3) Add tests where possible

- Add a small regression in `tests/run-pass/` or module-local tests if the project uses them.
- Keep doc examples either compilable or clearly marked aspirational.

## References

- Stdlib map: `references/stdlib-navigation.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sounio-lang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
