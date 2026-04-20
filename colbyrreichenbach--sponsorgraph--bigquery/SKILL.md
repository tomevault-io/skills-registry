---
name: bigquery-expert
description: BigQuery query optimization, schema design, partitioning, cost control. Use when writing SQL, designing tables, or optimizing query performance. Use when this capability is needed.
metadata:
  author: colbyrreichenbach
---

# BigQuery Expert Skill

## When to Use This Skill

Activate this skill when:
- Writing BigQuery SQL queries
- Designing table schemas
- Optimizing query performance
- Estimating query costs
- Implementing partitioning/clustering
- Troubleshooting query errors

## Core Patterns

### 1. Table Partitioning (MANDATORY for large tables)

```sql
-- Good: Partitioned by date, clustered by common filters
CREATE TABLE `mart.fact_conversions` (
    conversion_id STRING NOT NULL,
    creator_id STRING NOT NULL,
    video_id STRING NOT NULL,
    conversion_date DATE NOT NULL,
    revenue_usd FLOAT64,
    platform STRING,
    attribution_model STRING
)
PARTITION BY conversion_date
CLUSTER BY creator_id, platform;

-- Bad: No partitioning (expensive scans)
CREATE TABLE `mart.fact_conversions` (
    conversion_id STRING,
    ...
);
```

**Why**: Partitioning reduces query costs by 10-100x. A non-partitioned 1TB table costs $5 per scan. Partitioned by date, scanning 1 day = $0.01.

### 2. Cost-Safe Query Pattern

```python
from google.cloud import bigquery

def safe_query(sql: str, max_gb: float = 1.0) -> pd.DataFrame:
    """
    Dry-run query before executing to prevent expensive mistakes
    """
    client = bigquery.Client()
    
    # Dry run to estimate cost
    job_config = bigquery.QueryJobConfig(dry_run=True, use_query_cache=False)
    job = client.query(sql, job_config=job_config)
    
    bytes_processed = job.total_bytes_processed
    gb_processed = bytes_processed / 1e9
    cost_usd = bytes_processed / 1e12 * 5  # $5 per TB
    
    if gb_processed > max_gb:
        raise ValueError(
            f"Query would scan {gb_processed:.2f}GB (${cost_usd:.4f}). "
            f"Limit: {max_gb}GB. Optimize or increase limit."
        )
    
    # Actually run query
    return client.query(sql).to_dataframe()
```

**Always use this wrapper** for ad-hoc queries to avoid surprise costs.

### 3. Efficient Joins

```sql
-- Good: Filter before joining (reduces shuffle)
WITH recent_videos AS (
    SELECT video_id, creator_id, views
    FROM `raw.youtube_videos`
    WHERE published_date >= '2024-01-01'  -- Partition filter
),
active_creators AS (
    SELECT creator_id, name
    FROM `mart.dim_creator`
    WHERE status = 'active'
)
SELECT 
    c.name,
    v.video_id,
    v.views
FROM active_creators c
INNER JOIN recent_videos v
    ON c.creator_id = v.creator_id;

-- Bad: Join full tables then filter
SELECT 
    c.name,
    v.video_id,
    v.views
FROM `mart.dim_creator` c
INNER JOIN `raw.youtube_videos` v
    ON c.creator_id = v.creator_id
WHERE v.published_date >= '2024-01-01'
  AND c.status = 'active';
```

**Why**: Filtering early reduces data shuffled in join. Can improve performance 5-10x.

### 4. Window Functions for Attribution

```sql
-- Good: Window functions (efficient)
SELECT 
    conversion_id,
    touchpoint_id,
    touchpoint_timestamp,
    ROW_NUMBER() OVER (
        PARTITION BY conversion_id 
        ORDER BY touchpoint_timestamp
    ) as position,
    COUNT(*) OVER (PARTITION BY conversion_id) as total_touchpoints
FROM touchpoints;

-- Bad: Self-joins (slow and expensive)
SELECT 
    t1.conversion_id,
    t1.touchpoint_id,
    COUNT(t2.touchpoint_id) as position
FROM touchpoints t1
LEFT JOIN touchpoints t2 
    ON t1.conversion_id = t2.conversion_id
    AND t2.touchpoint_timestamp <= t1.touchpoint_timestamp
GROUP BY t1.conversion_id, t1.touchpoint_id;
```

**Why**: Window functions are optimized by BigQuery. Self-joins create Cartesian products and require shuffling.

### 5. Array Aggregation

```sql
-- Good: Use ARRAYs for multi-touch journeys
SELECT 
    conversion_id,
    ARRAY_AGG(
        STRUCT(
            video_id,
            view_timestamp,
            platform
        )
        ORDER BY view_timestamp
    ) as journey
FROM touchpoints
GROUP BY conversion_id;

-- Access array elements in another query
SELECT 
    conversion_id,
    journey[OFFSET(0)].video_id as first_touch,
    journey[OFFSET(ARRAY_LENGTH(journey) - 1)].video_id as last_touch
FROM customer_journeys;
```

**Why**: ARRAYs are native BigQuery types and very efficient for representing hierarchical data.

## Common Errors & Solutions

