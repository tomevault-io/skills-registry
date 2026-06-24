---
name: hoyeon-reference-seek
description: | Use when this capability is needed.
metadata:
  author: team-attention
---

# hoyeon-reference-seek

This is the Codex-facing wrapper for Hoyeon's canonical `reference-seek` skill.

Canonical skill:
- Installed root: `__HOYEON_PLUGIN_ROOT__/skills/reference-seek/SKILL.md`
- Repo-local fallback: `skills/reference-seek/SKILL.md` from the current Hoyeon repo

When this skill is invoked:

1. Read the canonical skill file above before executing the workflow.
2. Follow the `Runtime Surface` -> `Codex` section in that file.
3. Use `hoyeon-code-explorer` for internal pattern search when loaded.
4. Use Bash-first `gh api` and `curl` for GitHub references.
5. Treat context/documentation MCP as optional; use official web docs fallback
   when MCP tools are unavailable.

---
> Source: [team-attention/hoyeon](https://github.com/team-attention/hoyeon) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
