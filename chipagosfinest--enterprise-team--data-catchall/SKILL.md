---
name: data-catchall
description: | Use when this capability is needed.
metadata:
  author: chipagosfinest
---

# Data Department

Routes data work to the appropriate specialist role.

## Routing Targets

| Role | Handles |
|---|---|
| data-engineer | ETL pipelines, data warehouses, Airflow, dbt, Spark, data infrastructure |
| data-analyst | Dashboards, BI reports, SQL queries, KPIs, metrics, data exploration |
| data-scientist | ML models, statistical analysis, predictive analytics, experiments |
| qa-engineer | Test automation, CI/CD testing, data quality validation, regression testing |

## Examples

- "Build an ETL pipeline to sync Stripe data to our warehouse" -> data-engineer
- "Create a dashboard showing monthly revenue by product" -> data-analyst
- "Train a churn prediction model on our user data" -> data-scientist
- "Set up automated data quality checks for the pipeline" -> qa-engineer
- "Migrate our data warehouse from Redshift to BigQuery" -> data-engineer
- "Analyze conversion funnel drop-off rates" -> data-analyst

## Workflow

1. Identify whether the request is about data infrastructure, analysis, modeling, or testing.
2. For requests spanning multiple areas (e.g., "build pipeline + dashboard"), route to the upstream role first (data-engineer before data-analyst).
3. For ambiguous data requests, default to data-analyst.
4. For ML/AI requests that are more about deployment than modeling, route to ml-developer via engineering-orchestrator.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chipagosfinest) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
