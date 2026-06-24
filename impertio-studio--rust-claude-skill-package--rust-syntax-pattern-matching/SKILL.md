---
name: rust-syntax-pattern-matching
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-syntax-pattern-matching

The **mechanics** of pattern matching in Rust: `match`, `if let`, `let else`, `while let`, if-let chains (edition 2024, stable in 1.88), destructuring of tuples / structs / enums / slices / references, `ref` and `ref mut`, or-patterns, guards, `@` bindings, `..` rest patterns, range patterns, and literal patterns.

Cross-references: [[rust-syntax-edition-2024]] (if-let chain edition-gating, `if let` temporary scope change) [[rust-syntax-ownership]] (move vs borrow in match arms, when `ref` matters)

---

## When to use this skill

- User writes a `match` and gets E0004 ("non-exhaustive patterns") or wants to know which arms are missing
- User asks "should I use `match`, `if let`, or `let else` here"
- User wonders why their `if let` chain is rejected (edition or version too old)
- User destructures a tuple, struct, enum, slice, or reference and the compiler complains about moves
- User asks what `ref` / `ref mut` does and when to reach for it
- User wants or-patterns (`A | B`), guards (`if cond`), or `@` bindings explained
- User asks about `..` rest patterns in structs or slices
- User writes a range pattern (`1..=10`) or asks about exclusive ranges in patterns (stable in 1.80)
- User gets E0408 (variable not bound in all patterns) or E0409 (variable bound with different mode/type)

For broader edition 2024 changes (RPIT lifetime capture, unsafe extern, never type fallback) see [[rust-syntax-edition-2024]]. For move vs borrow semantics that drive `ref` see [[rust-syntax-ownership]].

---

## Quick reference table

| Construct | Refutable? | Use when | Example |
|---|---|---|---|
| `let` | irrefutable only | bind a value that cannot fail | `let (a, b) = pair;` |
| `let else` (1.65) | refutable, else MUST diverge | extract one variant, bail otherwise | `let Some(x) = opt else { return; };` |
| `if let` | refutable | one variant matters, ignore the rest | `if let Some(x) = opt { ... }` |
| `if let ... else` | refutable | two branches, one bound | `if let Some(x) = opt { ... } else { ... }` |
| `if let` chain (1.88, ed 2024) | refutable | combine multiple `let` + boolean conditions | `if let A = x && y > 0 && let B = z { ... }` |
| `while let` | refutable | loop while pattern matches | `while let Some(item) = iter.next() { ... }` |
| `match` | exhaustive over scrutinee | branch on every variant of an enum / value | `match x { Some(n) => ..., None => ... }` |

ALWAYS prefer `match` when every variant must be handled. ALWAYS prefer `if let` / `let else` when only one variant matters.

---

## Decision tree: which construct to use

```
Need every variant handled (exhaustive)?
├── YES → use `match`
└── NO → only one variant matters?
        ├── failure must short-circuit (return / panic / break)?
        │   └── YES → use `let else`
        ├── two simple branches (matched / not matched)?
        │   └── YES → use `if let { ... } else { ... }`
        ├── multiple conditions chained with &&?
        │   └── YES → use `if let` chain (edition 2024, Rust 1.88+)
        └── loop on consecutive matches?
            └── YES → use `while let`
```

---

## `match` : exhaustiveness, arm order, wildcards

```rust
enum Shape { Circle(f64), Square(f64), Rect(f64, f64) }

fn area(s: Shape) -> f64 {
    match s {
        Shape::Circle(r)    => std::f64::consts::PI * r * r,
        Shape::Square(s)    => s * s,
        Shape::Rect(w, h)   => w * h,
    }
}
```

Rules :

1. **Exhaustiveness** : every possible value of the scrutinee MUST be covered. Missing arms produce E0004 ("non-exhaustive patterns").
2. **Arm order** : first matching arm wins. Place more specific arms before more general ones.
3. **Wildcard `_`** : the catch-all pattern. Does not bind.
4. **Bind-all `name`** : `name => ...` binds the value to `name`. Use when the value is still useful.
5. **`#[non_exhaustive]` enums** from external crates ALWAYS require a `_` arm. The compiler refuses to assume the enum is closed.
6. **Empty types** (Rust 1.82+) : `Result<T, Infallible>` can omit the `Err` arm because `Infallible` has no constructors.

ALWAYS write `_ => unreachable!("...")` instead of `_ => ()` when you believe the arm is impossible, this surfaces logic bugs at runtime instead of silently swallowing them.

[Source: Rust Book Ch 6.2, The match Control Flow Construct][match] [Source: Rust Reference, match expressions][ref-match]

---

## `if let` : single pattern, optional else

```rust
if let Some(value) = optional {
    use_value(value);
}

if let Ok(parsed) = "42".parse::<i32>() {
    println!("{parsed}");
} else {
    println!("not a number");
}
```

Rules :

