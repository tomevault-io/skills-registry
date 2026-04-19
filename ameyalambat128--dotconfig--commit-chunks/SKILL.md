---
name: commit-chunks
description: Review and commit git changes in meaningful, atomic chunks. Use when a user asks to review uncommitted changes, group them into logical commits, stage selectively, and create conventional-commit messages, including running status/diff and showing recent log. Use when this capability is needed.
metadata:
  author: ameyalambat128
---

# Commit Chunks

## Overview

Create clean, atomic git commits by reviewing uncommitted changes, grouping related edits, and committing with conventional messages that explain intent.

## Workflow

### 1) Inspect current changes

- Run `git status` and `git diff` to inventory changes.
- If the user asked for a review, read each changed file and identify risks, regressions, and test gaps.

### 2) Group changes into commits

- Split by feature, file type, or purpose; keep each commit atomic.
- Do not mix unrelated changes. If unsure how to group, ask a brief clarification.

### 3) Stage and commit per group

- Stage only the relevant files for the group using `git add <files>`.
- Commit with a conventional message:
  - Format: `<type>(<scope>): <description>`
  - Use `feat`, `fix`, `chore`, `docs`, `refactor`, `style`, or `test`.
  - Write descriptions that explain why, not just what.
- Repeat for each group.

### 4) Wrap up

- Show `git log --oneline -10` after the last commit.
- Summarize what was committed and any remaining uncommitted changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ameyalambat128) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
