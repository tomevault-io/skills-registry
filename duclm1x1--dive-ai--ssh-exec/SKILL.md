---
name: ssh-exec
description: Run a single command on a remote Tailscale node via SSH without opening an interactive session. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# SSH Exec Skill

Run a single command on a remote Tailscale node via SSH without opening an interactive session. Requires SSH access to the target (key in `~/.ssh/` or `SSH_AUTH_SOCK`) and `SSH_TARGET` env var (e.g., `100.107.204.64:8022`).

## Execute a Remote Command

Run a command on the target and return stdout/stderr:

```bash
ssh -p 8022 user@100.107.204.64 "uname -a"
```

## Execute with Custom Port

Use the `SSH_TARGET` env var:

```bash
ssh -p "${SSH_PORT:-22}" "$SSH_HOST" "df -h"
```

## Run a Script Remotely

Pipe a local script to the remote host:

```bash
ssh -p 8022 user@100.107.204.64 'bash -s' < local-script.sh
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
