---
name: database-design
description: Production-grade database design skill for SQL/NoSQL selection, schema design, indexing, and query optimization Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Database Design Skill

> **Purpose**: Atomic skill for database architecture with comprehensive parameter validation and optimization patterns.

## Skill Identity

| Attribute | Value |
|-----------|-------|
| **Scope** | Schema Design, Indexing, Query Optimization |
| **Responsibility** | Single: Data modeling and storage patterns |
| **Invocation** | `Skill("database-design")` |

## Parameter Schema

### Input Validation
```yaml
parameters:
  database_context:
    type: object
    required: true
    properties:
      use_case:
        type: string
        enum: [oltp, olap, htap, time_series, document, graph]
        required: true
      data_characteristics:
        type: object
        required: true
        properties:
          volume:
            type: string
            pattern: "^\\d+[KMGTP]B$"
          velocity:
            type: string
            pattern: "^\\d+[KM]?\\s*(reads?|writes?)/s$"
          variety:
            type: string
            enum: [structured, semi_structured, unstructured]
      access_patterns:
        type: array
        items:
          type: object
          properties:
            query_type: { type: string }
            frequency: { type: string, enum: [high, medium, low] }
            latency_sla: { type: string, pattern: "^\\d+ms$" }
      consistency:
        type: string
        enum: [strong, eventual, read_your_writes]
        default: strong

validation_rules:
  - name: "oltp_latency"
    rule: "use_case == 'oltp' implies latency_sla <= '100ms'"
    error: "OLTP workloads typically require <100ms latency"
  - name: "volume_velocity_match"
    rule: "high_volume implies adequate_velocity"
    error: "Volume and velocity constraints may be incompatible"
```

### Output Schema
```yaml
output:
  type: object
  properties:
    database_recommendation:
      type: object
      properties:
        primary: { type: string }
        secondary: { type: string }
        rationale: { type: string }
    schema:
      type: object
      properties:
        tables: { type: array }
        relationships: { type: array }
        constraints: { type: array }
    indexes:
      type: array
      items:
        type: object
        properties:
          table: { type: string }
          columns: { type: array }
          type: { type: string }
          rationale: { type: string }
    optimization_tips:
      type: array
      items: { type: string }
```

## Core Patterns

### Database Selection Matrix
```
OLTP (Transactional):
├── PostgreSQL
│   ├── Best for: Complex queries, ACID, JSON support
│   ├── Scale: Vertical + read replicas
│   └── Limit: ~10TB effective, 10K TPS
├── MySQL/MariaDB
│   ├── Best for: Web applications, simple queries
│   ├── Scale: Read replicas, ProxySQL
│   └── Limit: Similar to PostgreSQL
└── CockroachDB/TiDB
    ├── Best for: Distributed ACID
    ├── Scale: Horizontal auto-sharding
    └── Trade-off: Higher latency

OLAP (Analytical):
├── ClickHouse
│   ├── Best for: Real-time analytics, columnar
│   ├── Performance: 1B+ rows/second
│   └── Use: Log analysis, metrics
├── Snowflake/BigQuery
│   ├── Best for: Data warehouse, serverless
│   ├── Scale: Unlimited, pay-per-query
│   └── Use: BI, reporting
└── DuckDB
    ├── Best for: Embedded analytics
    └── Use: Edge, single-machine analytics

NoSQL:
├── MongoDB
│   ├── Best for: Documents, flexible schema
│   └── Pattern: Embedding, denormalization
├── Cassandra
│   ├── Best for: Write-heavy, wide-column
│   └── Pattern: Partition key design
├── Redis
│   ├── Best for: Cache, sessions, pub/sub
│   └── Limit: Memory-bound
└── Neo4j
    ├── Best for: Graph relationships
    └── Pattern: Cypher queries
```

### Schema Design Patterns
```
Normalization (OLTP):
├── 1NF: Atomic values
├── 2NF: No partial dependencies
├── 3NF: No transitive dependencies
└── BCNF: Strict key dependencies

Denormalization (OLAP/NoSQL):
├── Embedding
│   ├── Nest related documents
│   ├── Best for: 1:few relationships
│   └── Avoid: Large arrays, frequent updates
├── Bucketing
│   ├── Group time-series data
│   ├── Reduce document count
│   └── Improve write performance
└── Materialized Views
    ├── Pre-computed aggregates
    ├── Trade: Storage vs query time
    └── Refresh: Incremental or full
```

### Indexing Strategies
```
Index Types:
├── B-tree (default)
│   ├── Range queries, sorting
│   ├── Overhead: ~10% storage
│   └── Example: WHERE date BETWEEN x AND y
├── Hash
│   ├── Equality only
│   ├── Faster lookup
│   └── Example: WHERE id = 123
├── GIN (PostgreSQL)
│   ├── Full-text, JSONB, arrays
│   ├── Higher write cost
│   └── Example: WHERE tags @> '{redis}'
├── BRIN (Block Range)
│   ├── Large sorted tables
│   ├── Minimal storage
│   └── Example: Time-series data
└── Composite
    ├── Multi-column index
    ├── Order matters (leftmost prefix)
    └── Example: (user_id, created_at DESC)

Index Selection Rules:
├── Query frequency > 10% of total
├── Selectivity > 10% (filter out 90%)
├── Write overhead acceptable
└── Index size < table size
```

