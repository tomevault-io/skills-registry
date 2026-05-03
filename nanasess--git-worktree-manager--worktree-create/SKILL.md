---
name: worktree-create
description: Create git worktrees for a task across all repositories in the project. Use when starting parallel work, creating isolated workspaces for subagents, or branching all repos at once. Use when this capability is needed.
metadata:
  author: nanasess
---

# Create worktrees for a task

Run the following command from the project root:

```bash
worktree create $ARGUMENTS
```

## What this does

1. Detects git repositories (single repo or multiple sub-repos)
2. Runs `git fetch origin` on each repository
3. Creates a worktree for each repository based on origin/HEAD (default branch)
4. Generates CLAUDE.md with worktree context (task name, working directory, project root)
5. Symlinks non-git items into the task directory (multi-repo only)
6. Executes `.worktreerc` `post_create()` hook if present
7. Auto-installs dependencies based on lock files (unless `--no-install`)

## Worktree layout

Worktrees are placed outside the project to avoid polluting code search:

### Multi-repo project

```
~/git-repos/
├── my-project/                  # Original project (you are here)
├── my-project.worktrees/        # Created by this command
│   └── <task-name>/
│       ├── CLAUDE.md            # Generated with worktree context
│       ├── repo-a/  (branch: <task-name>)
│       └── repo-b/  (branch: <task-name>)
```

### Single-repo project

```
~/git-repos/
├── my-project/                  # Original project (you are here)
├── my-project.worktrees/
│   └── <task-name>/             # Worktree (branch: <task-name>)
│       ├── CLAUDE.md            # Generated with worktree context
│       └── src/
```

## After creation

The task directory path is printed at the end. Use it to:
- Point subagents to the isolated workspace with `--add-dir`
- Run builds or tests in the worktree without affecting the main checkout
- Work on multiple tasks in parallel without branch conflicts

## Options

| Option | Description |
|---|---|
| `--branch-prefix <prefix>` | Add a prefix to branch names (e.g. `nanasess/`) |
| `--no-install` | Skip automatic dependency installation |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nanasess) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
