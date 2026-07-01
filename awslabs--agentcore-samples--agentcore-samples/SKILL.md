---
name: executive-financial-briefing
description: Generates a concise executive-level financial briefing or summary suitable
metadata:
  author: awslabs
---

# Executive Financial Briefing

Produces a one-page executive financial briefing covering the most recent
quarter's performance, key KPIs, notable trends, and 3 action items.

## Prerequisites

No inputs required. Uses the most recent quarter (Q3 2025) as the primary
period, with Q2 2025 as the comparison.

## Steps

### Step 1: Gather all data in parallel

Fetch current and prior quarter P&L:

    get_financial_data(period="Q3 2025")
    get_financial_data(period="Q2 2025")
    get_kpi_benchmarks()

### Step 2: Compute the full KPI dashboard

Use python_exec to calculate all KPIs for both quarters:

```python
# Q3 2025
r3 = 4200000; c3 = 1890000; o3 = 1050000; e3 = 1260000
# Q2 2025
r2 = 3800000; c2 = 1710000; o2 = 980000;  e2 = 1110000

# KPIs
gm3   = round((r3 - c3) / r3 * 100, 1)
em3   = round(e3 / r3 * 100, 1)
op3   = round(o3 / r3 * 100, 1)
qoq3  = round((r3 - r2) / r2 * 100, 1)

gm2   = round((r2 - c2) / r2 * 100, 1)
em2   = round(e2 / r2 * 100, 1)
op2   = round(o2 / r2 * 100, 1)

def status(val, benchmark, higher_better=True):
    diff = val - benchmark
    if higher_better:
        if diff >= 0:    return "GREEN"
        if diff >= -5:   return "YELLOW"
        return "RED"
    else:
        if diff <= 0:    return "GREEN"
        if diff <= 5:    return "YELLOW"
        return "RED"

print("=== Q3 2025 EXECUTIVE DASHBOARD ===")
print(f"Revenue:          ${r3:,.0f}  (QoQ: {qoq3:+.1f}%)")
print(f"EBITDA:           ${e3:,.0f}  (margin: {em3}%)  [{status(em3,15)}]")
print(f"Gross Margin:     {gm3}%  (benchmark: 40%)  [{status(gm3,40)}]")
print(f"OpEx Ratio:       {op3}%  (benchmark: 30%)  [{status(op3,30,False)}]")
print(f"\nQoQ Changes:")
print(f"  Revenue:        {qoq3:+.1f}%")
print(f"  Gross Margin:   {gm3-gm2:+.1f}pp")
print(f"  EBITDA Margin:  {em3-em2:+.1f}pp")
print(f"  OpEx Ratio:     {op3-op2:+.1f}pp")
```

### Step 3: Compose the briefing

Write a structured executive briefing with exactly these sections:

**FINANCIAL BRIEFING — Q3 2025**
*Prepared by Financial Analyst Agent | [today's date]*

---

**Headline Numbers**
- Revenue, EBITDA, and Gross Margin in a 3-item bullet list with QoQ delta

**Performance vs Benchmarks**
- A compact table: Metric | Value | Benchmark | Status (GREEN/YELLOW/RED)

**What's Working**
- 2 bullet points on the strongest results

**Watch List**
- 1–2 bullet points on areas of concern or metrics approaching thresholds

**Recommended Actions**
- 3 numbered action items for leadership, each one sentence

Keep the entire briefing to under 400 words. Write for a time-pressured CFO
who reads this in 60 seconds.

---
> Source: [awslabs/agentcore-samples](https://github.com/awslabs/agentcore-samples) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
