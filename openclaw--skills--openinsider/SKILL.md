---
name: openinsider
description: Fetch SEC Form 4 insider trading data (Directors, CEOs, Officers) from OpenInsider. Use this to track corporate insider buying/selling signals. Use when this capability is needed.
metadata:
  author: openclaw
---

# OpenInsider Skill

Fetch real-time insider trading data (SEC Form 4) from OpenInsider.com.

## Usage

This skill uses a Python script to scrape and parse the OpenInsider data table.

### Get Insider Trades
Get the latest transactions for a specific ticker.

```bash
skills/openinsider/scripts/fetch_trades.py NVDA
```

### Options
- `--limit <n>`: Limit number of results (default 10)

```bash
skills/openinsider/scripts/fetch_trades.py TSLA --limit 5
```

## Output Fields

- `filing_date`: When the Form 4 was filed
- `trade_date`: When the trade happened
- `insider_name`: Name of the executive/director
- `title`: Role (CEO, CFO, Dir, etc.)
- `trade_type`: Purchase (P) or Sale (S)
- `price`: Transaction price
- `qty`: Number of shares traded
- `value`: Total value of the trade

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
