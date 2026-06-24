---
name: orquestra-mcp-connect
description: | Use when this capability is needed.
metadata:
  author: berkayoztunc
---

# Orquestra MCP — Connection Setup

MCP endpoint: `https://api.orquestra.dev/mcp` (Streamable HTTP, stateless)

## Claude Desktop

File: `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "orquestra": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/client-streamable-http",
        "https://api.orquestra.dev/mcp"
      ]
    }
  }
}
```

Restart Claude Desktop after saving.

## Cursor

File: `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global)

```json
{
  "mcpServers": {
    "orquestra": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/client-streamable-http",
        "https://api.orquestra.dev/mcp"
      ]
    }
  }
}
```

## Claude Code (CLI)

```bash
claude mcp add orquestra --transport http https://api.orquestra.dev/mcp
```

Or in `.claude/settings.json`:

```json
{
  "mcpServers": {
    "orquestra": {
      "type": "http",
      "url": "https://api.orquestra.dev/mcp"
    }
  }
}
```

## Verify Connection

After connecting, ask Claude:

> "List the tools available in the orquestra MCP server"

You should see 8 tools: `search_programs`, `list_instructions`, `build_instruction`,
`list_pda_accounts`, `derive_pda`, `read_llms_txt`, `get_ai_analysis`, `simulate_instruction`.

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Tools not appearing | Restart the client after config change |
| `npx` not found | Install Node.js ≥ 18 |
| Connection refused | Check internet access to `api.orquestra.dev` |
| `search_programs` returns empty | The program may not be indexed — upload IDL at orquestra.dev |
| Wrong tool name format | Tool names use underscores: `search_programs`, NOT `searchPrograms` |

## Agent Pipeline (optional)

To install the full Orquestra agent pipeline (researcher → builder → signer):

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/berkayoztunc/orquestra/main/install-skills.sh)
```

Agents installed: `orquestra`, `orquestra-researcher`, `orquestra-pda-explorer`,
`orquestra-tx-builder`, `orquestra-simulator`, `orquestra-signer`

---
> Source: [berkayoztunc/orquestra](https://github.com/berkayoztunc/orquestra) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
