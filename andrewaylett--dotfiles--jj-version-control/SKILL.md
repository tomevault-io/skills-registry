---
name: jj-version-control
description: Use when working with version control, Git operations, commits, branches, bookmarks, PRs, rebasing, merging, pushing, pulling, fetching, status, diff, log, history, stashing, cherry-picking, or any source control management task. Triggers on phrases like "git status", "commit changes", "create branch", "push to remote", "pull request", "rebase", "merge", "show history", "show log", "checkout", "switch branch".
metadata:
  author: andrewaylett
---

# Jujutsu (jj) Version Control

Use Jujutsu (`jj`) instead of Git for version control operations in repositories that have a `.jj` directory.

## Detection

Before running version control commands, check if the repository uses jj:

1. Look for a `.jj` directory in the repository root
2. If `.jj` exists: use jj commands
3. If `.jj` does not exist: use standard Git commands

## Key Concepts

Jujutsu differs fundamentally from Git:

**No staging area**: All changes in the working copy are automatically part of the current commit. There is no `git add` equivalent - files are tracked automatically.

**Working copy is a commit**: Your working directory always represents a commit (shown as `@` in the log). Changes appear immediately in that commit.

**Change IDs vs commit hashes**: Each commit has both a commit hash and a stable change ID. The change ID persists across rebases and amendments, making it easier to track logical changes.

**Bookmarks, not branches**: What Git calls "branches" are "bookmarks" in jj. They're lightweight pointers to commits, similar to Git branches but with different semantics for movement.

**Conflicts are commits**: Conflicted states are valid commits in jj. You can continue working, create new commits, and resolve conflicts later.

**Operation log**: Every jj command that modifies the repository is recorded. Use `jj op log` to see history and `jj undo` to reverse any operation.

## Essential Command Reference

| Task | jj Command |
|------|------------|
| Status | `jj st` |
| Diff | `jj diff` |
| Log | `jj log` |
| Set commit message | `jj desc -m "message"` |
| Create new commit | `jj new` |
| Commit and move on | `jj commit -m "message"` |
| Edit previous commit | `jj edit <rev>` |
| Abandon commit | `jj abandon` |
| Create bookmark | `jj bookmark create <name>` |
| List bookmarks | `jj bookmark list` |
| Fetch from remote | `jj git fetch` |
| Push to remote | `jj git push` |
| Push bookmark | `jj git push --bookmark <name>` |

See `references/git-to-jj-commands.md` for comprehensive mappings.

## Common Workflows

### Making Changes

```bash
# Check current status
jj st

# View changes
jj diff

# Set commit message on current work
jj desc -m "Add feature X"

# Create new commit to continue working
jj new
```

### Creating a PR Branch

```bash
# Create a bookmark at current commit
jj bookmark create feature-name

# Push the bookmark to remote
jj git push --bookmark feature-name
```

### Updating a PR

```bash
# Make changes (automatically in working copy commit)
# Set/update description
jj desc -m "Updated implementation"

# Push - jj auto-force-pushes bookmarks you own
jj git push --bookmark feature-name
```

### Rebasing

```bash
# Rebase current commit onto main
jj rebase -d main

# Rebase a commit and all descendants
jj rebase -s <source> -d <destination>
```

### Squashing Changes

```bash
# Squash working copy into parent
jj squash

# Squash with message
jj squash -m "Combined commit message"
```

### Undoing Mistakes

```bash
# View operation history
jj op log

# Undo last operation
jj undo

# Restore to specific operation
jj op restore <operation-id>
```

## Workflow Preferences

When completing work on a commit, run `jj commit -m "description"` to set the commit message and create a new commit for the next set of changes

After, if appropriate and the repository uses pre-commit, you may run `jj pre-commit` to execute tests, linting, and formatting. This will always create a fresh commit.

## What NOT to Do

- Do NOT use `git add` - it's not needed and may cause confusion
- Do NOT use `git stash` - the working copy is already a commit
- Do NOT use `git pull` - use `jj git fetch` instead (pulling conflates fetch and merge)
- Do NOT reference Git's staging area or index concepts

## References

- `references/git-to-jj-commands.md` - Complete Git to jj command mapping
- `references/jj-concepts.md` - Detailed conceptual differences from Git

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewaylett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
