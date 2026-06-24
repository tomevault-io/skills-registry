---
name: where-agents-md
description: Scan the current repository and recommend which directories deserve their own scoped Codex AGENTS.md or AGENTS.override.md instruction file, as a ranked report with one-line rationales. Does not generate files. Trigger when the user says "where should AGENTS.md go", "which directories need AGENTS.md", "where-agents-md", "scout AGENTS.md placement", "find nested AGENTS.md candidates", or "where should Codex instructions live". Use when this capability is needed.
metadata:
  author: ada-ggf25
---

# Where To Place AGENTS.md

Recommend high-value locations for scoped Codex instruction files. This skill reports
only; it does not create or edit files.

## Placement Model

Codex reads `AGENTS.md` instruction files as project guidance. Nested files should be
used only when a subtree has meaningful local conventions that the parent file should
not carry.

Use `AGENTS.md` for shared project guidance. Use `AGENTS.override.md` only when the user
explicitly wants a local override pattern and the repo already uses that convention.

## Scoring Heuristic

Recommend a directory only when it clearly clears the bar.

Positive signals:

- Module boundary: its own `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`,
  `*.csproj`, `pom.xml`, `build.gradle`, or similar manifest.
- Distinct stack or conventions: different language, framework, build tool, formatter,
  test runner, deployment target, or safety constraint from the parent.
- Local config: its own lint, format, test, CI, codegen, fixtures, or tooling rules.
- Size/depth: enough source files that context is repeatedly re-derived.
- Architectural boundary: `apps/<x>`, `packages/<x>`, `services/<x>`, `cmd/<x>`, or a
  bounded domain module in a monorepo.

Negative signals:

- Generated, vendored, dependency, virtualenv, cache, coverage, or git-ignored dirs.
- Tiny leaf dirs, asset-only dirs, fixtures-only dirs, or directories already covered by
  a parent instruction file.
- The repo root when it lacks `AGENTS.md`; report that as a prerequisite, not a nested
  recommendation.

## Procedure

### 1. Orient

- Find existing instruction files:
  `find . \( -name AGENTS.md -o -name AGENTS.override.md \) -not -path '*/node_modules/*'`
- Read root `AGENTS.md`, `CLAUDE.md` if present, and `README*` to understand what is
  already documented.
- Map manifests/configs and respect `.gitignore`.

### 2. Score

- Apply the heuristic to each candidate directory.
- Collapse redundancy: prefer the parent unless the child has genuinely distinct local
  context.
- For a large repo, delegate broad directory exploration to a read-only subagent when
  available and score from its summary.

### 3. Report

Return a short ranked list. For each recommendation include:

- directory path;
- recommended file name, usually `AGENTS.md`;
- one-line rationale naming the strongest signals;
- whether it should supplement or replace parent guidance.

Add a "Skipped / not worth it" section for notable directories you intentionally left
out and why.

## Guardrails

- Report only; never create, edit, or auto-generate instruction files.
- Never recommend a directory already covered well by an existing or parent
  `AGENTS.md`.
- Respect `.gitignore` and exclude generated/vendor/dependency directories.
- Keep recommendations few and evidence-based.

---
> Source: [ada-ggf25/AI-Tools](https://github.com/ada-ggf25/AI-Tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
