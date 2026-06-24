---
name: git-workflow
description: Branch management, rebasing, and push safety for the Pokédex project. Use when creating branches, rebasing, resolving conflicts, or pushing code. Activates on git operations. Use when this capability is needed.
metadata:
  author: edsonesf
---

# Git Workflow — Branch and Push Safety

Use this skill for all git operations.

## Creating a Branch

Always branch from fresh main:

```bash
git fetch origin
git checkout -b {type}/{issue}-{description} origin/main
```

**Branch naming:** `{type}/{issue}-{description}`
- Types: `feat`, `fix`, `docs`, `test`, `refactor`, `perf`
- Example: `fix/135-to-dto-model-validate`

## Before Pushing

1. Verify not on main:
   ```bash
   git branch --show-current
   ```
2. Run the `testing` skill checklist (all checks must pass)
3. Stage specific files (avoid `git add .`):
   ```bash
   git add {specific files}
   ```
4. Commit with clear message:
   ```
   type: description (#issue)
   ```

## Rebasing

When main has moved forward:

```bash
git fetch origin
git rebase origin/main
```

**If conflicts occur:**
1. Check conflicting files: `git status`
2. Read both versions, resolve by integrating your changes with the logic in main
3. `git add {resolved files}`
4. `git rebase --continue`
5. Run full test suite after resolution
6. If unsure — stop and ask the human

**If detached HEAD after rebase:**
```bash
git checkout -B {branch-name}
```

## Pushing

- **First push:** `git push -u origin {branch-name}`
- **After rebase:** `git push --force-with-lease`
- **Never use `git push --force`** (unless explicitly told to overwrite a destructive remote commit)
- **Never push to main directly**

## Pre-Push Verification

Before every push, confirm:
```bash
git diff --name-only origin/main  # Only expected files changed
git log --oneline origin/main..HEAD  # Only your commits
```

## Safety Rules

- Never run `git pull` on feature branches — use `fetch` + `rebase`
- Never rebase shared branches others may have pulled
- Check `git status` before any checkout or rebase (clean working tree required)
- If working tree is dirty: commit or `git stash` first

---
> Source: [edsonesf/ATU-CSD-POKEDEX](https://github.com/edsonesf/ATU-CSD-POKEDEX) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
