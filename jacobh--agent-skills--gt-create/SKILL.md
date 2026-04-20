---
name: gt-create
description: Create a Graphite branch with staged changes. Use when the user mentions Graphite, gt create, stacked PRs, or wants to create a branch for review using Graphite's workflow. Runs gt create with appropriate flags and commit message. Use when this capability is needed.
metadata:
  author: jacobh
---

# Graphite Branch Creation

## Background

Graphite is a CLI tool and web platform for managing stacked pull requests. It wraps git and provides commands like `gt create`, `gt submit`, and `gt sync` to make working with stacks of dependent PRs easier. Stacked PRs help break large changes into smaller, reviewable pieces while maintaining dependencies between them.

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits on this branch: !`git log --oneline -5`

## Your task

Create a new Graphite branch with the current changes using `gt create`.

Run `gt create` with:
- `-a` flag to stage all changes (no need for separate `git add`)
- `--no-interactive` flag
- A commit message

**Note:** When surgically staging specific files or hunks, use `git add` manually instead of `-a`.

### Commit message guidelines

- Write a clear, concise commit message based on the staged changes
- First line should be a short summary (50 chars or less ideally)
- If more context is needed, include a body separated by a blank line

### Multiline commit messages

Use bash's `$'...'` syntax to embed literal newlines in the commit message. This properly interprets `\n` as actual newlines:

```bash
gt create -a --no-interactive -m $'Short summary of changes\n\nLonger explanation of why this change was made, any relevant context, etc.\n\n- Bullet points work too'
```

### Skipping hooks

To skip pre-commit hooks, use `--no-verify`. Note that Graphite uses `--no-verify`, not `-n` (which is what `git commit` uses).

### Command format

```bash
# Stage all changes
gt create -a --no-interactive --no-verify -m $'summary\n\noptional body'

# Or stage specific files first
git add src/foo.ts src/bar.ts && gt create --no-interactive --no-verify -m $'summary'
```

If the user provided specific instructions via `$ARGUMENTS`, incorporate them into the commit message. Otherwise, derive an appropriate message from the changes.

User instructions: $ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jacobh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
