---
name: repo-bootstrap
description: Bootstrap repository context by completing PROJECT.md and aligning the repository's agent-instruction files with the specs-driven workflow. Use when starting a new project/feature or when repository context is missing/unclear. Use when this capability is needed.
metadata:
  author: jpiedrafita
---

# repo-bootstrap

Purpose: Bootstrap repository context for agents by completing `PROJECT.md`, aligning existing instruction files, and identifying the repo's real execution and quality entrypoints.

## Inputs

- `PROJECT.md` or equivalent project charter/source-of-truth doc
- Repository-wide agent instructions, if present:
  - `CLAUDE.md`
  - `AGENTS.md`
  - `.github/copilot-instructions.md`
  - `.cursor/rules/*` or `.cursorrules`
  - `.windsurfrules`
- Core onboarding docs, if present:
  - `README.md`
  - `docs/` overview docs
  - existing `specs/requirements.md`, `specs/design.md`, `specs/tasks.md`
- Repo execution/convention sources, if present:
  - `Makefile`, `Taskfile`, `package.json`, `pyproject.toml`
  - CI workflows under `.github/workflows/`

## Steps

1. Read `PROJECT.md`.
2. If `PROJECT.md` has placeholders like `[...]`:
   - Ask only the minimum questions needed to replace placeholders.
   - STOP after asking questions (do not edit files yet).
3. If `PROJECT.md` has no placeholders:
   - Do not ask onboarding questions just to restate existing content.
4. Update `PROJECT.md` with the user-provided answers (replace placeholders only).
5. Inspect existing instruction files and align them to:
   - reference `PROJECT.md` as the source of truth
   - enforce the workflow gates `requirements.md` -> `design.md` -> `tasks.md`
   - tell the agent to stop and ask if repository context is incomplete
   - avoid duplicating policy across multiple instruction files
6. Inspect repo entrypoints and capture the real commands and quality gates already present in the repo.
7. Keep changes minimal. Do not modify `specs/*` as part of onboarding.

## Rules

- Do not invent project details. If missing, ask.
- Prefer aligning the repository's existing instruction files over forcing one specific file path.
- Treat manifests and workflows as the source of truth for commands and quality gates.
- If a file is already compliant, do not touch it.

## Output

- Updated `PROJECT.md` (only if placeholders were filled)
- Updated instruction file(s) only where alignment was missing
- A short summary of the repo's real commands and quality gates if they were unclear before

## Final response

- Summarize changes in 3-5 bullets.
- If blocked, list only the missing answers required to proceed.

---
> Source: [jpiedrafita/agent-templates](https://github.com/jpiedrafita/agent-templates) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
