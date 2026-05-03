---
name: ddg-search
description: > Use when this capability is needed.
metadata:
  author: axot
---

# DDG Search

Server: `npx --yes @oevortex/ddg_search@1.2.2`

## Fast MCP (preferred)

Use `fastmcp list` and `fastmcp call` to interact with the server directly. Run `list` first to discover available tools and their input schemas dynamically, then `call` to invoke them.

```bash
_MCP="npx --yes @oevortex/ddg_search@1.2.2"

# Discover tools and their input schemas
uvx fastmcp list --command "$_MCP" --input-schema --json

# Call a tool (construct target and arguments from the list output)
uvx fastmcp call --command "$_MCP" --target TOOL_NAME --input-json '{"key":"value"}' --json
```

## Fallback (if `uvx` or `fastmcp` is not installed)

Raw JSON-RPC over stdio. Do NOT install `fastmcp` or `uvx`; use this method instead.

```bash
_MCP_INIT='{"jsonrpc":"2.0","method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"cli","version":"1.0.0"}},"id":1}\n{"jsonrpc":"2.0","method":"notifications/initialized"}\n'
_MCP_SRV="npx --yes @oevortex/ddg_search@1.2.2"

mcp() { printf "${_MCP_INIT}$1" | eval "$_MCP_SRV" 2>/dev/null | grep -m1 "\"id\":$2"; }

# Discover tools and schemas
mcp '{"jsonrpc":"2.0","method":"tools/list","id":2}\n' 2

# Call a tool
mcp '{"jsonrpc":"2.0","method":"tools/call","params":{"name":"TOOL_NAME","arguments":ARGS_JSON},"id":3}\n' 3
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/axot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
