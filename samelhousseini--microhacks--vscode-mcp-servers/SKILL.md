---
name: vscode-mcp-servers
description: Configure and use MCP (Model Context Protocol) servers in VS Code. Use when setting up MCP servers, configuring mcp.json, using MCP tools in chat, managing server trust, or troubleshooting MCP connections. Use when this capability is needed.
metadata:
  author: samelhousseini
---

# MCP Servers in VS Code

Model Context Protocol (MCP) is an open standard that lets AI models use external tools and services through a unified interface. MCP servers provide tools for file operations, databases, APIs, and more.

## Add MCP Servers

### From GitHub MCP Server Registry

1. Enable `chat.mcp.gallery.enabled` setting
2. Open Extensions view (`Ctrl+Shift+X`)
3. Search `@mcp` or run `MCP: Browse Servers`
4. Select **Install** (user profile) or **Install in Workspace**

### Configuration Locations

| Scope | Location |
|-------|----------|
| Workspace | `.vscode/mcp.json` |
| User | Settings via `MCP: Open User Configuration` |
| Dev Container | `devcontainer.json` |

## Configuration Format

### Basic Structure
```json
{
  "servers": {
    "server-name": {
      // Server configuration
    }
  },
  "inputs": [
    // Optional input variables for sensitive data
  ]
}
```

### Standard I/O (stdio) Servers

For locally-run servers communicating via stdin/stdout:
```json
{
  "servers": {
    "my-server": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem"],
      "env": {
        "API_KEY": "${input:api-key}"
      },
      "envFile": "${workspaceFolder}/.env"
    }
  }
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | `"stdio"` |
| `command` | Yes | Executable command |
| `args` | No | Command arguments array |
| `env` | No | Environment variables |
| `envFile` | No | Path to .env file |

### HTTP/SSE Servers

For remote servers over HTTP:
```json
{
  "servers": {
    "remote-server": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${input:api-token}"
      }
    }
  }
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | `"http"` or `"sse"` |
| `url` | Yes | Server URL |
| `headers` | No | HTTP headers |

**Unix sockets**: `unix:///path/to/server.sock`
**Windows pipes**: `pipe:///pipe/named-pipe`

### Input Variables

Securely store sensitive data:
```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "api-key",
      "description": "GitHub Personal Access Token",
      "password": true
    }
  ],
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${input:api-key}"
      }
    }
  }
}
```

## Use MCP Tools in Chat

### Automatic Invocation

1. Open Chat view (`Ctrl+Alt+I`)
2. Use tool picker to enable MCP tools (grouped by server)
3. Ask naturally: "List my GitHub issues"
4. Review and approve tool invocations when prompted

### Explicit Tool Reference

Type `#` followed by tool name to reference directly.

### In Custom Prompts/Agents

Reference MCP tools in frontmatter:
```yaml
tools: ['github/*', 'filesystem/*']
```

Use `<server>/*` format to include all tools from a server.

## MCP Resources

Access server-provided resources as context:

1. In Chat view, select **Add Context > MCP Resources**
2. Select resource type and provide parameters
3. Resources returned by tools can be saved via **Save** or drag-and-drop

## MCP Prompts

Invoke preconfigured prompts with slash commands:
```
/mcp.servername.promptname
```

## Server Management

### Commands

| Command | Action |
|---------|--------|
| `MCP: List Servers` | View all servers with actions |
| `MCP: Browse Servers` | Browse GitHub registry |
| `MCP: Reset Cached Tools` | Clear tool cache |
| `MCP: Reset Trust` | Reset server trust |
| `MCP: Open User Configuration` | Edit user mcp.json |
| `MCP: Open Workspace Folder Configuration` | Edit workspace mcp.json |

### Auto-Start

Enable `chat.mcp.autostart` (experimental) to automatically restart servers on configuration changes.

## Server Naming Conventions

- Use camelCase: `uiTesting`, `githubIntegration`
- No whitespace or special characters
- Unique names per server
- Descriptive names reflecting functionality

## Development Mode

Enable debugging in server configuration:
```json
{
  "servers": {
    "my-server": {
      "type": "stdio",
      "command": "node",
      "args": ["server.js"],
      "dev": {
        "watch": "**/*.js",
        "debug": true
      }
    }
  }
}
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Server not starting | Check command/args, verify not using Docker `-d` flag |
| Error indicator in Chat | Select error → **Show Output** for logs |
| "Cannot have more than 128 tools" | Reduce enabled tools in picker or enable `github.copilot.chat.virtualTools.threshold` |
| Trust prompt not appearing | Starting from mcp.json bypasses trust prompt |

### View Server Logs

1. `MCP: List Servers` → Select server → **Show Output**
2. Or click error notification → **Show Output**

## Security

- Only add servers from trusted sources
- Review configuration before starting
- Trust confirmation required on first start
- Use `MCP: Reset Trust` to revoke trust

## Settings Sync

Synchronize MCP servers across devices:

1. Run `Settings Sync: Configure`
2. Ensure **MCP Servers** is included

## Example: GitHub MCP Server
```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "github-token",
      "description": "GitHub Personal Access Token",
      "password": true
    }
  ],
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${input:github-token}"
      }
    }
  }
}
```

Then in chat: "List my GitHub issues" or "Create a new issue in repo X"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samelhousseini) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
