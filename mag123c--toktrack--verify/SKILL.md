---
name: verify
description: Self-healing verification loop (test → clippy → fmt) Use when this capability is needed.
metadata:
  author: mag123c
---

# Verify

## Flow
```
cargo test → cargo clippy → cargo fmt --check
    │            │              │
    └── On fail: fix and retry (notify user after 3 same failures)
```

## Commands
```bash
cargo test --quiet
cargo clippy --all-targets --all-features -- -D warnings
cargo fmt --all -- --check
```

## Self-Healing
- Fail → analyze error → fix code → retry
- Same error 3 times → notify user

## Rules
- Required before commit
- Order: test → clippy → fmt
- All must pass to proceed

## Next Step
On all pass → immediately call `/review`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mag123c) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
