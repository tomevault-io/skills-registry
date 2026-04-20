---
name: dbt-expert
description: dbt transformation patterns, testing, documentation, incremental models, and workflow optimization. Use when building data models, writing dbt code, or optimizing transformation pipelines. Use when this capability is needed.
metadata:
  author: colbyrreichenbach
---

# dbt Expert Skill

## When to Use This Skill

Activate this skill when:
- Writing dbt models (staging, intermediate, mart)
- Creating or updating schema.yml files
- Writing dbt tests (generic or custom)
- Implementing incremental models
- Optimizing dbt performance
- Debugging dbt errors
- Setting up dbt projects

## Core Patterns

### 1. Model Layering (MANDATORY)

```
raw → staging → intermediate → mart
 ↓        ↓           ↓          ↓
API    Clean      Business    Final
data   + cast      logic     product
```

**Staging Models** (stg_):
```sql
-- models/staging/youtube/stg_youtube__videos.sql
WITH source AS (
    SELECT * FROM {{ source('raw', 'youtube_videos') }}
),

renamed AS (
    SELECT
        -- Keys
        video_id,
        channel_id AS creator_id,
        
        -- Dimensions
        title,
        description,
        
        -- Metrics (cast to proper types)
        CAST(view_count AS INT64) AS views,
        CAST(like_count AS INT64) AS likes,
        CAST(comment_count AS INT64) AS comments,
        
        -- Timestamps
        PARSE_TIMESTAMP('%Y-%m-%dT%H:%M:%SZ', published_at) AS published_at,
        
        -- Metadata
        'youtube' AS platform,
        _loaded_at AS source_loaded_at
    FROM source
)

SELECT * FROM renamed
```

**Key principles**:
- 1:1 with source tables
- Only renaming, type casting, and simple calculations
- No joins, no aggregations, no business logic
- Add platform identifier for cross-platform models

**Intermediate Models** (int_):
```sql
-- models/intermediate/int_creator_video_metrics.sql
WITH video_stats AS (
    SELECT * FROM {{ ref('stg_youtube__videos') }}
),

creator_info AS (
    SELECT * FROM {{ ref('stg_youtube__creators') }}
),

joined AS (
    SELECT
        v.video_id,
        v.creator_id,
        c.creator_name,
        c.subscriber_count,
        
        -- Calculated metrics
        v.views,
        v.likes,
        v.comments,
        SAFE_DIVIDE(v.likes, v.views) AS like_rate,
        SAFE_DIVIDE(v.comments, v.views) AS comment_rate,
        (v.likes + v.comments * 2) / v.views AS engagement_rate,
        
        v.published_at,
        v.platform
    FROM video_stats v
    INNER JOIN creator_info c
        ON v.creator_id = c.creator_id
)

SELECT * FROM joined
```

**Key principles**:
- Joins between staging models
- Business logic and calculations
- Can be ephemeral or table materialized
- Break complex logic into multiple intermediate steps

**Mart Models** (fact_, dim_):
```sql
-- models/mart/fact_video_performance.sql
{{
    config(
        materialized='table',
        partition_by={
            "field": "published_date",
            "data_type": "date"
        },
        cluster_by=["creator_id", "platform"]
    )
}}

WITH video_metrics AS (
    SELECT * FROM {{ ref('int_creator_video_metrics') }}
),

final AS (
    SELECT
        -- Keys
        video_id,
        creator_id,
        
        -- Dimensions
        creator_name,
        platform,
        DATE(published_at) AS published_date,
        
        -- Metrics
        views,
        likes,
        comments,
        like_rate,
        comment_rate,
        engagement_rate,
        
        -- Metadata
        published_at,
        CURRENT_TIMESTAMP() AS dbt_updated_at
    FROM video_metrics
)

SELECT * FROM final
```

**Key principles**:
- Final tables for consumption (APIs, dashboards, analysts)
- Proper materialization strategy
- Clear grain (one row = ?)
- All columns documented in schema.yml

### 2. Incremental Models Pattern

