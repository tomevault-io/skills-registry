---
name: cassandra
description: Design Cassandra tables, write efficient queries, and avoid distributed database pitfalls. Use when this capability is needed.
metadata:
  author: openclaw
---

## Data Modeling Mistakes

- Design tables around queries, not entities—denormalization is mandatory, not optional
- One table per query pattern—Cassandra has no JOINs; duplicate data across tables
- Partition key determines data distribution—all rows with same partition key on same node
- Wide partitions kill performance—keep under 100MB; add time bucket to partition key if growing

## Primary Key Traps

- `PRIMARY KEY (a, b, c)`: `a` is partition key, `b` and `c` are clustering columns
- `PRIMARY KEY ((a, b), c)`: `(a, b)` together is partition key—compound partition key
- Clustering columns define sort order within partition—query must respect this order
- Can't query by clustering column without partition key—unlike SQL indexes

## Query Restrictions

- `WHERE` must include full partition key—partial partition key fails unless `ALLOW FILTERING`
- `ALLOW FILTERING` scans all nodes—never use in production; redesign table instead
- Range queries only on last clustering column used—`WHERE a = ? AND b > ?` works, `WHERE a = ? AND c > ?` doesn't
- `IN` on partition key hits multiple nodes—expensive; prefer single partition queries

## Consistency Levels

- `QUORUM` for most operations—majority of replicas; balances consistency and availability
- `LOCAL_QUORUM` for multi-datacenter—avoids cross-DC latency
- `ONE` for pure availability—may read stale data; fine for caches, bad for critical reads
- Write + read consistency must overlap for strong consistency—`QUORUM` + `QUORUM` safe

## Tombstones (Silent Performance Killer)

- DELETE creates a tombstone, not actual deletion—tombstones persist until compaction
- Mass deletes destroy read performance—thousands of tombstones scanned per query
- TTL also creates tombstones—don't use short TTLs with high write volume
- Check with `nodetool cfstats -H table`—`Tombstone` columns show problem

## Batch Misuse

- UNLOGGED BATCH is not faster—use only for atomic writes to same partition
- LOGGED BATCH for multi-partition atomicity—adds coordination overhead
- Don't batch unrelated writes—hurts coordinator; send individual async writes
- Batch size limit ~50KB—larger batches fail or timeout

## Anti-Patterns

- Secondary indexes on high-cardinality columns—scatter-gather query, slow
- Secondary indexes on frequently updated columns—creates tombstones
- `SELECT *`—always list columns; schema changes break queries
- UUID as partition key without time component—random distribution, hot spots during bulk loads

## Lightweight Transactions

- `IF NOT EXISTS` / `IF column = ?`—uses Paxos, 4x slower than normal write
- Serial consistency for LWTs—`SERIAL` or `LOCAL_SERIAL`
- Don't use for counters or high-frequency updates—contention kills throughput
- Returns `[applied]` boolean—must check if operation succeeded

## Collections and Counters

- Sets/Lists/Maps stored with row—can't exceed 64KB, no pagination
- List prepend is anti-pattern—creates tombstones; use append or Set
- Counters require dedicated table—can't mix with regular columns
- Counter increment is not idempotent—retry may double-count

## Compaction Strategies

- `SizeTieredCompactionStrategy` (default)—good for write-heavy, uses more disk space
- `LeveledCompactionStrategy`—better read latency, higher write amplification
- `TimeWindowCompactionStrategy`—for time-series with TTL; reduces tombstone overhead
- Wrong strategy for workload = degraded performance over time

## Operations

- `nodetool repair` regularly—inconsistencies accumulate without repair
- `nodetool status` shows cluster health—UN (Up Normal) is good, DN is down
- Schema changes propagate eventually—wait for `nodetool describecluster` to show agreement
- Rolling restarts: one node at a time, wait for UN status before next

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw)
> Context snippets also available to append to your CLAUDE.md, GEMINI.md, and copilot-instructions.md — [download at TomeVault](https://tomevault.io/claim/openclaw)
<!-- tomevault:4.0:skill_md:2026-04-08 -->
