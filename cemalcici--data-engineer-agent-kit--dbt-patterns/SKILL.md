---
name: dbt-patterns
description: dbt development patterns for model design, testing, documentation, and production deployment. Use when this capability is needed.
metadata:
  author: cemalcici
---

# dbt Patterns

> **Learn to THINK in layers, not just SQL files.**

## ⚠️ Core Principles

### Staging → Intermediate → Marts
- **Staging**: 1:1 with sources, rename, recast
- **Intermediate**: Business logic, complex joins
- **Marts**: Final fact/dimension tables

### Test Everything
- Every model needs tests
- Sources need freshness checks
- Custom tests for business rules

---

## Model Organization

```
models/
├── staging/
│   ├── stg_source_a/
│   │   ├── _stg_source_a__models.yml
│   │   ├── stg_source_a__orders.sql
│   │   └── stg_source_a__customers.sql
├── intermediate/
│   └── int_orders_enriched.sql
└── marts/
    ├── core/
    │   ├── dim_customers.sql
    │   └── fct_orders.sql
    └── marketing/
        └── agg_customer_ltv.sql
```

## Common Patterns

### Staging Model
{% raw %}
```sql
-- stg_source_a__orders.sql
with source as (
    select * from {{ source('source_a', 'orders') }}
),

renamed as (
    select
        id as order_id,
        customer_id,
        cast(order_date as date) as order_date,
        cast(total_amount as decimal(10,2)) as total_amount,
        status
    from source
)

select * from renamed
```
{% endraw %}

### Incremental Model
{% raw %}
```sql
-- fct_orders.sql
{{
    config(
        materialized='incremental',
        unique_key='order_id',
        on_schema_change='append_new_columns'
    )
}}

select
    order_id,
    customer_id,
    order_date,
    total_amount,
    current_timestamp() as _loaded_at
from {{ ref('stg_source_a__orders') }}

{% if is_incremental() %}
where order_date > (select max(order_date) from {{ this }})
{% endif %}
```
{% endraw %}

### Testing (schema.yml)
```yaml
models:
  - name: fct_orders
    description: "Order fact table"
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: customer_id
        tests:
          - not_null
          - relationships:
              to: ref('dim_customers')
              field: customer_id
      - name: total_amount
        tests:
          - not_null
          - dbt_expectations.expect_column_values_to_be_between:
              min_value: 0
```

---

## Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| Logic in staging | Keep staging simple, move logic to intermediate |
| No tests | Add unique, not_null, relationships |
| Undocumented models | Add descriptions to schema.yml |

---

## Related Skills

- For Spark: `spark-patterns`
- For Airflow: `airflow-patterns`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
