---
name: project-bootstrap
description: > Use when this capability is needed.
metadata:
  author: t3chn
---

# Project Bootstrap (v0.1)

## Required inputs
- `SKILLREGISTRY_GIT`: git URL or local path to the trusted skillregistry repo
- `SKILLREGISTRY_REF`: branch/tag/commit (default: `main`)

## What this skill must do
1) Ensure `.agent/skillregistry` exists by cloning `SKILLREGISTRY_GIT` and checking out `SKILLREGISTRY_REF`.
2) Run:
   `python3 .agent/skillregistry/skills/project-bootstrap/scripts/bootstrap.py init`
   with:
   - `--skillregistry-git`
   - `--skillregistry-ref`
3) After init, print next steps:
   - review `.agent/skills_todo.md`
   - restart Codex CLI to ensure skills are reloaded (recommended)

## Constraints
- Install only from the trusted skillregistry.
- Never fetch skills from external catalogs.
- Treat installed registry skills as read-only; project customizations go into overlay skills.
- Never overwrite overlay skills silently unless the overlay is unchanged (safe overwrite policy) or `--force-overwrite-overlays`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t3chn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
