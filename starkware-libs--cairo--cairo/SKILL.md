---
name: golden-tests
description: Help with Cairo's file-based golden test framework. Use /golden-tests for creating test runners, writing test files, or fixing test outputs. Use when this capability is needed.
metadata:
  author: starkware-libs
---

# Golden Tests Skill

Helps with Cairo's `cairo_lang_test_utils` file-based test framework.

## When Invoked

Read the full documentation at `~/.claude/skills/golden-tests/docs.md` and then help the user with their request.

Common tasks:
- Creating new test runners (`test_file_test!` macro)
- Writing test data files (`//! >` tag format)
- Fixing test outputs (`CAIRO_FIX_TESTS=1`)
- Understanding test failures

---
> Source: [starkware-libs/cairo](https://github.com/starkware-libs/cairo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-02 -->
