---
name: mcp-deployment-registry
description: Packaging, distributing, and connecting MCP servers to production environments. Use when this capability is needed.
metadata:
  author: jcorpac
---

# MCP Deployment & Registry

Making your MCP server available to users and other agents.

## Packaging
- **`uv`**: Using `uv` for ultra-fast, reproducible Python environments.
- **Docker**: Packaging the server as a container for cloud deployment (e.g., Google Cloud Run).

## Configuration
- **Claude Desktop**: Deploying via `claude_desktop_config.json`.
- **Remote Access**: Using SSE to host servers accessible over the internet with auth.

## Best Practices
- **Health Checks**: Implement a `/health` endpoint for remote servers.
- **Versioning**: Tag your Docker images to ensure stable client connections.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
