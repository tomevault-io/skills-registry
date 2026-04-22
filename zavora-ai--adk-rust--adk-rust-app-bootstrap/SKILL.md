---
name: adk-rust-app-bootstrap
description: Bootstrap new ADK-Rust applications with correct crate/features, provider setup, and verification steps. Use when starting a new agent app or converting an existing Rust app to ADK-Rust. Use when this capability is needed.
metadata:
  author: zavora-ai
---

# ADK Rust App Bootstrap

## Overview
Set up a new ADK-Rust app with stable defaults and immediate verification.

## Workflow
1. Choose dependency scope: `adk-rust` umbrella crate or focused per-crate dependencies.
2. Select provider feature flags and required environment variables.
3. Start with the smallest runnable `LlmAgentBuilder` path.
4. Validate with `cargo check` and one runtime smoke command.

## Guardrails
1. Keep first iteration minimal and deterministic.
2. Add optional crates only when the app needs them.
3. Verify feature flags against examples before coding custom abstractions.

## References
- Use `references/bootstrap-checklist.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zavora-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
