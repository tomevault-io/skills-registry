---
name: hk-finance
description: Query Hong Kong financial and monetary data from the Hong Kong Monetary Authority (HKMA). Use when the user asks about HKD exchange rates, HIBOR interest rates, interbank liquidity, aggregate balance, Hong Kong monetary statistics, or financial market data. Provides daily monetary statistics and market data. Use when this capability is needed.
metadata:
  author: samsonllam
---

# HK Finance Skill

Query Hong Kong financial data from HKMA (金管局). No API key needed.

## API Base

```
https://api.hkma.gov.hk/public/market-data-and-statistics/daily-monetary-statistics/
```

## Endpoints

### Daily Interbank Liquidity (Most Useful)
```bash
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/daily-monetary-statistics/daily-figures-interbank-liquidity"
```

**Response fields**:
- `end_of_date` — Date
- `cu_weakside` — USD/HKD Convertibility Undertaking weak-side (7.85)
- `cu_strongside` — USD/HKD strong-side (7.75)
- `disc_win_base_rate` — Discount Window Base Rate (%)
- `hibor_overnight` — HIBOR overnight rate (%)
- `hibor_fixing_1m` — HIBOR 1-month fixing (%)
- `twi` — Trade Weighted Index
- `opening_balance` — Aggregate Balance opening (HKD million)
- `closing_balance` — Aggregate Balance closing (HKD million)

### Query Parameters

| Param | Description | Example |
|-------|-------------|---------|
| `choose` | Filter mode | `by_date` |
| `from` | Start date | `2026-02-01` |
| `to` | End date | `2026-02-16` |
| `sortby` | Sort field | `end_of_date` |
| `sortorder` | Sort direction | `desc` |
| `pagesize` | Results per page | `10` |

### Example: Latest Data
```bash
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/daily-monetary-statistics/daily-figures-interbank-liquidity?sortby=end_of_date&sortorder=desc&pagesize=1"
```

### Daily Exchange Rates
```bash
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/monthly-statistical-bulletin/er-ir/er-eeri-daily?pagesize=1"
```

**Response fields**:
- `end_of_day` — Date
- `usd`, `gbp`, `jpy`, `cad`, `aud`, `sgd`, `eur`, `cny`, `krw` — Exchange rates vs HKD
- `php`, `inr`, `idr`, `zar`, `thb`, `myr`, `twd`, `chf` — More currencies
- `neeri_2020_trade_wgt` — Nominal Effective Exchange Rate Index (trade-weighted)

Note: This endpoint does not support `sortby`. Use `pagesize` and `offset` for pagination.

## Response Format

```json
{
  "header": { "success": true },
  "result": {
    "datasize": 100,
    "records": [
      {
        "end_of_date": "2026-02-16",
        "cu_weakside": 7.85,
        "cu_strongside": 7.75,
        "hibor_overnight": 2.93,
        "hibor_fixing_1m": 2.45792,
        "closing_balance": 58646
      }
    ]
  }
}
```

## Presentation Tips

- Show rates with proper decimal places (HIBOR to 5dp, exchange rates to 2dp)
- Aggregate Balance in HKD millions — convert to billions for readability
- Compare with previous day if user asks about changes
- Use 📈📉 for rate movements
- HKMA data updates daily after market close

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samsonllam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
