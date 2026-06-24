---
name: rust-syntax-gats
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-syntax-gats

The **mechanics** of Generic Associated Types (GATs): how to declare an associated type that takes its own lifetime or type parameters, why the compiler frequently demands `where Self: 'a` bounds on lifetime GATs, why a trait that uses GATs ceases to be `dyn`-compatible, and the canonical patterns (lending iterator, callback registry, pointer family) that finally became expressible after the 1.65 stabilization.

Cross-references: [[rust-syntax-traits]] (regular associated types and trait mechanics) [[rust-syntax-generics]] (type and lifetime parameters) [[rust-syntax-lifetimes]] (how `'a` flows through signatures) [[rust-syntax-trait-objects]] (a trait with GATs is NOT dyn-compatible).

---

## When to use this skill

- User writes `type Item<'a>` or `type Output<T>` inside a trait
- User wants to write a `next()` whose returned value borrows from `&mut self` (lending iterator)
- User asks how to express "the associated type depends on a lifetime / type parameter that varies per call"
- User imports `frunk`, `fp-rust`, or another higher-kinded-type emulation crate to work around a missing GAT
- User hits `the trait ... is not dyn compatible` after adding `type X<'a>` and asks why
- User hits `the parameter type Self may not live long enough` on a GAT impl and needs the fix
- User confuses GAT bounds (`Self::Item<'a>: Bound`) with regular associated-type bounds (`Self::Item: Bound`)

For regular (non-generic) associated types see [[rust-syntax-traits]]. For `dyn Trait` consequences see [[rust-syntax-trait-objects]].

---

## What changed in Rust 1.65 (Nov 2022)

> "Generic Associated Types (GATs): Lifetime, type, and const generics can now be defined on associated types."
> [Source: Rust 1.65 release notes][rust165]

Before 1.65, an associated type could only be a single concrete type per impl. With GATs, the associated type becomes a **type family** indexed by generics declared on the associated type itself:

```rust
// Pre-GAT: Item is a single concrete type per impl.
trait IteratorOld {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}

// GAT: Item is a family parameterised by the borrow lifetime.
trait LendingIterator {
    type Item<'a> where Self: 'a;
    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>>;
}
```

ALWAYS reach for a regular associated type first. GATs are the right tool ONLY when the associated type must be parameterised at call time. NEVER add `'a` or `T` to an associated type "because it might be useful": you lose `dyn`-compatibility and add bound complexity.

---

## GAT declaration syntax (Reference)

The Reference covers GATs under `items/associated-items`:

```text
type IDENTIFIER GenericParams? ( : TypeParamBounds? )? WhereClause? ( = Type )? ;
```

[Source: Rust Reference: associated-items][ref-assoc]

Concretely, every GAT consists of:

1. An associated type declaration **with** generic parameters: `type Item<'a, T>;`
2. Optional bounds on the GAT's output: `type Item<'a>: Debug;`
3. Optional `where` clauses that constrain when the GAT may be used: `where Self: 'a`
4. In the impl, a definition that supplies the family: `type Item<'a> = &'a T;`

```rust
trait LendingIterator {
    type Item<'a>: 'a where Self: 'a;          // bound + where on the GAT
    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>>;
}

struct WindowsMut<'s, T> { slice: &'s mut [T], size: usize, pos: usize }

impl<'s, T> LendingIterator for WindowsMut<'s, T> {
    type Item<'a> = &'a mut [T] where Self: 'a;    // the family at the impl level
    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>> {
        if self.pos + self.size > self.slice.len() { return None; }
        let out = &mut self.slice[self.pos..self.pos + self.size];
        self.pos += 1;
        Some(out)
    }
}
```

ALWAYS repeat the generic parameter list AND every `where` clause from the trait on the impl-side `type Item<'a>` line. The compiler does not infer them.

---

## Quick reference table

| Concept | Trait declaration | Impl definition |
|---------|-------------------|-----------------|
| Lifetime GAT | `type Item<'a> where Self: 'a;` | `type Item<'a> = &'a T where Self: 'a;` |
| Type GAT | `type Output<T>;` | `type Output<T> = Wrap<T>;` |
| GAT with bound | `type Item<'a>: Debug where Self: 'a;` | `type Item<'a> = MyDbg where Self: 'a;` |
| Const GAT | `type Buf<const N: usize>;` | `type Buf<const N: usize> = [u8; N];` |
| Use inside method | `fn next<'a>(&'a mut self) -> Self::Item<'a>` | same signature, impl supplies body |
| Bound on use site | `where for<'a> T::Item<'a>: Send` | `for<'a>` quantifies over all lifetimes |
| `dyn Trait` | NOT allowed | Trait is not dyn-compatible (E0038) |

---

## The `where Self: 'a` requirement

The compiler frequently requires `where Self: 'a` on a lifetime GAT. The rule, stated by the GAT stabilization announcement:

> "An associated type that captures a lifetime `'a` from a method signature requires that `Self: 'a` is provable when the method is called."
> [Source: GAT stabilization post (paraphrased)][rust165]

