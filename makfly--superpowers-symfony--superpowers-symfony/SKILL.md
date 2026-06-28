---
name: symfonyapi-platform-state-providers
description: Master API Platform v4 State Providers and Processors (ProviderInterface/ProcessorInterface) to decouple data retrieval and persistence from entities Use when this capability is needed.
metadata:
  author: MakFly
---

# Api Platform State Providers (Symfony)

## Use when
- Designing or evolving API Platform contracts and operations.
- Aligning serialization, validation, and security behavior.

## Default workflow
1. Define operation-level contract and payload boundaries.
2. Implement resource/DTO/provider/processor changes with explicit mapping.
3. Apply operation-specific validation and security constraints.
4. Validate functional behavior across happy and negative paths.

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
> Source: [MakFly/superpowers-symfony](https://github.com/MakFly/superpowers-symfony) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-28 -->
