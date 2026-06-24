---
name: code-review
description: > Use when this capability is needed.
metadata:
  author: PetrovC
---

# Code Review Skill

## Goal
Find real problems before they reach production. Not cosmetic issues.

A good review catches: incorrect behavior, regressions, security holes, missing tests,
and architectural drift. It does not nitpick style unless style hides a real problem.

## Quick reference

| Focus | Check |
|---|---|
| Scope | Small, reviewable PRs (one concern per PR, < 200 lines preferred) |
| Security | Sanitized inputs, parameterized SQL, authorized endpoints, no committed secrets |
| Correctness | Handle async errors, prevent race conditions, check null/undefined boundary cases |
| Testing | Verify changed code is covered by automated unit/integration tests |

## Full guidance
Extended how-to, patterns, anti-patterns, and checklists: [`SKILL.deep.md`](SKILL.deep.md)

---
> Source: [PetrovC/ai-agent-kit](https://github.com/PetrovC/ai-agent-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
