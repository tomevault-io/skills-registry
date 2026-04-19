---
name: regenerate
description: Run git commands to discard any pending uncommitted changes Use when this capability is needed.
metadata:
  author: jbouder
---

# Regenerate Skill

Discard uncommitted git changes to restore a clean working state. Like an ancient technique that restores order, this skill resets your environment to its rightful form.

## Warning

This skill performs **destructive operations** that cannot be undone. Always confirm with the user before executing commands that discard changes.

## Capabilities

- **Check State**: View current git status and pending changes to see where the balance is broken.
- **Discard Changes**: Reset tracked files and remove untracked files to return to a clean state.
- **Stash Changes**: A safe alternative to save progress before restoring the environment.

## Usage Examples

**Discard all uncommitted changes:**

```
/regenerate
```

**Preview changes (Dry run):**

```
/regenerate --dry-run
```

**Stash changes instead of discarding:**

```
/regenerate --stash
```

**Discard changes to a specific file:**

```
/regenerate src/app.js
```

## Workflow

1. **Show Status**: Display `git status` and `git diff --stat` to show what will be affected.
2. **Confirm**: Ask for confirmation before any destructive action.
3. **Execute**: Run the appropriate git commands with focus.
4. **Verify**: Show final `git status` to confirm the clean state.

## Commands Reference

| Command                       | Purpose                                |
| ----------------------------- | -------------------------------------- |
| `git status`                  | Show working tree status               |
| `git diff`                    | Show unstaged changes                  |
| `git diff --cached`           | Show staged changes                    |
| `git restore <file>`          | Discard changes in working directory   |
| `git restore --staged <file>` | Unstage changes                        |
| `git checkout -- <file>`      | Discard changes (legacy)               |
| `git clean -fd`               | Remove untracked files and directories |
| `git clean -fdn`              | Dry run - preview removal              |
| `git reset --hard HEAD`       | Reset all tracked files to HEAD        |
| `git stash`                   | Save changes to stash                  |
| `git stash pop`               | Restore stashed changes                |

## Safety Notes

- **Always show status first** before any destructive operation.
- **Always confirm** with the user before running `git clean -f`, `git reset --hard`, or `git checkout --`.
- **Suggest stash** as a safer alternative when appropriate.
- **Never force** operations without explicit user consent.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbouder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
