---
name: rust-lifetimes
description: > Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Lifetimes

Lifetimes are a variety of generics that tell the borrow checker how long references must
remain valid. Every reference already has a lifetime; annotations only make implicit
relationships explicit so the compiler can verify them. Lifetimes prevent dangling
references at zero runtime cost — they are erased before codegen.

Book: *"Validating References with Lifetimes"* — Chapter 10 §3
(https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)

---

## When to Use

**Do annotate** when:
- A function takes multiple reference parameters and returns a reference — the compiler
  cannot infer which input the output borrows from.
- A struct holds a reference field — the struct must not outlive the data it borrows.
- A `where` clause is needed to satisfy `T: 'a` (E0309) or `T: 'static` (E0310) — note
  that when a struct field is `&'a T`, rustc edition 2021+ implies `T: 'a` automatically;
  the explicit bound is only required when the struct does not directly hold `&'a T`.

**Do NOT annotate** when the three elision rules already cover the case (see Core Idioms).
Redundant annotations add noise and are a Forbidden pattern.

---

## Core Idioms

### Elision covers it — do not annotate
```rust
// ✅ Elision rule 2: single input lifetime flows to output
fn first_word(s: &str) -> &str { ... }

// ❌ Redundant — compiler infers this identically
fn first_word<'a>(s: &'a str) -> &'a str { ... }
```

### Multiple inputs returning a reference — annotate
```rust
// ✅ Tells the borrow checker: output lives as long as the shorter of x and y
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// ❌ E0106: compiler cannot determine which input the output borrows
fn longest(x: &str, y: &str) -> &str { ... }
```

### Struct holding a reference — lifetime required
```rust
// ✅ Instance cannot outlive the string slice it borrows
struct Excerpt<'a> {
    part: &'a str,
}

// ❌ E0106: missing lifetime specifier on reference field
struct Excerpt {
    part: &str,
}
```

### Method with &self — elision rule 3 applies
```rust
// ✅ Rule 3: output lifetime is inferred from &self; no annotation needed
impl<'a> Excerpt<'a> {
    fn level(&self) -> i32 { 3 }
    fn announce(&self, note: &str) -> &str { self.part }
}

// ❌ Over-annotated — matches what the compiler infers anyway
fn announce<'b>(&'b self, note: &str) -> &'b str { self.part }
```

### Lifetime bound on a generic type parameter
```rust
// ✅ rustc 2021+ implies T: 'a from the &'a T field — explicit bound accepted but optional
struct Wrapper<'a, T> {
    value: &'a T,
}

// ✅ rustc 2021+ implies T: 'static from the &'static T field — explicit bound accepted but optional
struct Forever<T> {
    value: &'static T,
}
```

> **Note (Rust edition 2021+):** When a struct field is `&'a T`, the compiler implies the
> `T: 'a` bound automatically. Writing `T: 'a` explicitly is accepted but not required.
> The explicit form is genuinely needed only when the struct does not directly hold a
> `&'a T` field but still needs the invariant — for example, via an associated type
> (`T::Output: 'a`) or a complex generic constraint where inference cannot fill it in.

---

## The Three Lifetime Elision Rules

*(Book: "Lifetime Elision" subsection of ch10-03)*

The compiler applies these rules in order before requiring explicit annotations.

1. **Each reference parameter gets its own lifetime.**
   `fn foo(x: &i32, y: &i32)` → `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`

2. **If exactly one input lifetime, it flows to all output lifetimes.**
   `fn foo<'a>(x: &'a str) -> &str` → output gets `'a` automatically.

3. **If one of the inputs is `&self` or `&mut self`, its lifetime flows to all outputs.**
   Methods rarely need explicit return-lifetime annotations.

If the rules exhaust without resolving every output lifetime the compiler emits E0106.

---

## Forbidden Patterns

### Forbidden 1 — Slapping `'static` to Silence E0597

**Forbidden:** Adding a `'static` bound or changing a return type to `&'static T` solely
to stop "does not live long enough" errors.

**Why:** `'static` means the data lives for the entire program. Forcing it hides the real
problem — a reference outliving its owner — rather than fixing the ownership relationship.
Book: *"The Static Lifetime"* — "most of the time, an error message suggesting the
`'static` lifetime results from attempting to create a dangling reference or a mismatch of
the available lifetimes."

```rust
// ❌ Cargo-culted 'static silences E0597 but creates unsound contract
fn get_name<'a>(s: &'a str) -> &'static str {
    s  // E0597 hidden by transmuting lifetime, not by fixing the borrow
}

// ✅ Tie the output lifetime to the input
fn get_name<'a>(s: &'a str) -> &'a str {
    s
}
```

