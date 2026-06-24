---
name: code-reviewer
description: Review code for bugs, security flaws, maintainability risks, and missing test coverage. Use when evaluating a diff, pull request, or source file before merge. Use when this capability is needed.
metadata:
  author: danaidev
---

# Code Reviewer

## Review Workflow

1. Identify language/framework and intended behavior.
2. Scan for correctness and regression risk first.
3. Check security-sensitive paths and input handling.
4. Evaluate maintainability and readability tradeoffs.
5. Identify missing tests and suggest targeted additions.

## Output Format

- Findings ordered by severity.
- File + line reference for each issue.
- Concrete fix suggestion.
- Short summary of residual risks.

## Severity Guide

- Critical: exploitability, data loss, auth bypass.
- High: likely production breakage.
- Medium: correctness or maintainability issue with clear impact.
- Low: polish and consistency issues.

## Edge Cases

- If context is incomplete, state assumptions explicitly.
- If no issues are found, state that clearly and highlight test gaps.

## Reference

Use `references/checklist.md` for a consistent scan order.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danaidev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
