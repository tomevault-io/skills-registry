---
name: skillstash-review
description: Review skillstash PRs and summarize validation issues Use when this capability is needed.
metadata:
  author: galligan
---

# Skillstash Review

Use this skill to review a skill PR and report validation issues.

## When this skill activates

- A PR updates `skills/**`
- A review or validation is requested

## What this skill does

1. Run validation checks (structure, frontmatter, naming, size).
2. Report blocking issues with file/line context.
3. Suggest improvements for non-blocking issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/galligan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
