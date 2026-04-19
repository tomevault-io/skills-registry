---
name: mcp
description: > Use when this capability is needed.
metadata:
  author: fsaint
---

# MCP Server Management

You have access to remote MCP (Model Context Protocol) servers that provide
additional tools beyond your built-in capabilities. These tools appear in your
tool list with a `<server>__<tool>` naming pattern (e.g., `tavily__search`,
`github__create_issue`).

## When to Use MCP Tools

- When the user asks you to perform a task that matches an MCP tool's description
- Treat MCP tools exactly like built-in tools — call them directly
- If a tool call returns a 401 or 403 error, inform the user they need to
  authenticate with `/mcp auth <server>`

## Available Commands

Users can manage MCP servers with these commands:

| Command | Description |
|---------|-------------|
| `/mcp servers` | List all configured MCP servers and their status |
| `/mcp tools [server]` | List available tools from MCP servers |
| `/mcp status <server>` | Show detailed status for a specific server |
| `/mcp disconnect <server>` | Disconnect from an MCP server |
| `/mcp refresh [server]` | Re-discover tools from a server |
| `/mcp auth <server>` | Trigger OAuth authentication for a server |
| `/mcp connect <url>` | Connect to a new MCP server |
| `/mcp help` | Show available commands |

## Error Handling

- If a tool call fails with a connection error, suggest the user check
  `/mcp status <server>` for details
- If authentication is required, guide the user to run `/mcp auth <server>`
- Do not retry failed tool calls more than once

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fsaint) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
