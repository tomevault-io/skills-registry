---
name: atomic-commits
description: Analyzes git changesets to create atomic, non-breaking, incremental commits. Use when you have multiple changes in the working directory and need to commit them in logical, independent steps that "just work".
metadata:
  author: kutayilmaaz
---

# Atomic Commits

This skill guides the process of creating atomic git commits from an existing changeset.

## Core Principle

"The goal is that every commit just works - so you need to commit features in a non-breaking, incremental way."

## Workflow

1.  **Analyze Changes**
    *   Examine the current status with `git status`.
    *   Review diffs with `git diff` to understand all pending changes.

2.  **Plan Atomic Commits**
    *   Group changes into logical units.
    *   **Rule**: Dependencies must be committed before dependents.
    *   **Rule**: Each commit must be self-contained and non-breaking.
    *   **Rule**: Tests (if present and relevant) should ideally pass after each commit.

3.  **Execute**
    *   Stage specific files or hunks using `git add` (or `git add -p` for partial file staging).
    *   Commit with a clear, descriptive message explaining *why* the change was made, not just *what* changed.
    *   Repeat until the working directory is clean (or only contains changes meant for a later batch).

## Example Scenario

**Context**: You have modified a backend API, updated the frontend to use the new API, and added a new dependency in `package.json`.

**Bad Approach**: `git commit -am "update api and frontend"` (Too large, mixes concerns).

**Atomic Approach**:
1.  `git add package.json package-lock.json` -> Commit: "Add `axios` dependency" (Safe, pre-requisite).
2.  `git add backend/api.py` -> Commit: "Update API endpoint to support user filtering" (Backend works independently).
3.  `git add frontend/UserList.js` -> Commit: "Connect frontend to new user filtering API" (Frontend uses the now-available API).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kutayilmaaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
