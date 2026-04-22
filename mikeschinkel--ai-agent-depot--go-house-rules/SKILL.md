---
name: go-house-rules
description: Apply Mike's Go house rules (ClearPath, doterr-only errors, go-dt path/file types, and testing conventions). Use for ANY Go code you write, refactor, review, or explain. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# Go House Rules (router skill)

This skill enforces a consistent Go style across production code, tests, and package design in this workspace.

## When to use
Use this skill for **any** of the following:
- Writing or refactoring Go code
- Designing Go packages/APIs
- Reviewing Go code or suggesting changes
- Writing Go tests
- Handling files/paths, errors, CLI code, config, logging, JSON, SQL, web routing, etc.

## Mandatory reading order (load these references first)
Read these references **in order** and apply them as rules:

1) **Non‑negotiables (always)**: `references/nonnegotiables.md`  
2) **ClearPath production style**: `references/clearpath.md`  
3) **Error handling (doterr only)**: `references/doterr.md`  
4) **File/path handling (go‑dt types)**: `references/go-dt-paths.md`  
5) **Testing rules**: `references/testing.md`  
6) **Package design guidelines**: `references/package-design.md`  

Keep `references/packages-catalog.md` as the on-demand catalog for Mike's house packages and their README guidance.

## Priority rules & conflict resolution
- **Tests override ClearPath**: if writing tests, follow `references/testing.md` even where it conflicts with ClearPath.
- **Non‑negotiables override everything**.
- If uncertain: choose the path that **reduces long-term maintenance**, uses house packages, and avoids reinventing.

## Output expectations
When responding with Go code:
- Produce code that compiles (or clearly mark stubs).
- Follow the formatting constraints from non-negotiables (notably: **no compound `if` init statements**).
- Prefer Mike's packages over stdlib/third-party alternatives when the catalog indicates a house package exists.
- Do not “just sketch”; provide complete, usable code for the requested scope.

## Final self-check (before you answer)
Before presenting the final output, verify:
- No ignored errors (`_ =` / `_` for errors) unless explicitly approved.
- No `if init; cond {}` compound `if` statements.
- `regexp.MustCompile()` assigned directly to a package-level var.
- No unnecessary casts or string<->domain-type churn; prefer go-dt types end-to-end.
- Production code follows ClearPath (single return at end, `goto end`, minimal nesting, no `else`) unless explicitly exempted.
- Tests follow the testing guide and do **not** use ClearPath patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
