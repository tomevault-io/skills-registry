---
name: rust-api-guidelines
description: Review, design, or revise Rust public APIs using only the bundled Rust API Guidelines files in this skill's assets directory as the API standard. Use when Codex works on Rust library surfaces, exported structs/enums/traits/functions, crate ergonomics, semver-compatible API evolution, error types, builders, conversions, trait implementations, documentation, or API review feedback. Use when this capability is needed.
metadata:
  author: linuxperf
---

# Rust API Guidelines

## Overview

Use this skill to review or revise Rust APIs against the bundled guideline files in `assets/`. Do not introduce Rust API rules, examples, or recommendations that are not grounded in those files.

## Guideline Assets

Start with `assets/SUMMARY.md` to see the available local guideline chapters.

Use `assets/checklist.md` for a first-pass review. Load the matching topical chapter when a checklist item needs interpretation, examples, or a more precise recommendation.

If no chapter applies, present the recommendation as general engineering judgment rather than as part of this skill's API standard.

## Workflow

1. Identify the public surface.
   - Inspect `pub` items, crate features, modules, re-exports, trait impls, error types, constructors, builders, macros, and documented examples.
   - Separate public API concerns from private implementation style.

2. Classify the task.
   - For a review, report only findings supported by `assets/checklist.md` or a topical guideline file.
   - For implementation, use only the relevant `assets/` files as the API standard.
   - For a new API, read the relevant `assets/` files before proposing an API shape.

3. Apply the bundled guidelines.
   - Read `assets/checklist.md` before giving broad API guidance.
   - Read the relevant topical `assets/*.md` file before making a detailed claim about a guideline code such as `C-CASE`, `C-GOOD-ERR`, or `C-STRUCT-PRIVATE`.
   - Read `assets/elegant-apis-in-rust.md` when the task benefits from supplemental ergonomic API-design heuristics that connect multiple guideline chapters.
   - If no bundled guideline supports a recommendation, present it as general engineering judgment rather than as part of this skill's standard.

4. Verify with Rust tooling when code changes are made.
   - Prefer the repo's existing commands.
   - Otherwise run the narrowest relevant commands, such as `cargo fmt`, `cargo test`, `cargo clippy --all-targets --all-features`, or `cargo doc --no-deps`.

## Review Output

When reviewing, organize findings by severity and include file/line references when available. Cite the supporting asset file and guideline code when available.

When implementing, summarize public API impact separately from internal changes, and cite the bundled asset files used for API guidance.

---
> Source: [linuxperf/infra-programmer-skills](https://github.com/linuxperf/infra-programmer-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
