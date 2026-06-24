---
name: rust-errors-lifetimes
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-errors-lifetimes

Decodes the six lifetime-related compiler error codes and gives one correct fix per code. A lifetime error almost always reports a **real soundness gap**: the signature promises something the body cannot prove. The fix is to make the relationship explicit, not to silence it.

Cross-references: [[rust-syntax-lifetimes]] (lifetime mechanics, elision rules, variance), [[rust-syntax-edition-2024]] (RPIT capture migration), [[rust-errors-borrow-checker]] (E0502/E0499/E0596 aliasing errors), [[rust-agents-compile-fix]] (automated compile-fix loop).

---

## When to use this skill

- The compiler reports `E0106`, `E0623`, `E0495`, `E0700`, `E0759`, or `E0515`.
- The user asks "why does my function need a lifetime annotation".
- The user asks why elision did not infer the output lifetime.
- A 2021-edition signature using `impl Trait` stopped compiling after `cargo fix --edition` to 2024.
- The user reaches for `'static` to make a lifetime error disappear.
- The user wants to return a reference but the value is local to the function.

---

## The one rule behind every lifetime error

> A reference must never outlive the data it points to. The signature is a contract; the body must prove it.

ALWAYS state this first. Every code below is a specific way that contract is unverifiable. The fix establishes the missing relationship; it does NOT bypass the check.

---

## Error code routing table

| Code | Message (canonical) | One-line cause | First fix to try |
|------|---------------------|----------------|------------------|
| E0106 | "missing lifetime specifier" | Elision rules cannot pick an output lifetime | Add `'a` or `'_` per elision analysis |
| E0623 | "lifetime mismatch" | Two unrelated input/output lifetimes used as if related | Add `'long: 'short` outlives bound, or unify to one lifetime |
| E0495 | "cannot infer an appropriate lifetime" | Output lifetime not constrained by any input | Add outlives bound, or use one lifetime for in and out |
| E0700 | "hidden type captures lifetime that does not appear in bounds" | RPIT return type references a lifetime not captured | Add `+ 'x` or `+ use<'x, ...>` to the return type |
| E0759 | RPIT/`dyn Trait` return needs `'static` but got a borrow | RPIT return implicitly requires `'static` | Add `+ '_` / `+ 'a` to the return type |
| E0515 | "cannot return reference to local variable" | Returned reference points at a value dropped at `}` | Return the owned value, or have the caller pass a buffer |

E0495 and E0759 are no longer emitted verbatim by current rustc (newer diagnostics phrase them differently), but the underlying situations and fixes are unchanged. Treat the wording as the symptom class, not a literal string match.

---

## E0106: missing lifetime specifier

`error[E0106]: missing lifetime specifier` means a type needs a lifetime parameter and none was given. The lifetime elision rules require any function with an elided output lifetime to have **either** exactly one input lifetime **or** a `&self` / `&mut self` receiver.

```rust
// FAILS E0106: a reference field with no lifetime parameter
struct Parser { input: &str }

// CORRECT: declare and use the lifetime
struct Parser<'a> { input: &'a str }
```

```rust
// FAILS E0106: two input lifetimes, no self, elision cannot choose
fn longest(a: &str, b: &str) -> &str { if a.len() > b.len() { a } else { b } }

// CORRECT: one named lifetime ties both inputs to the output
fn longest<'a>(a: &'a str, b: &'a str) -> &'a str {
    if a.len() > b.len() { a } else { b }
}
```

Decision: **named lifetime `'a`** when you must relate several references; **anonymous `'_`** when the lifetime is forced and you only want to silence the "elided" warning (e.g. `fn parse(&self) -> Token<'_>`). NEVER guess `'static` here.

See `references/methods.md` for the three elision rules and `references/examples.md` for the elision-doesn't-fire decision tree.

---

## E0623 / E0495: unrelated input and output lifetimes

`error[E0623]: lifetime mismatch` and `cannot infer an appropriate lifetime` are the same defect: the body produces a reference with one lifetime, the signature demands another, and no bound links them.

