---
name: databento-c-extension
description: Python bindings for Databento's C++ Historical API. Use when this capability is needed.
metadata:
  author: quantdiy
---

# Databento C++ Extension (`databento_ext`)

High-performance Cython bindings for Databento's C++ API.

## Installation
Run `python3 setup.py build_ext --inplace` in `extensions/cpp/databento/cython/`.
Requires `cmake` to fetch dependencies first.

## Usage
```python
import databento_ext

# 1. Historical Client
builder = databento_ext.PyHistoricalBuilder().set_key_from_env()
client = builder.build()
# Client is now a valid C++ Historical client wrapper.
```

## Status
- **Historical Builder**: ✅ Implemented (Cython)
- **Historical Client**: ✅ Implemented (Cython)
- **Live Data**: 🚧 Pending Migration
- **DuckDB Connector**: 🚧 Pending Migration (Requires re-implementing `timeseries_to_duckdb` with new Cython types)

## Dependencies
- `libdatabento` (Static)
- `zstd`, `openssl`, `curl`, `brotli` (System libraries)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quantdiy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
