---
name: rust-design-patterns
description: Choose, review, or apply Rust idioms, design patterns, anti-patterns, FFI patterns, functional patterns, and refactoring guidance using only the bundled Rust Design Patterns files in this skill's assets directory as the pattern standard. Use when Codex works on Rust architecture, API shape, ownership-oriented design, trait-based design, unsafe containment, builders, newtypes, RAII, command/interpreter/visitor/strategy patterns, FFI wrappers, or anti-pattern review. Use when this capability is needed.
metadata:
  author: linuxperf
---

# Rust Design Patterns

## Overview

Use this skill to review or revise Rust code against the bundled Rust Design Patterns files in `assets/`. Do not introduce pattern rules, examples, or recommendations that are not grounded in those files.

## Pattern Assets

Start with `assets/SUMMARY.md` to see the available local chapters.

Load the matching chapter before making a pattern recommendation. If no chapter applies, present the recommendation as general engineering judgment rather than as part of this skill's pattern standard.

## Workflow

1. Identify the design problem.
   - Inspect the relevant Rust code, public API, ownership flow, trait bounds, error flow, FFI boundary, unsafe boundary, or repeated structure.
   - Distinguish a real recurring design problem from a local implementation detail.

2. Select local source material.
   - Read `assets/SUMMARY.md`.
   - Read the most relevant chapter file before naming a pattern, anti-pattern, idiom, or refactoring.
   - If several chapters may apply, compare them briefly and choose the one that best matches the code.

3. Apply only bundled guidance.
   - Ground recommendations in the selected `assets/` file.
   - If no bundled chapter supports a recommendation, present it as general engineering judgment rather than as part of this skill's pattern standard.
   - Prefer the repository's existing style and constraints over introducing a pattern for its own sake.

4. Verify with Rust tooling when code changes are made.
   - Prefer the repo's existing commands.
   - Otherwise run the narrowest relevant commands, such as `cargo fmt`, `cargo test`, `cargo clippy --all-targets --all-features`, or `cargo doc --no-deps`.

## Output

When reviewing, organize findings by severity and include file/line references when available. Cite the supporting bundled asset file for each pattern-based recommendation.

When implementing, summarize the pattern or idiom applied, the public API or architecture impact, and the bundled asset files used for guidance.

---
> Source: [linuxperf/infra-programmer-skills](https://github.com/linuxperf/infra-programmer-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
