---
name: unit-test
description: Write and review xterm.js unit tests with project conventions. Use when editing `*.test.ts` files or when asked for test authoring guidelines. Use when this capability is needed.
metadata:
  author: xtermjs
---

# Unit Test Instructions

When writing unit tests, follow these rules:

- When writing unit tests for addons, always create a real xterm.js instance instead of mocking it.
- Prefer `assert.ok` over `assert.notStrictEqual` when checking something is undefined or not.
- Avoid comments as most tests should be self-documenting.

---
> Source: [xtermjs/xterm.js](https://github.com/xtermjs/xterm.js) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
