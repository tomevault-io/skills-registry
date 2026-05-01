---
name: clawdmigrate
description: Migrate from **moltbot** or **clawdbot** to **openclaw**. Preserves config, memory, and clawdbook (Moltbook) data safely on any system. Use when this capability is needed.
metadata:
  author: openclaw
---
# clawd-migrate

Migrate from **moltbot** or **clawdbot** to **openclaw**. Preserves config, memory, and clawdbook (Moltbook) data safely on any system.

## What it does

- **Discovers** existing bot assets (memory files, config, clawdbook/Moltbook credentials)
- **Backs up** everything into a timestamped folder before any changes
- **Migrates** files into the openclaw layout: `memory/`, `.config/openclaw/`, `.config/clawdbook/`
- **Verifies** every source file was copied to its destination (existence + size match)
- **Reinstalls openclaw** (`npm i -g openclaw`) and runs `openclaw onboard` automatically

## Quick start

```bash
npx clawd-migrate
```

Interactive menu walks you through: Discover -> Backup -> Migrate -> Verify -> Reinstall openclaw.

## CLI commands

```bash
clawd-migrate                     # Interactive menu (default)
clawd-migrate discover [--root PATH]
clawd-migrate backup [--root PATH]
clawd-migrate migrate [--root PATH] [--no-backup] [--output PATH] [--setup-openclaw]
```

## Requirements

- Node.js 14+
- Python 3.x

## What gets migrated

- **Memory/identity:** SOUL.md, USER.md, TOOLS.md, IDENTITY.md, AGENTS.md, MEMORY.md
- **Config:** `.config/moltbook/`, `.config/moltbot/`
- **Clawdbook/Moltbook:** Kept under `.config/clawdbook/` (credentials, API keys)
- **Extra:** `projects/` (if present)

## Tags

migration, openclaw, moltbot, clawdbot, clawdbook, moltbook, backup, verify

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
