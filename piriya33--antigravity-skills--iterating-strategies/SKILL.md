---
name: iterating-strategies
description: name: iterating-strategies Use when this capability is needed.
metadata:
  author: piriya33
---
---
name: iterating-strategies
description: Track strategy versions, log changes, and prevent regression during Pine Script development.
---

# Strategy Iteration Framework

## Triggering Contexts

- User is modifying an existing strategy
- User asks "which version was better?"
- User wants to compare before/after changes
- After `analyzing-backtests` recommends improvements

## Purpose

Prevent the common trap of:

1. Making too many changes at once
2. Forgetting what changed between versions
3. Losing track of which version performed best
4. "Kitchen sink" syndrome (adding endless filters)

---

## Core Workflow

### 1. Before Any Change

Ask the user:

- "What is the current version number?"
- "Do you have baseline metrics for this version?"

If no baseline exists, prompt to run backtest first.

### 2. One Change at a Time

ENFORCE the discipline:

```
❌ BAD: "Add RSI filter, change SL to trailing, and use ATR for sizing"
✅ GOOD: "Add RSI > 50 filter for long entries"
```

If user requests multiple changes, respond:
> "I see 3 changes. Let's implement them one at a time so we can measure
> each impact. Which should we start with?"

### 3. Document Every Version

After implementing a change, prompt user to record:

```markdown
### v[X.Y] - [Brief Description]
**Date:** [Date]
**Change:** [What was modified]
**Hypothesis:** [Why this should improve performance]

**Backtest Results:**
- Net Profit: $[X] ([X]%)
- Profit Factor: [X]
- Max Drawdown: [X]%
- Total Trades: [X]
- Win Rate: [X]%

**Verdict:** ✅ KEEP / ❌ REVERT / ⚠️ NEEDS MORE TESTING
**Notes:** [Observations]
```

### 4. Compare Versions

When comparing, create a table:

| Metric | v1.0 | v1.1 | v1.2 | Best |
|--------|------|------|------|------|
| Net Profit | $X | $Y | $Z | vX.X |
| Profit Factor | X | Y | Z | vX.X |
| Max Drawdown | X% | Y% | Z% | vX.X |
| Win Rate | X% | Y% | Z% | vX.X |

---

## Changelog Template

Suggest users maintain this file alongside their strategy:

```markdown
# [Strategy Name] Changelog

## Current Best: v[X.X]

---

### v1.0 - Baseline
**Date:** YYYY-MM-DD
**Description:** Initial version with EMA crossover

**Results:**
- PF: 1.2 | DD: 18% | Trades: 89 | WR: 45%

---

### v1.1 - Added ADX filter
**Date:** YYYY-MM-DD
**Change:** Only trade when ADX > 25
**Hypothesis:** Reduce noise in ranging markets

**Results:**
- PF: 1.5 | DD: 12% | Trades: 52 | WR: 48%

**Verdict:** ✅ KEEP - Better PF, lower DD
**Trade-off:** Fewer trades (acceptable)

---

### v1.2 - Tightened stops
**Date:** YYYY-MM-DD
**Change:** SL from 2% → 1.5%
**Hypothesis:** Reduce average loss

**Results:**
- PF: 1.3 | DD: 15% | Trades: 52 | WR: 42%

**Verdict:** ❌ REVERT - Higher DD, lower WR
**Learning:** Stop was too tight, got stopped out of winners

---
```

---

## Red Flags to Call Out

### Too Many Parameters

If strategy has > 5 optimizable parameters, warn:
> "This strategy has [N] parameters. Each parameter increases overfitting risk.
> Consider simplifying."

### "Improvement" That Reduces Trades by > 50%
>
> "This change reduced trades from 100 → 40. The new results may not be
> statistically significant. Consider testing on more data."

### Contradictory Changes

If user reverts a change but then adds similar logic:
> "Note: You reverted the RSI filter in v1.3 but are now adding a Stochastic
> filter. These serve similar purposes. Are you sure?"

---

## Integration Points

- **Before iteration:** Use `planning-trading-systems` if strategy needs redesign
- **During iteration:** Use `coding-pinescript` for implementation
- **After each version:** Use `analyzing-backtests` to assess results
- **Log everything:** Use this skill's changelog template

---

## Quick Commands

When user asks:

- "What changed?" → Show last N changelog entries
- "Which version is best?" → Compare metrics across versions
- "Should I revert?" → Compare current vs previous version metrics
- "How many changes have I made?" → Count versions, warn if > 5 untested

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/piriya33) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
