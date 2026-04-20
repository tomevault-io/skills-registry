---
name: code-review
description: Two-pass code review system using junior and senior personas to catch issues before presenting work to the developer. Use when this capability is needed.
metadata:
  author: spenceriam
---

# Code Review Skill

This skill implements a two-pass code review system to ensure quality before work is presented as complete.

## What it is

A structured review process that uses two distinct personas with different focus areas:

- A junior developer focusing on fundamentals and surface-level issues.
- A senior developer focusing on architecture, edge cases, and long-term maintainability.

## What it does

- First pass (Junior): surfaces syntax issues, obvious bugs, style violations, and unclear naming.
- Second pass (Senior): evaluates architecture, edge cases, performance, security, and technical debt.
- Produces actionable feedback that should be addressed before delivery.

## Why it is important

Single-pass AI reviews tend to miss the same categories of issues repeatedly. By splitting review responsibilities across personas with different experience levels, more defects are caught before code reaches the developer. Junior finds what is obviously wrong; Senior finds what is subtly wrong.

## Process

1. Complete the implementation or change set.
2. Load `skills/code-review/PERSONA-junior.md` and review the diff as a junior developer.
3. List issues with file and line references and address them.
4. Load `skills/code-review/PERSONA-senior.md` and review the updated diff as a senior developer.
5. Produce a prioritized list of remaining issues and address them.
6. Only after both passes are satisfied should the work be presented to the developer.

This skill is harness-agnostic and can be applied in Claude Code, Gemini CLI, Goose, Verdent, or any other agentic environment that can load persona documents.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spenceriam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
