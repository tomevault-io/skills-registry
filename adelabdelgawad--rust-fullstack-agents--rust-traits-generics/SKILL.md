---
name: rust-traits-generics
description: Canonical reference for Rust traits and generics: defining traits, default methods, impl Trait (argument and return position), generic type parameters, where clause bounds, trait bounds on structs vs impls, blanket impls, associated types vs generic type params, static dispatch via monomorphization, dynamic dispatch via dyn Trait / trait objects, object safety (E0038), unsatisfied bound errors (E0277), the orphan/coherence rule, From/Into/TryFrom conventions, marker traits (Copy, Send, Sync, Sized), and PhantomData. Auto-triggers when: adding a new trait definition, using dyn anywhere, returning impl Trait, implementing From/Into/TryFrom, hitting E0277 (trait bound not satisfied) or E0038 (trait not object-safe), or designing an abstraction that spans multiple concrete types. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Traits & Generics

Traits define shared behaviour; generics let you write code once and reuse it across many types. Together they are the primary abstraction mechanism in Rust — the right combination determines whether dispatch is static (zero cost) or dynamic (vtable), and whether the compiler enforces correctness at the call site or at runtime.

## When to Use

- Designing any abstraction that should work across multiple concrete types.
- Deciding between `impl Trait`, a generic parameter, and `dyn Trait`.
- Implementing standard conversion traits (`From`, `Into`, `TryFrom`).
- Hitting compiler errors E0277 (unsatisfied bound) or E0038 (trait not object-safe).
- Adding a `where` clause, a blanket impl, or an associated type.
- Any time `Box<dyn Trait>` appears — verify it is actually necessary.

---

## Generic vs `impl Trait` vs `dyn Trait` — Decision Table

| Need | Use | Dispatch | Cost |
|---|---|---|---|
| Single behavior, caller picks concrete type | `fn foo<T: Trait>(x: T)` | Static (monomorphized) | None at runtime |
| Convenience sugar for single-bound arg | `fn foo(x: impl Trait)` | Static (monomorphized) | None at runtime |
| Return a single opaque concrete type | `fn foo() -> impl Trait` | Static | None at runtime |
| Heterogeneous collection / type erased at runtime | `Box<dyn Trait>` / `&dyn Trait` | Dynamic (vtable) | Heap alloc + indirect call |
| Same concrete type required for multiple params | `fn foo<T: Trait>(a: T, b: T)` | Static | None at runtime |
| Different concrete types allowed per parameter | `fn foo(a: impl Trait, b: impl Trait)` | Static | None at runtime |

> Book (ch10-02): "`impl Trait` syntax is convenient and makes for more concise code in simple cases, while the fuller trait bound syntax can express more complexity in other cases."

> Book (ch18-02): "When we use trait objects, Rust must use dynamic dispatch. The compiler doesn't know all the types that might be used with the code that's using trait objects, so it doesn't know which method implemented on which type to call."

**Default**: prefer static dispatch (generic or `impl Trait`). Reach for `dyn Trait` only when you need a heterogeneous collection or need to erase the concrete type across a runtime boundary.

---

## Core Idioms

### Bound complex signatures with `where` ✅

```rust
// ✅ where clause keeps the signature readable
fn process<T, U>(t: T, u: U) -> String
where
    T: Display + Clone,
    U: Debug + Send,
{
    format!("{} {:?}", t, u)
}

// ❌ inline bounds become unreadable past two constraints
fn process<T: Display + Clone, U: Debug + Send>(t: T, u: U) -> String { ... }
```

### Prefer `impl Trait` for simple single-bound arguments; generic `<T>` when the same type must appear twice ✅

```rust
// ✅ two parameters may differ — impl Trait is clearest
pub fn log_item(item: &impl Summary) { ... }

// ✅ both parameters must be the SAME type — named generic enforces it
pub fn merge<T: Summary>(a: T, b: T) -> T { ... }
```

### Use default methods to share behaviour without forcing every implementor to repeat it ✅

Traits may provide default implementations; any type can override them.