Why: if `Self` is dropped or moved before `'a` ends, then `Self::Item<'a>` (which may borrow from `self`) would dangle. The bound forces the call site to prove `self` outlives `'a`.

```rust
trait LendingIterator {
    type Item<'a> where Self: 'a;    // <-- required
    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>>;
}
```

If you omit the `where Self: 'a`, the compiler emits a help suggesting you add it. **ALWAYS** accept that suggestion when the GAT can borrow from `self`. NEVER suppress it with `unsafe` tricks: the bound encodes a real soundness invariant.

When the GAT does NOT borrow from `self` (e.g. it is an owned `String` parameterised only nominally), you can omit `where Self: 'a`, but then the parameter `'a` serves no purpose and you should drop the GAT entirely.

---

## Pattern 1: LendingIterator (the canonical motivation)

The standard `Iterator` trait cannot return a value that borrows from `self`, because `type Item;` is a single concrete type and the borrow lifetime cannot appear in it. GATs solve this:

```rust
pub trait LendingIterator {
    type Item<'a> where Self: 'a;
    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>>;
}

// Example: iterate over mutable windows without allocating.
pub struct WindowsMut<'s, T> {
    slice: &'s mut [T],
    size: usize,
    start: usize,
}

impl<'s, T> LendingIterator for WindowsMut<'s, T> {
    type Item<'a> = &'a mut [T] where Self: 'a;
    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>> {
        if self.start + self.size > self.slice.len() { return None; }
        let out = &mut self.slice[self.start..self.start + self.size];
        self.start += 1;
        Some(out)
    }
}
```

Important: `LendingIterator` is NOT `Iterator`. Methods like `map`, `filter`, `collect` are not provided by std. The `itertools`, `streaming-iterator`, and `lending-iterator` crates supply combinators tailored to the lending shape.

ALWAYS pick `LendingIterator` when each yielded item must borrow from a mutable resource that you own. NEVER force the result through `Iterator` by cloning every item: that defeats the purpose.

---

## Pattern 2: Callback registry parameterised by lifetime

A registry that calls user-supplied callbacks, where each callback must accept a borrow of the registry's internal state, is naturally expressed with a lifetime GAT:

```rust
pub trait Callback {
    type Arg<'a>;
    fn call<'a>(&mut self, arg: Self::Arg<'a>);
}

pub struct LoggingCallback;
impl Callback for LoggingCallback {
    type Arg<'a> = &'a str;
    fn call<'a>(&mut self, arg: Self::Arg<'a>) {
        println!("got: {arg}");
    }
}
```

The registry can now dispatch to any `Callback` impl without knowing the concrete `Arg<'a>` until the call site fixes `'a`. This was impossible pre-GAT because `type Arg = &'_ str` could not name a borrowed `'a` that varied per call.

---

## Pattern 3: Pointer family indexed by lifetime

Express "a family of pointer-like wrappers parameterised by the borrow lifetime":

```rust
pub trait PointerFamily {
    type Pointer<'a, T: 'a>: Deref<Target = T> where Self: 'a;
}

pub struct RefFamily;
impl PointerFamily for RefFamily {
    type Pointer<'a, T: 'a> = &'a T where Self: 'a;
}

pub struct BoxFamily;
impl PointerFamily for BoxFamily {
    type Pointer<'a, T: 'a> = Box<T> where Self: 'a;   // 'a unused but required by trait
}
```

Now generic code can be written over `F: PointerFamily` without committing to `&T` vs `Box<T>` vs `Rc<T>` until instantiation. This is the GAT version of the "higher-kinded type" pattern that previously required `frunk` or hand-written workarounds.

---

## GAT bounds at the use site (`for<'a>` HRTB)

When code is generic over a type that **has** a GAT, you often need a Higher-Ranked Trait Bound:

```rust
fn consume_all<I>(mut iter: I)
where
    I: LendingIterator,
    for<'a> I::Item<'a>: std::fmt::Debug,    // HRTB across all lifetimes
{
    while let Some(item) = iter.next() {
        println!("{item:?}");
    }
}
```

`for<'a> I::Item<'a>: Debug` reads as "for every lifetime `'a`, the type `I::Item<'a>` implements `Debug`". This is the GAT analogue of the regular `I::Item: Debug` bound. Without `for<'a>`, the bound has no `'a` in scope and will not compile.

ALWAYS use `for<'a>` when constraining a lifetime GAT in generic code. NEVER pin the GAT to a specific lifetime (`I::Item<'static>: Debug`) unless you genuinely need only that one lifetime; doing so will reject most concrete impls.

---

## GATs and `dyn Trait` (object safety)

A trait that contains a GAT is **NOT** dyn-compatible. The Reference lists this directly in the dyn-compatibility rules:

> "No associated types with generics."
> [Source: Rust Reference: items/traits, dyn-compatibility][ref-traits]

```rust
trait LendingIterator { type Item<'a> where Self: 'a; /* ... */ }

fn bad(_x: &dyn LendingIterator) {}   // E0038: not dyn-compatible
```

If you need dynamic dispatch over a GAT-bearing trait, you must:

