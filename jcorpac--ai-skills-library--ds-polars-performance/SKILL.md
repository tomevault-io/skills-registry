---
name: ds-polars-performance
description: High-performance data processing using the Rust-based Polars library. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Polars Performance

Polars is a lightning-fast DataFrame library written in Rust, designed for massive datasets where Pandas hits memory or speed limits.

## Core Concepts
- **Eager vs Lazy**: Use `pl.scan_csv()` for lazy execution, allowing Polars to optimize the query plan before execution.
- **Expressions**: Polars uses a powerful expression API (`pl.col("name").filter(...)`) that is more readable and faster than standard indexing.

## Memory Management
- **Zero-copy**: Polars uses Apache Arrow memory format, which allows for efficient data sharing.
- **Streaming**: Handle datasets larger than RAM by processing them in chunks.

## Best Practices
- **Prefer Expressions**: Avoid using `apply()` or custom Python loops; use built-in Polars expressions for maximum performance.
- **Type Safety**: Leverage Polars' strict schema enforcement to prevent data quality issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
