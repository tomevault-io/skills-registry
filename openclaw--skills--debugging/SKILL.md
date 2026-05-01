---
name: xmtp-cli-debugging
description: Enable CLI debug logging with environment variables. Use when troubleshooting or inspecting CLI behavior. Use when this capability is needed.
metadata:
  author: openclaw
---

# CLI debugging

Enable debug logging for the XMTP CLI via environment variables.

## When to apply

- Troubleshooting CLI behavior or connection issues
- Inspecting verbose logs (debug level)

## Rules

- `force-debug-env` – `XMTP_FORCE_DEBUG` and `XMTP_FORCE_DEBUG_LEVEL`

## Quick start

Set in `.env` or export:

```bash
XMTP_FORCE_DEBUG=true
XMTP_FORCE_DEBUG_LEVEL=debug
```

Read `rules/force-debug-env.md` for details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
