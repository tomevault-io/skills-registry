---
name: system-skills
description: | Use when this capability is needed.
metadata:
  author: campbellmcgregor
---

# System Skills

This skill group manages NOMAD v2.0 system operations.

## Available Commands

| Command | Description | Arguments |
|---------|-------------|-----------|
| `/status` | System health dashboard | None |
| `/export` | Export data/configuration | `[format]` |
| `/help` | Command reference | `[command]` |
| `/setup-verification` | Configure verification settings | None |
| `/verification-status` | Verification system status | None |

## Agent Integration

These commands primarily use:
- **query-handler**: System coordination
- **truth-verifier**: Verification status and metrics

## Data Sources

- `config/user-preferences.json` - Configuration status
- `data/threats-cache.json` - Cache status
- `data/verification-metrics.json` - Verification stats
- `data/feed-quality-metrics.json` - Feed health

## Trigger Patterns

These commands are auto-suggested when users:
- Ask about system status ("status", "health", "diagnostics")
- Need help ("help", "how to", "commands")
- Want to export ("export", "backup", "download")
- Check verification ("verification", "confidence", "accuracy")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/campbellmcgregor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
