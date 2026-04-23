---
name: skills-auditor
description: Detect incorrectly installed skills and report issues. Use when this capability is needed.
metadata:
  author: cheekycodexconjurer
---

## Purpose
Find broken or missing Codex-native skills.

## Steps
1. Validate `.codex/skills/*/SKILL.md` frontmatter and folder names.
2. Compare `.codex/skills/` with `.agent-docs/skills/`.
3. Record findings in `.agent-docs/memory/SKILLS_STATUS.md`.

## Guardrails
- Propose fixes via merge protocol, do not overwrite blindly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cheekycodexconjurer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
