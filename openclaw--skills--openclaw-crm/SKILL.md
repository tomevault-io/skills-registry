---
name: openclaw-crm
description: Local-first CRM for tracking leads, deals, follow-ups, and pipeline. Uses SQLite with WAL mode, CLI via Commander. Use when this capability is needed.
metadata:
  author: openclaw
---
# openclaw-crm

Local-first CRM for tracking leads, deals, follow-ups, and pipeline. Uses SQLite with WAL mode, CLI via Commander.

## Quick Start
- `cd skills/crm && npm install`
- Run `node src/cli.js lead add "John Doe" --email john@example.com`
- Generate interchange: `node src/cli.js refresh`

## Integration
Use `exec` tool: `crm lead list`, `crm deal add "New Deal" --contact abc123 --value 10000`

Interchange files in workspace/interchange/crm/ for cross-agent sharing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
