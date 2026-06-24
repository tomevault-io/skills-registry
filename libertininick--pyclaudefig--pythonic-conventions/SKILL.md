---
name: pythonic-conventions
description: Essential Pythonic idioms and conventions. Apply when writing or reviewing Python code to ensure idiomatic patterns like comprehensions, built-in functions, context managers, and unpacking. Use when this capability is needed.
metadata:
  author: libertininick
---

# Pythonic Conventions

Essential Python idioms that make code more readable, concise, and efficient.

**Layers**:
- `rules.md` - Quick reference and concise rules
- `examples.md` - Detailed code examples

## Quick Reference

| Pattern | Use Instead Of |
|---------|----------------|
| List/dict/set comprehensions | Manual loops to build collections |
| `enumerate()` | Manual index tracking |
| `zip()` | Manual parallel iteration |
| `any()` / `all()` | Loop with flag variable |
| Context managers (`with`) | Manual resource cleanup |
| Unpacking | Index access for known structures |
| `in` operator | Manual membership loops |
| Walrus operator (`:=`) | Separate assignment + condition |
| Generator expressions | List comprehension when iterating once |
| `defaultdict` / `Counter` | Manual dict initialization |
| Modern generics (`[T]` syntax) | `TypeVar` declarations |

For rules: see `rules.md`
For examples: see `examples.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/libertininick) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
