---
name: brainwash
description: Manage OpenClaw persona context files - view, audit, edit, and validate your agent's identity. Use when checking persona files, auditing conventions, or generating avatar prompts. Use when this capability is needed.
metadata:
  author: joelklabo
---

# brainwash

Manage OpenClaw persona context files with ease.

## Quick Reference

| Task | Command |
|------|---------|
| List all context files | `brainwash list` |
| Show a specific file | `brainwash show soul` |
| Audit conventions | `brainwash audit` |
| Edit a file | `brainwash edit soul` |
| Generate avatar prompt | `brainwash avatar` |
| Create missing files | `brainwash init` |
| Quick status | `brainwash status` |

## Commands

### list
Show all context files with status (exists, lines, size).

### show \<file\>
Display contents of a context file.
- soul → SOUL.md
- identity → IDENTITY.md  
- user → USER.md
- agents → AGENTS.md
- tools → TOOLS.md
- heartbeat → HEARTBEAT.md
- memory → MEMORY.md

### audit
Validate all files against OpenClaw conventions:
- PURPOSE comments
- Required fields in IDENTITY.md
- File size warnings (approaching 20K truncation)
- Naming consistency

### edit \<file\>
Open a context file in your `$EDITOR`.

### avatar
Generate an avatar prompt from IDENTITY.md and SOUL.md for use with image generation tools.

### init
Create missing context files from templates.

### status
Quick summary of workspace health.

## Workspace Selection

brainwash auto-discovers workspaces in this order:

1. `BRAINWASH_WORKSPACE` env var
2. Config file (`~/.config/brainwash/settings.json`)
3. OpenClaw config (`~/.openclaw/openclaw.json` agents)
4. `OPENCLAW_PROFILE` env (`~/.openclaw/workspace-$PROFILE`)
5. Default: `~/.openclaw/workspace`

Override with: `brainwash --workspace /path/to/workspace`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joelklabo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
