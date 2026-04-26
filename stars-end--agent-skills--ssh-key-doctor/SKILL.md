---
name: ssh-key-doctor
description: | Use when this capability is needed.
metadata:
  author: stars-end
---

# SSH Key Doctor

> **⚠️ DEPRECATED for canonical VM access (DX V8.3+)**
> All SSH access between canonical VMs should use **Tailscale SSH** instead of SSH keys.
> See [CANONICAL_TARGETS.md](/docs/CANONICAL_TARGETS.md) for Tailscale SSH standard.

This skill checks local SSH setup and (optionally) remote reachability for **legacy SSH key scenarios**.

## Tailscale SSH (Preferred for Canonical VMs)

```bash
# Enable Tailscale SSH (one-time, requires sudo)
sudo tailscale up --ssh

# Connect to canonical VMs
tailscale ssh fengning@macmini "command"
ssh fengning@100.117.177.18 "command"
```

## Legacy SSH Key Usage

Only use this skill for non-Tailscale SSH scenarios (external servers, GitHub, etc.):

```bash
~/agent-skills/health/ssh-key-doctor/check.sh --local-only
DX_SSH_DOCTOR_REMOTE=1 ~/agent-skills/health/ssh-key-doctor/check.sh --remote-only
~/agent-skills/health/ssh-key-doctor/check.sh --strict
```

## Notes

- Never prints private key material.
- Uses `BatchMode=yes` + short timeouts to avoid hangs.
- GitHub SSH check is opt-in: `--check-github` (skips if `timeout` is unavailable).

## Canonical VMs (Tailscale Addresses)

| Host | Tailscale Address | SSH User |
|------|-------------------|----------|
| homedesktop-wsl | `100.109.231.123` | `fengning@` |
| macmini | `100.117.177.18` | `fengning@` |
| epyc6 | `100.101.113.91` | `feng@` |

## Usage

```bash
~/agent-skills/ssh-key-doctor/check.sh --local-only
DX_SSH_DOCTOR_REMOTE=1 ~/agent-skills/ssh-key-doctor/check.sh --remote-only
~/agent-skills/ssh-key-doctor/check.sh --strict
```

## Notes

- Never prints private key material.
- Uses `BatchMode=yes` + short timeouts to avoid hangs.
- GitHub SSH check is opt-in: `--check-github` (skips if `timeout` is unavailable).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stars-end) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
