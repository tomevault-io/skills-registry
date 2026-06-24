---
name: code-review
description: Review Python code for bugs, security issues, and style problems Use when this capability is needed.
metadata:
  author: roomkit-live
---

# Code Review Skill

When reviewing Python code, follow this checklist:

1. **Security** -- check for SQL injection, command injection, XSS, and unsafe deserialization.
2. **Logic errors** -- verify edge cases, off-by-one errors, and unhandled None values.
3. **Performance** -- flag O(n^2) patterns, unnecessary copies, and blocking calls in async code.
4. **Style** -- enforce project conventions from the style-guide reference.

Always start by reading the style-guide reference to understand the project's conventions
before commenting on style issues. Cite specific line numbers when reporting problems.

---
> Source: [roomkit-live/roomkit](https://github.com/roomkit-live/roomkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
