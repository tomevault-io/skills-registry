---
name: xmtp-cli-debug
description: Get debug and diagnostic information from the XMTP CLI. Use when resolving address, inspecting inbox, or getting general info. Use when this capability is needed.
metadata:
  author: openclaw
---

# CLI debug

Get debug and diagnostic information: general info, resolve address to inbox ID, inspect address or inbox.

## When to apply

- Getting general CLI/client info
- Resolving an Ethereum address to an inbox ID
- Inspecting address or inbox details
- Checking installations or key package

## Rules

- `info-resolve-address-inbox` – `debug info` / `address` / `inbox` / `resolve` / `installations` / `key-package` and options

## Quick start

```bash
xmtp debug info
xmtp debug resolve --address 0x1234...
xmtp debug address --address 0x1234...
xmtp debug inbox --inbox-id abc...
```

Read `rules/info-resolve-address-inbox.md` for details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
