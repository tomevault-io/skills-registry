---
name: git-updated-pusher
description: Commit and push repository changes using a fixed commit message `updated`. Use when you want a one-command stage-all, commit, and push workflow with minimal prompts. Use when this capability is needed.
metadata:
  author: vocalynz
---

# Git Updated Pusher

## When To Use

Use this skill when you explicitly want to:

1. Stage all tracked/untracked changes.
2. Commit with the exact message `updated`.
3. Push the current branch to remote.

## Workflow

Before running the push script, inspect and report:

- current branch,
- `git status --short --branch`,
- whether the worktree includes obviously unrelated changes,
- target remote and branch.

If the worktree contains unexpected unrelated changes, surface that explicitly before continuing. This skill is only appropriate when staging everything is intentional.

Run:

`scripts/commit_push_updated.sh [repo_root] [remote] [branch]`

Defaults:

- `repo_root`: current directory (`.`)
- `remote`: `origin`
- `branch`: current checked-out branch

## Behavior

- Prints the current branch and worktree status before staging.
- Validates the target is a git repository.
- Validates the target remote exists.
- Runs `git add -A`.
- Prints a staged diff summary before commit/push.
- If staged changes exist, commits with message `updated`.
- Pushes to the specified remote/branch.
- If no staged changes exist, skips commit and still runs push.

## Safety Notes

- This skill intentionally stages everything (`git add -A`).
- This skill intentionally uses a constant commit message (`updated`).
- This skill should not be used to separate unrelated changes into different commits.
- Use only when that behavior is desired.

---
> Source: [vocalynz/voicednut](https://github.com/vocalynz/voicednut) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
