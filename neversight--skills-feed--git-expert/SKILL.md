---
name: git-expert
description: Advanced Git operations wrapper. Optimizes token usage by guiding complex git workflows into efficient CLI commands. Use when this capability is needed.
metadata:
  author: neversight
---

# Git Expert Skill

## ⚡ Token-Efficient Workflow

Do not use `git status` repeatedly. Use this workflow:

1.  **Check State**: `git status -s` (Short format saves tokens)
2.  **Diff**: `git diff --cached` (Only check what you are about to commit)
3.  **Log**: `git log --oneline -5` (Context without the noise)

## 🔄 Common Patterns

### Safe Commit

```bash
git add <file>
git diff --cached # REVIEW THIS!
git commit -m "feat: description"
```

### Undo Last Commit (Soft)

```bash
git reset --soft HEAD~1
```

### Fix Merge Conflict

1. `git status` to see conflict files.
2. Edit file to resolve markers (`<<<<`, `====`, `>>>>`).
3. `git add <file>`
4. `git commit --no-edit`

## 🛡️ Safety Rules

- NEVER use `git push --force`.
- NEVER commit secrets.
- ALWAYS run tests before pushing.

## Related Skills

- [`gitflow`](../gitflow/SKILL.md) - Branch workflow patterns (feature, release, hotfix branches)

## Memory Protocol (MANDATORY)

**Before starting:**
Read `.claude/context/memory/learnings.md`

**After completing:**

- New pattern -> `.claude/context/memory/learnings.md`
- Issue found -> `.claude/context/memory/issues.md`
- Decision made -> `.claude/context/memory/decisions.md`

> ASSUME INTERRUPTION: If it's not in memory, it didn't happen.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
