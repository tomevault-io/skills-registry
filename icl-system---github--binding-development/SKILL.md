---
name: binding-development
description: Use when creating or modifying language bindings (Python/JS/Go) that wrap the Rust core. Covers PyO3, wasm-pack, cgo binding patterns, type conversion, error mapping, and package publishing.
metadata:
  author: icl-system
---

# Binding Development

## When to Use

- Working in `bindings/python/`, `bindings/javascript/`, or `bindings/go/`
- Exposing a new `icl-core` function to a language
- Debugging binding issues (type conversion, FFI)
- Publishing a package (PyPI, npm, crates.io)

## Context

Bindings are **thin wrappers** around `icl-core`. They convert language-native types to Rust types, call the Rust function, and convert back. Zero logic lives in bindings.

Reference files:
- `ICL-Docs/PLAN.md` — binding architecture diagram
- `crates/icl-core/src/lib.rs` — the API being wrapped
- `bindings/<language>/` — binding source

## Procedure

1. Identify which `icl-core` public function to expose
2. Write the binding function (type conversion + call + conversion back)
3. The binding function must match the Rust API signature semantically
4. Error handling: convert `icl-core::Error` → language-native exception/error
5. Add type hints/stubs:
   - Python: `.pyi` stub file
   - JavaScript: `.d.ts` TypeScript definitions
   - Go: godoc comments
6. Write test: binding produces **identical result** to calling Rust directly
7. Test with the language's standard tooling (`pytest`, `npm test`, `go test`)
8. Verify package builds (`maturin build`, `wasm-pack build`, `go build`)

## Rules

- **ZERO logic in bindings** — if you're writing an `if` statement, it belongs in `icl-core`
- **All bindings wrap the same Rust functions** — identical semantics guaranteed
- **Error types map cleanly** — don't swallow errors or change error messages
- **Bindings are versioned in lockstep** — all packages share the same version number
- **Type conversions are explicit** — no implicit coercion between language types and Rust types

## Architecture

```
Python/JS/Go code
       ↓
Thin binding layer (~80-100 lines)
  - Convert native types → Rust types
  - Call icl-core function
  - Convert Rust output → native types
  - Convert Rust error → native exception
       ↓
icl-core (Rust) — ALL logic here
```

## Technology per Language

| Language | Tool | Package format |
|----------|------|---------------|
| Python | PyO3 + maturin | Wheel → PyPI |
| JavaScript | wasm-pack | WASM → npm |
| Go | cgo + cbindgen | Shared library → go module |

## Anti-Patterns

- Reimplementing parsing/validation in the binding language
- Adding "convenience functions" that don't exist in icl-core
- Different error handling behavior between bindings
- Publishing bindings at different versions than core
- Not generating type stubs/definitions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icl-system) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
