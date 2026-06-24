---
name: data-quality
description: Data quality frameworks and patterns using Great Expectations, Soda Core, and dbt tests. Use when this capability is needed.
metadata:
  author: cemalcici
---

# Data Quality Patterns

> **Learn to THINK in expectations, not just values.**

## ⚠️ Core Principles

### Define Expectations Before Data
- What should be true about this data?
- What would break downstream consumers?
- What anomalies indicate source issues?

### Automate Validation
- Quality checks run in CI/CD
- Failures block pipelines
- Alerts notify on degradation

---

## Great Expectations

### Basic Expectations
```python
import great_expectations as gx

context = gx.get_context()
validator = context.get_validator(batch_request=batch_request)

# Completeness
validator.expect_column_values_to_not_be_null("order_id")
validator.expect_table_row_count_to_be_between(min_value=1000)

# Uniqueness
validator.expect_column_values_to_be_unique("order_id")

# Validity
validator.expect_column_values_to_be_in_set("status", ["pending", "completed"])
validator.expect_column_values_to_be_between("amount", min_value=0)

# Consistency
validator.expect_column_pair_values_A_to_be_greater_than_B("total", "discount")
```

## Soda Core

### Check File
```yaml
# checks/orders.yml
checks for orders:
  # Row count
  - row_count > 0
  
  # Completeness
  - missing_count(order_id) = 0
  - missing_percent(customer_id) < 1%
  
  # Uniqueness
  - duplicate_count(order_id) = 0
  
  # Freshness
  - freshness(created_at) < 1d
  
  # Distribution
  - avg(total_amount) between 50 and 500
```

## dbt Tests

```yaml
models:
  - name: orders
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: amount
        tests:
          - dbt_expectations.expect_column_values_to_be_between:
              min_value: 0
```

---

## Quality Dimensions

| Dimension | Check | Tool |
|-----------|-------|------|
| Completeness | Not null, row count | GE, Soda |
| Uniqueness | Unique constraints | GE, dbt |
| Validity | Value ranges, types | GE, Soda |
| Consistency | Cross-table checks | dbt |
| Freshness | Timestamp checks | Soda, dbt |

---

## Related Skills

- For dbt tests: `dbt-patterns`
- For lineage: `data-lineage`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cemalcici) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
