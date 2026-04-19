---
name: jj-workflow
description: Guides Claude on using Jujutsu (jj) version control system. Use when working with jj repositories, making commits, syncing changes, managing version control workflows, resolving conflicts, rewriting history, or managing branches. Also reference for Git users transitioning to Jujutsu or comparing workflows between systems. Use when this capability is needed.
metadata:
  author: roei12
---

# Jujutsu (jj) Workflow

Jujutsu is a modern version control system that provides a simpler mental model than Git while remaining Git-compatible. This skill covers the core concepts and workflow commands.

## Core Concepts

### Changes vs Commits
- **Change**: A mutable revision identified by a change ID (e.g., `kkmpptxz`). Changes can be modified.
- **Commit**: An immutable snapshot identified by a commit ID (SHA). Once created, commits are permanent.
- The working copy (`@`) is always a change that can be modified freely.

### Working Copy
- The working copy is denoted by `@` and represents your current state.
- `@-` refers to the parent of the working copy.
- Unlike Git, there's no staging area—all changes are automatically tracked.

## Essential Commands

### Viewing State

```bash
jj status          # Show working copy status
jj log             # View commit history
jj diff            # Show uncommitted changes
jj show @          # Show current change details
```

### Creating and Describing Changes

```bash
jj new                           # Create a new empty change on top of @
jj describe -m "feat: message"   # Set commit message for current change
jj new -m "feat: message"        # Create new change with message
```

### Syncing with Remote

```bash
jj tug             # Fetch updates and rebase current change onto latest remote
jj git fetch       # Fetch from remote without rebasing
jj git push        # Push current changes to remote
```

### Modifying History

```bash
jj squash                    # Squash current change into parent
jj squash --into @-          # Explicitly squash into parent
jj split <file> -m "msg"     # Split specific files into their own commit
jj edit <change-id>          # Edit an existing change
```

### Working with Branches

```bash
jj branch create <name>      # Create a branch pointing to @
jj branch set <name>         # Move branch to current change
jj branch list               # List all branches
```

## Commit Message Format

Use **Conventional Commits** format:

- `feat:` — New feature
- `fix:` — Bug fix
- `refactor:` — Code change that neither fixes a bug nor adds a feature
- `perf:` — Performance improvement
- `docs:` — Documentation changes
- `chore:` — Maintenance tasks
- `test:` — Adding or updating tests

Examples:
```bash
jj describe -m "feat: add user authentication"
jj describe -m "fix: resolve null pointer in parser"
jj describe -m "refactor: extract validation logic"
```

## Common Patterns

### Starting New Work
```bash
jj tug                              # Sync with remote
jj new -m "feat: new feature"       # Start new change
# ... make changes ...
jj new                              # Finalize and start next
```

### Amending Current Change
Make changes directly—all are tracked in `@`. Use `jj describe` to update message.

### Rebasing onto Latest
```bash
jj tug    # Fetch and rebase in one command
```

### Checkpoints
Use checkpoints before risky operations:

```bash
jj new -m "checkpoint: safe state"    # Create checkpoint
jj new -m "trying: experiment"         # Work on risky change
# If needed: jj op log → jj op restore <id>
jj squash -m "feat: final result"      # Squash when done
```

## Conflict Resolution

Conflicts are recorded as state and resolved later. See [CONFLICT-RESOLUTION.md](references/conflict-resolution.md) for:
- Detecting and resolving conflicts
- Manual and automated resolution
- Conflict scenarios (rebase, merge)

## Advanced Workflows

For expanded patterns and complex workflows:
- Bisecting, undoing operations, splitting changes
- Collaboration patterns (review, PRs, merging)
- Testing and experimental workflows

See [PATTERNS.md](references/patterns.md).

## Git Comparison

Reference for Git users transitioning to Jujutsu, including:
- Command mapping table
- Workflow comparisons
- Mental model shifts
- Common pitfalls

See [GIT-COMPARISON.md](references/git-comparison.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roei12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
