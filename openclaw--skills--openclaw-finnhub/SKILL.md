---
name: openclaw-finnhub
description: OpenClaw skill for real-time stock quote, and financials via Finnhub API. Use when this capability is needed.
metadata:
  author: openclaw
---
# OpenClaw-Finnhub
OpenClaw skill for real-time stock quote, and financials via Finnhub API.

## Requirements
- Python 3.11+
- Finnhub pip package `pip3 install finnhub-python`
- Finnhub API Key ([Get in website](https://finnhub.io)), Set in environment `finnhub_api_key`

## Script usage & features
**Script path: `./scripts/app.py`**

### 1. Get real-time quote data for US stocks
Example: `python3 ./scripts/app.py 1 NVDA`
Result: `Current price: $185.61, Change: $-5.52(-2.8881%), Highest price: $190.3, Lower price: $184.88`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
