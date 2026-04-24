---
name: manage-worktrees
description: Manages Git worktrees using gwq. Lists, shows status, removes, and navigates worktrees.
metadata:
  author: sato-dev1234
---

# /manage-worktrees

Manages Git worktrees using gwq.

## Quick Reference

| Command | Description |
|---------|-------------|
| list | Show all worktrees |
| status | Show git status of all worktrees |
| remove | Remove a worktree |
| path | Show worktree directory path |

**Prerequisite**: gwq must be installed. See `docs/prerequisites.md`.

## Progress Checklist

```
- [ ] Step 1: Parse command
- [ ] Step 2: Execute command
- [ ] Step 3: Report result
```

## Steps

### Command: list

Show all worktrees for the current repository.

```bash
gwq list
```

### Command: status

Show git status of all worktrees.

```bash
gwq status
```

### Command: remove

Remove a worktree.

1. If no worktree specified, show list first:
   ```bash
   gwq list
   ```

2. Confirm with user before removal using AskUserQuestion.

3. Remove the worktree:
   ```bash
   gwq remove <worktree-name>
   ```

Use `--force` flag only if explicitly requested by user.

### Command: path

Show the directory path of a worktree.

```bash
gwq list
```

Parse output to find the worktree path matching the query, then display:
```
Worktree path: <path>

To navigate in your terminal: cd <path>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sato-dev1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
