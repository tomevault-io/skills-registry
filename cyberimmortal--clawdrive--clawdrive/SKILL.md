---
name: clawdrive
description: This strategy runs as an HTTP server. All interaction goes through HTTP. Use when this capability is needed.
metadata:
  author: CyberImmortal
---
# {{name}} Trading Strategy

{{description}}

## HTTP API

This strategy runs as an HTTP server. All interaction goes through HTTP.

| Action | Method | URL |
|--------|--------|-----|
| Strategy info | GET | `http://localhost:{{port}}/info` |
| Stats & P&L | GET | `http://localhost:{{port}}/stats` |
| Trade history | GET | `http://localhost:{{port}}/trades?days=7` |
| Health check | GET | `http://localhost:{{port}}/health` |
| Stop trading | POST | `http://localhost:{{port}}/stop` |
| Resume trading | POST | `http://localhost:{{port}}/start` |

## CLI

```bash
# Activate venv and start:
CLAWDRIVE_ROOT=/path/to/clawdrive python server.py --port {{port}}
```

## Backtest

```bash
python -m agent.trading.backtest \
    --strategy-dir {{strategy_dir}} \
    --days 30 --pair {{pair_symbol}} --interval 5m
```

---
> Source: [CyberImmortal/clawdrive](https://github.com/CyberImmortal/clawdrive) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