```bash
# Detector — candidate list, not a verdict; 'static on string literals is correct
grep -rn "'static" src/ | grep -v '"' | grep -v '// @intentional-static'
```

---

### Forbidden 2 — Returning a Reference to a Local Variable (E0515)

**Forbidden:** Returning a `&T` that points to a value created inside the function.

**Why:** The local is dropped at the closing brace; the reference would dangle. The borrow
checker enforces this as E0515 ("cannot return value referencing local variable"). Book:
*"Generic Lifetimes in Functions"* — "If the reference returned does not refer to one of
the parameters, it must refer to a value created within this function. However, this would
be a dangling reference because the value will go out of scope at the end of the function."

```rust
// ❌ E0515: result is dropped when the function returns
fn make_str<'a>() -> &'a str {
    let result = String::from("hello");
    result.as_str()  // dangling after drop
}

// ✅ Return owned data; caller is responsible for its lifetime
fn make_str() -> String {
    String::from("hello")
}
```

```bash
# Detector — E0515 is a compile error, not a runtime bug; this grep flags the pattern
# of returning a borrow of a local created inside the fn (heuristic, false positives exist)
grep -rn 'let.*=.*String::from\|let.*=.*Vec::new\|let.*=.*format!' src/ \
  | grep -v '#\[cfg(test'
# Then check if any such binding is borrowed in the return position in the same function.
```

---

### Forbidden 3 — Redundant Explicit Lifetimes Elision Already Covers

**Forbidden:** Annotating a function with `<'a>` and `&'a` parameters when all three
elision rules already resolve to the same result.

**Why:** Redundant annotations increase cognitive load and signal that the author did not
apply the elision rules. Book: *"Lifetime Elision"* — "After writing a lot of Rust code,
the Rust team found that Rust programmers were entering the same lifetime annotations over
and over."

```rust
// ❌ Redundant annotation — elision rule 2 produces the same signature
fn first_word<'a>(s: &'a str) -> &'a str {
    let boundary = s.find(' ').unwrap_or(s.len());
    &s[..boundary]
}

// ✅ Let elision do its job
fn first_word(s: &str) -> &str {
    let boundary = s.find(' ').unwrap_or(s.len());
    &s[..boundary]
}
```

```bash
# Detector — any fn with a lifetime annotation is a candidate; many legitimate uses exist
# (multi-param fns that genuinely need explicit annotations will also match)
# Caveat: use -E (ERE) so the pattern is unambiguous; the old BRE form with a bare ( caused
# exit 2 under ERE-mode grep.
grep -rnE "fn .*<.*'[a-z][a-z]*.*>.*&.*'[a-z]" src/ --include='*.rs' \
  | grep -v struct | grep -v impl
```

---

### Forbidden 4 — `Box<dyn Trait + 'static>` Where a Scoped Borrow Suffices

**Forbidden:** Using `Box<dyn Trait + 'static>` (or `Arc<dyn Trait + 'static>`) for
trait objects that are only needed for the duration of a known scope.

**Why:** `'static` rules out any borrowed data from being placed in the box, forcing
unnecessary cloning or Arc-wrapping of values that have a natural shorter lifetime. The
correct bound is the scope's lifetime: `Box<dyn Trait + 'a>`. Book: *"The Static
Lifetime"* — "the solution is to fix those problems, not to specify the `'static` lifetime."

```rust
// ❌ Rejects &str, &[T], and any struct containing references
fn process(handler: Box<dyn Fn() + 'static>) { ... }

// ✅ Accept anything valid for the function's call duration
fn process<'a>(handler: Box<dyn Fn() + 'a>) { ... }
// or equivalently with impl Fn when boxing is not required:
fn process(handler: impl Fn()) { ... }
```

```bash
# Detector — flag 'static on dyn bounds; legitimate uses exist (thread::spawn, task queues)
grep -rn "dyn.*'static\|'static.*dyn" src/ | grep -v '// @static-required'
```

---

### Forbidden 5 — Self-Referential Structs (Without Pin/Owning)

**Forbidden:** A struct that holds a reference to data owned by another field of the same
struct (e.g., `struct S { data: String, view: &'??? str }`).

