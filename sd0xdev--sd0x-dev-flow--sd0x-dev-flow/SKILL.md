---
name: codex-review-branch
description: Fully automated review of an entire feature branch using Codex MCP Use when this capability is needed.
metadata:
  author: sd0xdev
---

# Codex Review Branch

Thin entry-point skill — routes to the parent skill for full workflow.

## Parent Skill

This is the **Branch** variant of `codex-code-review`. Full workflow, prompt templates, and review logic are defined in the parent skill.

See `@skills/codex-code-review/SKILL.md`

## Variant

| Property | Value |
|----------|-------|
| Scope | Full branch (all commits since base) |
| Pre-checks | None |
| Prompt template | `@skills/codex-code-review/references/codex-prompt-branch.md` |

## Trigger

- Keywords: branch review, full branch, review branch, codex-review-branch

## When NOT to Use

- Quick diff-only review (use `/codex-review-fast`)
- Full review with lint + build (use `/codex-review`)
- Document review (use `/codex-review-doc`)

---
> Source: [sd0xdev/sd0x-dev-flow](https://github.com/sd0xdev/sd0x-dev-flow) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
