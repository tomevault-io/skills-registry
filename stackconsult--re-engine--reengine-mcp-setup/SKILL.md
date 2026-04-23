---
name: reengine-mcp-setup
description: Configure Windsurf MCP servers for RE Engine (dev stdio and prod remote HTTP), including env interpolation and tool enablement. Use when this capability is needed.
metadata:
  author: stackconsult
---

# RE Engine MCP Setup

## Inputs needed
- target environment: dev/staging/prod
- credentials stored in env vars

## Procedure
1) Read `docs/WINDSURF-MCP.md`
2) Draft `mcp_config.json` entries for GitHub + RE Engine servers
3) Ensure no secrets are written into repo
4) Validate tool list stays under 100 enabled tools

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stackconsult) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