**Why:** When the struct moves, `data` moves to a new address and the reference dangles.
Rust's ownership model forbids stable addresses for ordinary stack/heap values. The
correct solutions are: (a) use an owned type (`String`) instead of a borrow, (b) use an
index into the data, (c) use a crate like `ouroboros` or `self_cell` that wraps the
struct in `Pin`, or (d) separate the data and the view into two distinct structs. Book:
*"In Struct Definitions"* — "An instance of `ImportantExcerpt` can't outlive the reference
it holds in its `part` field."

```rust
// ❌ Cannot express — no lifetime can describe 'own data'
struct Parser {
    input: String,
    current: &str,   // E0106: wants to borrow from `input`, but `input` also moves
}

// ✅ Store an index instead of a reference
struct Parser {
    input: String,
    cursor: usize,
}
```

```bash
# Detector — struct fields with a bare unannotated &str / &[T] (no lifetime tick)
# The pattern skips &'a-annotated fields so it does not flag correct code like
# the Book's own ImportantExcerpt<'a>. Use only to find fields missing a lifetime
# annotation (E0106: "missing lifetime specifier").
grep -rn -A10 '^struct ' src/ --include='*.rs' | grep -E ':[ \t]*&[^'"'"'][^;]*,$'
```

---

### Forbidden 6 — Cargo-Culted `'a: 'b` Outlives Constraints

**Forbidden:** Adding `'a: 'b` ("lifetime `'a` outlives `'b`") or `T: 'a` bounds copied
from a compiler suggestion without understanding why they are needed.

**Why:** `'a: 'b` is a *lifetime subtyping* constraint — it says `'a` is at least as long
as `'b`. It is correct and necessary when a shorter-lived reference must hold a value
borrowed from a longer-lived region. Cargo-culting it causes over-constrained APIs that
reject valid callers. E0309 is the correct trigger: it fires when a `T: 'a` bound is
missing from a struct definition; it is fixed by adding the exact bound the compiler
requires, not by adding every outlives relationship in sight. Book: *"Generic Type
Parameters, Trait Bounds, and Lifetimes"* and error index E0309.

```rust
// ❌ Over-constrained: forces 'a to outlive 'b — requires callers to provide a longer-lived first argument
fn pick<'a: 'b, 'b>(x: &'a str, y: &'b str) -> &'b str { y }

// ✅ No constraint needed — output ties to 'b only
fn pick<'a, 'b>(x: &'a str, y: &'b str) -> &'b str { y }

// ✅ Explicit T: 'a accepted; implied by &'a T field since Rust 2021 — needed only
// when the invariant can't be inferred (e.g. associated type T::Output: 'a)
struct Wrapper<'a, T: 'a> {
    inner: &'a T,
}
```

```bash
# Detector — explicit outlives syntax; flag for review (legitimate uses exist in
# trait-object lifetimes and complex generic bounds)
grep -rn "'"'[a-z]*:.*'"'"'[a-z]" src/ | grep -v '// @outlives-required'
```

---

## Book References

- **Chapter 10: Generic Types, Traits, and Lifetimes**
  https://doc.rust-lang.org/book/ch10-00-generics.html

- **§10.3 "Validating References with Lifetimes"** — why lifetimes exist; dangling
  references; the borrow checker; elision rules; struct/fn/method annotation; `'static`;
  combined generics + lifetimes + trait bounds
  https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html

- **Error index E0106** — "missing lifetime specifier" — return type borrows but
  signature does not say from which parameter
  https://doc.rust-lang.org/error_codes/E0106.html

- **Error index E0309** — "`T` may not live long enough" — struct containing associated
  type output where `impl` requires `T: 'a` but struct definition does not declare it;
  fix: add `T: 'a` where-clause to the struct
  https://doc.rust-lang.org/error_codes/E0309.html

- **Error index E0310** — "`T` may not live long enough" — struct stores `&'static T`
  but `T` is not bounded by `'static`; fix: add `T: 'static`
  https://doc.rust-lang.org/error_codes/E0310.html

- **Error index E0515** — "cannot return value referencing local variable" — returning a
  borrow of a value that will be dropped when the function returns
  https://doc.rust-lang.org/error_codes/E0515.html

- **Error index E0597** — "does not live long enough" — reference outlives the value it
  points to; primary signal that lifetimes need restructuring, not a `'static` patch
  https://doc.rust-lang.org/error_codes/E0597.html

---

## Related Skills

- **rust-ownership-borrowing** — the ownership and borrowing rules that lifetimes enforce
- **rust-traits-generics** — combining trait bounds with lifetime parameters (`T: Display + 'a`)
- **rust-smart-pointers** — `Arc`/`Rc` and when owning data beats borrowing it across scopes

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
