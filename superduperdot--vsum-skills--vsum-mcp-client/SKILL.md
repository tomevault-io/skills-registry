---
name: vsum-mcp-client
description: Wire the V-Sum MCP server (https://v-sum.com/mcp) into any streamable-HTTP MCP client. Use when the user wants to add V-Sum as a tool source for Claude Desktop, Cursor, ChatGPT, or another MCP-capable agent, or when they ask how to call V-Sum tools from code. Use when this capability is needed.
metadata:
  author: superduperdot
---

# V-Sum MCP client quickstart

V-Sum publishes a read-only [Model Context Protocol](https://modelcontextprotocol.io) server so any MCP-capable agent can call its archive of fintech briefings and interviews as first-class tools.

## When to use this skill

- User wants to "add V-Sum to Claude" (or Cursor, ChatGPT, OpenAI agents, etc.)
- User asks how to call V-Sum tools from code
- User wants to preview the server before connecting

## Endpoint

```
POST https://v-sum.com/mcp
```

- Transport: **streamable HTTP** (not stdio, not SSE-only)
- Protocol: JSON-RPC 2.0, MCP `2025-06-18`
- Auth: none (public, read-only)
- Required request headers:
  - `Content-Type: application/json`
  - `Accept: application/json, text/event-stream`
- Server card (static preview, no connection needed): <https://v-sum.com/.well-known/mcp/server-card.json>

Lifecycle notifications like `notifications/initialized` return **HTTP 202 Accepted** with an empty body.

## Claude Desktop config

Add to `claude_desktop_config.json`:

```jsonc
{
  "mcpServers": {
    "v-sum": {
      "type": "streamable-http",
      "url": "https://v-sum.com/mcp"
    }
  }
}
```

Restart Claude. V-Sum tools (`search_briefings`, `get_briefing`, `list_briefings`, `list_events`, `list_companies`) appear in the tool picker.

## Cursor / Codex / any `mcp.json`

```jsonc
{
  "mcpServers": {
    "v-sum": { "url": "https://v-sum.com/mcp" }
  }
}
```

## Raw JSON-RPC (for debugging)

```bash
# 1. Initialize
curl -s https://v-sum.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'

# 2. List tools
curl -s https://v-sum.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list"}'

# 3. Call a tool
curl -s https://v-sum.com/mcp \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"search_briefings","arguments":{"query":"stablecoin","limit":3}}}'
```

## MCP Apps (interactive UI)

All read tools advertise:

```json
"_meta": { "ui": { "resourceUri": "ui://v-sum/briefing-browser" } }
```

Hosts that support MCP Apps can `resources/read` that URI to render an inline briefing browser. Hosts that don't support MCP Apps ignore `_meta` and continue to work with the JSON tool results.

## Tools reference

| Tool | Arguments | Returns |
|---|---|---|
| `search_briefings` | `query` (string), `limit` (int, default 10) | Ranked briefing hits |
| `get_briefing` | `slug` (string) | One briefing with full details + YouTube URL |
| `list_briefings` | `limit`, `cursor`, optional `company`, `event` | Paginated briefings |
| `list_events` | `limit`, `cursor` | Paginated events |
| `list_companies` | `limit`, `cursor` | Paginated companies |

For schemas and examples, fetch the server card above or open <https://v-sum.com/openapi.json>.

## Alternatives

If the client doesn't speak MCP, use the public JSON API at <https://api.v-sum.com> or one of the typed SDKs:

- `npm install vsum-api-client`
- `pip install vsum-api-client`
- `go get github.com/superduperdot/vsum-sdk-go`

---
> Source: [superduperdot/vsum-skills](https://github.com/superduperdot/vsum-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