1. `if let PAT = expr { ... }` runs the block only when `expr` matches `PAT`. Bindings from `PAT` are in scope inside the block.
2. `else` branch is optional. When present, the binding is NOT in scope inside `else` (the match failed, so it could not be bound).
3. **Edition 2024 change** : temporaries in the `if let` scrutinee are dropped at the end of the `if let` arm, not at the end of the containing scope. This fixes drop-order bugs but can break pre-2024 code that depended on the old behaviour. See [[rust-syntax-edition-2024]].

[Source: Rust Book Ch 6.3, Concise Control Flow with `if let`][iflet] [Source: edition 2024 if-let scope][ed-iflet]

---

## `if let` chains : edition 2024, Rust 1.88+

Stabilized in **Rust 1.88.0** (2025-06-26). **Only available on edition 2024** because the feature depends on the `if let` temporary scope change.

```rust
// 2024 edition only, Rust 1.88+
if let Some(user) = lookup(id)
    && user.is_active
    && let Some(email) = user.email
{
    send(email);
}
```

Rules :

1. ANY combination of `let PAT = expr` and boolean expressions, joined by `&&`.
2. ALL conditions must evaluate to true (and all `let`-patterns must match) for the body to run.
3. Bindings from earlier `let`-patterns are in scope in later conditions and in the body.
4. `||` is NOT allowed across `let` conditions, only `&&`.
5. NEVER use this on edition 2021 or earlier, it will not compile. Migrate the crate or use nested `if let`.

ALWAYS prefer an `if let` chain over deep nesting when targeting edition 2024.

[Source: Rust 1.88.0 release notes, let chains stabilization][rust188]

---

## `let else` : extract one variant or diverge

Stabilized in **Rust 1.65** (Nov 2022).

```rust
fn parse_config(s: &str) -> Config {
    let Ok(cfg) = serde_json::from_str(s) else {
        return Config::default();
    };
    cfg
}
```

Rules :

1. The `else` branch MUST **diverge** : `return`, `break`, `continue`, `panic!`, `unreachable!`, `std::process::exit`, or any expression of type `!`.
2. Falling through the `else` block is a hard compile error.
3. Bindings from the pattern are in scope **after** the statement, in the surrounding block, unlike `if let`, which scopes them inside the block only.
4. Use `let else` to flatten the happy path. Replaces `let x = match opt { Some(x) => x, None => return };`.

NEVER write `let Some(x) = opt else { /* nothing */ };`, the compiler rejects it. The `else` must diverge.

[Source: Rust 1.65 release notes, let-else][rust165] [Source: Rust Reference, let statements][ref-let]

---

## `while let` : loop while pattern matches

```rust
let mut stack = vec![1, 2, 3];
while let Some(top) = stack.pop() {
    println!("{top}");
}
```

Rules :

1. The loop runs the body each iteration **as long as** the pattern matches.
2. The loop stops on the first non-match.
3. The binding is in scope inside the loop body only.
4. NEVER use `while let` for an iterator when `for` works, `for x in iter` desugars to a `loop` + `match` and handles `Iterator::next` returning `None` correctly without the visual overhead.

USE `while let` when the source is not a regular iterator (e.g. `VecDeque::pop_front`, `Receiver::recv` returning a `Result`, repeated `next_if`).

[Source: Rust Book Ch 6.3, while let][whilelet]

---

## Destructuring

Patterns can take apart compound values in `let`, `match`, function parameters, and closures.

### Tuples and tuple structs

```rust
let (x, y, z) = (1, 2, 3);

struct Point(i32, i32);
let Point(a, b) = Point(3, 4);
```

### Structs (named fields)

```rust
struct User { name: String, age: u32 }

let u = User { name: "Sam".into(), age: 42 };
let User { name, age } = u;             // shorthand: same field names
let User { name: n, age: a } = u;       // rebind to local names
let User { name, .. } = u;              // ignore remaining fields with `..`
```

Rule : if a struct has more fields than you destructure and you OMIT `..`, the compiler emits E0027 ("pattern does not mention field"). ALWAYS use `..` when you intentionally ignore fields.

### Enums

```rust
match result {
    Ok(value) => use_value(value),
    Err(e)    => log_error(e),
}
```

### Slices and arrays

```rust
match slice {
    []                   => println!("empty"),
    [only]               => println!("one: {only}"),
    [first, .., last]    => println!("{first} ... {last}"),
    [first, rest @ ..]   => println!("first={first}, rest={rest:?}"),
}
```

Rule : `..` matches any number of elements (zero or more). Combined with `@` it binds the rest as a sub-slice.

### References

```rust
let value = 5;
let r = &value;
let &v = r;     // destructure to get owned i32 (Copy required)
```

[Source: Rust Book Ch 18, Patterns and Matching][book-pat] [Source: Rust Reference, Patterns][ref-pat]

---

## `ref` and `ref mut` : bind by reference

By default, a binding pattern **moves or copies** the matched value. `ref` makes the binding a reference instead. `ref mut` makes it a mutable reference.

