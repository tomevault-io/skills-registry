---
name: hk-mortgage
description: Query Hong Kong residential mortgage statistics from the Hong Kong Monetary Authority (HKMA). Use when the user asks about mortgage rates, property loan data, housing loan statistics, mortgage approval numbers, or the state of the Hong Kong property lending market. Use when this capability is needed.
metadata:
  author: samsonllam
---

# HK Mortgage Skill

Query Hong Kong residential mortgage survey data from HKMA. No API key needed.

## API Endpoint

```bash
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/monthly-statistical-bulletin/banking/residential-mortgage-survey"
```

### With Filters
```bash
# Latest record only
curl -s "https://api.hkma.gov.hk/public/market-data-and-statistics/monthly-statistical-bulletin/banking/residential-mortgage-survey?sortby=end_of_month&sortorder=desc&pagesize=1"

# Date range
curl -s "...?choose=by_date&from=2025-01&to=2025-12&sortby=end_of_month&sortorder=desc"
```

## Response Format

```json
{
  "header": { "success": true },
  "result": {
    "records": [{
      "end_of_month": "2025-12",
      "outstanding_loans_amt": 1917499,
      "outstanding_loans_amt_mom": 0.002,
      "outstanding_loans_amt_yoy": 0.025,
      "new_loans_drawn_amt": 20010,
      "new_loans_drawn_amt_mom": 0.017,
      "new_loans_approved_amt": 31201,
      "new_loans_approved_amt_mom": 0.071,
      "new_loans_approved": 6068,
      "new_loans_approved_mom": 0.037,
      "new_loans_approved_avg_size": 5.14,
      "new_loans_approved_lv_ratio": 61,
      "delinquency_ratio": 0.14,
      "ir_new_loans_approved_hibor": 0.898,
      "ir_new_loans_approved_blr": 0.013,
      "ir_new_loans_approved_fixed": 0.048
    }]
  }
}
```

**Key Fields**:
- `outstanding_loans_amt` — Total outstanding mortgage loans (HKD million)
- `outstanding_loans_amt_mom` — Month-on-month change (decimal, e.g., 0.002 = 0.2%)
- `outstanding_loans_amt_yoy` — Year-on-year change
- `new_loans_drawn_amt` — New loans drawn down (HKD million)
- `new_loans_approved_amt` — New loans approved amount (HKD million)
- `new_loans_approved` — Number of new loan applications approved
- `new_loans_approved_avg_size` — Average loan size (HKD million)
- `new_loans_approved_lv_ratio` — Average loan-to-value ratio (%)
- `delinquency_ratio` — Mortgage delinquency ratio (%)
- `ir_new_loans_approved_hibor` — Share of new loans on HIBOR-based rates (decimal)
- `ir_new_loans_approved_blr` — Share on best lending rate (decimal)
- `ir_new_loans_approved_fixed` — Share on fixed rate (decimal)

## Query Parameters

| Param | Description | Example |
|-------|-------------|---------|
| `sortby` | Sort field | `end_of_month` |
| `sortorder` | Direction | `desc` |
| `pagesize` | Results per page | `10` |
| `choose` | Filter mode | `by_date` |
| `from` / `to` | Date range | `2025-01` / `2025-12` |

## Tips

- Amounts are in HKD millions — divide by 1000 for billions
- MoM/YoY changes are decimals (multiply by 100 for percentage)
- Data updates monthly, usually with 1-2 month lag
- Useful for property market analysis and trend tracking
- Use 📊 and trend arrows (📈📉) when presenting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samsonllam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