### Error: "Resources exceeded during query execution"

**Cause**: Query too large (usually Cartesian product)

**Solutions**:
1. Add WHERE clause to filter early
2. Use partition filters: `WHERE date >= '2024-01-01'`
3. Replace self-joins with window functions
4. Break into smaller queries with temp tables

```sql
-- Instead of massive join, use temp tables
CREATE TEMP TABLE filtered_data AS
SELECT * FROM huge_table WHERE date = CURRENT_DATE();

SELECT * FROM filtered_data WHERE condition = TRUE;
```

### Error: "Query exceeded resource limits during execution"

**Cause**: Not using partition filter

**Solution**: ALWAYS filter on partition column

```sql
-- Add partition filter
WHERE partition_date >= '2024-01-01'
```

### Error: "Syntax error: Expected end of input but got keyword"

**Common causes**:
- Missing comma in SELECT list
- Reserved keyword as column name (wrap in backticks)
- Incorrect quote type (use ` for identifiers, ' for strings)

```sql
-- Good
SELECT `order` FROM table;  -- 'order' is reserved

-- Bad  
SELECT order FROM table;
```

## BigQuery-Specific SQL

### QUALIFY for Window Function Filtering

```sql
-- Good: QUALIFY filters window function results
SELECT 
    creator_id,
    video_id,
    views,
    ROW_NUMBER() OVER (PARTITION BY creator_id ORDER BY views DESC) as rank
FROM videos
QUALIFY rank <= 10;  -- Top 10 videos per creator

-- Bad: Subquery wrapper
SELECT * FROM (
    SELECT 
        creator_id,
        video_id,
        views,
        ROW_NUMBER() OVER (PARTITION BY creator_id ORDER BY views DESC) as rank
    FROM videos
)
WHERE rank <= 10;
```

### UNNEST for Array Processing

```sql
-- Explode journey array into rows
SELECT 
    conversion_id,
    touchpoint.video_id,
    touchpoint.timestamp
FROM customer_journeys,
UNNEST(journey) as touchpoint;
```

### PIVOT for Wide Tables

```sql
-- Convert long to wide format
SELECT * FROM (
    SELECT creator_id, platform, revenue
    FROM revenues
)
PIVOT (
    SUM(revenue) FOR platform IN ('youtube', 'instagram', 'tiktok')
);
```

## Performance Optimization Checklist

Before finalizing any query:

- [ ] Partitioned table uses partition filter
- [ ] Clustered table filters on cluster columns
- [ ] CTEs filter before joins
- [ ] No SELECT * (specify columns)
- [ ] Window functions instead of self-joins
- [ ] No Cartesian products
- [ ] Dry-run shows acceptable bytes scanned
- [ ] Added to query cost dashboard

## Cost Control

### Query Byte Limits

```sql
-- Set max bytes billed (safety limit)
OPTIONS (
    maximum_bytes_billed = 10737418240  -- 10GB
)
SELECT ...
```

### Partition Expiration

```sql
-- Auto-delete old partitions to reduce storage costs
ALTER TABLE `raw.youtube_videos`
SET OPTIONS (
    partition_expiration_days = 365
);
```

### Materialize Expensive Queries

```sql
-- For queries run repeatedly, materialize
CREATE OR REPLACE TABLE `mart.daily_creator_stats`
PARTITION BY stats_date
AS
SELECT ... -- expensive query

-- Then query the materialized table
SELECT * FROM `mart.daily_creator_stats`
WHERE stats_date = CURRENT_DATE();
```

## Integration with dbt

When writing dbt models that generate BigQuery queries:

```sql
-- Use dbt's partition config
{{ config(
    materialized='incremental',
    partition_by={
      "field": "event_date",
      "data_type": "date"
    },
    cluster_by=["creator_id", "platform"]
) }}

SELECT 
    event_date,
    creator_id,
    platform,
    SUM(revenue) as total_revenue
FROM {{ ref('stg_conversions') }}
{% if is_incremental() %}
    WHERE event_date > (SELECT MAX(event_date) FROM {{ this }})
{% endif %}
GROUP BY 1, 2, 3
```

## Schema Design Principles

### Star Schema (Recommended)

```
Facts (Events):
- fact_conversions
- fact_video_views
- fact_touchpoints

Dimensions (Attributes):
- dim_creator
- dim_video
- dim_platform
- dim_date
```

**Why**: Easy to understand, fast joins, works with BI tools

### Column Naming

```sql
-- Good: Explicit, typed
creator_id STRING
revenue_usd FLOAT64
conversion_date DATE
is_active BOOL

-- Bad: Ambiguous
id STRING
amount FLOAT64
date DATE
flag BOOL
```

## Resources

- **Official Docs**: https://cloud.google.com/bigquery/docs
- **Query Optimization**: https://cloud.google.com/bigquery/docs/best-practices-performance-compute
- **Cost Optimization**: https://cloud.google.com/bigquery/docs/best-practices-costs
- **Standard SQL**: https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax

## Related Skills

- `dbt/SKILL.md` - For transformation patterns
- `ml-pipeline/SKILL.md` - For feature engineering queries

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colbyrreichenbach) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
