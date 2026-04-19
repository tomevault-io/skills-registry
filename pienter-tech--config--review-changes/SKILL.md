---
name: review-changes
description: Review recent repository changes for correctness, security, performance, maintainability, and missing tests. Use when the user asks for a code review, review of commits, or quality assessment before merge. Use when this capability is needed.
metadata:
  author: pienter-tech
---

# Review Changes

Perform a practical review focused on high-impact issues first.

## Inputs to inspect

- `git log --oneline -10 --graph --decorate`
- `git status --porcelain`
- `git diff --cached --stat`
- `git diff --stat`
- `git diff HEAD~5..HEAD --name-only`

## Review priorities

1. Correctness and regressions
2. Security and secret exposure
3. Performance risks
4. Maintainability and architecture fit
5. Testing and documentation gaps

## Output format

Provide findings first, ordered by severity:
- Critical
- Major
- Minor

For each finding, include:
- Why it matters
- Concrete fix guidance
- File reference

If no issues are found, state that explicitly and note residual risks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pienter-tech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
