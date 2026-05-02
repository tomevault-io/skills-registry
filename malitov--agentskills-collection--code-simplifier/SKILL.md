---
name: code-simplifier
description: Simplifies and refines code for clarity, consistency, and maintainability while preserving exact functionality. Use for safe refactors/cleanups, reducing nesting, removing redundancy, and improving readability—defaulting to recently modified diffs unless asked to broaden scope. Use when this capability is needed.
metadata:
  author: malitov
---

# Code Simplifier

## Overview

Simplify code for clarity, consistency, and maintainability without changing behavior.
Default to refining only recently modified code (current diff/session) unless asked to review a broader scope.

## Workflow

1. Identify the scope (default: recent changes)
   - Prefer `git diff`, `git diff --staged`, and `git status` to find the files/hunks touched most recently.
   - If git context is unavailable, ask for the intended scope or use the smallest set of files implied by the request.

2. Load and follow project standards
   - Read the nearest relevant guidance files (e.g., `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, `.editorconfig`, lint/formatter configs).
   - Match existing local patterns when they conflict with generic preferences.

3. Preserve exact functionality
   - Do not change runtime behavior, outputs, side effects, public APIs, error semantics, or observable timing.
   - Do not broaden types, tighten validations, or change defaults unless explicitly requested.

4. Apply simplifications (clarity over cleverness)
   - Reduce nesting with early returns and guard clauses.
   - Eliminate redundant variables, dead code, and duplicated logic.
   - Prefer explicit, readable control flow.
     - Avoid nested ternary operators; use `if/else` or `switch` for multi-branch logic.
   - Improve naming for intent (variables, helpers, props) while keeping public contracts unchanged.
   - Keep concerns separated; avoid “mega-functions” that do too much.

5. Apply JS/TS/React hygiene (when applicable)
   - Prefer ES modules and consistent, predictable import ordering (let the repo’s linter/formatter lead).
   - Prefer `function` declarations for top-level/exports when consistent with the codebase.
   - Add explicit return types for exported/top-level TypeScript functions when it improves clarity.
   - For React components, use explicit `Props` types and idiomatic component patterns.
   - Prefer structured error handling; avoid broad `try/catch` when a narrower approach exists.

6. Verify and summarize
   - Run the smallest relevant checks (tests, typecheck, lint) if available.
   - Document only changes that affect understanding (e.g., renamed helpers, extracted logic, changed control flow structure).

## Non-goals

- Do not do “style-only” rewrites across untouched files.
- Do not introduce new abstractions unless they clearly reduce duplication and improve readability.
- Do not re-architect modules; keep changes local and reviewable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malitov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
