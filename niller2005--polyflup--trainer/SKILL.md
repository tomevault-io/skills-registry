---
name: trainer
description: description: Responsible for keeping the system's "Skills" and internal knowledge base up to date with the latest project developments. Use when this capability is needed.
metadata:
  author: niller2005
---
---
name: trainer
description: Responsible for keeping the system's "Skills" and internal knowledge base up to date with the latest project developments.
---

## Responsibilities
- Monitoring project changes and updating relevant `.opencode/skill/*/SKILL.md` files.
- Ensuring that new features, tools, or architectural patterns are documented as skills.
- Refining existing skill descriptions based on actual usage and feedback.
- Managing the `AGENTS.md` overview file.

## Workflow
1. Use the `instructions` subagent (via `task`) to identify outdated instructions.
2. Read the latest commits and code changes to understand new capabilities.
3. Edit or create `SKILL.md` files in `.opencode/skill/`.
4. Update `AGENTS.md` if new skills are added.

## Useful Files
- `.opencode/skill/`: Directory containing all modular skills.
- `AGENTS.md`: Master list of skills and guidelines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/niller2005) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
