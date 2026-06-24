---
name: rust-iterators-closures
description: > Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Iterators & Closures

Rust's closures and iterators are zero-cost abstractions: the compiler lowers iterator
chains to the same machine code as hand-written loops, with no heap allocation and no
virtual dispatch. The Book devotes Chapter 13 to them precisely because mastering these
two features is the primary path from working Rust to *idiomatic* Rust.

The three rules to internalize before writing any loop:

1. **Iterators are lazy.** Creating an adapter chain does nothing; you must attach a
   consuming adaptor (`collect`, `sum`, `fold`, `for_each`, …) to drive it.
2. **Closures capture by inference.** The compiler picks immutable borrow → mutable borrow
   → move in that order; `move` overrides the choice and forces ownership transfer.
3. **`Fn`/`FnMut`/`FnOnce` form a hierarchy.** Every closure implements at least `FnOnce`.
   `FnMut` additionally allows mutation of captured values. `Fn` additionally guarantees
   no mutation and no move-out — safe for concurrent calls.

---

## When to Use

Invoke this skill proactively when any of the following appear:

- A C-style `for i in 0..v.len()` loop that does not require the index
- An E0525 error ("expected a closure that implements `Fn`/`FnMut`")
- `.unwrap()` or `.expect()` inside a closure passed to `.map()` or `.filter()`
- An iterator adapter chain with no consuming terminal (compiler: "unused Map/Filter that must be used")
- A `move ||` closure that clones a large struct just before moving it, when `Arc` would suffice
- Any `FnOnce`/`FnMut`/`Fn` bound mismatch in a method accepting a closure

---

## Core Idioms

### ✅ Replace index loops with iterator adapters

```rust
// ❌ C-style index loop — verbose, error-prone off-by-one
let mut squares = Vec::new();
for i in 0..v.len() {
    squares.push(v[i] * v[i]);
}

// ✅ Iterator adapter — declarative, bounds-safe, zero overhead
let squares: Vec<_> = v.iter().map(|x| x * x).collect();
```

### ✅ Collect a fallible iterator into `Result<Vec<_>, E>`

```rust
// ❌ unwrap inside map — panics on the first error
let parsed: Vec<u32> = strings.iter().map(|s| s.parse::<u32>().unwrap_or(0)).collect();

// ✅ collect::<Result<Vec<_>, _>>() — returns first Err, or Ok(Vec)
let parsed: Result<Vec<u32>, _> = strings.iter().map(|s| s.parse()).collect();
```

The turbofish is load-bearing: `collect` is generic over the output container; without it
the compiler cannot resolve which `FromIterator` impl to use.

### ✅ Use `move` closures for spawned tasks and `'static` bounds

```rust
// ❌ borrows data that may not outlive the thread
let list = vec![1, 2, 3];
thread::spawn(|| println!("{list:?}"));   // compile error: list may not live long enough

// ✅ move transfers ownership into the closure; list is valid for 'static
thread::spawn(move || println!("{list:?}"));
```

Book (ch13-01): "If you want to force the closure to take ownership of the values it uses
in the environment even though the body of the closure doesn't strictly need ownership,
you can use the `move` keyword before the parameter list. This technique is mostly useful
when passing a closure to a new thread to move the data so that it's owned by the new
thread."

### ✅ Borrow, don't clone, inside closures

```rust
// ❌ clones every element to dodge the borrow checker — allocates needlessly
let names: Vec<String> = users.iter().map(|u| u.name.clone()).collect();

// ✅ collect references when only reading; clone only what the return type requires
let names: Vec<&str> = users.iter().map(|u| u.name.as_str()).collect();
// or, when owned Strings are genuinely needed, into_iter() avoids the clone:
let names: Vec<String> = users.into_iter().map(|u| u.name).collect();
```

### ✅ A lazy iterator must be consumed

```rust
// ❌ adapter chain with no terminal — dead code, compiler warns
v.iter().map(|x| x * 2);   // warning: unused `Map` that must be used

// ✅ attach a consuming adaptor
let doubled: Vec<_> = v.iter().map(|x| x * 2).collect();
// or drive with for_each when side-effects are the goal
v.iter().map(|x| x * 2).for_each(|x| println!("{x}"));
```

### ✅ Choose `iter` / `iter_mut` / `into_iter` deliberately

| Method | Yields | Collection after |
|---|---|---|
| `v.iter()` | `&T` — immutable refs | still valid |
| `v.iter_mut()` | `&mut T` — mutable refs | still valid |
| `v.into_iter()` | `T` — owned values | consumed / moved |

(Book ch13-02) `into_iter` takes ownership and yields owned values; `iter_mut` yields
mutable references.

---

## Forbidden Patterns

### Forbidden 1 — C-style index loop where an iterator adapter fits

```rust
// ❌
for i in 0..items.len() {
    process(items[i]);
}

// ✅
for item in &items {
    process(item);
}
// or, when the index is genuinely needed:
for (i, item) in items.iter().enumerate() {
    process_with_index(i, item);
}
```

