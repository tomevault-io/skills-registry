---
name: go-error-handling-author
description: Create, extend, and refactor Go error sentinels and error composition using Mike's doterr pattern (no fmt.Errorf). Use when defining new errors, adding context/metadata, or standardizing error handling. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# Error Handling Author

Use this skill when the user asks to:
- add new error types/sentinels
- refactor existing error handling to doterr
- improve error context/metadata
- standardize errors across packages

## Mandatory references (read in order)
1) `references/nonnegotiables.md`
2) `references/doterr.md`
3) `references/clearpath.md` (production code only; do not apply to tests)
4) `references/testing.md` (only when working in tests)
5) `references/doterr-package-notes.md` (catalog notes)

## Hard constraints
- **Do not** use `fmt.Errorf`, `errors.New`, `errors.Join`, or stringly-typed error chains.
- All exported errors must be **sentinel variables** or doterr-defined error values per the doterr guide.
- Add context **once** at the function boundary (typically at `end:`) rather than sprinkling context everywhere.
- When refactoring: preserve behavior unless user requested semantic changes.

## What to deliver
When asked to implement doterr changes, produce:
1) **The sentinel definitions** (usually in `doterr.go` in the package) including comments.
2) **Refactored call sites** showing correct composition.
3) **A short mapping** of old → new behavior if any message/metadata changes.

## Authoring rules for new errors
- Prefer **small, composable sentinels** over many bespoke strings.
- Use metadata key/value pairs for specifics (ids, paths, owner, etc.).
- When multiple errors occur (e.g., per-item failures), use doterr combine/append patterns from the guide.
- For public APIs: define stable sentinel names and document what metadata keys they may include.

## Refactor checklist
- Replace `fmt.Errorf("...: %w", err)` with doterr context + WithErr patterns.
- Replace `errors.Join(...)` with doterr combine/append patterns.
- Replace ad-hoc string comparisons with sentinel checks.
- Ensure no ignored errors and no compound `if init; cond {}` patterns.

## Output formatting
- Provide code blocks per file with filenames as headings.
- If changes are large, include a unified diff in addition to full file snippets.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
