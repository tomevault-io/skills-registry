---
name: go-dt-filepath-refactorer
description: Refactor Go code from string-based file/path handling to Mike's go-dt domain types and helpers (Filepath, DirPath, etc.) while preserving behavior. Use for any work touching paths/files. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# go-dt Filepath Refactorer

Use this skill when the user asks to:
- clean up path/file handling
- remove stringly-typed `filepath.*` / `os.*` usage in favor of go-dt types
- reduce casting churn between string and dt types
- introduce dt types into APIs safely

## Mandatory references (read in order)
1) `references/nonnegotiables.md`
2) `references/go-dt-paths.md`
3) `references/clearpath.md` (production code only)
4) `references/testing.md` (only when in tests)
5) `references/go-dt-package-notes.md` (catalog notes)

## Refactor strategy (default)
Prioritize **mechanical, behavior-preserving refactors**:

1) Identify externally-sourced strings (CLI args, env, config, JSON, flags).
2) Convert them at the boundary into dt types (e.g., `dt.Filepath(...)`, parse helpers, or join helpers).
3) Propagate dt types internally to avoid repeated casts.
4) Replace `filepath.Join` / `path/filepath` calls with dt join helpers (`dt.FilepathJoin`, etc.).
5) Replace `os.ReadFile(string(fp))` with `fp.ReadFile()` and similar dt methods when available.
6) Ensure errors produced follow doterr usage if the calling code uses doterr.

## Things to avoid
- Don't introduce dt types in public API boundaries unless requested; prefer internal migration first.
- Don't add conversions that increase casting churn.
- Don't convert tests to ClearPath style; but dt types are still fine in tests.

## Common rewrite patterns
- `filepath.Join(a, b)` → `dt.FilepathJoin(dt.DirPath(a), dt.Filename(b))` (or the most appropriate dt join helper)
- `os.Stat(p)` → `dt.Filepath(p).Stat()` (if available) or a dt helper method
- `os.ReadFile(p)` → `dt.Filepath(p).ReadFile()`

(Use the exact helpers/methods from the reference guide; do not invent APIs.)

## Deliverables
- Provide updated code for the touched files.
- If many files: include a summary of “boundary conversions” (where you accepted strings and converted to dt).
- Include a small “risk notes” section if any semantic edge cases exist (relative vs absolute, symlinks, Windows separators, etc.).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
