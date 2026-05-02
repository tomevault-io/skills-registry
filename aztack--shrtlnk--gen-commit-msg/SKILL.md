---
name: commit-grouped
description: Group git changes by logic and commit them using English Conventional Commit format. Quick command: commit-grouped [lang=en], [scope=shared|api|fe], [exclude=file-pattern] Use when this capability is needed.
metadata:
  author: aztack
---

# Commit Grouped by Logic

Group current git changes by logic and commit them using English Conventional Commit format.

## Commit Rules

### Scope Rules
- **shared**: Changes related to shared modules
- **api**: Changes related to backend API
- **fe**: Changes related to frontend
- **Global scope**: Changes that do not belong to the above scopes (no scope in commit message)

### Commit Order
Commit according to dependencies, in the following order:
1. Global configurations and dependencies (e.g., package.json, tsconfig.json, etc.)
2. Changes in the shared module
3. Changes in the api backend
4. Changes in the fe frontend
5. Project progress and documentation updates (e.g., README.md, docs/, etc.)
6. **Last commit: lock file** (use fixed message: "chore: update lock file")

### Ignored Files
- Changes in `.bootstrap.ts` files will be ignored.

## Execution Workflow

1. **Analyze changes**: Read `git status` and `git diff` to analyze all changed files.
2. **Logical grouping**: Group files based on file paths and change content.
3. **Generate commit messages**: Generate English commit messages for each group following the Conventional Commit format.
4. **Display plan**: List the files included in each commit group and the corresponding commit message.
5. **Wait for review**: **Do not commit automatically**; wait for user review and confirmation.
6. **Accept adjustments**: Users may request to exclude certain files or adjust the grouping.

## Commit Message Format

Use Conventional Commit format with English descriptions:

```
<type>(<scope>): <description>

[optional body]
```

### Type
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation update
- `style`: Formatting adjustment (no functional changes)
- `refactor`: Refactoring
- `perf`: Performance optimization
- `test`: Test-related
- `chore`: Build, config, dependencies, etc.

### Examples
- `feat(api): add user authentication endpoint`
- `fix(fe): fix login page styling issue`
- `refactor(shared): refactor utility functions module`
- `docs: update project documentation`
- `chore: update lock file`

## Execution Steps

When the user calls this skill:

1. Run `git status --porcelain` to get all changed files.
2. Run `git diff` to get the change content.
3. Filter out `.bootstrap.ts` files.
4. Determine scope based on file paths:
   - Contains `/shared/` or `packages/shared/` → shared
   - Contains `/api/` or `apps/*-api/` → api
   - Contains `/fe/`, `apps/*-fe/`, or `/frontend/` → fe
   - Lock files (package-lock.json, yarn.lock, pnpm-lock.yaml, etc.) → separate group
   - Others → global scope
5. Determine type (feat, fix, refactor, docs, chore, etc.) based on change content.
6. Sort groups by dependency order.
7. Generate English commit message for each group.
8. Display the commit plan clearly:
   ```
   Commit 1: feat(shared): add new utility functions
   Files:
   - packages/shared/utils/helper.ts
   - packages/shared/types/index.ts

   Commit 2: feat(api): implement user management API
   Files:
   - apps/metamove-api/src/controllers/user.ts
   - apps/metamove-api/src/routes/user.ts

   ...

   Commit N: chore: update lock file
   Files:
   - package-lock.json
   ```
9. Clearly inform the user: **Please review the above commit plan; I will proceed with the commits after your confirmation.**
10. If the user requests to exclude certain files, readjust the grouping and plan.

## Notes

- **Do not commit automatically**: You must wait for explicit user confirmation before executing `git add` and `git commit`.
- **Flexible adjustments**: Users may request to modify groupings or exclude certain files.
- **Maintain atomicity**: Each commit should be a logical unit.
- **English descriptions**: Use English for the description part of the commit message.
- **Lock file last**: Always place the lock file in the final commit.

## Argument Explanation

The optional `$ARGUMENTS` can be used to specify file patterns to exclude, for example:
```
/commit-grouped "*.test.ts"
```
This will exclude all test files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aztack) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
