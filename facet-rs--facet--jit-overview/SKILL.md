---
name: jit-overview
description: Orientation to facet-format JIT deserialization (tiering, fallbacks, key types/entry points) and where to look when changing or debugging JIT code Use when this capability is needed.
metadata:
  author: facet-rs
---

# JIT deserialization overview (facet-format)

Facet’s JIT lives in `facet-format` and is used by format crates like `facet-json`.

## When to read this

- You’re touching anything under `facet-format/src/jit/` or enabling the `jit` feature.
- You’re investigating performance changes in deserialization (especially “tier2”/JIT benchmarks).
- You’re debugging a JIT crash (SIGSEGV/UB-ish symptoms).

## Mental model

`facet-format` defines a **format-neutral** deserialization pipeline:

- A `FormatParser` (implemented by each format crate) produces a stream of `ParseEvent`s.
- A shape-driven layer consumes those events and writes to an output value (often via `facet-reflect`).

The JIT accelerates this by compiling deserialization code specialized for:

1. the **target type** (`T` / its `Shape`), and sometimes
2. the **format parser** (`P`).

## Two-tier architecture (high level)

### Tier 1 (shape JIT)

- Compiles code that consumes `ParseEvent`s and writes directly into the output’s memory at known offsets.
- Works with any format that implements `FormatParser` (JSON/YAML/TOML/…).

### Tier 2 (format JIT)

- For the “entire input slice is available” case, a format crate can provide a `FormatJitParser` + `JitFormat` implementation.
- Tier 2 emits Cranelift IR to parse bytes directly, bypassing the `ParseEvent` stream for maximum throughput.

### Fallbacks are part of the design

- Tier 2 may return “unsupported” for shapes/input it can’t handle, and must be side-effect-free in that case.
- Callers typically try tier 2, then tier 1, then reflection.

## Entry points & where to look

- Main docs and contracts: `facet-format/src/jit/mod.rs`
- JIT-enabled parser trait: `facet-format/src/parser.rs` (`FormatJitParser`)
- JIT usage in a format crate:
  - `facet-json/Cargo.toml` feature `jit = ["facet-format/jit"]`
  - Example: `facet-json/examples/profile_jit_vec` (requires `jit`)
- Windows crash debugging notes: `.claude/skills/windbg-jit.md`
- Memory debugging: `.claude/skills/debug-with-valgrind/SKILL.md` (uses nextest profiles)

## Debugging checklist (practical)

1. Reproduce with a minimal type + input (see `.claude/skills/reproduce-reduce-regress/SKILL.md`).
2. Run the failing test under:
   - valgrind: `cargo nextest run --profile valgrind …` (configured in `.config/nextest.toml`)
   - or Miri when applicable (`just miri`) for UB/provenance issues outside the JIT itself.
3. If the crash is in JIT codegen/execution:
   - Prefer isolating the smallest shape that triggers tier selection and failure.
   - Look for tier selection diagnostics and caching behavior in `facet-format/src/jit/mod.rs`.

## Common pitfalls

- Assuming tier 2 supports “all shapes”: it intentionally supports a performance-focused subset.
- Forgetting that “unsupported” must not advance the parser cursor or partially initialize output.
- Introducing new unsafe paths without tests that exercise drop/cleanup on error paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/facet-rs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
