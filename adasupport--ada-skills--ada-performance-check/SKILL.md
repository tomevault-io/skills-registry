---
name: ada-performance-check
description: Quick health check of Ada AI agent performance metrics including CSAT, AR rate, and conversation volume. Use when the user wants a quick status update, performance overview, trend analysis, or asks questions like "how is my bot doing" or "what are my metrics". Use when this capability is needed.
metadata:
  author: adasupport
---

# Ada Performance Health Check

## When to use this skill

Use this skill when the user wants to:
- Get a quick performance overview
- Check current metrics (CSAT, AR, volume)
- See trends over time
- Compare periods (week-over-week, month-over-month)
- Answer "how is my bot doing?" type questions

## Quick check workflow

### For immediate status

```
Use get_ada_metric with:
- Metrics: CSAT rate, AR rate, engaged conversation volume
- Date range: Last 7 days
```

Report in a clear summary format.

### For trend analysis

```
Use get_ada_metric twice:
1. Current period (e.g., last 7 days)
2. Previous period (e.g., 7-14 days ago)
```

Calculate percentage changes and identify trends.

### For deeper comparison

```
Use get_ada_metric for multiple periods:
- This week vs last week
- This month vs last month
- This quarter vs last quarter
```

## Output format

### Quick Status

```markdown
## Ada Performance Summary
**Period**: Last 7 days (Jan 20-27, 2026)

| Metric | Value | Trend |
|--------|-------|-------|
| CSAT | 78% | ↑ 2% from last week |
| Automated Resolution | 71% | ↔ Stable |
| Conversation Volume | 12,450 | ↓ 5% from last week |

**Quick Take**: CSAT improving, AR stable. Volume dip may be seasonal.
```

### Trend Analysis

```markdown
## Performance Trends

### CSAT Trend (Last 4 Weeks)
- Week 1: 74%
- Week 2: 75%
- Week 3: 76%
- Week 4: 78%
**Trend**: Steady improvement (+4% over period)

### AR Trend (Last 4 Weeks)
- Week 1: 70%
- Week 2: 71%
- Week 3: 70%
- Week 4: 71%
**Trend**: Stable (±1% fluctuation)

### Key Observations
1. CSAT consistently improving - recent changes are working
2. AR plateaued - may need new automation opportunities
3. Volume correlates with [pattern observed]
```

## Common questions this skill answers

| Question | Approach |
|----------|----------|
| "What's my CSAT this week?" | Single metric, 7-day range |
| "How is my AR trending?" | AR metric, multiple periods |
| "Compare this month to last month" | All metrics, two 30-day periods |
| "Give me a quick health check" | All metrics, 7-day with week-over-week |
| "Are things getting better or worse?" | All metrics, trend analysis |

## Tips

- Default to 7 days if no timeframe specified
- Always include comparison to previous period when possible
- Highlight significant changes (>5% movement)
- Keep the summary concise for quick checks
- Offer to dig deeper if metrics look concerning

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adasupport) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
