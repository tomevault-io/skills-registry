---
name: webauthn-conformance-negative
description: Use when adding or hardening negative-path tests for ceremony and validation behaviors.
metadata:
  author: szijpeter
---

# WebAuthn Conformance Negative

## Trigger

Use when adding strict validation behavior in core/model/serialization.

## Workflow

1. Add one positive-path and one negative-path test per rule change.
2. Prefer explicit error expectations and stable fixtures.
3. Validate with module-targeted `allTests` or `test` tasks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/szijpeter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
