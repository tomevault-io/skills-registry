---
name: mahout-build
description: Build and test Apache Mahout (qumat + qdp). Use when this capability is needed.
metadata:
  author: guan404ming
---

# Mahout Build

## Usage
```
/mahout-build
```

## Commands

| Step | Command |
|---|---|
| Setup | `uv sync --group dev` |
| Pre-commit | `uv run pre-commit run --all-files` |
| Rust tests (GPU) | `cd qdp && cargo test` |
| Python build (GPU) | `uv run --active maturin develop --manifest-path qdp/qdp-python/Cargo.toml` |
| Python tests | `uv run pytest` |
| All tests | `make tests` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guan404ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
