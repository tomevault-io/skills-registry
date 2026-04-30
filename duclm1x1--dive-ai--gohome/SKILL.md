---
name: gohome
description: Use when Moltbot needs to test or operate GoHome via gRPC discovery, metrics, and Grafana.
metadata:
  author: duclm1x1
---

# GoHome Skill

## Quick start

```bash
export GOHOME_HTTP_BASE="http://gohome:8080"
export GOHOME_GRPC_ADDR="gohome:9000"
```

## CLI

```bash
gohome-cli services
```

## Discovery flow (read-only)

1) List plugins.
2) Describe a plugin.
3) List RPC methods.
4) Call a read-only RPC.

## Metrics validation

```bash
curl -s "${GOHOME_HTTP_BASE}/gohome/metrics" | rg -n "gohome_"
```

## Stateful actions

Only call write RPCs after explicit user approval.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
