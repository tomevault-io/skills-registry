---
name: rust-workspace
description: Build and test the aitop Rust workspace (8 crates, edition 2024, MSRV 1.95). Covers cargo build, test, clippy, and the aitop doctor health checks. Use when this capability is needed.
metadata:
  author: drdave-flexnetos
---

# Rust Workspace

Build and test the `aitop/` Cargo workspace — 8 crates producing the `aitop` CLI binary.

## Prerequisites

- Rust toolchain with edition 2024 support (MSRV 1.95)
- NVIDIA driver 595.71.05 + CUDA 13.2 (for `cudarc`/`nvml-wrapper` crates)
- cu132 venv at `/mnt/aitop-data/venv-cu132/` (doctor calls Python via subprocess)

## Steps

1. **Build the workspace**
   ```bash
   cd aitop && make build
   ```
   Runs `cargo build --workspace --release`.

2. **Run tests**
   ```bash
   make test
   ```
   Runs `cargo test --workspace`.

3. **Run clippy (zero warnings required)**
   ```bash
   make clippy
   ```
   Runs `cargo clippy --workspace --release -- -D warnings`.

4. **Run the doctor (10/10 PASS expected)**
   ```bash
   make doctor
   ```
   Runs `./target/release/aitop doctor`. Mirrors `model-training-prep/training/doctor.py` 1:1.

5. **Run CUDA kernel smoketest (optional, requires GPUs)**
   ```bash
   make kernels
   ```
   Tests `cuda-oxide`/`cudarc` on both RTX 5090s (sm_120).

## Key crates

| Crate | Purpose |
|-------|---------|
| `aitop-doctor` | 10 health checks (NVML, venv, CUDA, torch, env vars) |
| `aitop-doctor/src/env_loader.rs` | Once-guarded `/etc/profile.d/aitop-data.sh` sourcer |
| `aitop-cli` | CLI entry point (`aitop doctor`, `aitop status`) |

## Common failures

- **`unsafe set_var` lint**: env mutation requires BOTH `Once` AND single-threaded invocation
- **cudarc link errors**: ensure CUDA 13.2 toolkit is on `LD_LIBRARY_PATH`
- **doctor subprocess failures**: cu132 venv must be at `/mnt/aitop-data/venv-cu132/`

## Postconditions

- `cargo build --workspace --release` succeeds
- `cargo test --workspace` all pass
- `cargo clippy --workspace --release -- -D warnings` zero warnings
- `aitop doctor` reports 10/10 PASS

---
> Source: [drdave-flexnetos/ai-top-utility](https://github.com/drdave-flexnetos/ai-top-utility) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
