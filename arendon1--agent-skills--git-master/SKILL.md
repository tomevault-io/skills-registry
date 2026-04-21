---
name: git-master
description: >- Use when this capability is needed.
metadata:
  author: arendon1
---

# Git Master

Expert Git operations with built-in safety rails and best practices.

## 🚀 Self-Deployment & Bootstrapping

If this skill's Git workflows are not appearing in your agent's slash-commands, run the following command from the skill's root directory:
`python scripts/bootstrap.py --workspace .`

This will automatically detect your agent's configuration directory (e.g., `.agents`, `.cursor`, `.gemini`, or `.agent`) and deploy the necessary `.md` or `.mdc` files.

## 🚨 CRITICAL RULES

1. **Safety First**: Before ANY destructive operation (`reset --hard`, `force push`, `filter-repo`), you MUST:
    * Explicitly warn the user.
    * Offer to create a backup branch.
    * Ask for confirmation.
2. **User Preference**: At the start of a task, ask: "Do you want automatic commits or manual control?"
3. **Cross-platform Paths**: Use forward slashes (`/`) in examples for cross-platform compatibility.
    Windows git handles forward slashes correctly.

## 📚 References

Load these specific references based on the valid user task:

| Topic | Reference File |
| :--- | :--- |
| **Basics** | `references/basic-operations.md` |
| **Branch/Merge** | `references/branching-merging.md` |
| **Remotes** | `references/remote-operations.md` |
| **Advanced** | `references/advanced-operations.md` |
| **Platforms** | `references/platform-guide.md` |

## 🛠️ Quick Reference

### Safety Check Pattern

```bash
# Before dangerous ops
git status
git branch backup-$(date +%Y%m%d-%H%M%S)
# Proceed only after confirmation
```

### Most Common Operations

**Status & Log**

```bash
git status
git log --oneline --graph --all
```

**Undo Last Commit (Keep Changes)**

```bash
git reset --soft HEAD~1
```

**Emergency Recovery**

```bash
git reflog
# Find the hash before the mistake, then:
# git reset --hard <hash> (if local)
# git cherry-pick <hash> (to recover specific commit)
```

## 🔍 When to Use

* **Repository Setup**: Init, clone, config.
* **Daily Work**: Commit, push, pull, branch, merge.
* **Fixing Issues**: Conflicts, detached HEAD, wrong branch.
* **Advanced**: Interactive rebase, stash, submodules, worktrees.
* **Disaster Recovery**: Reflog, fsck, filter-repo.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arendon1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
