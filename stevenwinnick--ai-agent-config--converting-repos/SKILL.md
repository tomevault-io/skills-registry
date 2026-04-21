---
name: converting-repos
description: Converts a standard-format git repo to the preferred worktree directory structure. Use when the user wants to convert an existing repo clone to the worktree layout.
metadata:
  author: stevenwinnick
---

# Convert Repo

Converts an existing standard-format git repo (a normal clone) into the preferred worktree directory structure.

## Usage

Based on $ARGUMENTS, run the script:

```bash
~/.claude/skills/converting-repos/scripts/convert-repo.sh $ARGUMENTS
```

### Arguments

- `<path-to-repo>` (required) — Path to the existing repo to convert

### What It Does

1. Validates the source is a git repo
2. Creates the preferred directory structure at `$CODE_ROOT/<repo-name>/`
3. Moves the existing repo into `$CODE_ROOT/<repo-name>/<default-branch-name>/<repo-name>/`
4. Creates the empty `worktrees/` directory
5. Ensures the checked-out branch is the default branch

### Result

Report the new path to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stevenwinnick) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
