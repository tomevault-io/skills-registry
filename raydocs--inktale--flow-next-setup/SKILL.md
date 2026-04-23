---
name: flow-next-setup
description: Local install of flowctl CLI and CLAUDE.md/AGENTS.md instructions. Use when user runs /flow-next:setup. Use when this capability is needed.
metadata:
  author: raydocs
---

# Flow-Next Setup

Install flowctl locally and add instructions to project docs.

## Benefits

- `flowctl` accessible from command line (add `.flow/bin` to PATH)
- Other AI agents (OpenCode, Cursor, etc.) can read instructions from CLAUDE.md/AGENTS.md
- Works without Claude Code plugin installed

## Workflow

Read [workflow.md](workflow.md) and follow each step in order.

## Notes

- Copies scripts (not symlinks) for portability across environments
- Safe to re-run - will detect existing setup and offer to update
- Use the **question** tool for interactive prompts in setup

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raydocs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
