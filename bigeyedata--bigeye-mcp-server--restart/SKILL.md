---
name: restart
description: Restart the Docker container for the Bigeye MCP Server Use when this capability is needed.
metadata:
  author: bigeyedata
---

# Container Restart

Restart the Bigeye MCP Server Docker container. This is useful when you need to refresh the service or recover from container issues without rebuilding the image.

## Usage

When invoked, this skill will:
1. Restart the container via docker compose
2. Report success

## Use Cases

- Apply configuration changes that require restart
- Recover from container crashes or hangs
- Reset application state

## Implementation

Execute the project's container management script:
```bash
./bigeye-mcp.sh restart
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bigeyedata) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
