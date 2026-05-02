---
name: bugfix
description: Use when given a bug or error report. Follows the Prove It Pattern — reproduce first with a test, then fix, then confirm the test passes.
metadata:
  author: aomartinao
---

# Bug Fixes: Prove It Pattern

When given a bug or error report, the **first step** is to spawn a subagent to write a test that reproduces the issue. Only proceed once reproduction is confirmed.

## Test Level Hierarchy

Reproduce at the lowest level that can capture the bug:

1. **Unit test** — Pure logic bugs, isolated functions (lives next to the code)
2. **Integration test** — Component interactions, API boundaries (lives next to the code)
3. **UX spec test** — Full user flows, browser-dependent behavior (lives in `apps/web/specs/`)

## For Every Bug Fix

1. **Reproduce with subagent** — Spawn a subagent to write a test that demonstrates the bug. The test should *fail* before the fix.
2. **Fix** — Implement the fix.
3. **Confirm** — The test now *passes*, proving the fix works.

If the bug is truly environment-specific or transient, document why a test isn't feasible rather than skipping silently.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aomartinao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
