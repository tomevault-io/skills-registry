---
name: documentation-autowriter
description: Generates and keeps README.md and ARCHITECTURE.md in sync with components, stores, and agents so judges can quickly evaluate the project.
metadata:
  author: cargdev
---

# Documentation Autowriter

When to use this skill

- Use whenever you add new components, agents, or stores and want docs updated for reviewers.
- Triggered by prompts like "update README" or "generate architecture overview".

Instructions

1. First Step: Scan `src/components/` and `src/stores/` to build a component map and store summary.

2. Second Step: Render `README.md` with development steps, demo instructions (how to use Copilot CLI agents), and a brief feature summary.

3. Third Step: Produce `ARCHITECTURE.md` documenting Atomic Design layout, store relationships, and agent/skill list for the judges.

Examples

- README includes: setup, run, test, demo CLI steps (`gh copilot ask "Find endorsers for my paper"`).

Notes

- Keep automated docs human-editable and include pointers to extend the output (how to add a new component/module).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