```sql
-- models/mart/fact_daily_creator_stats.sql
{{
    config(
        materialized='incremental',
        partition_by={
            "field": "stat_date",
            "data_type": "date"
        },
        cluster_by=["creator_id", "platform"],
        unique_key='creator_stat_key',
        on_schema_change='fail'
    )
}}

WITH daily_stats AS (
    SELECT
        -- Create unique key
        {{ dbt_utils.generate_surrogate_key(['creator_id', 'stat_date', 'platform']) }} AS creator_stat_key,
        
        creator_id,
        DATE(event_timestamp) AS stat_date,
        platform,
        
        SUM(views) AS total_views,
        SUM(revenue_usd) AS total_revenue,
        COUNT(DISTINCT video_id) AS videos_published
        
    FROM {{ ref('stg_platform_events') }}
    WHERE 1=1
        AND event_timestamp >= '2024-01-01'  -- Performance: limit historical scan
        
        {% if is_incremental() %}
            -- Only process new/updated data
            AND DATE(event_timestamp) > (
                SELECT MAX(stat_date) 
                FROM {{ this }}
            )
        {% endif %}
        
    GROUP BY 1, 2, 3, 4
)

SELECT * FROM daily_stats
```

**Key patterns**:
- **unique_key**: Required for deduplication
- **is_incremental()**: Only process new data on incremental runs
- **on_schema_change**: 'fail' prevents accidental schema changes
- **Partition + cluster**: For query performance
- **Watermark column**: Use MAX(date) for incremental logic

**When to use incremental**:
- ✅ Large fact tables (>1M rows)
- ✅ Append-only data
- ✅ Clear watermark column
- ❌ Small dimension tables
- ❌ Data can be updated/deleted
- ❌ Complex backfill requirements

### 3. Testing Strategy

```yaml
# models/staging/youtube/stg_youtube__videos.yml
version: 2

models:
  - name: stg_youtube__videos
    description: Cleaned YouTube video data from raw API ingestion
    
    tests:
      - dbt_utils.expression_is_true:
          expression: "views >= 0"
          config:
            severity: error
    
    columns:
      - name: video_id
        description: Unique YouTube video identifier
        tests:
          - not_null
          - unique
        
      - name: creator_id
        description: YouTube channel ID of video creator
        tests:
          - not_null
          - relationships:
              to: ref('stg_youtube__creators')
              field: creator_id
              config:
                severity: warn  # Warning if creator not found
        
      - name: views
        description: Number of video views
        tests:
          - not_null
          - dbt_utils.accepted_range:
              min_value: 0
              inclusive: true
        
      - name: published_at
        description: Video publication timestamp
        tests:
          - not_null
          - dbt_utils.not_null_proportion:
              at_least: 0.99
        
      - name: platform
        description: Platform identifier (always 'youtube' for this model)
        tests:
          - not_null
          - accepted_values:
              values: ['youtube']
```

**Test categories**:

1. **Primary Key Tests** (REQUIRED):
```yaml
- not_null
- unique
```

2. **Foreign Key Tests** (REQUIRED):
```yaml
- relationships:
    to: ref('parent_table')
    field: parent_key
```

3. **Data Quality Tests**:
```yaml
- accepted_values:
    values: ['value1', 'value2']
- dbt_utils.accepted_range:
    min_value: 0
    max_value: 100
```

4. **Custom Tests**:
```sql
-- tests/assert_revenue_not_negative.sql
SELECT
    conversion_id,
    revenue_usd
FROM {{ ref('fact_conversions') }}
WHERE revenue_usd < 0
```

### 4. Sources Configuration

```yaml
# models/staging/sources.yml
version: 2

sources:
  - name: raw
    description: Raw data ingested from platform APIs
    database: sponsorgraph-prod
    schema: raw
    
    tables:
      - name: youtube_videos
        description: Raw YouTube video data from YouTube Data API v3
        
        loaded_at_field: _loaded_at
        freshness:
          warn_after: {count: 24, period: hour}
          error_after: {count: 48, period: hour}
        
        columns:
          - name: video_id
            description: YouTube video ID
            tests:
              - not_null
              - unique
          
          - name: _loaded_at
            description: Timestamp when data was loaded to BigQuery
            tests:
              - not_null
```

**Source freshness checks**:
```bash
# Check if data is fresh
dbt source freshness

# Output:
# 15:32:05  1 of 1 START freshness of raw.youtube_videos ..................... [RUN]
# 15:32:06  1 of 1 WARN freshness of raw.youtube_videos ...................... [WARN in 0.84s]
```

