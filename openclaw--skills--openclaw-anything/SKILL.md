---
name: openclaw-anything
description: description: OpenClaw CLI wrapper — gateway, channels, models, agents, nodes, browser, memory, security, automation. Use when this capability is needed.
metadata:
  author: openclaw
---
﻿---
name: openclaw
description: OpenClaw CLI wrapper — gateway, channels, models, agents, nodes, browser, memory, security, automation.
---

# OpenClaw Skill

CLI wrapper + docs companion. Does NOT contain OpenClaw runtime source.
Wraps `openclaw` CLI and provides local reference docs aligned to `https://docs.openclaw.ai`.

## Prerequisites
- `openclaw` CLI in `PATH` (required)
- Node.js (install/update flows), Playwright deps (browser), Tailscale (remote nodes) — optional

## Quick Reference

| Need | File |
|------|------|
| Find a command | `references/cli-full.md` → search by keyword |
| Security rules | `references/security-policy.md` |
| Config syntax | `references/config-schema.md` |
| Deploy/update | `references/deployment.md` |
| Platform notes | `references/nodes-platforms.md` |
| Doc links | `references/hubs.md` |

## Global Flags
`--dev` `--profile <name>` `--no-color` `--json` `-V`

## Security Model
Default: least privilege. High-risk ops require explicit per-action approval.

### Low-risk (default)
Status, list, health, doctor, logs, config read, docs search, memory search.

### High-risk (require `OPENCLAW_WRAPPER_ALLOW_RISKY=1`)
Shell exec · nodes invoke/run/camera/screen/location · browser automation · cron mutate · plugin/hook install · device pairing · secrets apply · sandbox recreate · webhooks · dns setup.

Wrapper: `bash scripts/openclaw.sh <command> [args]`
Granular gating: plugin gates only install/enable, secrets gates only apply, sandbox gates only recreate.

## Wrapper Command Routes
```
LOW-RISK (pass-through):
  install setup doctor status reset version tui dashboard
  update uninstall health logs configure completion config docs qr
  channel model agent agents message sessions memory skills
  security approvals system directory acp gateway service

HIGH-RISK (OPENCLAW_WRAPPER_ALLOW_RISKY=1):
  cron browser webhooks dns nodes node devices pairing prose
  plugin (install|enable only)
  hooks (install|enable only)
  secrets (apply only)
  sandbox (recreate only)
```

## Non-goals
- Not the OpenClaw runtime source
- Does not provision system packages
- Does not manage networking/VPN
- Does not authorize autonomous privileged execution

---
Last normalization: 2026-02-27 · Source: `https://docs.openclaw.ai`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
