---
name: gen-test-data
description: Generate realistic test data fixtures for a data modality Use when this capability is needed.
metadata:
  author: matthandzel
---

Generate test data for the `$ARGUMENTS` modality.

1. Look up the proto message definition in `proto/lifelog.proto`
2. Look up any `Distribution<T> for StandardUniform` implementations in `common/lifelog-proto/src/lib.rs` for existing patterns
3. Generate a Rust function that produces realistic test fixtures:
   - Random but plausible field values (real-looking URLs for browser, sensible timestamps, etc.)
   - A builder pattern or factory function
   - Both single-item and batch generators
4. Place the function in the appropriate test module or `server/tests/harness/mod.rs`
5. Run `just test` to verify it compiles and works

Follow the existing `Distribution<ScreenFrame>` pattern in lifelog-proto as a template.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthandzel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
