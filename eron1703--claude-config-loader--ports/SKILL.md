---
name: ports
description: Load port mappings for all projects when working with networking, docker-compose, or service configuration Use when this capability is needed.
metadata:
  author: eron1703
---

# Port Configuration

Use commander-mcp tools for live port data:
- `get_context_ports` — full port allocation table
- `list_ports` — port list with optional conflict detection
- `get_port(port_number)` — what uses a specific port

For manual reference: `cat ~/projects/claude-config-loader/config/ports.yaml`

Use this information when:
- Working with docker-compose files
- Configuring service URLs
- Troubleshooting connection issues
- Adding new services
- Checking for port conflicts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eron1703) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
