---
name: pipeline-intelligence
description: Smart pipeline analytics that learns from deal outcomes over time — identify patterns, predict risks, and surface coaching opportunities from pipeline data. Use this skill whenever a manager wants pipeline insights beyond basic reporting, when analyzing conversion rates, deal velocity, or stage progression, when someone says "why are we losing deals at stage X", "what's wrong with our pipeline", "pipeline trends", or when building data-driven coaching strategies. Also trigger when someone mentions pipeline health, deal velocity, conversion analysis, or RevOps analytics. This is the layer that turns pipeline data into actionable intelligence. Use when this capability is needed.
metadata:
  author: jbalbu01
---

# Pipeline Intelligence

Go beyond pipeline reporting into pipeline understanding. Traditional dashboards show you what's happening — this skill tells you why and what to do about it. It learns from your deal outcomes over time, so insights get sharper with each quarter.

## What Makes This Different from Reports

A CRM report says: "Your win rate is 22%."
Pipeline Intelligence says: "Your win rate is 22%, down from 28% last quarter. The drop is concentrated in deals over $100K where you're competing against CompX. Reps who do multi-threaded discovery have a 35% win rate in the same segment. Here are 3 deals in your current pipeline that match the loss pattern — take action this week."

---

## How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                 PIPELINE INTELLIGENCE                              │
├─────────────────────────────────────────────────────────────────┤
│  ANALYSES                                                         │
│  1. Pipeline Health — Overall assessment with risk flags         │
│  2. Conversion Analysis — Where and why deals stall/drop        │
│  3. Velocity Analysis — What speeds up or slows down deals      │
│  4. Pattern Detection — Correlations between behaviors & wins   │
│  5. Risk Prediction — Which current deals match loss patterns   │
│  6. Coaching Signals — Data-driven coaching priorities           │
├─────────────────────────────────────────────────────────────────┤
│  LEARNING                                                         │
│  • Updates deal-patterns.md with each analysis                   │
│  • Predictions improve as more outcomes are logged               │
│  • Compares predictions to actuals to calibrate confidence       │
└─────────────────────────────────────────────────────────────────┘
```

---

## Getting Started

- "Analyze our pipeline health" (with CSV upload or CRM connection)
- "Why are deals stalling at the demo stage?"
- "Which deals in my pipeline are most at risk?"
- "What separates our top reps from average performers?"
- "Give me a weekly pipeline intelligence briefing"

---

## Output Format: Pipeline Intelligence Report

```markdown
# Pipeline Intelligence Report

**Period:** [Date range]
**Deals Analyzed:** [N]
**Total Pipeline Value:** $[X]

---

## Health Summary

| Metric | Current | Last Period | Trend | Benchmark |
|--------|---------|------------|-------|-----------|
| Win Rate | [X]% | [X]% | ↑↓→ | [Industry avg] |
| Avg Deal Size | $[X] | $[X] | ↑↓→ | |
| Avg Cycle Length | [X] days | [X] days | ↑↓→ | |
| Pipeline Coverage | [X]x | [X]x | ↑↓→ | 3-4x target |
| Stage Conversion (Overall) | [X]% | [X]% | ↑↓→ | |

**Overall Assessment:** [2-3 sentences on pipeline health with the most important insight]

---

## Conversion Funnel

| Stage | Deals | Value | Conversion | Avg Days | Bottleneck? |
|-------|-------|-------|-----------|----------|-------------|
| Qualified | [N] | $[X] | — | — | |
| Discovery | [N] | $[X] | [X]% | [X] | [Yes/No] |
| Demo | [N] | $[X] | [X]% | [X] | [Yes/No] |
| Proposal | [N] | $[X] | [X]% | [X] | [Yes/No] |
| Negotiation | [N] | $[X] | [X]% | [X] | [Yes/No] |
| Closed Won | [N] | $[X] | [X]% | [X] | |

**Biggest Leak:** [Stage] — [X]% of deals and $[X] in value die here
**Root Cause Hypothesis:** [Based on deal patterns and outcomes]
**Recommended Action:** [Specific intervention]

---

## Risk Alerts

Deals that match historical loss patterns:

| Deal | Size | Risk Signal | Confidence | Action |
|------|------|------------|------------|--------|
| [Deal A] | $[X] | [Matches loss pattern: no champion] | [X]% | [What to do] |
| [Deal B] | $[X] | [Risk signal] | [X]% | [Action] |

---

## Pattern Insights

### What Winners Do Differently
1. [Behavior] — Found in [X]% of wins vs [Y]% of losses
2. [Behavior] — [Evidence]
3. [Behavior] — [Evidence]

### Emerging Trends
- [New pattern not previously tracked — flagged for monitoring]

---

## Coaching Priorities

Based on pipeline data, these are the highest-leverage coaching investments:

| Rep | Issue | Evidence | Suggested Coaching |
|-----|-------|---------|-------------------|
| [Rep A] | [Pattern] | [Data] | [Specific coaching conversation] |
| [Rep B] | [Pattern] | [Data] | [Coaching] |
```

---

## Learning Over Time

Pipeline Intelligence improves by comparing predictions to actual outcomes:

1. **Predict:** Flag deals at risk based on current patterns
2. **Track:** Record which flagged deals actually close or lose
3. **Calibrate:** Adjust pattern confidence based on prediction accuracy
4. **Improve:** Refine which signals actually predict outcomes

This creates a flywheel: the more deals flow through the system, the better it gets at identifying risk and opportunity.

---

## Related Skills

- **deal-qualification** → Qualification scores feed pipeline intelligence
- **win-loss-analysis** → Deal outcomes update pattern library
- **sales-coaching** → Pipeline insights drive coaching priorities
- **rep-profile** → Individual patterns tracked per rep
- **gtm-memory** → All patterns stored in deal-patterns.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbalbu01) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
