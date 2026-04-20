---
name: git-commits
description: Git commit workflow and message format. Use when making commits, asking about commit conventions, or after completing code changes. Enforces atomic commits with structured messages (type/scope/summary + what/why body). Use when this capability is needed.
metadata:
  author: iamhenry
---

# Git Commits

## WORKFLOW

1. Run `git status --porcelain` to see all changes (modified and untracked)
2. Run `git diff` to understand the changes
3. Stage and commit ALL changes in one command:
   - For ALL files (including untracked): `git add -A && git commit -m "..."`
   - git commit -a only stages tracked/modified files, use git add -A for full coverage
4. Push changes
5. Verify clean: `git status --porcelain` should return empty
6. Then stop

## Format
```
<type>(<scope>): <summary>
<body with what changed and why>
```
- Title (first line): Required - follows `<type>(<scope>): <summary>` format
- Body (after blank line): REQUIRED - for each changed file, add a parent bullet with the file path, then nested bullets for what changed and why.

## Example
```
  <type>(<scope>): <summary>

  src/components/button.js
    - What: Added Button with size props.
    - Why: Enable dynamic size adjustments for a customizable UI.

  tests/button.test.js
    - What: Created tests for Button sizing.
    - Why: Ensure reliable rendering and detect regressions.
```

## SCOPE

Specifies area of change:
- auth, user, dashboard, api, database, ui, transfer, etc.

## ANTI-PATTERNS

- NEVER skip the body - it documents intent

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iamhenry) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