1. Wrap each concrete impl in a struct that erases the lifetime via owned values (clone or move), OR
2. Expose a non-GAT companion trait whose method does the work in a closure-friendly form (`Box<dyn FnMut(&[T])>` style), OR
3. Use the `dyn-clone` / `erased-serde` style manual vtable pattern for your specific case.

ALWAYS check the dyn-compatibility consequence before adding a GAT to a trait that downstream users may want to box behind `dyn Trait`. NEVER add a GAT for a "maybe useful in the future" reason: it permanently removes `dyn` dispatch from the trait.

See [[rust-syntax-trait-objects]] for the full E0038 reason list.

---

## Decision tree: regular associated type vs GAT

```
Need to associate a type with the trait impl?
|
+-- Does the type need to vary PER CALL with a lifetime or type parameter?
|   |
|   +-- NO  -> regular associated type:  type Item;
|   +-- YES -> step deeper
|
+-- Does the variation come from a lifetime that BORROWS FROM self?
|   |
|   +-- YES -> lifetime GAT with `where Self: 'a`:  type Item<'a> where Self: 'a;
|   +-- NO  -> step deeper
|
+-- Does the variation come from a generic type parameter chosen by the caller?
|   |
|   +-- YES -> type GAT:  type Output<T>;
|   +-- NO  -> step deeper
|
+-- Is this a const-indexed family (e.g. fixed-size buffers)?
    |
    +-- YES -> const GAT:  type Buf<const N: usize>;
    +-- NO  -> reconsider; you probably do not need a GAT.
```

ALWAYS justify each generic on an associated type. NEVER add `'a` "just in case": every generic on a GAT increases the where-clause surface and breaks `dyn` compatibility.

---

## Common compile errors and fixes

| Error | Trigger | Fix |
|-------|---------|-----|
| E0106 | Missing lifetime in GAT method signature | Name the lifetime explicitly: `fn next<'a>(&'a mut self) -> Option<Self::Item<'a>>` |
| E0309 (Self may not live long enough) | Lifetime GAT borrows from `self` without `where Self: 'a` | Add `type Item<'a> where Self: 'a;` to trait AND impl |
| E0277 | `for<'a> T::Item<'a>: Debug` not satisfied | Implement `Debug` on the concrete `Item<'a>` for every `'a`, or relax the bound |
| E0038 | Used `dyn TraitWithGAT` | Remove the `dyn`, use `impl TraitWithGAT` or generic parameter, or design a non-GAT companion trait |
| E0478 | GAT outlives bound not met | Add the matching `: 'a` bound on the GAT output, or fix the impl-side `where` |
| E0658 | `type Item<'a>` on stable below 1.65 | Upgrade toolchain to >= 1.65 (skill targets 1.85+) |

Full E-code drilldown lives in [[rust-errors-trait-bounds]].

---

## Section: Avoid these mistakes

(Full list with WHYs in `references/anti-patterns.md`.)

- Using a GAT when a plain associated type suffices. Cost: lost `dyn`-compatibility and extra bounds with zero benefit.
- Forgetting `where Self: 'a` on a lifetime GAT that borrows from `self`. Compiles only when the impl-side type is owned; fails the moment a real borrow is introduced.
- Adding `type Item<'a>` to a published trait and shipping it. Breaking change: every existing `dyn Trait` use site now fails with E0038.
- Reaching for `frunk` / `fp-rust` / hand-rolled higher-kinded-type emulation in 2025. GAT solves the same problem with first-class language support since 1.65.
- Naming a lifetime GAT and a non-GAT associated type the same way (`type Item;` in one impl, `type Item<'a>;` in another) for "compatibility". They are different trait shapes; pick one.
- Using `Self::Item<'static>: Bound` to dodge a missing `for<'a>` HRTB. Almost no real impl satisfies `'static`; the bound is unusable.
- Putting the `where Self: 'a` clause only on the trait OR only on the impl. Must appear on BOTH (the impl repeats it verbatim).
- Implementing both `Iterator` and `LendingIterator` for the same type with different `Item` shapes and assuming combinators interoperate. They do not; pick one trait per type.

---

## Reference links

[ref-assoc]: https://doc.rust-lang.org/reference/items/associated-items.html
[ref-traits]: https://doc.rust-lang.org/reference/items/traits.html
[rust165]: https://blog.rust-lang.org/2022/11/03/Rust-1.65.0/
[reference-gats]: https://doc.rust-lang.org/reference/items/generics.html
[nightly-features]: https://doc.rust-lang.org/nightly/unstable-book/

- [Rust Reference: associated-items][ref-assoc]
- [Rust Reference: items/traits (dyn-compatibility list)][ref-traits]
- [Rust 1.65.0 release: GAT stabilization][rust165]
- [Rust Reference: items/generics][reference-gats]

For deeper drill-downs see:
- `references/methods.md`: GAT syntax forms (lifetime, type, const, bound, where-clause) verbatim from the Reference.
- `references/examples.md`: complete working examples for `LendingIterator`, callback registry, pointer family, and `for<'a>` HRTB usage.
- `references/anti-patterns.md`: the most common GAT design and impl mistakes with root-cause explanations and fixes.

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
