---
name: gsp-test-skill
description: Test skill for installer unit tests Use when this capability is needed.
metadata:
  author: jubscodes
---

# Test Skill

Read shared prompts from `${CLAUDE_SKILL_DIR}/../../prompts/system.md`.
Load templates from `${CLAUDE_SKILL_DIR}/../../templates/config.json`.

Use the AskUserQuestion tool to ask for project name.
Use the Skill tool to chain skills.

Spawn the `gsp-project-builder` agent with the brief.
spawn the `gsp-project-reviewer` agent for QA.

Text with <sub>emphasis</sub> here.

Run /gsp:build after setup.
Configure at ~/.claude/config.json.

---
> Source: [jubscodes/get-shit-pretty](https://github.com/jubscodes/get-shit-pretty) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
