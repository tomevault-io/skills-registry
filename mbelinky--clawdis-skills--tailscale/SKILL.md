---
name: tailscale
description: Manage Tailscale VPN, Serve, and Funnel from the CLI. Use when this capability is needed.
metadata:
  author: mbelinky
---

# Tailscale CLI

Use `tailscale` to inspect status, fetch tailnet IPs, and expose services.

Quick start
- `tailscale status`
- `tailscale ip -4`
- `tailscale serve 3000`
- `tailscale funnel 3000`

Notes
- `serve` shares a local service inside your tailnet; `funnel` exposes it publicly.
- Serve/Funnel CLI changed in Tailscale 1.52+; use `--help` for exact flags.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbelinky) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