```rust
// FAILS: returns a &'in_ but signature promises &'out, unrelated
fn first<'in_, 'out, T>(x: &'in_ T) -> &'out T { x }
```

Two correct fixes:

```rust
// Fix A: outlives bound. 'in_ must live at least as long as 'out.
fn first<'in_: 'out, 'out, T>(x: &'in_ T) -> &'out T { x }

// Fix B (preferred when nothing forces two lifetimes): unify to one.
fn first<'a, T>(x: &'a T) -> &'a T { x }
```

ALWAYS prefer Fix B (one lifetime) unless an external API forces the two-lifetime shape. Two lifetimes plus an outlives bound is strictly more general but harder to read; introduce it only when callers genuinely need the freedom.

NEVER "fix" E0623 by changing the output to `&'static T`. That over-constrains every caller to pass `'static` data and just relocates the error to the call site.

---

## E0700: hidden type captures a lifetime not in bounds (RPIT)

`error[E0700]: hidden type for impl Trait captures lifetime that does not appear in bounds`. The `impl Trait` return type only captures lifetimes its rules say it captures; if the concrete returned value references another lifetime, the contract breaks.

```rust
use std::cell::Cell;
trait Trait<'a> {}
impl<'a, 'b> Trait<'b> for Cell<&'a u32> {}

// FAILS E0700: returns Cell<&'x u32>, signature only mentions 'y
fn foo<'x, 'y>(x: Cell<&'x u32>) -> impl Trait<'y> where 'x: 'y { x }

// CORRECT: add the hidden lifetime to the return bound
fn foo<'x, 'y>(x: Cell<&'x u32>) -> impl Trait<'y> + 'x where 'x: 'y { x }
```

In edition 2024 the modern fix is the **precise capturing** bound `+ use<...>`:

```rust
fn foo<'x, 'y>(x: Cell<&'x u32>) -> impl Trait<'y> + use<'x, 'y> where 'x: 'y { x }
```

See "Edition 2024 RPIT capture" below for which lifetimes are captured by default.

---

## E0759: RPIT / dyn Trait return implicitly requires 'static

A bare `impl Trait` or `Box<dyn Trait>` return type carries an implicit `'static` bound. Returning a borrow violates it.

```rust
use std::fmt::Debug;

// FAILS: impl Debug return implies 'static, but x is borrowed
fn show(x: &i32) -> impl Debug { x }

// CORRECT: add an explicit lifetime bound to the return type
fn show(x: &i32) -> impl Debug + '_ { x }

// Equivalent with a named lifetime
fn show2<'a>(x: &'a i32) -> impl Debug + 'a { x }

// dyn Trait form
fn boxed(x: &i32) -> Box<dyn Debug + '_> { Box::new(x) }
```

Decision: add `+ '_` (or `+ 'a`) to let the return value borrow. Only require `'static` (`x: &'static i32`) when the value genuinely is static; do not push `'static` onto callers to dodge the annotation.

---

## E0515: cannot return reference to local variable

`error[E0515]: cannot return reference to local variable`. Local variables, parameters, and temporaries are dropped at the closing `}`; a returned reference to one would dangle.

```rust
// FAILS E0515: x is dropped at }, &x would dangle
fn make() -> &'static i32 { let x = 0; &x }

// Fix A: return the owned value
fn make() -> i32 { let x = 0; x }
```

```rust
// FAILS E0515: v is dropped, v.iter() borrows it
fn nums<'a>() -> std::slice::Iter<'a, i32> { let v = vec![1, 2, 3]; v.iter() }

// Fix B: return an owning iterator
fn nums() -> std::vec::IntoIter<i32> { let v = vec![1, 2, 3]; v.into_iter() }
```

```rust
// Fix C: caller owns the buffer, function borrows it
fn fill<'a>(buf: &'a mut String) -> &'a str { buf.push_str("data"); buf }
```