### 5. Macros for Reusability

```sql
-- macros/calculate_engagement_rate.sql
{% macro calculate_engagement_rate(likes, comments, shares, views) %}
    SAFE_DIVIDE(
        {{ likes }} + ({{ comments }} * 2) + ({{ shares }} * 3),
        {{ views }}
    )
{% endmacro %}
```

**Usage**:
```sql
SELECT
    video_id,
    {{ calculate_engagement_rate('likes', 'comments', 'shares', 'views') }} AS engagement_rate
FROM {{ ref('stg_youtube__videos') }}
```

**Common macros**:
```sql
-- macros/generate_schema_name.sql
{% macro generate_schema_name(custom_schema_name, node) -%}
    {%- set default_schema = target.schema -%}
    {%- if custom_schema_name is none -%}
        {{ default_schema }}
    {%- else -%}
        {{ custom_schema_name | trim }}
    {%- endif -%}
{%- endmacro %}

-- macros/grant_select.sql
{% macro grant_select(schema, role) %}
    GRANT SELECT ON ALL TABLES IN SCHEMA {{ schema }} TO {{ role }};
{% endmacro %}
```

### 6. dbt Project Configuration

```yaml
# dbt_project.yml
name: 'sponsorgraph'
version: '1.0.0'
config-version: 2

profile: 'sponsorgraph'

model-paths: ["models"]
analysis-paths: ["analyses"]
test-paths: ["tests"]
seed-paths: ["seeds"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

target-path: "target"
clean-targets:
  - "target"
  - "dbt_packages"

models:
  sponsorgraph:
    # Staging models
    staging:
      +materialized: view
      +schema: staging
      
    # Intermediate models (ephemeral = not materialized)
    intermediate:
      +materialized: ephemeral
      
    # Mart models
    mart:
      +materialized: table
      +schema: mart
      
      # Fact tables use incremental
      fact:
        +materialized: incremental
        +on_schema_change: fail

seeds:
  sponsorgraph:
    +schema: seed
    +quote_columns: false

tests:
  sponsorgraph:
    +severity: error  # Default: fail on test failure
```

### 7. Performance Optimization

**Use CTEs, not subqueries**:
```sql
-- Good
WITH base AS (
    SELECT * FROM {{ ref('large_table') }}
    WHERE date >= '2024-01-01'
),
aggregated AS (
    SELECT
        creator_id,
        SUM(revenue) AS total_revenue
    FROM base
    GROUP BY 1
)
SELECT * FROM aggregated;

-- Bad
SELECT
    creator_id,
    SUM(revenue) AS total_revenue
FROM (
    SELECT * FROM {{ ref('large_table') }}
    WHERE date >= '2024-01-01'
) subquery
GROUP BY 1;
```

**Use ref() for dependencies**:
```sql
-- Good: dbt tracks dependencies
SELECT * FROM {{ ref('stg_youtube__videos') }}

-- Bad: breaks dependency graph
SELECT * FROM `project.staging.stg_youtube__videos`
```

**Limit expensive operations**:
```sql
-- Good: Filter early
WITH filtered AS (
    SELECT * FROM {{ ref('fact_events') }}
    WHERE event_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY)  -- Partition filter
)
SELECT
    creator_id,
    COUNT(*) AS event_count
FROM filtered
GROUP BY 1;

-- Bad: Filter after aggregation
SELECT
    creator_id,
    COUNT(*) AS event_count
FROM {{ ref('fact_events') }}
GROUP BY 1
HAVING event_date >= DATE_SUB(CURRENT_DATE(), INTERVAL 30 DAY);
```

## Common Issues & Solutions

### Issue: "Ambiguous column name"

**Cause**: Column exists in multiple CTEs

**Solution**: Prefix with CTE name
```sql
WITH cte1 AS (
    SELECT creator_id, name FROM table1
),
cte2 AS (
    SELECT creator_id, platform FROM table2
)
SELECT
    cte1.creator_id,  -- Explicitly specify
    cte1.name,
    cte2.platform
FROM cte1
JOIN cte2 ON cte1.creator_id = cte2.creator_id;
```

