---
name: test
description: Run the repoverlay test suite Use when this capability is needed.
metadata:
  author: tylerbutler
---

Run the test suite for repoverlay:

1. Build the debug binary first (required for CLI integration tests)
2. Run `cargo test --all-features`
3. If tests fail, analyze the failure output and suggest fixes
4. For verbose output, use `cargo test -- --nocapture`

The test structure is in `src/main.rs` under `mod tests` with:
- Unit tests for `remove_section`, `state`
- Integration tests for `apply`, `remove`, `status`, `create`, `switch`
- CLI tests using `assert_cmd`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tylerbutler) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
