---
name: gumroad-analytics
description: Pull daily Gumroad product/sales analytics safely (no raw PII persistence by default). Use when this capability is needed.
metadata:
  author: openclaw
---

# Gumroad Analytics

Collect Gumroad analytics in a privacy-conscious way.

## What this skill does

- Fetches **sales** and **products** from Gumroad API
- Produces a **daily summary JSON** (counts + revenue totals)
- **Does not store raw API payloads by default**

## Credentials

Expected file: `~/.config/gumroad/credentials.json`

Example:

```json
{
  "access_token": "YOUR_GUMROAD_ACCESS_TOKEN"
}
```

Harden permissions:

```bash
chmod 600 ~/.config/gumroad/credentials.json
```

## Run

```bash
bash skills/gumroad-analytics/scripts/fetch_metrics.sh
```

Optional raw storage (explicit opt-in):

```bash
bash skills/gumroad-analytics/scripts/fetch_metrics.sh --store-raw
```

## Output

Summary file (default):
- `memory/metrics/gumroad/YYYY-MM-DD-summary.json`

Raw files (only with `--store-raw`):
- `memory/metrics/gumroad/YYYY-MM-DD-raw-sales-redacted.json`
- `memory/metrics/gumroad/YYYY-MM-DD-raw-products.json`

## Notes

- Sales raw output is redacted before writing (`email` and buyer name fields removed).
- If you do not need raw data, avoid `--store-raw`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
