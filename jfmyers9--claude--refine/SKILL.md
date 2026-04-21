---
name: refine
description: > Use when this capability is needed.
metadata:
  author: jfmyers9
---

# Refine

Polish uncommitted changes: simplify code, improve comments.

## Arguments

- `<file-pattern>` — limit to matching files (glob or path)

## Steps

### 1. Identify Files

- If `$ARGUMENTS`: use as file pattern
- Otherwise: `git diff --name-only` + `git diff --cached --name-only`
- Filter to code files (exclude config, lock, generated)
- No files → inform + exit

### 2. Read Files (parallel)

Read all identified files.

### 3. Analyze + Apply

For each file, find and fix:

**Simplify code** — apply standard simplification patterns
(guard clauses, naming, single-responsibility, etc).

**Improve comments:**
- Remove code-restating comments ("increment counter",
  "loop through items", "return result")
- Remove contextless TODOs
- Keep: why-explanations, edge case warnings, business logic,
  perf constraints
- Update inaccurate/outdated comments (don't remove)

**Doc comments** (JSDoc, docstrings, GoDoc, RustDoc):
- Preserve by default — consumed by tools + IDEs
- Remove only if vacuous (empty, or restates signature with
  zero info)
- If inaccurate → update, don't remove

### 4. Verify

Check syntax after changes (linter/parser). Revert + note
if verification fails.

### 5. Summary

Per file: simplifications applied, comments removed/improved.
Offer `git diff` to review.

## Boundaries

Do NOT:
- Add features or change behavior
- Add error handling or abstractions
- Add comments to unchanged code
- Touch code outside the diff
- Refactor beyond uncommitted changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jfmyers9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
