---
name: syntra-setup
description: Initialize and configure a Syntra backend. Use when the user wants to set up Syntra, connect to their backend, verify the connection, configure MCP, or bootstrap a new project with Syntra as the backend. Use when this capability is needed.
metadata:
  author: mauricioperera
---

# Syntra Setup

Syntra is a self-hosted Backend-as-a-Service connected via MCP. All operations use MCP tools — no HTTP calls needed.

## Verify connection

1. Call `system_get_metadata` to confirm connectivity and get server version
2. Call `system_list_modules` to see active modules
3. Call `database_list_tables` to check existing schema

## First-time bootstrap

After verifying connection, set up a basic project:

1. Create the data model with `database_create_table`
2. Insert seed data with `database_insert_records`
3. Verify with `database_select_records`
4. Create a storage bucket with `storage_create_bucket` if files are needed

## MCP client configuration

Add to the MCP client config file:

```json
{
  "mcpServers": {
    "syntra": {
      "type": "streamable-http",
      "url": "http://localhost:7130/api/mcp",
      "headers": { "X-API-Key": "ik_your_api_key" }
    }
  }
}
```

Config file locations:
- Claude Code: `~/.claude.json`
- Claude Desktop: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Cursor: `.cursor/mcp.json` in the project root

## Get an API key

If the user doesn't have an API key, they need to:
1. Login as admin via the HTTP API or dashboard
2. Call `POST /api/secrets/rotate-api-key` with their access token
3. The returned `ik_...` key goes in the MCP config

## Environment reference

For detailed environment variable documentation, see [env-reference.md](references/env-reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauricioperera) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
