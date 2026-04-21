---
name: cli-design
description: Design patterns and conventions for the vm0 CLI user experience Use when this capability is needed.
metadata:
  author: vm0-ai
---

# CLI Design Skill

Use this skill when writing new CLI commands, reviewing CLI code, or fixing inconsistencies.

## Documentation

Read the CLI design guideline: [docs/cli-design-guideline.md](../../../docs/cli-design-guideline.md)

## Key Principles

1. **Atomic Command** — each command does one operation, agents compose them freely
2. **TTY & Non-TTY** — every command works in both interactive and programmatic modes
3. **Guided Flow** — output always guides to the next action (success → next step, error → remediation, empty → creation)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vm0-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
