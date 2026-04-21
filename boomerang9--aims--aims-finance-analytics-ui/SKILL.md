---
name: aims-finance-analytics-ui
description: > Use when this capability is needed.
metadata:
  author: boomerang9
---

# A.I.M.S. Finance & Analytics UI Skill

This skill standardizes finance and analytics views: LUC estimators, cost dashboards, token usage, and revenue metrics.

Use with `aims-global-ui`.

## When to Use

Activate when:

- Editing `app/finance/**`, `app/luc/**`, `app/analytics/**`.
- The user mentions: "LUC dashboard", "cost view", "analytics".

---

## Layout

- KPI Row at Top:
  - Cards for key metrics (LUC total, monthly cost, tokens used, etc.).
- Main Chart Area:
  - Primary graph (time-series cost, usage).
- Secondary Area:
  - Breakdown tables (by Plug, by Boomer_Ang, by client).

Controls:

- Date-range selector (e.g., 7d / 30d / 90d / custom).
- Filter by Plug, Boomer_Ang, or environment.

---

## Rules

- Format numbers correctly (currency, tokens, percentages).
- Keep the dashboard legible on mobile (stack KPIs and charts).
- Highlight trends and anomalies clearly (arrows, color coding).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boomerang9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
