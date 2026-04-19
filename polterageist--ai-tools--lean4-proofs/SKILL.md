---
name: lean4-proofs
description: Guidelines for theorem proving in Lean 4, including project setup and proof conventions. Use when this capability is needed.
metadata:
  author: polterageist
---

# Lean 4 Proofs

## Setup

```bash
lake init MyProject
lake build
```

## Structure

```
lean/
├── lakefile.lean
├── lean-toolchain
├── Main.lean
└── MyProject/
```

## Naming

- Types: `PascalCase`
- Terms/theorems: `camelCase`
- Namespaces: `PascalCase`

## Tactics

```lean
-- Basic: intro, apply, exact, rfl, simp, ring
-- Structural: have, let, show, calc
-- Case analysis: cases, induction, rcases
-- Finishing: trivial, contradiction, omega
```

## Best Practices

- Start with `sorry` placeholders
- Build incrementally with `lake build`
- Leverage Mathlib when appropriate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/polterageist) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
