---
name: perry-workspaces
description: Create and manage isolated Docker workspaces on your tailnet with Claude Code and OpenCode pre-installed. Use when working with Perry workspaces, connecting to coding agents, or managing remote development environments. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Perry Workspaces

Isolated Docker workspaces on your tailnet with coding agents pre-installed.

## Commands
```bash
perry start <name> --clone git@github.com:user/repo.git  # Create
perry ls                                                  # List
perry stop <name>                                         # Stop
perry remove <name>                                       # Delete
perry shell <name>                                        # Interactive shell
```

## SSH Access
```bash
ssh workspace@<name>        # User is always 'workspace'
ssh workspace@<IP>          # Use IP if MagicDNS fails
```

## Coding Agents
- **OpenCode**: `http://<workspace>:4096` (web UI) or attach via CLI
- **Claude Code**: Run inside workspace shell (`perry shell` then `claude`)

## Project Location
Projects clone to `~/<name>`, not `/workspace`:
```bash
cd ~/my-project  # Correct
```

## Troubleshooting
- **Can't reach**: Check `tailscale status`, use IP instead of hostname
- **SSH fails**: User must be `workspace`, not your local user
- **Slow start**: Check web UI for progress

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
