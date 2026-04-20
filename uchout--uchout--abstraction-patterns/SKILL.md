---
name: rust-abstraction-patterns
description: Use when writing Rust structs with generic type parameters, implementing traits for polymorphism, designing service/client/handler layers, choosing between Box<dyn Trait> vs enum vs generics, adding middleware (retry, logging, caching), wrapping or decorating existing types, forwarding methods to inner fields, or refactoring to reduce duplication across similar structs.
metadata:
  author: uchout
---

# Rust Abstraction Patterns

## Files

| File | Content |
|------|---------|
| `generics-pattern.md` | Generic parameters, infection chain, meaningful generics |
| `reuse-patterns.md` | Wrapper (composition), trait polymorphism (dispatch), extension traits |

## When to Use

- Composing behaviors (retry, logging, caching)
- Enhancing existing types without modification
- Choosing dispatch strategy (enum vs generic vs dyn)
- Cutting generic infection in deep struct hierarchies
- Adding methods to foreign types

## Prerequisites

- **rust-over-engineering** - Abstraction summarizes existing code, not predicts future cases. Start concrete first.

## Workflow

1. **Adding generics to structs?** → Read `generics-pattern.md` first
2. **Enhancing/decorating types?** → Use wrapper pattern in `reuse-patterns.md`
3. **Finite polymorphism?** → Use enum dispatch
4. **Need to cut generic infection?** → Enum at appropriate layer
5. **Plugin system or heterogeneous collection?** → `dyn Trait` is acceptable

## Priority

| Pattern | Priority | Use Case |
|---------|----------|----------|
| Generic methods | High | Flexible APIs without struct infection |
| Wrapper (Inner) | High | Layer enhancement, decoration |
| Enum dispatch | High | Finite variants, cut infection |
| Extension traits | Medium | Add methods to foreign types |
| `dyn Trait` | Low | Only when static dispatch impossible |

## Quick Reference

- **Wrapper**: `struct Wrapper<T> { inner: T }` - composable, stackable
- **Enum dispatch**: Known variants, no vtable, cuts infection
- **Generic method**: `fn process<T>(&self, arg: T)` - no struct infection
- **Generic struct**: `struct S<T>` - infects all layers above, use cautiously

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uchout) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
