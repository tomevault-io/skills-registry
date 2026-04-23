---
name: implement-agent-teams
description: Set up and validate Claude agent team configurations, collaboration flows, and operator guidance for team-based execution. Use when this capability is needed.
metadata:
  author: yu-iskw
---

# Implement Agent Teams

Implement team setup and coordination patterns for multi-agent workflows, including display modes and operational constraints.

## Workflow

1. Define team objective, members, and ownership boundaries.
2. Configure team metadata and task routing conventions.
3. Document coordination rules and display mode choices.
4. Run environment and config checks.
5. Verify limitations and fallback behavior.

## Progressive Disclosure

- Team setup structure: `references/team-setup.md`
- Coordination contracts: `references/team-coordination.md`
- Display mode selection: `references/team-display-modes.md`
- Team best practices: `references/team-best-practices.md`
- Known limitations: `references/team-limitations.md`

- Team config checker: `scripts/check-team-config.sh`
- Environment verifier: `scripts/verify-team-env.sh`

- Team config example: `assets/templates/team-config-example.json`
- Team task example: `assets/templates/team-task-example.md`

## Related Skills

- Umbrella routing and component comparison: `../implement-claude-extensions/SKILL.md`
- Sub-agent design: `../implement-sub-agents/SKILL.md`

## Sources

- https://code.claude.com/docs/en/agent-teams
- https://code.claude.com/docs/en/sub-agents

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
