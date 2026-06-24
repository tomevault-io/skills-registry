---
name: portainer-control
description: Start, stop, or restart a Docker container on a Portainer-managed host. Use when asked to restart, stop, or start a specific container. Use when this capability is needed.
metadata:
  author: sinositato
---

## User Input

```text
$ARGUMENTS
```

Control (start/stop/restart) a named container on a Docker host via the Portainer API.

## Steps

1. Parse `$ARGUMENTS`:
   - Extract action: `start`, `stop`, or `restart`
   - Extract container name
   - Extract host if specified after `on` (e.g. `on docker01`)

2. Run:

```bash
python3 $SKILL_DIR/portainer_control.py <action> <container> [--host <host>]
```

   Example: `python3 $SKILL_DIR/portainer_control.py restart grafana --host docker01`

3. Report the script output to the user.

4. If the script reports multiple matches, ask the user to specify the host.

5. If auth fails, tell the user:
   > Set `PORTAINER_TOKEN=ptr_...` before invoking. Generate one in Portainer: User Settings > Access Tokens.

---
> Source: [sinositato/tato-claude-plugins](https://github.com/sinositato/tato-claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