### Issue: "Incremental model not updating"

**Cause**: is_incremental() condition too restrictive

**Solution**: Check watermark logic
```sql
{% if is_incremental() %}
    -- Make sure this actually matches new data
    AND updated_at > (SELECT MAX(updated_at) FROM {{ this }})
{% endif %}
```

### Issue: "Compilation Error: depends on a node named 'X' which was not found"

**Cause**: Typo in ref() or source()

**Solution**: Check spelling
```sql
-- Wrong
SELECT * FROM {{ ref('stg_youtube__video') }}  -- Missing 's'

-- Right
SELECT * FROM {{ ref('stg_youtube__videos') }}
```

### Issue: "Resource 'X' not found"

**Cause**: Wrong schema or database

**Solution**: Check sources.yml configuration
```yaml
sources:
  - name: raw
    database: sponsorgraph-prod  # Must match actual database
    schema: raw                  # Must match actual schema
```

## dbt Commands Reference

```bash
# Development workflow
dbt debug                          # Test connection
dbt deps                           # Install packages
dbt seed                           # Load seed files
dbt run                            # Run all models
dbt run --select stg_youtube__*    # Run specific models
dbt run --full-refresh             # Force full refresh (ignore incremental)
dbt test                           # Run all tests
dbt test --select stg_youtube__videos  # Test specific model
dbt docs generate                  # Generate documentation
dbt docs serve                     # Serve docs locally

# Advanced selectors
dbt run --select tag:daily         # Run models with tag
dbt run --select +fact_conversions # Run model + upstream dependencies
dbt run --select fact_conversions+ # Run model + downstream dependencies
dbt run --select @fact_conversions # Run model + all dependencies
dbt run --exclude tag:deprecated   # Exclude models

# Target environments
dbt run --target dev               # Run against dev
dbt run --target prod              # Run against prod

# Monitoring
dbt source freshness               # Check source freshness
dbt run-operation grant_select --args '{schema: mart, role: analyst}'  # Run macro
```

## Documentation Best Practices

### Model Documentation

```yaml
models:
  - name: fact_video_performance
    description: |
      Video performance metrics aggregated daily by creator and platform.
      
      **Grain**: One row per video
      
      **Refresh**: Incremental (daily)
      
      **SLA**: Updated by 2 AM daily
      
      **Owners**: Data Team (@data-team)
    
    meta:
      owner: "@data-team"
      contains_pii: false
      
    config:
      tags: ['daily', 'mart', 'revenue']
```

### Column Documentation

```yaml
columns:
  - name: engagement_rate
    description: |
      Calculated engagement rate using weighted formula:
      (likes + comments*2 + shares*3) / views
      
      **Formula**: See macro `calculate_engagement_rate`
      
      **Range**: 0.0 to 1.0 (0% to 100%)
      
      **Null handling**: Returns 0 if views = 0
    
    meta:
      metric_type: "rate"
      aggregation: "average"
```

## Testing Checklist

Before merging dbt changes:

- [ ] All models have descriptions
- [ ] All columns documented (especially calculated fields)
- [ ] Primary keys have not_null + unique tests
- [ ] Foreign keys have relationship tests
- [ ] `dbt run` completes successfully
- [ ] `dbt test` passes all tests
- [ ] `dbt docs generate` works
- [ ] Incremental models tested with --full-refresh
- [ ] Query costs estimated (check compiled SQL)
- [ ] No SELECT * in production models
- [ ] Proper materialization strategy selected

## Performance Benchmarks

Target performance (adjust based on data volume):

- **Staging models**: <30 seconds each
- **Intermediate models**: <2 minutes each  
- **Mart models**: <5 minutes each
- **Full dbt run**: <15 minutes total
- **Incremental run**: <5 minutes total

If slower:
1. Check partition filters
2. Review join strategies
3. Consider materializing intermediate steps
4. Profile with `dbt run --profiles-dir . --profile-limit 10`

## Related Resources

- Main context: `.claude/CLAUDE.md`
- BigQuery skill: `.claude/skills/bigquery/SKILL.md`
- dbt docs: https://docs.getdbt.com
- dbt utils: https://github.com/dbt-labs/dbt-utils

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colbyrreichenbach) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
