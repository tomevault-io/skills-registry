---
name: mcp-inspector
description: Test Model Context Protocol (MCP) servers using the MCP Inspector CLI Use when this capability is needed.
metadata:
  author: git-fg
---

# MCP Inspector Testing Skill

Quick testing for MCP servers (stdio, HTTP, SSE). Run Inspector CLI commands to validate tools, resources, and prompts.

## Quick Start

```bash
# Basic tool test (local server)
npx @modelcontextprotocol/inspector --cli node build/index.js --method tools/list

# Call a tool
npx @modelcontextprotocol/inspector --cli node build/index.js \
  --method tools/call --tool-name mytool --tool-arg key=value

# Remote server (HTTP/SSE)
npx @modelcontextprotocol/inspector --cli https://server.com --method tools/list
```

## Common Methods

| Method | Purpose | Required Flags |
|--------|---------|----------------|
| `tools/list` | List available tools | none |
| `tools/call` | Execute a tool | `--tool-name` |
| `resources/list` | List resources | none |
| `resources/read` | Read resource content | `--uri` |
| `resources/templates/list` | List resource templates | none |
| `prompts/list` | List prompts | none |
| `prompts/get` | Get prompt with args | `--prompt-name` |

## Key CLI Flags

**Transport:**
- `--cli` - Enable non-interactive mode (required)
- `--transport <stdio\|sse\|http>` - Explicit transport type
- `--server-url <url>` - Server URL for SSE/HTTP

**Testing:**
- `--method <name>` - MCP method to call
- `--tool-arg key=value` - Tool arguments (repeatable)
- `--uri <uri>` - Resource URI
- `--prompt-args key=value` - Prompt arguments (repeatable)

**Config:**
- `--config <path>` - Load from mcp.json
- `--server <name>` - Select server from config
- `-e KEY=VALUE` - Set environment variable
- `--header "Name: Value"` - HTTP headers for remote servers

## Transport Auto-Detection

- URLs ending in `/mcp` → HTTP (streamable)
- URLs ending in `/sse` → SSE
- No URL → stdio (local server)

## Reference Docs

See `references/` directory:
- `cli-flags.md` - Complete flag reference
- `transports.md` - stdio/SSE/HTTP details
- `testing-methods.md` - All methods with examples
- `config-files.md` - mcp.json patterns
- `known-issues.md` - Server vs CLI bugs

## Language-Specific Examples

See `examples/` directory:
- `julia-stdio.md` - Julia server testing
- `remote-servers.md` - HTTP/SSE testing, curl, mcp-remote

## Official Docs

- [MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector)
- [GitHub](https://github.com/modelcontextprotocol/inspector)
- [MCP Spec (2025-06-18)](https://modelcontextprotocol.io/specification/2025-06-18/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/git-fg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
