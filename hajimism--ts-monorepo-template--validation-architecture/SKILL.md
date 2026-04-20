---
name: validation-architecture
description: Validation design rationale. Use when asking why validation works this way, understanding the layered validation strategy, or making architectural decisions about validation. Use when this capability is needed.
metadata:
  author: hajimism
---

# Validation Architecture

## Layer Responsibilities

```
core/           → SSOT: constraints + schemas (ArkType, Standard Schema)
api/            → Gatekeeper: reject invalid requests (sValidator)
front/          → UX: phase-based feedback (withValidationErrorMessages)
```

## Design Decisions

### Why constraints separate from schemas?
- Constraints are plain values (reusable in messages, tests, etc.)
- Schemas are validation logic (may change libraries)
- Types derived from constraints (SSOT)

### Why Standard Schema?
- Library-agnostic interface (`@standard-schema/spec`)
- ArkType today, can swap to Zod/Valibot later
- Both api (sValidator) and front use same interface

### Why phase-based validation in front?
- `onChange`: lenient (no required checks) → no red during typing
- `onDraftSubmit`: intermediate
- `onConfirmedSubmit`: strict (all rules) → block invalid submit

Core schema is for "final correctness". Front needs UX timing control.

### Why validate twice (front + api)?
- Front validation: UX improvement (optional, can fail)
- API validation: security boundary (required, never trust client)

## Key Files

- `core/src/model/*/constraints.ts` - constraint values
- `core/src/model/*/schema.ts` - ArkType schemas
- `api/src/model/*/index.ts` - sValidator usage
- `front/src/model/common/lib/validation.ts` - phase-based utilities
- `front/src/model/*/detail/form/inputs/*/validation.ts` - field validation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hajimism) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
