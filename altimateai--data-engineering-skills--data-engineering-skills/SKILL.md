---
name: finding-expensive-queries
description: | Use when this capability is needed.
metadata:
  author: AltimateAI
---

# Finding Expensive Queries

**Query history → Rank by metric → Identify patterns → Recommend optimizations**

## Workflow

### 1. Ask What to Optimize For

Before querying, clarify:
- Time period? (last day, week, month)
- Metric? (execution time, bytes scanned, cost, spillage)
- Warehouse? (specific or all)
- User? (specific or all)

### 2. Find Expensive Queries by Cost

Use QUERY_ATTRIBUTION_HISTORY for credit/cost analysis:

```sql
SELECT
    query_id,
    warehouse_name,
    user_name,
    credits_attributed_compute,
    start_time,
    end_time,
    query_tag
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
WHERE start_time >= DATEADD('days', -7, CURRENT_TIMESTAMP())
ORDER BY credits_attributed_compute DESC
LIMIT 20;
```

### 3. Get Performance Stats for Specific Queries

Use QUERY_HISTORY for detailed performance metrics (run separately, not joined):

```sql
SELECT
    query_id,
    query_text,
    total_elapsed_time/1000 as seconds,
    bytes_scanned/1e9 as gb_scanned,
    bytes_spilled_to_local_storage/1e9 as gb_spilled_local,
    bytes_spilled_to_remote_storage/1e9 as gb_spilled_remote,
    partitions_scanned,
    partitions_total
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
WHERE query_id IN ('<query_id_1>', '<query_id_2>', ...)
  AND start_time >= DATEADD('days', -7, CURRENT_TIMESTAMP());
```

### 4. Identify Patterns

Look for:
- High `credits_attributed_compute` queries
- Same `query_hash` repeated (caching opportunity)
- `partitions_scanned = partitions_total` (no pruning)
- High `gb_spilled` (memory pressure)

### 5. Return Results

Provide:
1. Ranked list of expensive queries with key metrics
2. Common patterns identified
3. Top 3-5 optimization recommendations
4. Specific queries to investigate further

## Common Filters

```sql
-- Time range (required)
WHERE start_time >= DATEADD('days', -7, CURRENT_TIMESTAMP())

-- By warehouse
AND warehouse_name = 'ANALYTICS_WH'

-- By user
AND user_name = 'ETL_USER'

-- Only queries over cost threshold
AND credits_attributed_compute > 0.01

-- Only queries over time threshold
AND total_elapsed_time > 60000  -- over 1 minute
```

---
> Source: [AltimateAI/data-engineering-skills](https://github.com/AltimateAI/data-engineering-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
