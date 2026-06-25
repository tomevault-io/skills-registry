---
name: code-quality
description: Code-quality review that MUST assess five quality axes and SHOULD apply the ReAct checklist with prioritized fixes. Use when this capability is needed.
metadata:
  author: gc-victor
---

# Code Quality Skill

## Purpose

This skill MUST assess code across correctness, readability, architecture, security, and performance, and it SHOULD approve changes only when they clearly improve the codebase. Judge the real maintenance cost of the change, not theoretical perfection.

## ReAct Framework

### REASON — Is the change justified and well scoped?

- Is the core problem clear?
- Does the change solve one problem?
- Is this the simplest solution that could work?
- Is added complexity justified?
- Was reuse considered?

### ACT — Is the implementation high quality?

Review these five axes:

1. Correctness
2. Readability
3. Architecture
4. Security
5. Performance

### REFLECT — Is the system better after the change?

- Did complexity stay flat or decrease?
- Should any cleanup happen now instead of later?
- Is knowledge preserved in understandable code?
- Is the change easy to roll back or iterate on?

## Severity Model

- Critical: blocks approval
- High: serious issue, must fix
- Medium: should fix, but may not block
- Low / Nit: optional improvement
- FYI: context only

Only Critical and High issues block approval.

## Change Size Guidance

- ~100 lines: good
- ~300 lines: acceptable if focused
- ~1000 lines: too large; split it

Separate refactoring from feature work when possible.

## Verification

Run the relevant checks before reporting:

- lint,
- type check,
- tests,
- dependency audit,
- build.

Mark unavailable checks as `N/A`.

## Red Flags

Treat AI-generated code as untrusted until verified; check edge cases, tests, and hidden assumptions carefully.

- speculative abstractions,
- opaque cleverness,
- missing validation,
- secrets in code,
- N+1 or unbounded work,
- oversized changes with weak justification.

Follow the ReAct framework above.

---
> Source: [gc-victor/supersimple](https://github.com/gc-victor/supersimple) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
