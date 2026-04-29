---
name: openclaw-cost-auditor
description: Parse logs, query API metrics, forecast bills, optimize spend with reports & alerts. Use when this capability is needed.
metadata:
  author: openclaw
---

# OpenClaw Cost Auditor v1.0.0

## 🎯 Purpose
- Daily/weekly cost reports
- Top models/users by tokens
- Cost per query forecasts
- Optimization recs (quantize, prune)

## 🚀 Quick Start
```
!openclaw-cost-auditor --period last7d --format pdf
```

## Files
- `scripts/audit.py`: Log parser & calculator
- `templates/report.md`: Cost dashboard template

## Integrations
OpenClaw logs, Grok/xAI API, custom providers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
