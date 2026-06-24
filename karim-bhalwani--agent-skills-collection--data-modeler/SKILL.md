---
name: data-modeler
description: Design and implement data pipelines using Medallion architecture, Data Vault 2.0, and Spark optimization. Use when building ETL/ELT processes, designing data schemas, optimizing Spark performance, implementing data governance, or architecting lakehouse solutions. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Data Modeler Skill - Lakehouse & Data Engineering

## Overview

The Data Modeler skill manages the lifecycle of data within a Lakehouse environment. It focuses on performance, governance, and reliable data transformations.

## Core Capabilities

1. **Medallion Architecture**: Implementation of Bronze (Raw), Silver (Clean/Vault), and Gold (Business) layers.
2. **Data Vault 2.0**: Designing Hubs (Keys), Links (Relationships), and Satellites (Attributes/SCD2).
3. **Spark Optimization**: Native PySpark functions, Z-Ordering, partitioning, and file compaction (OPTIMIZE/VACUUM).
4. **Governance**: Unity Catalog integration, RBAC, and storage credential management.
5. **CDC (Change Data Capture)**: Implementing append-only audit trails and Delta Lake change feeds.

## Standards

- **PySpark**: Avoid UDFs. Use explicit imports and aliases (`F`, `T`). Use `.transform()` for modularity.
- **Idempotency**: All pipelines must be safe to re-run and support backfilling.
- **Partitioning**: Date-based for time-series; 100-1000 partitions ideal.
- **Data Quality**: Use Great Expectations or Chispa for transformation validation.

## When to Use

- Designing database schemas or lakehouse layouts.
- Building ETL/ELT pipelines.
- Optimizing slow queries or high-cost data processing.
- Implementing data privacy and governance controls.

## Constraints

- **NO implementation of application business logic.**
- **NO infrastructure deployment** (route to ops-manager).
- **NO code without data quality checks.**

## Outputs & Deliverables

- **Primary Output**: Data model designs, schema definitions, and pipeline specs (e.g., `specs/<pipeline>.md`)
- **Secondary Output**: Example pipeline code snippets and data quality checks
- **Success Criteria**: Models include schema, partitioning strategy, and validation rules
- **Quality Gate**: Data model reviewed by `implementer` and `ops-manager` before production run

## Additional Constraints

- **Technical Constraints:** Do not deploy infrastructure; hand off IaC to `ops-manager`.
- **Scope Constraints:** In scope: model design and pipeline specs. Out of scope: production deployment and cluster configuration.
- **Governance Constraints:** All models must include data quality tests and backfill strategy.

## Common Pitfalls

- **Over-Partitioning**: Excessive partitions increase query latency and metadata overhead. 100-1000 is ideal; validate before deployment.
- **Missing Backfill Strategy**: Not planning how to reload historical data leads to operational nightmares. Every model needs a backfill procedure.
- **Ignoring Data Quality**: Garbage in = garbage out. Define validation rules *before* implementation, not after bugs appear.
- **Wrong Medallion Layer**: Mixing business logic into Bronze or Silver layers defeats Medallion purpose. Keep layers clean and separated.
- **No SCD (Slowly Changing Dimensions)**: Not tracking how attributes change over time breaks historical analysis. Use SCD2 for audit trails.
- **Skipping Schema Evolution Planning**: Schemas change; not planning migration paths causes production incidents.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Design | `architect`, requirements | Schema specification | Understand business primitives and data flows |
| Pipeline Dev | Schema specs | `implementer` | Hand off for ETL/ELT implementation |
| Optimization | Performance metrics | `ops-manager` | Request infrastructure scaling if needed |
| Quality | Data rules | Monitoring/alerts | Set up data quality checks and validations |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
