---
name: financial-skills
description: Financial analysis and trading skills for Claude Code Use when this capability is needed.
metadata:
  author: neversight
---

# Financial Skills

Collection of financial analysis and trading skills.

## Skills

### trading-agents

Multi-agent stock analysis workflow with 4 phases: Analysis → Research Debate → Trading → Risk.

**Usage:** `/trading-agents NVDA` or `/trading-agents AAPL analysis`

**Phases:**
- Analysis: news, market, fundamentals, social analysts (parallel)
- Research: bull/bear researchers + manager
- Trading: trader proposal
- Risk: risk debate + manager → final decision

**Sub-skills:** `news-analyst`, `market-analyst`, `fundamentals-analyst`, `social-analyst`, `bull-researcher`, `bear-researcher`, `research-manager`, `trader`, `risk-debate`, `risk-manager`

**Output:** `report/<TICKER>/<DATE>/<RANK>/*.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
