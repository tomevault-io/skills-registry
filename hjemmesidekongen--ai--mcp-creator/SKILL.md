---
name: mcp-creator
description: > Use when this capability is needed.
metadata:
  author: hjemmesidekongen
---

# MCP Creator

Create and configure MCP server integrations for Claude Code plugins.

## When to trigger

- Adding external service connections to a plugin
- Setting up `.mcp.json` configuration
- Configuring stdio, SSE, HTTP, or WebSocket MCP servers
- Integrating MCP tools into commands or agents
- Debugging MCP server connectivity issues

## Server types

| Type | Transport | Best for | Auth method |
|------|-----------|----------|-------------|
| stdio | Child process (stdin/stdout) | Local tools, custom servers, NPM packages | Env vars |
| sse | HTTP + Server-Sent Events | Hosted services, cloud APIs | OAuth (auto) |
| http | REST requests | API backends, token auth | Bearer/API key |
| ws | WebSocket | Real-time streaming, low-latency | Bearer/headers |

## Configuration methods

| Method | Location | When to use |
|--------|----------|-------------|
| `.mcp.json` | Plugin root | Multiple servers, clean separation (recommended) |
| `mcpServers` in plugin.json | `.claude-plugin/plugin.json` | Single server, simple plugins |

## Tool naming

Format: `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`

Pre-allow specific tools in command frontmatter via `allowed-tools`. Avoid wildcards.

## Tool annotations

Add hints so Claude reasons about safety: `readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`. After build + tests pass, create `evaluation.xml` with 10 read-only Q&A pairs. Full process: `references/process.md`.

---
> Source: [hjemmesidekongen/ai](https://github.com/hjemmesidekongen/ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
