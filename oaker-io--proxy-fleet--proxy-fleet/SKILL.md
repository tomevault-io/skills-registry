---
name: proxy-fleet
description: > Use when this capability is needed.
metadata:
  author: oaker-io
---

# proxy-fleet

Manage multiple VPS proxy nodes from a single command line. Deploy 3x-ui + VLESS+Reality
and generate Clash/Mihomo subscription URLs that auto-sync when nodes change.

## Working Directory

```bash
cd /Users/wangzhuc/proxy-fleet
```

## Commands

All commands run from the project root:

```bash
python3 scripts/fleet.py init                              # First-time setup
python3 scripts/fleet.py status                            # Parallel health check
python3 scripts/fleet.py deploy <host> [--name X --emoji 🇯🇵]  # One-click deploy
python3 scripts/fleet.py deploy <host> --nat 10000-10009   # NAT machine
python3 scripts/fleet.py remove <host>                     # Remove from subscription
python3 scripts/fleet.py sync                              # Regenerate subscription YAML
```

## When to Use Each Command

| User says | Command |
|-----------|---------|
| "加台机器 / deploy / 部署代理" | `deploy <ssh-host>` |
| "删掉这个节点 / remove" | `remove <ssh-host>` |
| "节点状态 / 看下代理" | `status` |
| "更新规则 / 加个 AI 域名" | Edit `templates/rules/*.yaml` → `sync` |
| "刷新订阅 / 同步" | `sync` |

## Deploy Flow

```
SSH → scan ports (avoid conflicts) → pick available port
  → install 3x-ui → reset credentials via CLI
  → generate x25519 keys → create VLESS+Reality inbound via API
  → detect firewall (ufw/iptables) → open ports
  → verify connectivity → save to config.json → sync subscription
```

## Key Files

| File | Purpose |
|------|---------|
| `config.json` | Fleet state — credentials, nodes, subscription hosting (gitignored) |
| `config.example.json` | Template for new users |
| `scripts/fleet.py` | Main CLI — all operations |
| `templates/rules/ai.yaml` | AI service routing rules (OpenAI, Claude, Gemini, etc.) |
| `templates/rules/proxy.yaml` | Common proxy rules (Google, GitHub, Twitter, etc.) |
| `templates/rules/streaming.yaml` | Streaming rules (YouTube, Netflix, etc.) |
| `templates/rules/direct.yaml` | China-direct and LAN rules |

## Updating Rules

Edit the relevant file in `templates/rules/`, then run `sync` to regenerate and upload
the subscription. Users refresh in Clash Verge Rev to get changes.

## Technical Notes

These matter when debugging or extending the skill:

- **Xray v26+ key format**: `x25519` outputs `PrivateKey` / `Password` (= public key) / `Hash32`.
  Older versions use `Private key` / `Public key`. The script handles both.
- **3x-ui install** is interactive — the script installs with defaults, then resets credentials
  via the `/usr/local/x-ui/x-ui setting` CLI.
- **3x-ui API**: `POST /login` → session cookie → `/panel/api/inbounds/{add,update,del,list}`.
- **Reality returns HTTP 400** to non-VLESS clients — the connectivity check treats 400 as alive.
- **Port conflicts** are the #1 deploy failure cause — the script scans ports before configuring.
- **xray binary** path is auto-detected via glob (`/usr/local/x-ui/bin/xray-linux-*`), works
  on both amd64 and arm64.

---
> Source: [oaker-io/proxy-fleet](https://github.com/oaker-io/proxy-fleet) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
