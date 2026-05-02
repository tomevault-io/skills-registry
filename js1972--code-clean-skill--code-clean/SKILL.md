---
name: code-clean
description: Audit codebases for cleanup: TODO/FIXME review, dead-code triage, lint checks, and structure notes. Use when running periodic cleanup across projects or when asked to assess TODOs, unused code, lint issues, or codebase structure. Use when this capability is needed.
metadata:
  author: js1972
---

# Code Clean

## Overview

Audit a repository for cleanup opportunities, summarize findings, and ask for explicit approval before any edits.

## Workflow

1. Project discovery
   - Identify languages and tooling by scanning for configs and scripts.
   - Check for: `package.json` scripts (`lint`, `typecheck`, `format`), `pyproject.toml`, `ruff.toml`, `mypy.ini`, `.golangci.yml`, `Cargo.toml`, `.eslintrc*`, `eslint.config.*`, `tsconfig*.json`, `Makefile`.
   - Prefer project-provided commands when available; otherwise fall back to heuristic scans.

2. TODO scan
   - Use `rg` to find `TODO|FIXME|HACK|XXX` with file + line context.
   - Group findings by file.
   - Ask the user for disposition per item: complete, keep, or remove.

3. Dead code candidates
   - Prefer evidence from compiler/linter output (e.g., `tsc` unused checks, `go vet`, `ruff` F401/F841) when those tools are available in the repo.
   - If only heuristic evidence exists, label as “candidate” and explain uncertainty.
   - For each candidate, describe what it does, why it appears unused, and the safest removal approach.

4. Lint issues
   - Run project lint commands only when they exist (e.g., `npm run lint`, `pnpm lint`, `yarn lint`, `make lint`).
   - Do not run `--fix` unless the user explicitly requests it.
   - If no lint entrypoint is found, report that lint was skipped.

5. Structure review
   - Provide a short prioritized list of structural concerns.
   - If no meaningful structural issues are found, explicitly say so.

## Output format

- Summary counts: TODOs, dead code candidates, lint issues.
- Sections:
  - TODOs (grouped by file)
  - Dead code candidates (with evidence and confidence)
  - Lint results (command run or “skipped”)
  - Structure notes (prioritized, short list)
- Explicit questions for any change requests.

## Safety guardrails

- Never modify files without explicit user approval.
- Prefer evidence-based findings; label uncertain items as candidates.
- Skip vendor/build/generated directories and large dependency trees (e.g., `node_modules`, `vendor`, `dist`, `build`, `target`, `.git`, `.venv`, `.tox`, `coverage`, `.cache`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/js1972) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
