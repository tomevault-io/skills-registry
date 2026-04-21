---
name: skills-analyzer
description: Analyzes Claude Code Skills for lint issues and duplicate content. Use when user asks to analyze skills, lint skills, check quality, or find duplicates.
metadata:
  author: recca0120
---

# Skills Analyzer

## When to Use

This is **Step 1** of the refactoring workflow:

1. **analyze** ← You are here
2. extract → Run after analysis identifies issues
3. validate → Run after extraction completes

## Workflow

### Step 1: Scan Skills

Locate all `SKILL.md` files in `.claude/skills/` or `~/.claude/skills/`.

### Step 2: Check Issues

For each SKILL.md, check lint rules (frontmatter, content, naming, reference, security).

> Rule details: `references/lint-rules.md`

### Step 3: Detect Duplicates

Compare content across all SKILL.md files for exact (100%), near (≥80%), or structural duplicates.

> Detection patterns: `references/duplicate-patterns.md`

### Step 4: Calculate Quality Score

Score = 100 - (Errors × 10) - (Warnings × 5) + Bonus (max 100)

> Score details: `references/lint-rules.md#quality-score-calculation`

## Output

```markdown
# Analysis Report

## Summary
- Skills: [count] | Score: [0-100] | Errors: [n] | Warnings: [n]

## Issues
### `[rule]` skill-name/SKILL.md:[line]
**Problem**: [description]
**Fix**: [before/after code]

## Duplicates
- [type]: [locations] → Extract to shared/[name].md
```

## Next Step

If issues found → Run **skills-extractor** to refactor

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/recca0120) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
