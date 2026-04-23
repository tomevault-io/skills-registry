---
name: review
description: | Use when this capability is needed.
metadata:
  author: darkroomengineering
---

# Code Review

Reviews against the full Darkroom quality checklist defined in the reviewer agent.

Focus areas: TypeScript strictness, React patterns, accessibility, performance, security, file structure.

## Current State
- Branch: !`git branch --show-current 2>/dev/null || echo "unknown"`
- Staged files: !`git diff --staged --stat 2>/dev/null || echo "nothing staged"`
- Unstaged files: !`git diff --stat 2>/dev/null || echo "nothing unstaged"`

## Get Changes

```bash
# Unstaged changes
git diff

# Staged changes
git diff --staged

# Specific file
git diff path/to/file
```

## Output Format

```
## Summary
[1-2 sentence overview]

## Critical Issues
- [Must fix before merge]

## Warnings
- [Should fix, but not blocking]

## Suggestions
- [Nice to have improvements]

## Verdict
[APPROVED / NEEDS CHANGES / BLOCKED]
```

## Remember

- Be constructive, not just critical
- Explain WHY something is an issue
- Suggest specific fixes
- If you find a pattern worth remembering, store it as a learning

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darkroomengineering) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
