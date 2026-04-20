---
name: git-commit
description: Create well-formatted commits Use when this capability is needed.
metadata:
  author: ivanov-i
---

# Commit

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -40`

## Goal

Create well-formatted commits 

- Check which files are staged with `git status`.
- Analyze the diff to determine if multiple distinct logical changes are present.
- If multiple distinct changes are detected, break the commit into multiple smaller commits.
- For each commit (or the single commit if not split), create a commit message using the commit convention.
- Add all relevant changes with `git add`.
- Perform a `git diff` to understand what actual changes are being committed

## Commit Style

- Single-purpose commits: Each commit should contain related changes that serve a single purpose.
- Split large changes: If changes touch multiple concerns, split them into separate commits. Always reviews the commit diff to ensure the message matches the changes

## Guidelines for Splitting Commits

When analyzing the diff, consider splitting commits based on these criteria:

- Different concerns: Changes to unrelated parts of the codebase
- Different types of changes: Mixing features, fixes, refactoring, etc.
- File patterns: Changes to different types of files (e.g., source code vs documentation)
- Logical grouping: Changes that would be easier to understand or review separately
- Size: Very large changes that would be clearer if broken down
- Different fixes. Do not create commits with logically different changes. The word "and" usually indicates that a commit must be split

## Commit Message Convention
- Check historical commits to learn style and tone (`git log --oneline -40`).
- If not given a ticket, try to infer it from the current branch name.
- Keep description single line: Keep the whole commit message single line. It is forbidden to add more lines.
- Do not add Co-Authored-By: Do not add Co-Authored-By or "Generated with" in commit messages.
- Do not yse prefixes lije "fix:", "feat:", etc. Just write the message directly.

# Do not
- Do not do anything with remotes. do not push, fetch etc.
- Do do anything else besides creating commits.
- No "next steps"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ivanov-i) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
