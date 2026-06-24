---
name: rust-errors-trait-bounds
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# Rust Trait Bound Errors

Diagnose and fix the five trait-related rustc error codes. The root cause is almost always one of four things: a missing trait import, a missing bound on a generic, a missing trait implementation, or an inference failure that only looks trait-related.

## Quick Reference

| Code | Message shape | Most common cause | First fix to try |
|------|---------------|-------------------|------------------|
| E0277 | `the trait bound \`T: Trait\` is not satisfied` | Generic lacks the bound, or type never implements the trait | Add `T: Trait` to the generic, or `#[derive(Trait)]` on the type |
| E0599 | `no method named \`m\` found for type \`T\`` | Trait that defines the method is not imported | `use the::Trait;` to bring the method into scope |
| E0220 | `associated type \`A\` not found for \`Trait\`` | Wrong associated type name | Use the exact name the trait declares (e.g. `Item`, `Output`) |
| E0308 | `mismatched types` | Concrete vs `dyn Trait`, or `impl Trait` unification failure | Box the trait object, or align the two concrete types |
| E0282 | `type annotations needed` | Inference has no anchor type | Add a `let x: T` annotation or a turbofish `::<T>()` |

ALWAYS read the full error block, not just the first line. Rustc prints `the following trait bounds were not satisfied` and a `help:` note listing the exact missing bound and the source crate. That detail names the fix.

## Decision Tree: Which Trait Error Do I Have?

```
rustc rejects trait-related code
├── "no method named ..." ............................. E0599
│   ├── method exists on a trait? .................. add `use TheTrait;`
│   ├── typo in method name? ....................... fix spelling
│   ├── method takes `&mut self`, value is `&`? .... make the binding `mut` / take `&mut`
│   └── method behind a Cargo feature? ............. enable the feature in Cargo.toml
├── "the trait bound `T: Trait` is not satisfied" .... E0277
│   ├── `T` is a generic parameter? ................ add `T: Trait` (see decision tree below)
│   ├── `T` is a concrete type you own? ............ `#[derive(Trait)]` or hand-write `impl Trait for T`
│   ├── `T` is a foreign type? ..................... wrap it (newtype) or pick a type that impls it
│   └── impl exists but gated? ..................... enable the dependency feature that provides it
├── "associated type `A` not found" .................. E0220
│   └── use the exact associated-type name the trait declares
├── "mismatched types" with a trait in the text ...... E0308
│   ├── concrete value where `dyn Trait` expected? . `Box::new(value)` / `&value as &dyn Trait`
│   ├── two `impl Trait` returns disagree? ......... return one named type, or `Box<dyn Trait>`
│   └── otherwise align the concrete types
└── "type annotations needed" ........................ E0282
    └── add `let x: T`, turbofish `::<T>()`, or fully-qualified syntax
```

## Decision Tree: Missing where-clause vs Missing impl vs Missing import

When you see E0277, decide WHERE the fix belongs before touching code.

```
E0277: `T: SomeTrait` is not satisfied
│
├── Is `T` a generic type parameter (declared with `<T>`)?
│   │
│   ├── YES → the bound is MISSING from the signature.
│   │        Fix: add `T: SomeTrait` to the `<T>` list or a `where` clause.
│   │        Rust checks function bodies against the SIGNATURE only, so
│   │        every trait the body uses must be declared on the signature.
│   │
│   └── NO  → `T` is a concrete type. The trait is genuinely UNIMPLEMENTED.
│            │
│            ├── You own `T` → `#[derive(SomeTrait)]` if derivable,
│            │                  else hand-write `impl SomeTrait for T`.
│            │
│            └── `T` is foreign (std or another crate) →
│                 orphan rule blocks a direct impl. Use a newtype
│                 wrapper, or change `T` to a type that already impls it.
│
└── Does a `help:` note say "the trait `SomeTrait` is implemented for `U`"?
    → an impl EXISTS for a near-miss type. You passed the wrong type,
      or need a conversion (`.into()`, `&x`, `x.as_ref()`).
```

NEVER add a bound to a generic before confirming the body actually requires it. If the body never calls a method from `SomeTrait`, the bound is noise. See `references/anti-patterns.md`.

## E0277: Trait Bound Not Satisfied

`the trait bound \`i32: Foo\` is not satisfied` means a type was used where a trait was required. Rust checks the called function's SIGNATURE only, so the signature must declare every requirement.

```rust
// edition 2024
trait Foo {
    fn bar(&self);
}

// WRONG: body calls foo.bar() but T has no bound
fn run<T>(foo: T) {
    foo.bar(); // E0277: `T: Foo` is not satisfied
}

// FIX 1: add the bound the body needs
fn run<T: Foo>(foo: T) {
    foo.bar();
}
```

```rust
// FIX 2: the type is yours and never implemented the trait → derive or impl
#[derive(Debug, Clone, PartialEq)] // derive supplies the impls
struct Point { x: i32, y: i32 }

// FIX 3: hand-write the impl when no derive exists
impl std::fmt::Display for Point {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}
```

