---
name: research
description: Auto-routes between Deep Research and Perplexity based on daily quota. Use for complex research questions, fact-checking, and web-grounded queries. Use when this capability is needed.
metadata:
  author: trianglegrrl
---

# Research Skill

Auto-routes research queries between Deep Research (comprehensive, citations) and Perplexity (fast, web-grounded) based on daily quota.

## When to Use

- Complex multi-hop research questions → Deep Research
- Simple fact checks → Perplexity
- When you need citations and comprehensive analysis → Deep Research
- When you need quick web-grounded answers → Perplexity

## Usage

```bash
# Auto-route (checks quota, picks best provider)
bash scripts/research.sh "complex research question"

# Force Perplexity (save quota for important questions)
bash scripts/research.sh "simple fact check" --perplexity

# Force Deep Research (when comprehensive analysis needed)
bash scripts/research.sh "critical research topic" --deep
```

## Quota Management

- Default quota: 35 Deep Research queries/day
- Quota resets at midnight
- When quota exceeded, auto-routes to Perplexity
- Check current usage: `jq -r 'select(.provider=="deep")' .logs/research-usage.jsonl | wc -l`

## Output

Results saved to `research-results/YYYY-MM-DD/###_query_slug.md` with:
- Full research findings
- Citations with URLs
- JSON metadata file alongside

## Configuration

Quota settings in `config/research-config.json`:
```json
{
  "daily_deep_research_limit": 35,
  "default_provider": "auto"
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/trianglegrrl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