**Why** (Book ch13-02): iterator adapters are lazy — the compiler can optimize chains to
the same code as a manual loop, with none of the off-by-one risk. Use `enumerate` when
the position is semantically required.

```bash
# Detector (heuristic — review matches manually)
grep -rn 'for [a-z_]* in 0\.\.' src/
```

---

### Forbidden 2 — Unused lazy iterator (map/filter with no terminal)

```rust
// ❌ closure never executes; compiler emits "unused Map that must be used"
records.iter().map(|r| transform(r));

// ✅ drive to completion
let out: Vec<_> = records.iter().map(|r| transform(r)).collect();
```

**Why** (Book ch13-02): iterators are lazy — they have no effect until you call methods
that consume the iterator. The compiler warning is the signal; treat it as an error.

```bash
# Detector — find files where .map( or .filter( appear without a downstream terminal
grep -rn '\.map(|' src/ | grep -v '\.collect\|\.sum\|\.fold\|\.for_each\|\.find\|\.any\|\.all\|\.count\|\.max\|\.min\|\.last\|\.next\|\.position\|//\|#\[' || true
# Note: this grep is a starting heuristic; multi-line chains may not match in one pass.
# Pair with: cargo clippy -- -D unused_must_use
```

---

### Forbidden 3 — Collect into Vec then immediately iterate again

```rust
// ❌ allocates a temporary Vec only to loop over it once
let filtered: Vec<_> = records.iter().filter(|r| r.active).collect();
for r in &filtered {
    notify(r);
}

// ✅ chain without materialising; the for loop is the terminal
for r in records.iter().filter(|r| r.active) {
    notify(r);
}
// or, when consuming into another collection in one pass:
let ids: Vec<_> = records.iter().filter(|r| r.active).map(|r| r.id).collect();
```

**Why**: the intermediate `Vec` is pure overhead. Iterator chains are lazy and the compiler
can fuse adjacent adapters. Allocate only when you actually need a concrete collection.

```bash
# Detector (heuristic — flag collect followed by for .. in &var on the next line)
grep -rn '\.collect()' src/ -A2 | grep 'for '
```

---

### Forbidden 4 — `.clone()` inside `.map()` to dodge a borrow

```rust
// ❌ clones every element unnecessarily
let labels: Vec<String> = items.iter().map(|i| i.label.clone()).collect();

// ✅ borrow if a reference is sufficient
let labels: Vec<&str> = items.iter().map(|i| i.label.as_str()).collect();

// ✅ or consume ownership when you need the owned value
let labels: Vec<String> = items.into_iter().map(|i| i.label).collect();
```

**Why** (Book ch13-01): closures capture by immutable borrow by default. A `.clone()`
that exists only because `into_iter()` was forgotten pays an allocation tax on every
element. Reach for `.clone()` only when the collection must survive and an owned copy is
genuinely needed by the downstream type.

```bash
# Detector (heuristic — review each match; not every .clone() inside .map() is wrong)
grep -rn '\.map(|.*\.clone()' src/
```

---

### Forbidden 5 — `unwrap()` / `expect()` inside `.map()` or `.filter()`

```rust
// ❌ panics on first parse failure; hides the error from the caller
let counts: Vec<u32> = raw.iter().map(|s| s.parse::<u32>().unwrap()).collect();

// ✅ surface errors through the return type
let counts: Result<Vec<u32>, _> = raw.iter().map(|s| s.parse::<u32>()).collect();

// ✅ or, when a default is truly correct (not just convenient):
let counts: Vec<u32> = raw.iter().map(|s| s.parse().unwrap_or(0)).collect();
```

**Why** (Book ch13-01, ch13-02 + rust-error-handling skill): panics inside iterator
closures propagate as unwind at an unpredictable point, bypassing structured error
handling. `collect::<Result<Vec<_>, _>>()` short-circuits on the first `Err` and returns
it to the caller — the correct mechanism for fallible iteration.

```bash
# Detector (heuristic — grep is line-oriented; multi-line closures need manual review)
grep -rn -E '\.map\(\|.*\.unwrap\(\)' src/ | grep -v '//'
grep -rn -E '\.map\(\|.*\.expect\(' src/ | grep -v '//'
grep -rn -E '\.filter\(\|.*\.unwrap\(\)' src/ | grep -v '//'
grep -rn -E '\.filter\(\|.*\.expect\(' src/ | grep -v '//'
```

---

### Forbidden 6 — `move` closure capturing a large struct by value unnecessarily

```rust
// ❌ entire Config (hundreds of bytes) moved into every spawned task
// note: tokio::spawn requires a Future; handle must be async fn
let cfg = Config::load();
for id in ids {
    let cfg = cfg.clone();  // clones just to move
    tokio::spawn(async move { handle(id, cfg).await });
}

// ✅ wrap in Arc; each task clones a pointer, not the struct
let cfg = Arc::new(Config::load());
for id in ids {
    let cfg = Arc::clone(&cfg);
    tokio::spawn(async move { handle(id, cfg).await });
}
```

