---
name: twig-guide
description: | Use when this capability is needed.
metadata:
  author: 708u
---

# twig CLI Guide

twig is a CLI tool that simplifies git worktree workflows by automating branch
creation, symlinks, and change management in a single command.

## Commands Overview

| Command | Purpose |
| ------- | ------- |
| `twig init` | Initialize twig configuration |
| `twig add <name>` | Create a new worktree with symlinks |
| `twig remove <branch>...` | Remove worktrees and their branches |
| `twig list` | List all worktrees |
| `twig clean` | Remove unneeded worktrees |
| `twig sync` | Sync symlinks and submodules to worktrees |
| `twig overlay` | Temporarily overlay another branch's files |

## Typical Workflows

### Start new feature work

Create a new worktree for a feature branch:

```bash
twig add feat/new-feature
```

This creates a worktree at the configured destination directory, creates
a new branch if it doesn't exist, and sets up symlinks.

### Move current changes to a new branch

When you realize current work should be on a different branch:

```bash
twig add feat/correct-branch --carry
```

The `--carry` flag moves uncommitted changes to the new worktree.
The source worktree becomes clean.

### Copy changes to a new branch

When you want changes in both the current and new worktree:

```bash
twig add feat/experiment --sync
```

The `--sync` flag copies uncommitted changes to both worktrees.

### Carry only specific files

When you want to carry only certain files:

```bash
twig add feat/new --carry --file "*.go" --file "cmd/**"
```

### Clean up after merging

Remove worktrees for branches that have been merged:

```bash
twig clean
```

This shows candidates and prompts for confirmation. Use `--yes` to skip
the prompt.

### Test a feature branch in another worktree

Temporarily apply another branch's file contents:

```bash
twig overlay feat/x --target main
# ... test in main worktree ...
twig overlay --restore --target main
```

### Test uncommitted changes in another worktree

Include dirty (uncommitted) files from the source worktree:

```bash
twig overlay feat/x --target main --dirty
# ... test including uncommitted changes ...
twig overlay --restore --target main
```

This also works when source and target are at the same commit
(only dirty files are applied).

### Force remove a worktree

Remove a worktree even with uncommitted changes:

```bash
twig remove feat/abandoned -f
```

Use `-ff` to also remove locked worktrees.

## Configuration

see ./references/configuration.md

## Command Details

For detailed information on each command, refer to:

- ./references/commands/add.md - Create worktrees with sync/carry options
- ./references/commands/remove.md - Remove worktrees and branches
- ./references/commands/list.md - List worktrees
- ./references/commands/clean.md - Clean merged worktrees
- ./references/commands/sync.md - Sync symlinks and submodules
- ./references/commands/overlay.md - Overlay branch files temporarily
- ./references/commands/init.md - Initialize configuration
- ./references/configuration.md - Configuration file details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/708u) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
