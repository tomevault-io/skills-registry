---
name: session-start
description: Load full project context — reads all .md files, specs, changelog, and git history to produce a prioritized project briefing Use when this capability is needed.
metadata:
  author: ckallum
---

# /session-start

Load comprehensive project context by delegating to the `@context-loader` agent.

## Instructions

Run the `@context-loader` agent to read all project documentation, specs, tasks, and git history, then produce a prioritized briefing for the session.

If `$ARGUMENTS` is provided, scope context loading to that workspace subdirectory — still read root-level files (README.md, CLAUDE.md, SPECLOG.md, CHANGELOG.md) but focus spec and source file loading on the specified workspace.

If no arguments are provided, run at the project root and cover all workspaces.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ckallum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
