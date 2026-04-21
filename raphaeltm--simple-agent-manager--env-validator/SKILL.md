---
name: env-validator
description: Environment variable consistency validator. Checks GH_* vs GITHUB_* naming conventions, validates documentation matches code, and ensures deployment scripts correctly map secrets. Use proactively when modifying environment variables, updating CLAUDE.md, editing configure-secrets.sh, or changing the Env interface. Use when this capability is needed.
metadata:
  author: raphaeltm
---

# env-validator

This is a Codex skill wrapper around the Claude Code subagent definition in:
- .claude/agents/env-validator/

Use:

1. Read CLAUDE_AGENT.md.
2. Follow its checklist and constraints.
3. Report results with concrete file references.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raphaeltm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
