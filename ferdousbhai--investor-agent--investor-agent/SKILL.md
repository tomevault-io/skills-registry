---
name: investor-agent
description: Triggers on stock/market analysis, investment research, earnings, valuations, sentiment queries. Use when this capability is needed.
metadata:
  author: ferdousbhai
---

# Investment Analysis

The **investor-agent** MCP server provides financial data tools. Tools are self-documenting - check their signatures.

## Guidelines

- Validate ticker exists before multiple API calls
- Fear/Greed indices: <25 extreme fear, >75 extreme greed
- Present analysis with context, not raw data dumps
- Highlight both opportunities and risks
- Financial data may be delayed - note data freshness when relevant

---
> Source: [ferdousbhai/investor-agent](https://github.com/ferdousbhai/investor-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