For a foreign type you cannot impl directly (orphan rule), wrap it in a newtype. See `references/examples.md`.

## E0599: Method Not Found in Scope

`no method named \`m\` found for type \`T\` in the current scope` most often means the trait providing `m` is not imported. A trait method is only callable when the trait is in scope.

```rust
use std::io::Write; // WITHOUT this line, .flush() and .write_all() raise E0599

fn dump(out: &mut impl Write) -> std::io::Result<()> {
    out.write_all(b"data")?;
    out.flush()
}
```

Rustc's E0599 output frequently prints `items from traits can only be used if the trait is in scope` and lists the exact `use` path. Apply that path verbatim. Other E0599 causes: a misspelled method name, calling a `&mut self` method through a `&` binding, or a method gated behind a Cargo feature. See `references/methods.md`.

## E0220: Associated Type Not Found

`associated type \`F\` not found for \`T1\`` means you named an associated type the trait does not declare.

```rust
trait Container {
    type Item; // the ONLY associated type this trait declares
}

// WRONG: `Element` is not declared
// fn first<C: Container<Element = i32>>(c: C) {} // E0220

// FIX: use the exact declared name
fn first<C: Container<Item = i32>>(_c: C) {}
```

The same error fires inside a trait body when you reference `Self::Baz` without declaring `type Baz;`. Declare every associated type before referencing it.

## E0308: Mismatched Types (Trait-Related Cases)

`mismatched types` is general, but trait-flavoured cases have specific fixes.

```rust
trait Shape { fn area(&self) -> f64; }
struct Circle; struct Square;
impl Shape for Circle { fn area(&self) -> f64 { 3.14 } }
impl Shape for Square { fn area(&self) -> f64 { 4.0 } }

// WRONG: an array needs ONE element type; Circle and Square differ → E0308
// let shapes = [Circle, Square];

// FIX: erase the concrete type behind a trait object
let shapes: Vec<Box<dyn Shape>> = vec![Box::new(Circle), Box::new(Square)];
```

A function returning `impl Trait` must return exactly ONE concrete type on every path. Two different types on two branches raise E0308. Return `Box<dyn Trait>` when the types genuinely differ. See `references/examples.md`.

## E0282: Type Annotations Needed

`type annotations needed` is an INFERENCE failure, not a bound failure. Inference found no unique type and needs an anchor.

```rust
// WRONG: nothing tells the compiler what T is
// let v = Vec::new(); // E0282

// FIX 1: annotate the binding
let v: Vec<i32> = Vec::new();

// FIX 2: turbofish on the constructor
let v = Vec::<i32>::new();

// FIX 3: turbofish on a collecting method
let squares = (1..5).map(|n| n * n).collect::<Vec<i32>>();

// FIX 4: partial annotation, let inference fill the rest
let chars: Vec<_> = "abc".chars().collect();
```

Distinguish E0282 from E0277: E0282 says "needs annotation" (no type chosen yet); E0277 says "bound not satisfied" (a type WAS chosen and fails a requirement). NEVER paper over a deeper design problem with a turbofish. See `references/anti-patterns.md`.

## The 1.84 Next-Generation Trait Solver

The next-generation trait solver is a reimplementation of the component that checks trait bounds, normalization, and type equality. As of Rust 1.84.0 (January 2025), it is used ONLY for coherence checking of trait impls, and that use is the DEFAULT, not opt-in.

Deterministic facts:

- For coherence checking, the new solver is the DEFAULT in 1.84+. It is not behind a flag.
- For everything else (checking bounds in function bodies, method resolution), the OLD solver is still the default. The new solver can be opted into experimentally with `-Znext-solver` on a nightly toolchain only. NEVER rely on `-Znext-solver` in stable or production builds.
- The new solver fixes mostly theoretical correctness issues. It may report a few new `conflicting implementations of trait ...` errors that the old solver missed. The Rust team's Crater run found affected patterns to be very rare.
- It also proves non-overlap more precisely, so some previously rejected impls now compile.
- Impact on diagnostics: phrasing of trait errors can shift slightly under the new solver. The error CODES (E0277 etc.) and the fixes in this skill stay the same.

When a coherence error appears only after upgrading to 1.84+, treat it as a genuine overlap the old solver missed. Make the impls non-overlapping. Do not assume it is a compiler bug.

## Cross-References

- [[rust-syntax-traits]] : declaring traits, impl blocks, blanket impls, the orphan rule
- [[rust-syntax-generics]] : generic parameters, `where` clauses, bound placement
- [[rust-syntax-trait-objects]] : `dyn Trait`, boxing, dyn-compatibility
- [[rust-agents-compile-fix]] : ordered workflow for resolving a cascade of compiler errors

## Reference Files

- `references/methods.md` : every error code with full message text, all fix variants, the new-solver flag detail
- `references/examples.md` : complete compilable before/after programs for each error code
- `references/anti-patterns.md` : the bound-spam, wrong-import, and turbofish-masking traps

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
