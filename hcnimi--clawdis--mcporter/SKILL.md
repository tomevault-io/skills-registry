---
name: mcporter
description: Manage and call MCP servers (list, call, auth, daemon). Use when this capability is needed.
metadata:
  author: hcnimi
---

# mcporter

Use `mcporter` to list MCP servers and call tools.

Quick start
- `mcporter list`
- `mcporter list <server> --schema`
- `mcporter call <server.tool> arg=value`

Auth + lifecycle
- OAuth: `mcporter auth <server>`
- Daemon: `mcporter daemon status|start|stop`

Ad-hoc servers
- HTTP: `mcporter list --http-url https://host/mcp --name <name>`
- STDIO: `mcporter call --stdio "bun run ./server.ts" --name <name>`

Notes
- Config sources: `~/.mcporter/mcporter.json[c]` and `config/mcporter.json`.
- Prefer `--json` when you need machine-readable output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hcnimi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
