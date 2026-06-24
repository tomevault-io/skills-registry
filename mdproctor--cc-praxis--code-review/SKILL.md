---
name: code-review
description: > Use when this capability is needed.
metadata:
  author: mdproctor
---

# Code Review

Reads the project type from CLAUDE.md, then loads the appropriate language-specific
review checklist. Universal review principles apply to all types.

## Step 0 — Load universal principles

**Load `~/.hortora/garden/approaches/code-review.md`** before proceeding.
All review severity models, reporting formats, and workflow steps are defined there.

## Step 1 — Detect project type

```bash
grep -A 2 "## Project Type" CLAUDE.md 2>/dev/null | grep "^type:"
```

Extract: `java` | `ts` | `python` | `generic`

If type is missing or `generic`, inspect staged files:
- `.java` files or `pom.xml` changed → treat as `java`
- `.ts` / `.tsx` files changed → treat as `ts`
- `.py` files changed → treat as `python`
- Mixed → flag and ask user which review to run

## Step 2 — Load language-specific checklist

| Project type | File to read |
|---|---|
| `java` | `~/.claude/skills/code-review/java.md` |
| `ts` | `~/.claude/skills/code-review/typescript.md` |
| `python` | `~/.claude/skills/code-review/python.md` |

Read the file, then execute the review workflow it describes.

## Skill Chaining

**Invoked by:** [`java-dev`], [`ts-dev`], [`python-dev`] before committing; [`java-git-commit`] when no review has been run this session

**Invokes:** [`security-audit`] for auth/payment/PII code (offered, not automatic); [`git-commit`] after approval if user wants to commit

---
> Source: [mdproctor/cc-praxis](https://github.com/mdproctor/cc-praxis) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
