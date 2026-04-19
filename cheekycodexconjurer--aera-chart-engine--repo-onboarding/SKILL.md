---
name: repo-onboarding
description: Repository onboarding and agent bootstrap. Use at the start of a new repo session or before any task to load AGENTS.md, architecture/skills indexes, and discover local Codex skills. Use when this capability is needed.
metadata:
  author: cheekycodexconjurer
---

# Repo Onboarding

## Overview
Load repo governance and skills before acting.

## Workflow
1. Read `AGENTS.md`. Follow any "Golden Path" or referenced indexes.
2. Load architecture references:
- If `AGENTS.md` points to `.agent-docs/architecture.md`, open it.
- Otherwise open `architecture.md` and any index it references.
3. Load skills references:
- Open `.agent-docs/SKILLS.md` and `skills.md` if present.
- Scan `skills/` for `*/SKILL.md` and list available Codex skills (name + path).
4. Choose skills:
- If the task matches a skill description, load it and follow its workflow.
- Use `docs-auto-sync` whenever code changes are made.
5. Summarize: confirm which docs/skills were loaded and any constraints found.

## Resources
- `references/bootstrap-sources.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cheekycodexconjurer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
