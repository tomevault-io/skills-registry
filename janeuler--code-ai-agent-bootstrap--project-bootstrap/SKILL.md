---
name: project-bootstrap
description: Use when initializing a target repository with AGENTS.md, .ai memory files, Codex skills, testing policy, review policy, and memory update hooks.
metadata:
  author: Janeuler
---

# Project Bootstrap Skill

## Goal

Generate a project-specific AI agent workflow configuration inside a target repository.

## V2 default

For large Java/Spring projects, prefer:

```bash
./bin/ai-bootstrap ../target-project --deep
```

This runs deterministic scanning before Codex synthesis.

## Required input

- Target repository path.
- Bootstrap repository path.
- Whether existing files may be overwritten.
- Optional include/exclude module filters.

## Procedure

1. Inspect target project structure through scan artifacts, not blind whole-repo reading.
2. Identify technology stack, modules, build/test commands, docs, existing AI files, DB/cache/MQ usage, and Spring API entry points.
3. Generate `.ai/bootstrap/` facts and module context packs.
4. Generate concise target project files:
   - `AGENTS.md`
   - `.ai/*.md`
   - `.ai/modules/*.md`
   - `.agents/skills/*/SKILL.md`
   - `.githooks/pre-commit`
5. Do not modify business code during initialization.
6. If a file exists and overwrite is not allowed, generate `.new` and document the conflict.
7. Write `.ai/bootstrap-report.md`.

## Quality bar

Generated files should be specific to the target project. Do not blindly copy Spring rules into a non-Spring project. Do not invent facts; use confirmed / inferred / unknown.

---
> Source: [Janeuler/code-AI-agent-bootstrap](https://github.com/Janeuler/code-AI-agent-bootstrap) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
