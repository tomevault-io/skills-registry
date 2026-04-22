---
name: adk-rust-eval-observability-guardrails
description: Add evaluation, telemetry, and guardrails to ADK-Rust systems as one quality loop. Use when hardening agent behavior before production release. Use when this capability is needed.
metadata:
  author: zavora-ai
---

# ADK Rust Eval Observability Guardrails

## Overview
Treat eval, telemetry, and guardrails as a single production hardening system.

## Workflow
1. Define measurable eval criteria and failure thresholds.
2. Add telemetry spans for model, tool, and callback paths.
3. Add guardrails for PII/content/schema constraints.
4. Run eval suites and compare outcomes to thresholds.

## Guardrails
1. Block promotion when eval criteria regress.
2. Ensure telemetry includes request correlation IDs.
3. Keep guardrail severity and remediation policy explicit.

## References
- Use `references/eval-telemetry-guardrails.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zavora-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
