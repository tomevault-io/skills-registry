---
name: code-quality
description: Apply a minimal, repeatable quality checklist to changes (correctness, security, maintainability, tests) and suggest repo-aligned checks without inventing tooling. Use when this capability is needed.
metadata:
  author: jpiedrafita
---

# code-quality

Purpose: Keep quality high with a short, consistent checklist and repo-aligned verification steps.

## Inputs

- PROJECT.md (repo conventions, how to run, quality gates)
- Active PR or changed files (if available)
- Existing tooling config (pyproject.toml, go.mod, package.json, CI workflows, etc.)

## Rules

- Do not invent tooling. Prefer repo-provided commands (PROJECT.md, scripts, CI).
- Keep suggestions minimal and actionable.
- If a check cannot be run, propose it as a follow-up (do not assume it passed).

## Checklist (prioritized)

1) Correctness
- Edge cases and error handling
- Input validation and safe defaults

2) Security
- Secrets and credentials handling
- AuthN/AuthZ assumptions
- Unsafe deserialization / injection risks (where relevant)

3) Maintainability
- Naming, structure, single-responsibility
- Avoid unnecessary complexity

4) Tests
- New behavior covered by tests (or clear reason why not)
- Verification command exists and is documented

5) Performance (only if relevant)
- Obvious hot paths, N+1 style issues, excessive I/O

## Suggested checks (only if repo defines them)

- Tests: use PROJECT.md verification or existing scripts
- Lint/format/typecheck/build: only if repo has an entrypoint for them (Makefile/Taskfile/package scripts/CI)

## Output format

- Critical issues (must fix)
- Major issues
- Minor/nits
- Suggested checks (commands, if available)
- Summary (3 bullets)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpiedrafita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
