---
name: symfonyapi-platform-filters
description: Deliver robust API Platform contracts in Symfony with explicit operations, mapping, and policy-safe behavior. Use for api platform filters tasks. Use when this capability is needed.
metadata:
  author: makfly
---

# Api Platform Filters (Symfony)

## Use when
- Designing or evolving API Platform contracts and operations.
- Aligning serialization, validation, and security behavior.

## Default workflow
1. Define operation-level contract and payload boundaries.
2. Implement resource/DTO/provider/processor changes with explicit mapping.
2. Apply operation-specific validation and security constraints.
2. Validate functional behavior across happy and negative paths.

## Guardrails
- Keep API contract explicit and version-aware.
- Avoid exposing internal entity fields implicitly.
- Prevent drift between docs and actual serialization.

## Progressive disclosure
- Use this file for execution posture and risk controls.
- Open references when deep implementation details are needed.

## Output contract
- API artifacts changed (resource/DTO/provider/processor).
- Contract/security decisions and rationale.
- Functional verification results.

## References
- `reference.md`
- `docs/complexity-tiers.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/makfly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
