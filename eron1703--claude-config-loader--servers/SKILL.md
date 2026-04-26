---
name: servers
description: Load server information, infrastructure details, and access patterns when working with deployment or server configuration Use when this capability is needed.
metadata:
  author: eron1703
---

# Infrastructure Information

## Servers

Use commander-mcp tools for live server data:
- `get_context_servers` — all servers with specs and status
- `list_servers` — server list with filtering
- `get_server(server_id)` — detailed server info with services and networks

For manual reference: `cat ~/projects/claude-config-loader/config/servers.yaml`

Use this information when:
- Deploying applications
- Configuring SSH access
- Troubleshooting server issues
- Setting up new infrastructure
- Accessing production/staging environments

**Security Note:** Never expose credentials. Always reference password managers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eron1703) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
