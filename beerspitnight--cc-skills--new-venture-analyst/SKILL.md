---
name: new-venture-analyst
description: Generates comprehensive venture viability reports, financial models, and GTM strategy critiques for new business ideas or products. Use when a user needs to vet an idea, analyze a business plan, or stress-test strategic assumptions.
metadata:
  author: beerspitnight
---

# New Venture Analyst

## Process Overview

1.  **Deconstruction**: specific data points (TAM/SAM, Pricing, CAC, Churn, Burn).
2.  **Financial Modeling**: Use `scripts/model_financials.py` to generate 12-month projections.
3.  **Strategic Analysis**: Use `references/evaluation_guide.md` to critique the business model.
4.  **Reporting**: Synthesize findings into the final Markdown report.

## Step 1: Data Extraction & Validation

First, extract the following assumptions from the user's context. If missing, estimate based on industry standards but flag as "Assumed".

*   **Growth**: Monthly growth rate (%), Starting user count
*   **Unit Economics**: ARPU (Average Revenue Per User), Churn Rate (%)
*   **Costs**: CAC (Customer Acquisition Cost), COGS (Cost of Goods Sold/Service), Fixed Monthly Costs

## Step 2: Financial Modeling

Run the financial modeling script to generate the 12-month projection table (Pessimistic, Realistic, Optimistic).

```bash
python3 scripts/model_financials.py \
  --start_users [N] \
  --growth_rate [0.XX] \
  --arpu [N] \
  --churn [0.XX] \
  --cac [N] \
  --cogs [N] \
  --fixed_costs [N]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/beerspitnight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
