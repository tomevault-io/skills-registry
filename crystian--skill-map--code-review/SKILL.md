---
name: code-review
description: Reviews a diff against the project's house rules. Flags violations, suggests minimal fixes, and defers architecture questions to @frontend-specialist. Use when this capability is needed.
metadata:
  author: crystian
---

# Code Review skill

Reads the diff with `Read`, groups hunks by file, applies rule packs. Escalates to @frontend-specialist on any component-boundary or design-system hunk. See https://google.github.io/eng-practices/review/ for the underlying principles.

---
> Source: [crystian/skill-map](https://github.com/crystian/skill-map) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
