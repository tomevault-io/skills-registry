---
name: refactor-code
description: Refactor selected files according to the nearest AGENTS.md and applicable mapped guidance while preserving behavior. Use when Codex should improve code structure, readability, maintainability, or consistency without broad rewrites or behavior changes. Use when this capability is needed.
metadata:
  author: luastoned
---

# Refactor Code

## Overview

Refactor one or more selected files using the target repository's own guidance as the source of truth. This is a conservative cleanup workflow: preserve behavior, respect local patterns, and apply the nearest `AGENTS.md` plus any mapped repository, language, runtime, or private guides that are explicitly available.

## Workflow

1. Identify the requested file, files, folder, or current selection.
2. Read guidance before editing:
   - Nearest `AGENTS.md` for the target file.
   - Root `AGENTS.md` when it applies.
   - Any mapped guide referenced by `AGENTS.md` for the file type, language, runtime, or repository shape.
   - Private/local guidance only when explicitly requested or already active for the repository.
3. Inspect local context:
   - Existing neighboring code, imports, helpers, types, tests, and module boundaries.
   - Nearest formatter, linter, typechecker, test, and build configs.
   - Existing abstractions before adding a new one.
4. Decide the refactor scope:
   - Keep behavior unchanged unless the user explicitly asks for behavior changes.
   - Prefer readability, local consistency, clearer boundaries, and removal of incidental complexity.
   - Avoid broad architecture changes, cross-module rewrites, or dependency changes unless the user requested them.
   - Do not introduce a new abstraction unless it removes real repeated complexity or matches an established local pattern.
5. Edit with `apply_patch`.
6. Run the smallest relevant validation when practical:
   - Targeted tests for the touched area.
   - Project-local typecheck, lint, format check, or build command when relevant.
   - Nearest workspace/project validation before root-wide validation in multi-project repos.

## Refactor Rules

- Preserve public APIs, serialized shapes, persisted data, environment names, route paths, config keys, and cross-boundary behavior unless the user explicitly requests changes.
- Keep commits and edits within the requested file/project boundary when possible.
- Prefer simple, explicit code over cleverness.
- Remove duplication only when the repeated pattern is stable enough to justify it.
- Prefer existing helpers, types, modules, and conventions before creating new ones.
- Let formatter/linter tooling own formatting details such as import ordering, quote style, semicolons, and spacing.
- Do not use named practices such as KISS, DRY, YAGNI, SOLID, or the Rule of Three as automatic rewrite mandates; use them as lenses when they fit the task.
- If the best refactor requires a larger design change, stop and explain the recommended larger change instead of quietly expanding scope.

## Output Expectations

Summarize what was refactored, what behavior was preserved, which guidance was applied, and which validation ran. If validation could not run, say why.

---
> Source: [luastoned/code-kit](https://github.com/luastoned/code-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
