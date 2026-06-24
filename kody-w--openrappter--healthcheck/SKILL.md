---
name: healthcheck
description: Run system health checks on the openrappter agent framework and connected services. Use when this capability is needed.
metadata:
  author: kody-w
---

# Healthcheck

Verify openrappter system health and connected services.

## System Check

```bash
# Check Node.js version
node --version

# Check npm packages
npm ls --depth=0

# Verify gateway connectivity
curl -s http://localhost:18790/health
```

## Service Checks

- **Gateway**: WebSocket connection test
- **Storage**: SQLite read/write test
- **Memory**: Embedding service connectivity
- **Channels**: Per-channel auth validation
- **Skills**: Installed skills integrity

## Diagnostics

```bash
# Full diagnostics
openrappter doctor

# Check config validity
openrappter config validate
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
