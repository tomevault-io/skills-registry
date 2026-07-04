---
name: implement
description: TDD implementation (RED→GREEN→REFACTOR) → verify → review Use when this capability is needed.
metadata:
  author: mag123c
---

# Implement

## Flow
```
Analysis → Gate pre-run → TDD(RED→GREEN→REFACTOR) → /verify → /review → /wrap
```

## Execution

1. **Analysis**: Review plan, identify affected modules

2. **Gate pre-run (MUST — before coding)**
   Run the PLAN's `[unverified-gate: probe=…]` / Phase 0 probes before RED. Fail → halt + report (don't build on a falsified assumption). Map Acceptance/DoD → RED tests; mark unverifiable as `[unverified-gate]` to carry to verify/wrap. Detail → `references/gates.md` (tag vocab: `../clarify/references/provenance.md`).

   Rust-specific probes:
   - `cargo check` — whether it compiles
   - `cargo test -- --list` — verify test targets exist
   - `make check` — unified fmt + clippy + test gate

3. **TDD Cycle**:
   - RED: Write failing test first
   - GREEN: Minimal code to pass
   - REFACTOR: Clean up (keep tests passing)
4. **Auto-call `/verify`**: On implementation complete
5. **Auto-call `/review`**: On verify pass
6. **Auto-call `/wrap`**: On review PASS

## Commands
```bash
cargo test
cargo clippy -- -D warnings
cargo fmt --check
```

## Rules
- No implementation without test
- On verify fail → fix and retry
- On review FAIL → fix and retry
- Complete full chain without stopping

---
> Source: [mag123c/toktrack](https://github.com/mag123c/toktrack) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
