---
name: mapj-mcp
description: > Use when this capability is needed.
metadata:
  author: Mario-pereyra
---

# mapj-mcp — Agent Skill v2.0 (MCP 2025-06-18)

Run mapj as an MCP server (`mapj mcp serve` or the standalone `mapj-mcp`
binary). Reads JSON-RPC 2.0 requests on stdin, writes responses on stdout,
logs to stderr.

---

## Tools exposed (8 tools)

All tools carry `outputSchema` and annotations (`readOnlyHint: true`,
`idempotentHint: true`, `destructiveHint: false`).

| Tool | Purpose | Annotations |
|---|---|---|
| `search_totvs_docs` | Multi-source TOTVS docs search (7 native adapters + optional Brave/Exa) | readOnly, idempotent, openWorld |
| `fetch_totvs_doc`  | Retrieve a doc by canonical id or URL → Markdown (with `outline` and `section` modes for big docs, PDF support) | readOnly, idempotent, openWorld |
| `list_totvs_filters` | List valid `lines` / `produtos` filter values (CST BUSCA_FILTROS) | readOnly, idempotent, localOnly |
| `list_tdn_spaces`   | List all TDN (tdn.totvs.com) Confluence space keys + names | readOnly, idempotent, openWorld |
| `protheus_query`    | Live SELECT query on Protheus ERP SQL Server (SELECT-only enforced, audit log, `dry_run` for preview) | readOnly, idempotent, openWorld |
| `protheus_list_tables` | List Protheus DB tables (`INFORMATION_SCHEMA.TABLES`, optional prefix, shadow-table filter) | readOnly, idempotent, openWorld |
| `protheus_describe_table` | Describe Protheus table columns + optional `sample_tsv` (TOP 3 rows) | readOnly, idempotent, openWorld |
| `confluence_get_page` | Inline read of a TDN/Confluence page (markdown\|html\|json) by id or viewpage URL | readOnly, idempotent, openWorld |

See full schemas in `tools/list` response or in `mapj/internal/schema/schemas.go`.

### `protheus_query` safety layers

1. `ValidateReadOnly`: rejects non-SELECT/WITH/EXEC and forbidden keywords
2. Auto-inject `TOP N`: bare SELECT without TOP clause gets `TOP 1000` auto-added
3. Hard 30s timeout via `context.WithTimeout`
4. Client-side row cap (default 1000, max 10000)
5. Audit log: every call logged to `$CACHE/mapj/audit-protheus.jsonl`

Input: `{ query, profile_name?, max_rows? }` — never accepts raw connection strings.

---

## How to configure a client

### Claude Desktop / Claude Code

`%APPDATA%\Claude\claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "totvs-docs": {
      "command": "C:/path/to/mapj-mcp.exe",
      "env": {
        "BRAVE_API_KEY": "<optional>",
        "EXA_API_KEY":   "<optional>",
        "MAPJ_LOG_LEVEL": "info"
      }
    }
  }
}
```

### Cursor

`.cursor/mcp.json` in the project:
```json
{
  "mcpServers": {
    "totvs-docs": {
      "command": "/path/to/mapj-mcp",
      "args": []
    }
  }
}
```

### Alternative: invoke via Cobra

`mapj mcp serve` is equivalent to `mapj-mcp` (same code, just routed through
Cobra). Useful if you don't want to ship the second binary.

---

## Protocol summary

| Method | Behavior |
|---|---|
| `initialize` | Returns `{protocolVersion: "2025-06-18", serverInfo, capabilities}`. Degrades to "2024-11-05" if client requests it. |
| `notifications/initialized` | Acknowledged, no response (per spec) |
| `tools/list` | Returns all 8 tools with full inputSchema + outputSchema + annotations |
| `tools/call` | Dispatches to any of the 8 tools |
| `resources/list` | Returns catalog Resources (`mapj://catalog/filters`, `mapj://catalog/spaces`) + per-doc URI templates (`mapj://tdn/{id}`, `mapj://central/{id}`, `mapj://youtube/{id}`) |
| `resources/read` | Reads a Resource by URI; cached server-side (1h TTL) |
| `ping` | Returns `{}` |
| `shutdown` | Returns `null` |

JSON-RPC 2.0 errors: `-32700` parse, `-32600` invalid request, `-32601` method not found, `-32602` invalid params.

Tool-level errors come back as `result.isError=true` with a text content
block — the LLM can recover gracefully.

---

## Examples (manual JSON-RPC)

```bash
# List exposed tools
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | mapj-mcp

# Search (default 10 results, all 7 native adapters, default lang="any" = no locale filter)
echo '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{
  "name":"search_totvs_docs",
  "arguments":{"query":"NF-e inutilizar","max_results":10}
}}' | mapj-mcp

# Search with explicit locale filter (only returns docs tagged pt-br; excludes es/en-us).
# Use sparingly — the TOTVS corpus is mostly pt-br but has ES/EN translations you
# probably want visible. Default "any" is recommended for multilingual agents.
echo '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{
  "name":"search_totvs_docs",
  "arguments":{"query":"NF-e inutilizar","lang":"pt-br"}
}}' | mapj-mcp

# Discover valid --lines filter values before searching
echo '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{
  "name":"list_totvs_filters","arguments":{}
}}' | mapj-mcp

# List TDN spaces
echo '{"jsonrpc":"2.0","id":4,"method":"tools/call","params":{
  "name":"list_tdn_spaces","arguments":{}
}}' | mapj-mcp

# Query Protheus ERP (SELECT-only; profile must exist in local auth store)
echo '{"jsonrpc":"2.0","id":5,"method":"tools/call","params":{
  "name":"protheus_query",
  "arguments":{"query":"SELECT TOP 10 A1_COD, A1_NOME FROM SA1010","profile_name":"DEV"}
}}' | mapj-mcp

# Fetch a doc
echo '{"jsonrpc":"2.0","id":6,"method":"tools/call","params":{
  "name":"fetch_totvs_doc",
  "arguments":{"target":"tdn:66617420"}
}}' | mapj-mcp
```

---

## Operational notes

- The server is concurrency-safe; multiple requests can pipeline.
- All progress/error logs go to **stderr** so the JSON-RPC stream on stdout is clean.
- Cache: `pkg/cst` and `pkg/poui` use in-memory caches; no disk cache yet.
- Per-source timeout default: 12s. `protheus_query` has a hard 30s timeout.
- `protheus_query` writes every call to `$CACHE/mapj/audit-protheus.jsonl`.

---

## Failure modes the agent should expect

| Symptom | Likely cause | What to do |
|---|---|---|
| `result.isError=true` "query is required" | tool argument missing | Re-call with valid input |
| `result.isError=true` "validation error: query must start with SELECT" | non-SELECT in protheus_query | Fix the query; only SELECT/WITH/EXEC allowed |
| `result.isError=true` "profile not found" | unknown profile_name | Call `mapj protheus connection list` to see valid profiles |
| `result.isError=true` "query timeout after 30s" | very slow ERP query | Add TOP N or WHERE filter to narrow results |
| `result.isError=true` "fetch youtube: ...yt-dlp..." | yt-dlp not on PATH | Install yt-dlp or skip video fetches |
| Slow tool call (>10s) | Network latency in one adapter | Engine returns partial results with warnings |

---
> Source: [Mario-pereyra/mapj](https://github.com/Mario-pereyra/mapj) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