**Why** (Book ch15-04, ch16-03): `move` transfers ownership of every captured variable.
For large or non-`Copy` data shared across tasks, `Arc` (atomic reference count) is the
standard mechanism — clone the `Arc`, not the payload. Reserve `move` for data that is
genuinely single-owner per closure invocation. See also the rust-smart-pointers skill.

```bash
# Detector (heuristic — move closures passed to spawn)
grep -rn 'spawn(async move' src/ -B5 | grep -v 'Arc::\|Rc::\|//'
```

---

### Forbidden 7 — Using `FnOnce` bound where the closure is called repeatedly

```rust
// ❌ FnOnce allows only one call; loop will fail to compile if closure moves a value out
fn apply_all<F: FnOnce(u32) -> u32>(items: &[u32], f: F) -> Vec<u32> {
    items.iter().map(|x| f(*x)).collect()  // compile error: f moved on first call
}

// ✅ FnMut for closures that may mutate state but are called multiple times
fn apply_all<F: FnMut(u32) -> u32>(items: &[u32], mut f: F) -> Vec<u32> {
    items.iter().map(|x| f(*x)).collect()
}

// ✅ Fn for stateless, concurrent-safe multi-call
fn apply_all<F: Fn(u32) -> u32>(items: &[u32], f: F) -> Vec<u32> {
    items.iter().map(|x| f(*x)).collect()
}
```

**Why** (Book ch13-01): the three traits form a hierarchy — `Fn ⊆ FnMut ⊆ FnOnce`.
`FnOnce` promises the closure is called at most once (it may move values out of itself).
Use the *loosest* bound that satisfies the contract:
- Call once, may move out → `FnOnce`
- Call many times, may mutate → `FnMut`
- Call many times concurrently, no mutation → `Fn`

Using a tighter bound than necessary rejects valid callers; using a looser bound than
necessary signals wrong intent and may compile but behave incorrectly.

```bash
# Detector (heuristic — flags ALL FnOnce bounds, including valid single-call uses)
# Review each match: flag only FnOnce used as the bound for a closure passed to
# .map()/.filter()/an iterator adapter where the closure is invoked more than once.
grep -rn 'FnOnce' src/ | grep -v '// \|#\[cfg(test\|test::'
```

---

## Verification Hooks

Run these detectors together before committing iterator/closure changes:

```bash
# Hook 1 — C-style index loops
grep -rn 'for [a-z_]* in 0\.\.' src/

# Hook 2 — Unused iterator chains (complement with cargo clippy -D unused_must_use)
grep -rn '\.map(|' src/ | grep -v '\.collect\|\.sum\|\.fold\|\.for_each\|\.find\|\.any\|\.all\|\.count\|\.max\|\.min\|\.last\|\.next\|\.position\|//\|#\['

# Hook 3 — Needless collect+iterate (heuristic — multi-line chains require manual check)
grep -rn '\.collect()' src/ -A2 | grep 'for '

# Hook 4 — .clone() inside .map() (heuristic — not every hit is wrong)
grep -rn '\.map(|.*\.clone()' src/

# Hook 5 — unwrap/expect inside map/filter (heuristic — line-oriented; review multi-line closures manually)
grep -rn -E '\.map\(\|.*\.unwrap\(\)' src/ | grep -v '//'
grep -rn -E '\.map\(\|.*\.expect\(' src/ | grep -v '//'
grep -rn -E '\.filter\(\|.*\.unwrap\(\)' src/ | grep -v '//'
grep -rn -E '\.filter\(\|.*\.expect\(' src/ | grep -v '//'

# Hook 6 — move closures passed to spawn without Arc (heuristic)
grep -rn 'spawn(async move' src/ -B5 | grep -v 'Arc::\|Rc::\|//'

# Hook 7 — FnOnce in iterator-style contexts (heuristic — flags all FnOnce; see Forbidden 7)
grep -rn 'FnOnce' src/ | grep -v '// \|#\[cfg(test\|test::'
```

---

## Book References

- **Chapter 13 — Functional Language Features: Iterators and Closures**
  https://doc.rust-lang.org/book/ch13-00-functional-features.html
- **Chapter 13.1 — Closures: Anonymous Functions that Capture Their Environment**
  https://doc.rust-lang.org/book/ch13-01-closures.html
  (Capturing references or moving ownership; the three `Fn` traits; when each is required)
- **Chapter 13.2 — Processing a Series of Items with Iterators**
  https://doc.rust-lang.org/book/ch13-02-iterators.html
  (Iterator trait + `next`; consuming vs non-consuming adaptors; `iter` / `iter_mut` /
  `into_iter`; laziness; zero-cost abstraction)

---

## Related Skills

- **rust-ownership-borrowing** — borrow checker mechanics that govern what closures can capture
- **rust-error-handling** — `AppError`, `?`, `FromServerFnError`; pairs with the
  `collect::<Result<_,_>>()` pattern in Forbidden 5
- **rust-collections** — `Vec`, `HashMap`, `BTreeMap` construction patterns; `collect()`
  target types

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
