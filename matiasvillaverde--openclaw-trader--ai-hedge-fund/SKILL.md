---
name: ai-hedge-fund
description: Run virattt/ai-hedge-fund for multi-agent analysis and convert output into a risk-defined plan. Use when this capability is needed.
metadata:
  author: matiasvillaverde
---

# AI Hedge Fund

Path: `${AIHF_DIR:-$HOME/ai-hedge-fund}`

Run non-interactively:
```bash
${OPENCLAW_HOME:-$HOME/.openclaw}/workspace/skills/ai-hedge-fund/run.sh --tickers AAPL,NVDA --start-date 2025-01-01 --end-date 2025-02-01
```

Model routing:
- default model: `z-ai/glm-4.5-air` (OpenRouter)
- override with env `AIHF_MODEL=<model_name>`

Post-process requirements:
- Save findings under `trading/research/`
- Translate findings into actionable plans: long/short/call/put/spread
- Add entry, invalidation, stop logic, targets, and size
- If edge is unclear: `NO TRADE`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matiasvillaverde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