Decision: return **owned** (`String`, `Vec<T>`, `IntoIter`) when the function created the data; have the **caller pass a `&mut` buffer** when allocation should be the caller's choice. NEVER `Box::leak` to manufacture a `'static` reference; that is a permanent memory leak (see `references/anti-patterns.md`).

---

## Edition 2024 RPIT lifetime capture

The single most consequential lifetime change between editions.

> In Rust 2024, **all** in-scope generic parameters, including lifetime parameters, are implicitly captured when the `use<..>` bound is not present.

Pre-2024, an `impl Trait` return captured a lifetime only if it appeared syntactically in the bounds. Side by side:

```rust
fn f_implicit(_: &()) -> impl Sized {}

// Rust 2021 and earlier: behaves as if written with use<>
fn f_2021(_: &()) -> impl Sized + use<> {}

// Rust 2024 and later: behaves as if written with use<'_>
fn f_2024(_: &()) -> impl Sized + use<'_> {}
```

Consequence: a 2021 signature that compiled because the return type did **not** capture an input lifetime may stop compiling under 2024, because now it does (the opaque type becomes more constrained).

Migration: `cargo fix --edition` runs the `impl_trait_overcaptures` lint (part of `rust-2024-compatibility`) and inserts `+ use<>` to preserve the old, narrower capture.

```rust
// 2021 source
fn g<'a>(x: &'a ()) -> impl Sized { *x }

// after cargo fix --edition: explicit opt-out keeps 2021 semantics
fn g<'a>(x: &'a ()) -> impl Sized + use<> { *x }
```

`impl_trait_overcaptures` cannot auto-fix argument-position `impl Trait` (APIT) cases: those require naming the parameter so it can appear in `use<...>`. See `references/examples.md` for the APIT migration.

NEVER copy a pre-2024 `impl Trait` signature into 2024 code without checking capture: `+ use<...>` is the explicit control. Default (no `use<...>`) now captures everything in scope.

---

## Fix-selection decision tree

```
Lifetime error?
├─ "missing lifetime specifier" (E0106)
│   ├─ on a struct/enum/type alias  -> declare <'a>, use &'a T
│   ├─ fn, one input lifetime       -> elision should fire; if not, add '_
│   └─ fn, 2+ inputs, no &self      -> add named <'a>, tie inputs+output
├─ "lifetime mismatch" / "cannot infer" (E0623 / E0495)
│   ├─ caller needs two lifetimes   -> add 'long: 'short outlives bound
│   └─ otherwise                    -> unify to one lifetime 'a
├─ impl Trait return + borrowed value
│   ├─ "captures lifetime not in bounds" (E0700) -> add + 'x or + use<'x,..>
│   └─ "requires 'static" (E0759)               -> add + '_ or + 'a
├─ "return reference to local variable" (E0515)
│   ├─ function created the data    -> return owned (String/Vec/IntoIter)
│   └─ allocation is caller's job   -> caller passes &mut buffer
└─ 2021 signature broke after edition bump
    -> add + use<> (narrow) or + use<'a, T> (explicit), see Edition 2024 section
```

---

## Reference files

- `references/methods.md`, the three lifetime elision rules verbatim, `'static` reference vs `T: 'static` bound, HRTB (`for<'a>`), variance table, `use<...>` capturing semantics, error-code catalogue.
- `references/examples.md`, full failing-then-fixed examples for every code, the elision-doesn't-fire decision tree, the 2024 RPIT before/after migration including the APIT case.
- `references/anti-patterns.md`, the reflexive `'static`, lifetime-on-every-parameter noise, `Box::leak`, premature `.clone()`, copying pre-2024 RPIT signatures, and more.

## Sources

- E0106: https://doc.rust-lang.org/error_codes/E0106.html
- E0623: https://doc.rust-lang.org/error_codes/E0623.html
- E0495: https://doc.rust-lang.org/error_codes/E0495.html
- E0700: https://doc.rust-lang.org/error_codes/E0700.html
- E0759: https://doc.rust-lang.org/error_codes/E0759.html
- E0515: https://doc.rust-lang.org/error_codes/E0515.html
- Edition 2024 RPIT lifetime capture: https://doc.rust-lang.org/edition-guide/rust-2024/rpit-lifetime-capture.html
- Lifetime elision: https://doc.rust-lang.org/reference/lifetime-elision.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
