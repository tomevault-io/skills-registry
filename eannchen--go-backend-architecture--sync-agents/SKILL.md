---
name: sync-agents
description: Run after editing AGENTS.md (rules) or any .agents/skills/*/SKILL.md (skills) to propagate changes to Claude Code, Cursor, and Codex. Use when this capability is needed.
metadata:
  author: eannchen
---

# Sync AI agent configuration

After changing `AGENTS.md` or any `.agents/skills/*/SKILL.md`, run the sync script to propagate changes to all tools.

## Steps

1. Run from the repository root:
   ```bash
   ./scripts/sync-agents.sh
   ```
2. Confirm the script printed "Done. Claude Code, Cursor, and Codex are in sync."

No other steps. The script regenerates `.cursor/rules/`, `.claude/rules/`, and the `# Skills` block in `AGENTS.md`.

---
> Source: [eannchen/go-backend-architecture](https://github.com/eannchen/go-backend-architecture) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
