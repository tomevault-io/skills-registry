---
name: ssh-manager
description: Manage SSH connections, generate keys, organize config, and test remote server access. Use when this capability is needed.
metadata:
  author: openclaw
---

# SSH Manager

Manage SSH connections, config, keys, and remote server access.

## Requirements

- `ssh`, `ssh-keygen` (pre-installed on Linux/macOS)
- No API keys needed

## Instructions

1. **List connections** — Parse and display `~/.ssh/config`:
   ```bash
   grep -E '^Host |HostName |User |Port |IdentityFile ' ~/.ssh/config 2>/dev/null
   ```
   Format as a table: Alias | Hostname | User | Port | Key File

2. **Add new host** — Gather details and append to config:
   ```
   Host myserver
       HostName 192.168.1.100
       User deploy
       Port 22
       IdentityFile ~/.ssh/id_ed25519
   ```
   **Always show the block and confirm before writing.**

3. **Edit host** — Find the block, show current values, apply changes.

4. **Remove host** — Remove the `Host` block. **Always confirm before deleting.** Back up config first.

5. **Generate SSH key**:
   ```bash
   ssh-keygen -t ed25519 -C "user@hostname" -f ~/.ssh/id_ed25519_myserver
   ```
   Recommend Ed25519 over RSA. Suggest a passphrase for security.

6. **Key audit** — List keys and check permissions:
   ```bash
   ls -la ~/.ssh/id_* 2>/dev/null
   # Private keys must be 600, public keys 644
   # ~/.ssh/ directory must be 700
   ```

7. **Test connection**:
   ```bash
   ssh -o ConnectTimeout=5 -o BatchMode=yes myserver exit 2>&1 && echo "✅ OK" || echo "❌ Failed"
   ```

## Input Validation

Before writing to SSH config, validate all values:
- **Hostname**: `^[a-zA-Z0-9._-]+$` only
- **Username**: `^[a-zA-Z0-9_-]+$` only
- **Port**: numeric, 1–65535
- **Reject** any input containing shell metacharacters (`;`, `|`, `&`, `$`, `` ` ``)

## Edge Cases

- **Config doesn't exist**: Create `~/.ssh/config` with `chmod 644` and `~/.ssh/` with `chmod 700`.
- **Duplicate host alias**: Warn user — SSH uses first match. Suggest renaming.
- **ProxyJump/bastion**: Support `ProxyJump` directive for jump hosts.
- **Wildcard hosts**: Be careful with `Host *` blocks — explain they apply globally.

## Security

- **Never display private key contents** — only reference file paths.
- Back up `~/.ssh/config` to `~/.ssh/config.bak` before any modification.
- Verify permissions after changes: `chmod 700 ~/.ssh && chmod 644 ~/.ssh/config`.
- Recommend disabling password auth on servers (`PasswordAuthentication no`).
- Suggest `AddKeysToAgent yes` for convenience with passphrase-protected keys.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
