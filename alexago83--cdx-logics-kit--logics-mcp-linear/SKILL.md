---
name: logics-mcp-linear
description: Install, configure, and use a Linear MCP server to connect MCP clients to Linear (query issues, projects, cycles). Use when wiring MCP clients to Linear. Use when this capability is needed.
metadata:
  author: alexago83
---

# Linear MCP

## Quick start
1) Create a Linear API key and note the team/project IDs you need.
2) Add the MCP server config to your client (below).
3) Restart the client and test a simple query (list issues).

### Standard MCP config (replace with your server)
```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "<LINEAR_MCP_SERVER_PACKAGE>"]
    }
  }
}
```

### Environment variables (typical)
- `LINEAR_API_KEY`
- `LINEAR_API_URL` (optional)
- `LINEAR_API_TEAM_ID` (optional)

If your MCP server expects env vars, add them to the config:

```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "<LINEAR_MCP_SERVER_PACKAGE>"],
      "env": {
        "LINEAR_API_KEY": "***",
        "LINEAR_API_URL": "https://api.linear.app/graphql",
        "LINEAR_API_TEAM_ID": "***"
      }
    }
  }
}
```

## Usage flow (agent-side)
- Start with a read-only query (list issues for a team).
- Narrow by team/cycle/project to keep responses small.

## Safety
- Treat API keys as secrets. Do not log them.
- Prefer read-only integrations when possible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