```rust
let s = String::from("hi");
match s {
    ref borrowed => println!("{borrowed}"),
}
// s is still usable here, no move
```

Modern style : ALWAYS prefer to take a reference at the scrutinee instead of using `ref` in arm patterns. Match ergonomics (RFC 2005) handles the rest.

```rust
// Idiomatic
match &s {
    text => println!("{text}"),
}
```

Use `ref` only when you cannot take a reference at the scrutinee, typically inside an `if let` chain or destructuring a value you cannot reshape.

WARNING : Edition 2024 restricts some match-ergonomics combinations with `&` patterns to keep room for future syntax. If you hit a confusing error on a complex destructure, see [[rust-syntax-edition-2024]].

[Source: Rust Reference, identifier patterns][ref-ident]

---

## Or-patterns, guards, `@` bindings, `..` rest, ranges, literals

### Or-patterns

```rust
match n {
    1 | 2 | 3 => "small",
    _         => "other",
}
```

Rule (E0408 / E0409) : every alternative MUST bind the **same variables with the same types**. Or-patterns with different bindings are rejected.

### Guards

```rust
match pair {
    (x, y) if x == y => "equal",
    (x, y) if x > y  => "first bigger",
    _                => "other",
}
```

Rule : the compiler does NOT consider guard expressions for exhaustiveness. A guarded arm is treated as potentially failing. ALWAYS provide a fallback arm.

### `@` bindings

```rust
match age {
    n @ 0..=12       => println!("child {n}"),
    n @ 13..=19      => println!("teen {n}"),
    n                => println!("adult {n}"),
}
```

`name @ pattern` binds `name` to the entire value while also testing it against `pattern`.

### `..` rest

```rust
struct Big { a: u8, b: u8, c: u8, d: u8 }
let Big { a, .. } = big;
```

In structs : `..` ignores all unmentioned fields. In tuples and slices : `..` ignores any number of elements.

### Range patterns

```rust
match digit {
    '0'..='9' => "digit",
    'a'..='z' => "lower",
    _         => "other",
}

// Exclusive ranges (Rust 1.80+), pattern matches 0,1,2,...,9 (NOT 10)
match n {
    0..10 => "single digit",
    _     => "two or more digits",
}
```

ALWAYS use `..=` for inclusive ranges. `..` is exclusive (1.80+ in patterns).

### Literal patterns

```rust
match cmd.as_str() {
    "quit" => exit(0),
    "help" => print_help(),
    other  => unknown(other),
}
```

Strings (`&str`), characters, integers, floats (deprecated in patterns), and `bool` can be literal patterns. ALWAYS bind the wildcard so you can include the value in error output.

[Source: Rust Reference, Patterns][ref-pat] [Source: Rust 1.80 release notes, exclusive ranges in patterns][rust180]

---

## Common pitfalls

For the full list (with code examples and fixes) see `references/anti-patterns.md`. Highlights :

- Missing `_` arm on `#[non_exhaustive]` enums from external crates, compile error
- `let else` body that does not diverge, compile error
- Using nested `if let { if let { ... } }` on edition 2024 instead of a chain, readability regression
- `match value { ref x => ... }` instead of `match &value { x => ... }`, works but reads awkwardly
- Or-pattern arms that bind variables of different types (`Some(n) | Ok(n)` where `n` has two unrelated types), E0409
- Omitting `..` when destructuring a struct with extra fields, E0027
- Relying on guard exhaustiveness, the compiler does not see through guards
- Forgetting that `if let` chains need edition 2024 AND Rust 1.88+

---

## Further reading

- `references/methods.md` : full list of pattern syntax and constructs with one-line definitions
- `references/examples.md` : copy-pasteable end-to-end examples (state machine, parser dispatch, slice patterns, complex destructure)
- `references/anti-patterns.md` : the eight pitfalls in full, with compiler error text and fix

---

## Sources

[book-pat]: https://doc.rust-lang.org/book/ch18-00-patterns.html
[ref-pat]: https://doc.rust-lang.org/reference/patterns.html
[match]: https://doc.rust-lang.org/book/ch06-02-match.html
[iflet]: https://doc.rust-lang.org/book/ch06-03-if-let.html
[whilelet]: https://doc.rust-lang.org/book/ch18-01-all-the-places-for-patterns.html#while-let-conditional-loops
[ref-match]: https://doc.rust-lang.org/reference/expressions/match-expr.html
[ref-let]: https://doc.rust-lang.org/reference/statements.html#let-statements
[ref-ident]: https://doc.rust-lang.org/reference/patterns.html#identifier-patterns
[rust165]: https://blog.rust-lang.org/2022/11/03/Rust-1.65.0/
[rust180]: https://blog.rust-lang.org/2024/07/25/Rust-1.80.0/
[rust188]: https://blog.rust-lang.org/2025/06/26/Rust-1.88.0/
[ed-iflet]: https://doc.rust-lang.org/edition-guide/rust-2024/temporary-if-let-scope.html

Last verified : 2026-05-19.

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
