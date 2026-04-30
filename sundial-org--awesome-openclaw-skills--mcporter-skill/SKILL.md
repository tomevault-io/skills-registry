---
name: mcporter-skill
description: Use the mcporter CLI to list, configure, auth, and call MCP servers/tools directly (HTTP or stdio), including ad-hoc servers, config edits, and CLI/type generation. Use when this capability is needed.
metadata:
  author: sundial-org
---

# mcporter

Use `mcporter` to manage MCP (Model Context Protocol) servers and tools.

## Requirements
- `mcporter` CLI installed (via Homebrew: `brew install pdxfinder/tap/mcporter`)
- MCP server configuration in `~/.config/mcporter/`

## Common Commands

### List Configured Servers
```bash
mcporter list
```

### Authentication
```bash
mcporter auth --help
```

### Call MCP Tools
```bash
mcporter call <server-name> <tool-name> [arguments...]
```

### Generate CLI/Types
```bash
mcporter generate cli <server-name>
mcporter generate types <server-name>
```

### Config Management
```bash
mcporter config --help
```

## Notes
- mcporter supports both HTTP and stdio MCP servers
- Ad-hoc server creation is supported
- CLI generation creates typed wrappers for MCP tools
- Use `exec` tool to run mcporter commands

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
