---
name: benchmark
description: Run benchmarks for the crypto-rs-backtester project (Python and Rust). Use when this capability is needed.
metadata:
  author: takurot
---

# Benchmark Skill

This skill allows you to run performance benchmarks for the project.

## 1. Python Benchmarks

Run the Python-side benchmarks using `pytest`.

```bash
pytest -m bench -q
```

## 2. Rust Benchmarks (Standard)

Run the standard Rust core benchmarks.

```bash
cargo bench -p backtester-core
```

## 3. Rust Benchmarks (Custom Configuration)

Run Rust benchmarks with custom parameters using the helper script.
This allows you to tune the number of symbols, ticks per symbol, and batch window size.

```bash
# Run with default 'heavy' settings (8 symbols, 500k ticks)
./.agent/skills/benchmark/scripts/run_custom_bench.sh
```

You can also set environment variables manually if you prefer:

```bash
BACKTEST_BENCH_NSYMBOLS=8 \
BACKTEST_BENCH_TICKS_PER_SYMBOL=500000 \
cargo bench -p backtester-core --bench bench_core
```

## 4. Profile-Guided Optimization (PGO)

To build and benchmark with PGO (Linux/macOS).
**Note**: This process is time-consuming as it runs a full build-profile-rebuild pipeline.

```bash
make pgo
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/takurot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