```rust
// ✅ default method calls another (required) method — standard Book pattern
pub trait Summary {
    fn summarize_author(&self) -> String;           // required

    fn summarize(&self) -> String {                 // default
        format!("(Read more from {}...)", self.summarize_author())
    }
}

// Implementor only provides the required method; default summarize() works for free
impl Summary for Tweet {
    fn summarize_author(&self) -> String { format!("@{}", self.username) }
}
```

> Book (ch10-02): "Default implementations can call other methods in the same trait, even if those other methods don't have a default implementation."

### Implement `From`, not a custom `to_x` or `as_x` conversion ✅

```rust
// ✅ std convention — Into is auto-derived, ? operator works, ecosystem expects it
impl From<DbRecord> for UserDto {
    fn from(r: DbRecord) -> Self { ... }
}

// ❌ bespoke conversion bypasses Into, ? chains, and ecosystem interop
impl DbRecord {
    pub fn to_user_dto(self) -> UserDto { ... }
}
```

### Prefer associated types over a second generic parameter when there is a one-to-one relationship ✅

Use an **associated type** when each implementor fixes exactly one output type (`Iterator::Item`).
Use a **generic type parameter** when an implementor may provide multiple implementations for different type arguments (`impl From<u8> for Foo` and `impl From<u16> for Foo` are both valid).

```rust
// ✅ associated type — one output per implementor; no extra type param at call site
trait Parser {
    type Output;
    fn parse(&self, input: &str) -> Option<Self::Output>;
}

// ❌ generic parameter — allows multiple impls per type (usually unwanted for parsers)
trait Parser<Output> {
    fn parse(&self, input: &str) -> Option<Output>;
}
```

### Default to static dispatch; add `dyn` only for heterogeneous storage ✅

```rust
// ✅ static dispatch — zero runtime cost, inlining possible
fn draw_all<T: Draw>(shapes: &[T]) { for s in shapes { s.draw(); } }

// ✅ dyn — necessary when the Vec holds mixed concrete types at runtime
fn draw_all(shapes: &[Box<dyn Draw>]) { for s in shapes { s.draw(); } }

// ❌ dyn where a generic works — needless vtable + heap alloc
fn area(shape: &Box<dyn Shape>) -> f64 { shape.area() }
fn area<S: Shape>(shape: &S) -> f64 { shape.area() }  // ✅
```

---

## Forbidden Patterns

### Forbidden 1 — `Box<dyn Trait>` Where Static Dispatch Suffices ❌

**Problem:** Using `Box<dyn Trait>` (heap allocation + vtable) for a single concrete type or a homogeneous collection.

```rust
// ❌ needless heap alloc and vtable — callers always pass one type
fn process(handler: Box<dyn EventHandler>) { handler.handle(); }

// ✅ monomorphized, zero overhead
fn process<H: EventHandler>(handler: H) { handler.handle(); }
// ✅ borrow form — also zero overhead
fn process(handler: &impl EventHandler) { handler.handle(); }
```

**Why (Book ch18-02):** "When we use trait objects, Rust must use dynamic dispatch… prevents the compiler from choosing to inline a method's code which in turn prevents some optimizations." Static dispatch via generics costs nothing at runtime.

```bash
# Detector — flag Box<dyn> outside struct fields / type aliases where a generic would do
# Caveat: Box<dyn Error> in error chains and Box<dyn Fn> in callbacks are idiomatic; review hits manually.
grep -rn 'Box<dyn ' src/ | grep -v 'struct\|enum\|type\|//\s*@dyn-required'
```

---

### Forbidden 2 — Trait Method Breaks Object Safety Then Used as `dyn` (E0038) ❌

**Problem:** Adding methods with generic type parameters or `Self` return types to a trait, then using it as `dyn Trait`. The compiler emits E0038 ("trait not object safe").

```rust
// ❌ generic method makes the trait not object-safe
trait Codec {
    fn encode<T: Serialize>(&self, val: T) -> Vec<u8>;  // generic param → E0038
}
let c: Box<dyn Codec> = ...;  // error[E0038]: `Codec` cannot be made into an object

// ✅ remove the method-level generic — use associated types or a concrete byte slice
trait Encoder {
    fn encode_bytes(&self, bytes: &[u8]) -> Vec<u8>;    // no generic → object-safe
}
```

