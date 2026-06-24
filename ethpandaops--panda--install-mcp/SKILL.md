---
name: install-mcp
description: >- Use when this capability is needed.
metadata:
  author: ethpandaops
---

# Install panda MCP Server

Register panda as an MCP server in the user's AI coding assistants. Panda
exposes an SSE transport at `http://localhost:2480/sse` by default.

## Steps

1. Detect which clients are installed by checking for their config directories.
2. For each detected client, read the existing config file (or start with `{}`).
3. Merge the `mcpServers.panda` entry — preserve all existing keys.
4. Write the updated config back.
5. Report what was configured.

## Client Configurations

### Claude Code

- **Detection:** `~/.claude/` directory exists
- **Config file:** `~/.claude.json`
- **Entry to merge into `mcpServers`:**

```json
{
  "panda": {
    "type": "sse",
    "url": "http://localhost:2480/sse"
  }
}
```

### Claude Desktop (macOS)

- **Detection:** `~/Library/Application Support/Claude/` directory exists
- **Config file:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Entry to merge into `mcpServers`:**

```json
{
  "panda": {
    "url": "http://localhost:2480/sse"
  }
}
```

### Cursor

- **Detection:** `~/.cursor/` directory exists
- **Config file:** `~/.cursor/mcp.json`
- **Entry to merge into `mcpServers`:**

```json
{
  "panda": {
    "url": "http://localhost:2480/sse"
  }
}
```

## Rules

- Read the full config file before writing. Never overwrite — always merge.
- If `mcpServers` does not exist in the config, create it.
- If `panda` is already registered, tell the user and skip unless they ask to overwrite.
- Use 2-space JSON indentation with a trailing newline.
- If no supported clients are detected, tell the user and list the supported clients.

---
> Source: [ethpandaops/panda](https://github.com/ethpandaops/panda) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
