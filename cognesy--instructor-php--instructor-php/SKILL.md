---
name: project-context
description: Inject live project context via shell preprocessing Use when this capability is needed.
metadata:
  author: cognesy
---

## Project Context

Current date: !`date +%Y-%m-%d`
Git branch: !`git branch --show-current 2>/dev/null || echo unknown`

Use this context when reviewing $ARGUMENTS.

---
> Source: [cognesy/instructor-php](https://github.com/cognesy/instructor-php) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