**Why (Book ch18-02):** Object safety requires that the vtable can be constructed at compile time. Methods with generic parameters would need an infinite number of vtable entries. Methods returning `Self` can't be stored in a vtable without knowing the concrete size.

```bash
# Detector — trait methods that return Self or have generic params on the method
# Caveat: matches inside derive macros and test code; filter with | grep -v 'test\|derive'
# Caveat: HIGH false-positive rate on the second half of the alternation — `fn [a-z_]+<[A-Z]`
#   matches any fn whose name is followed immediately by an uppercase type param, but also
#   fires on return types such as `-> Vec<User>`, `-> Arc<Config>`, and `-> Result<T, E>`.
#   Review every hit manually: flag only method-level generic type params (e.g. `fn encode<T:`)
#   not parameter/return-type generics.
grep -rn -A 20 '^pub trait\|^trait ' src/ | grep -E 'fn .* -> Self|fn [a-z_]+<[A-Z]'
```

---

### Forbidden 3 — Ad-hoc `to_foo` / `as_foo` Instead of `From`/`TryFrom` ❌

**Problem:** Writing bespoke conversion methods instead of implementing `std::convert::From` or `TryFrom`.

```rust
// ❌ bespoke conversions bypass the std ecosystem
impl RawRow {
    pub fn to_dto(self) -> UserDto { ... }
    pub fn into_entity(self) -> User { ... }
}

// ✅ From impls — Into is auto-derived, ? works on TryFrom errors
impl From<RawRow> for UserDto {
    fn from(r: RawRow) -> Self { ... }
}

impl TryFrom<RawRow> for User {
    type Error = AppError;
    fn try_from(r: RawRow) -> Result<Self, Self::Error> { ... }
}
```