## Retry Logic

### Connection & Query Retry
```yaml
retry_config:
  connection:
    max_attempts: 3
    initial_delay_ms: 100
    max_delay_ms: 5000
    multiplier: 2.0

  query:
    max_attempts: 2
    timeout_ms: 30000
    retry_on:
      - CONNECTION_LOST
      - DEADLOCK
      - LOCK_TIMEOUT
    abort_on:
      - SYNTAX_ERROR
      - CONSTRAINT_VIOLATION
      - PERMISSION_DENIED

  transaction:
    retry_on_deadlock: true
    max_deadlock_retries: 3
    isolation_level: READ_COMMITTED
```

## Logging & Observability

### Log Format
```yaml
log_schema:
  level: { type: string }
  timestamp: { type: string, format: ISO8601 }
  skill: { type: string, value: "database-design" }
  event:
    type: string
    enum:
      - schema_analyzed
      - index_recommended
      - query_optimized
      - capacity_estimated
      - migration_planned
  context:
    type: object
    properties:
      table: { type: string }
      query: { type: string }
      execution_time_ms: { type: number }
      rows_affected: { type: integer }

example:
  level: INFO
  event: index_recommended
  context:
    table: orders
    columns: [user_id, status, created_at]
    type: composite
    rationale: "Covers 80% of queries"
```

### Metrics
```yaml
metrics:
  - name: query_duration_seconds
    type: histogram
    labels: [query_type, table]
    buckets: [0.001, 0.01, 0.1, 1, 10]

  - name: index_usage_ratio
    type: gauge
    labels: [index_name, table]

  - name: table_size_bytes
    type: gauge
    labels: [table, database]

  - name: connection_pool_usage
    type: gauge
    labels: [pool_name]
```

## Troubleshooting

### Common Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| Slow queries | Missing index | Add appropriate index |
| Lock waits | Long transactions | Split or optimize |
| Connection exhaustion | Pool too small | Increase pool, add pooler |
| Replication lag | Heavy writes | Add replicas, optimize queries |
| Disk full | Bloat, no vacuum | VACUUM, archival |

### Debug Checklist
```
□ EXPLAIN ANALYZE run?
□ Index usage verified?
□ Connection pool monitored?
□ Lock contention checked?
□ Table statistics current?
□ Slow query log enabled?
```

## Unit Test Templates

### Schema Validation Tests
```python
# test_database_design.py

def test_valid_oltp_schema():
    params = {
        "database_context": {
            "use_case": "oltp",
            "data_characteristics": {
                "volume": "100GB",
                "velocity": "10K writes/s",
                "variety": "structured"
            },
            "access_patterns": [
                {"query_type": "point_lookup", "frequency": "high", "latency_sla": "10ms"}
            ]
        }
    }
    result = validate_parameters(params)
    assert result.valid == True

def test_oltp_latency_validation():
    params = {
        "database_context": {
            "use_case": "oltp",
            "access_patterns": [
                {"latency_sla": "500ms"}  # Too high for OLTP
            ]
        }
    }
    result = validate_parameters(params)
    assert "latency" in result.warnings[0]

def test_index_recommendation():
    query = "SELECT * FROM orders WHERE user_id = ? AND status = ? ORDER BY created_at DESC"
    access_pattern = {"frequency": "high", "latency_sla": "10ms"}

    result = recommend_index(query, access_pattern)
    assert result.columns == ["user_id", "status", "created_at"]
    assert result.type == "composite"

def test_capacity_estimation():
    result = estimate_capacity(
        rows=10_000_000,
        row_size_bytes=500,
        growth_rate_monthly=0.05,
        months=12
    )
    assert result.current_size == "5GB"
    assert result.projected_size == "8.97GB"  # 5GB * 1.05^12
```

### Query Optimization Tests
```python
def test_explain_analysis():
    explain_output = """
    Seq Scan on orders (rows=1000000)
      Filter: (user_id = 123)
    """
    result = analyze_explain(explain_output)
    assert result.issues[0].type == "SEQ_SCAN"
    assert result.suggestions[0] == "Add index on orders(user_id)"

def test_composite_index_order():
    queries = [
        "WHERE user_id = ? AND status = ?",
        "WHERE user_id = ? ORDER BY created_at"
    ]
    result = optimize_index_order(queries)
    # user_id should be first (equality), created_at last (sort)
    assert result.recommended_order == ["user_id", "status", "created_at"]
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01 | Production-grade rewrite with optimization patterns |
| 1.0.0 | 2024-12 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
