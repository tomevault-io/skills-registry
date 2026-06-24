---
name: pr-review
description: Review a Pull Request for code quality, tests, merge conflicts, and readiness to merge. Use when this capability is needed.
metadata:
  author: agentjido
---

# PR Review

Review a Pull Request by analyzing code quality, tests, and overall changes.

## When to Use

Use this skill when asked to:
- Review a pull request
- Check if a PR is ready to merge
- Analyze PR code quality

## Workflow

1. Add a git remote for the PR branch to enable pushing changes
2. Analyze the quality of the code and tests
3. Check for merge conflicts with `main` and advise on fixes if conflicts exist
4. Provide feedback on whether the PR is ready to merge

## Usage

```
Review this Pull Request: <url>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentjido) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