**Why:** The standard library contains a blanket impl `impl<T, U: From<T>> Into<U> for T` — implementing `From` gives `Into` for free (std docs: <https://doc.rust-lang.org/std/convert/trait.Into.html>). `TryFrom`/`TryInto` follow the same pattern and integrate with `?`.

```bash
# Detector — bespoke conversion methods
# Caveat: HIGH false-positive rate. to_string(), to_owned(), as_str(), as_ref(), as_bytes(),
#         into_iter(), into_inner() are all idiomatic std conventions — do NOT flag those.
#         Review every hit manually before acting.
grep -rn '\bfn to_\|\bfn as_\|\bfn into_' src/ | grep -v '//\|#\[' | grep -v 'test\|to_string\|to_owned\|as_str\|as_ref\|as_bytes\|into_iter\|into_inner'
```

---

### Forbidden 4 — Over-Broad Bounds on the Struct Definition ❌

**Problem:** Adding trait bounds on the `struct`/`enum` definition when they are only needed on specific `impl` blocks.

```rust
// ❌ every consumer pays the bound even if they never call display_name
struct Cache<T: Display + Clone> {
    value: T,
}

// ✅ bound only where it is required; other impls remain unconstrained
struct Cache<T> {
    value: T,
}

impl<T: Display> Cache<T> {
    pub fn display_name(&self) -> String { format!("{}", self.value) }
}

impl<T: Clone> Cache<T> {
    pub fn clone_value(&self) -> T { self.value.clone() }
}
```

**Why:** Bounds on the struct definition propagate to every use site — consumers that never invoke the bounded method still have to satisfy the bound. Rust API Guidelines (C-STRUCT-BOUNDS) prefer bounds on `impl` blocks. Book ch10-01 demonstrates unbounded structs throughout; bounds appear on the method `impl` where they are first needed.

```bash
# Detector (struct/enum defs with bounds — not always wrong, e.g. newtype wrappers; review each)
grep -rn '^pub struct\|^struct\|^pub enum\|^enum' src/ | grep '<.*:'
```

---

### Forbidden 5 — `impl Trait` Return Hiding a Type Callers Must Name ❌

**Problem:** Using `-> impl Trait` for a return type when callers need to store, name, or further constrain the concrete type.

```rust
// ❌ callers cannot store the return value in a named-type field
fn make_handler() -> impl EventHandler { MyHandler::new() }
// struct Dispatcher { handler: ??? }  ← cannot write the type

// ✅ name the concrete type when callers need to store it
fn make_handler() -> MyHandler { MyHandler::new() }
// ✅ or erase it behind a trait object when the concrete type must stay hidden
fn make_handler() -> Box<dyn EventHandler> { Box::new(MyHandler::new()) }
```

**Why (Book ch10-02):** "You can only use `impl Trait` if you're returning a single type." The return-position opacity is intentional for iterators and closures, but becomes a liability when call sites need to hold or further parametrize the concrete type.

```bash
# Detector — impl Trait return in public function signatures
# Caveat: impl Trait returns are idiomatic for iterators and async fn; review hits for intent.
grep -rn 'pub fn.*-> impl ' src/ | grep -v '//\|test'
```

---

### Forbidden 6 — Blanket Impl That Violates the Orphan Rule ❌

**Problem:** Implementing a foreign trait for a foreign type — both defined outside your crate. The compiler emits E0117 ("only traits defined in the current crate can be implemented for arbitrary types"). A related violation (uncovered type parameter before the first local type) emits E0210.

```rust
// ❌ both Display (std) and Vec<T> (std) are foreign — E0117
impl<T: Debug> std::fmt::Display for Vec<T> {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result { ... }
}
// error[E0117]: only traits defined in the current crate can be implemented for types defined outside

// ✅ newtype wrapper — you own the wrapper, so you can impl any foreign trait on it
struct DisplayVec<T>(Vec<T>);
impl<T: Debug> std::fmt::Display for DisplayVec<T> { ... }
```

**Why (Book ch10-02):** "We can implement a trait on a type only if either the trait or the type is local to our crate. This rule ensures that other people's code can't break your code and vice versa." This is the orphan rule, part of the coherence property.

```bash
# Detector — impl of a likely-foreign trait on a likely-foreign container type
# Caveat: not exhaustive; the compiler is the authoritative check (E0117/E0210).
grep -rn '^impl.*for Vec<\|^impl.*for Option<\|^impl.*for Result<' src/ | grep -v 'crate::\|super::'
```

---

### Forbidden 7 — Unused Generic Parameter Without `PhantomData` ❌

**Problem:** A struct declares a generic type parameter `T` but no field stores `T`. The compiler emits E0392 ("parameter `T` is never used").

```rust
// ❌ T is declared but not used — E0392: parameter `T` is never used
struct TypedId<T> {
    id: u64,
}

// ✅ PhantomData expresses "this struct logically owns/uses T" without storing it
use std::marker::PhantomData;

struct TypedId<T> {
    id: u64,
    _marker: PhantomData<T>,
}
```

**Why:** Rust needs to know variance and drop-check rules for every generic parameter. `PhantomData<T>` provides that signal when no field actually holds `T`. Without it the compiler cannot determine whether dropping a `TypedId<User>` might run `T`'s destructor.

```bash
# Detector — E0392 already surfaces as a compile error; grep for it in compiler output
grep -rn 'E0392\|parameter.*never used' src/
```

---

## Marker Traits

Rust's standard marker traits encode invariants the compiler relies on:

| Trait | Meaning | Auto-derived? |
|---|---|---|
| `Copy` | Bitwise copy on assignment (no move) | Yes, if all fields are `Copy` |
| `Clone` | Explicit `.clone()` allowed | `#[derive(Clone)]` |
| `Send` | Safe to transfer across thread boundary | Auto (negative impl to opt out) |
| `Sync` | Safe to share `&T` across threads | Auto (negative impl to opt out) |
| `Sized` | Compile-time known size | Auto; `?Sized` opts out |

`Send + Sync` are almost always derived automatically. When writing `unsafe impl Send for T` you are asserting a safety invariant the compiler cannot verify — document the invariant immediately above the impl.

---

## Book References

- **ch10-01** "Generic Data Types" — syntax in functions, structs, enums, methods; monomorphization performance guarantee.
  <https://doc.rust-lang.org/book/ch10-01-syntax.html>

- **ch10-02** "Defining Shared Behavior with Traits" — defining traits, default methods, `impl Trait` vs trait-bound syntax, `where` clauses, returning `impl Trait`, blanket impls, orphan rule.
  <https://doc.rust-lang.org/book/ch10-02-traits.html>

- **ch18-02** "Using Trait Objects to Abstract over Shared Behavior" — trait objects, `dyn Trait`, dynamic vs static dispatch, object safety (dyn compatibility).
  <https://doc.rust-lang.org/book/ch18-02-trait-objects.html>

---

## Verification Hooks

Run all detectors in sequence for a one-shot audit of a file or directory:

```bash
# D1 — Box<dyn Trait> where a generic would do (idiomatic Box<dyn Error> / Box<dyn Fn> are expected)
grep -rn 'Box<dyn ' src/ | grep -v 'struct\|enum\|type\|//\s*@dyn-required'

# D2 — Trait methods returning Self or with method-level generic params (E0038 candidates)
# Caveat: HIGH false-positive rate — `fn [a-z_]+<[A-Z]` also fires on return-type generics;
#   review every hit manually and flag only method-level type params.
grep -rn -A 20 '^pub trait\|^trait ' src/ | grep -E 'fn .* -> Self|fn [a-z_]+<[A-Z]'

# D3 — Bespoke to_/as_/into_ conversion methods instead of From/TryFrom
# Caveat: HIGH false-positive rate — to_string, to_owned, as_str, as_ref, as_bytes,
#   into_iter, into_inner are all idiomatic std conventions; review every hit manually.
grep -rn '\bfn to_\|\bfn as_\|\bfn into_' src/ | grep -v '//\|#\[' | grep -v 'test\|to_string\|to_owned\|as_str\|as_ref\|as_bytes\|into_iter\|into_inner'

# D4 — Bounds on struct/enum definitions (prefer bounds on impl blocks)
grep -rn '^pub struct\|^struct\|^pub enum\|^enum' src/ | grep '<.*:'

# D5 — pub fn returning impl Trait (callers may need to name/store the concrete type)
# Caveat: impl Trait returns are idiomatic for iterators and async fn; review for intent.
grep -rn 'pub fn.*-> impl ' src/ | grep -v '//\|test'

# D6 — impl of a likely-foreign trait on a likely-foreign container type (orphan rule)
grep -rn '^impl.*for Vec<\|^impl.*for Option<\|^impl.*for Result<' src/ | grep -v 'crate::\|super::'

# D7 — E0392: unused generic parameter (compiler already surfaces this; grep for it in output)
grep -rn 'E0392\|parameter.*never used' src/
```

**Quick-reference anti-patterns:**

| Pattern | Forbidden | Correct alternative |
|---|---|---|
| `Box<dyn Trait>` for single concrete type | Forbidden 1 | `fn foo<H: Trait>(h: H)` |
| Trait method with `fn method<T>(...)` | Forbidden 2 | Associated type or concrete param |
| `impl T { fn to_dto(self) }` | Forbidden 3 | `impl From<T> for Dto` |
| `struct Foo<T: Display>` | Forbidden 4 | `struct Foo<T>` + bounds on `impl` |
| `-> impl Trait` when caller stores the type | Forbidden 5 | Return concrete type or `Box<dyn Trait>` |
| `impl ForeignTrait for ForeignType` | Forbidden 6 | Newtype wrapper |
| `struct Foo<T>` with no `T` field | Forbidden 7 | Add `_marker: PhantomData<T>` |

---

## Related Skills

- `rust-lifetimes` — lifetime parameters interact closely with trait bounds (`T: 'a`, `for<'a>`).
- `rust-smart-pointers` — `Box<T>`, `Rc<T>`, `Arc<T>` are the primary wrappers for `dyn Trait`.
- `dto-domain` — `From`/`TryFrom` conventions are the bridge between domain entities and DTOs.

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
