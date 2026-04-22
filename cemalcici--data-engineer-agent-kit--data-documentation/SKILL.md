---
name: data-documentation
description: Data documentation templates and best practices. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Data Documentation

> **Learn to THINK in users, not just developers.**

## ⚠️ Core Principles

### Living Documentation
- Tied to code
- Auto-generated where possible
- Version controlled

### User-Focused
- Written for consumers
- Clear examples
- Quick reference

---

## Common Templates

### Dataset README
```markdown
# Dataset: customer_orders

## Overview
Daily aggregation of customer orders from the e-commerce platform.

## Schema
| Column | Type | Description |
|--------|------|-------------|
| order_id | STRING | Unique order identifier |
| customer_id | STRING | Customer reference |
| order_date | DATE | Order creation date |
| total_amount | DECIMAL(12,2) | Order total (USD) |

## Access
- **Location**: `iceberg.curated.customer_orders`
- **Format**: Iceberg (Parquet)
- **Partitioned by**: `order_date`

## Freshness
- **Update frequency**: Daily at 6 AM UTC
- **SLA**: < 2 hours from source

## Usage Example
```sql
SELECT 
    DATE_TRUNC('month', order_date) as month,
    SUM(total_amount) as revenue
FROM iceberg.curated.customer_orders
WHERE order_date >= DATE '2024-01-01'
GROUP BY 1;
```

## Owner
- **Team**: Data Platform
- **Contact**: data-platform@company.com
```

### Pipeline README
```markdown
# Pipeline: daily-revenue-etl

## Purpose
Calculate daily revenue metrics from raw order data.

## Architecture
```
raw.orders → stg_orders → fct_orders → daily_revenue
```

## Schedule
- **Frequency**: Daily
- **Time**: 5:00 AM UTC
- **Depends on**: `raw-ingestion-complete`

## Configuration
| Parameter | Default | Description |
|-----------|---------|-------------|
| lookback_days | 7 | Days to reprocess |
| parallelism | 4 | Spark executors |

## Monitoring
- **Airflow DAG**: `daily_revenue_etl`
- **Alerts**: #data-alerts Slack channel

## Runbook
See [runbook.md](./runbook.md) for operational procedures.
```

### dbt Model Documentation
```yaml
# schema.yml
version: 2

models:
  - name: fct_orders
    description: |
      Fact table containing all order transactions.
      Grain: One row per order.
    columns:
      - name: order_id
        description: Primary key. Unique identifier for each order.
        tests:
          - unique
          - not_null
      - name: customer_id
        description: Foreign key to dim_customers.
      - name: order_amount
        description: Total order value in USD, before discounts.
```

---

## Automation

### Generate from Code
```python
# Auto-generate schema docs
from pyspark.sql import DataFrame

def generate_schema_docs(df: DataFrame, name: str) -> str:
    docs = f"# {name}\n\n| Column | Type | Nullable |\n|--------|------|----------|\n"
    for field in df.schema.fields:
        docs += f"| {field.name} | {field.dataType} | {field.nullable} |\n"
    return docs
```

---

## Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| No documentation | Start with template |
| Stale docs | Auto-generate from code |
| Developer-only focus | Include business context |

---

## Related Skills

- For dbt: `dbt-patterns`
- For governance: `data-governance`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
