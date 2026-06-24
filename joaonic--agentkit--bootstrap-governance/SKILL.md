---
name: bootstrap-governance
description: Bootstrap or normalize repository Cursor governance with canonical docs, stack-aware rules, and operational skills/agents. Use when this capability is needed.
metadata:
  author: Joaonic
---

# Bootstrap Governance

## Scope

Create or normalize:
- `.cursor/rules`
- `.cursor/skills`
- `.cursor/agents`
- `.cursor/plans`
- `.cursor/mcp.json`
- `.cursor/settings.json`
- `AGENTS.md`
- `docs/governance/cursor`
- `docs/cursor` bridge (optional but recommended)

## Mandatory Rules

- keep `docs/governance/cursor` canonical
- keep `docs/cursor` bridge-only
- do not create `.cursor/commands`
- keep skill frontmatter consistency
- adapt VCS wording and tooling to repository policy

## Output

- created/updated files map
- removed duplicates/conflicts
- residual governance gaps

---
> Source: [Joaonic/agentkit](https://github.com/Joaonic/agentkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
