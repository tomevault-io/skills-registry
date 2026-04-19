---
name: mcp-client
description: Connect to multiple MCP servers via HTTP to query data, run tools, and get information. Use when asked to check platform data, analytics, or interact with external services. Use when this capability is needed.
metadata:
  author: lktiep
---

# MCP Client — Multi-Server Tool Caller

Call tools on any configured MCP server via HTTP. Supports multiple servers — choose the right one based on user's request.

## Available MCP Servers

| Server | Description | When to use |
|--------|-------------|-------------|
| `keothom` | Kèo Thơm platform data | Financial data, users, wallets, games, transactions, revenue |

> More servers can be added by editing `/app/skills/mcp-client/servers.json`

## Quick Usage

### List available servers
```shell
python3 /app/skills/mcp-client/scripts/mcp_call.py --list-servers
```

### List tools on a server
```shell
python3 /app/skills/mcp-client/scripts/mcp_call.py --server keothom --list-tools
```

### Call a tool
```shell
python3 /app/skills/mcp-client/scripts/mcp_call.py --server keothom --tool platform_overview
```

### Call a tool with parameters
```shell
python3 /app/skills/mcp-client/scripts/mcp_call.py --server keothom --tool search_user --params '{"query": "demo"}'
```

### Call a tool with named args (shorthand)
```shell
python3 /app/skills/mcp-client/scripts/mcp_call.py --server keothom --tool get_user_wallet --params '{"display_name": "Tieple"}'
```

## Keothom Server — Available Tools

| Tool | Description | Parameters |
|------|-------------|------------|
| `platform_overview` | Tổng quan: users, sellers, games, wallets | none |
| `revenue_report` | Báo cáo doanh thu | `days` (default: 30) |
| `game_stats` | Thống kê games | `mode` (LOTTERY/AUCTION) |
| `search_user` | Tìm user | `query` (tên/email/ID) |
| `get_user_wallet` | Ví & giao dịch user | `user_id` or `display_name` |
| `list_wallets` | Danh sách ví | `limit`, `min_balance` |
| `list_transactions` | Giao dịch gần nhất | `days`, `limit`, `type`, `status` |
| `top_sellers` | Top sellers | `limit` |
| `frozen_balances` | Users có frozen balance | none |
| `anomalies` | Bất thường tài chính | `severity` (critical/high/medium) |
| `parity_check` | Kiểm tra toàn vẹn tài chính | none |
| `execute_sql` | Chạy SQL trực tiếp (admin) | `query` |

## Adding a New MCP Server

Edit `/app/skills/mcp-client/servers.json`:

```json
{
  "your_server": {
    "name": "Your Server",
    "url": "https://your-mcp-server.workers.dev",
    "auth_type": "bearer",
    "auth_env": "YOUR_MCP_API_KEY",
    "description": "What this server does"
  }
}
```

Then set the env var: `export YOUR_MCP_API_KEY=xxx`

## Important Notes

- MCP servers must support **HTTP transport** (Streamable HTTP or SSE)
- Auth tokens are read from **environment variables** (never hardcoded)
- Results are printed as formatted JSON
- Timeout: 30s per request (configurable with `--timeout`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lktiep) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
