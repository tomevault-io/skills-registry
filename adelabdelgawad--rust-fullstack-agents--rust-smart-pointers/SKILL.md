---
name: rust-smart-pointers
description: Detect and fix smart-pointer and interior-mutability bugs in Rust. Covers Box<T> (heap allocation, recursive types, trait objects), Deref/DerefMut coercions, Drop, Rc<T> (shared single-thread ownership), Arc<T> (atomic multi-thread), RefCell<T> (interior mutability, runtime borrow check, BorrowMutError panics), Cell<T>, RwLock<T>, Weak<T> (non-owning back-pointers), and reference-cycle memory leaks. Auto-triggers when: Rc<RefCell<…>> combinations appear in code review, RefCell::borrow_mut call sites are present, recursive enum/struct definitions need sizing, !Send compiler errors mention Rc, Arc<Mutex> usage needs review, or Weak::upgrade patterns need analysis. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Smart Pointers & Interior Mutability

Rust smart pointers implement `Deref`/`DerefMut` (transparent dereferencing) and `Drop` (deterministic cleanup). Unlike plain references, they *own* the data they point to and carry extra metadata. Choosing the wrong pointer type causes either a compile error (Rc across threads), a runtime panic (RefCell double-borrow), or a silent memory leak (Rc cycle with no Weak).

**Book source:** *The Rust Programming Language*, Chapter 15 — [Smart Pointers](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html)

---

## When to Use

Invoke this skill when:
- An `Rc<RefCell<…>>` combination appears in code review — check for reference cycles and missing `Weak` back-pointers
- A `RefCell::borrow_mut` call site is present — confirm no two mutable guards overlap in scope
- A recursive enum or struct definition needs sizing — ensure `Box<T>` is used to break the infinite-size cycle
- A `!Send` compiler error mentions `Rc` — the fix is `Arc` with appropriate synchronization
- `Arc<Mutex<T>>` usage needs review — confirm shared mutable state is actually concurrent; plain `&mut T` may suffice
- A `Weak::upgrade` call appears — verify the upgrade result is handled rather than unwrapped unconditionally

---

## Which Pointer When

| Need | Use | Notes |
|---|---|---|
| Heap-allocate a sized value | `Box<T>` | Zero overhead beyond the allocation |
| Recursive / self-referential type | `Box<T>` | Breaks infinite-size cycle with one level of indirection |
| Trait object (`dyn Trait`) | `Box<dyn Trait>` | Fixed-size pointer to heap-allocated erased type |
| Multiple read-only owners, single thread | `Rc<T>` | Cheap clone; `!Send`, `!Sync` |
| Multiple read-only owners, multi-thread | `Arc<T>` | Atomic ref-count; `Send + Sync` when `T: Send + Sync` |
| Interior mutability, single thread | `RefCell<T>` | Panics on borrow rule violation at runtime |
| Copy types, single thread, no borrow guard | `Cell<T>` | Cheaper than RefCell; get/set, no references |
| Shared mutability, multi-thread | `Arc<Mutex<T>>` or `Arc<RwLock<T>>` | Mutex blocks; RwLock allows concurrent reads |
| Back-pointer / observer without ownership | `Weak<T>` | Upgrade returns `Option<Rc<T>>`; never keeps value alive |

---

## Core Idioms

### Box for recursive types

```rust
// ✅ Box breaks the infinite-size cycle
enum List {
    Cons(i32, Box<List>),
    Nil,
}

// ❌ Infinite size — compiler error E0072
enum List {
    Cons(i32, List),
    Nil,
}
```

The Book ([ch15-01](https://doc.rust-lang.org/book/ch15-01-box.html)): the `Cons` variant's size becomes `i32` + pointer-width. Without Box the type has no finite representation.

### Rc for shared read-only ownership

```rust
use std::rc::Rc;

// ✅ Both 'b' and 'c' share the same allocation; Rc::clone is cheap (increments count)
let a = Rc::new(vec![1, 2, 3]);
let b = Rc::clone(&a);
let c = Rc::clone(&a);

// ❌ Not a deep copy, but written as .clone() — misleads readers into thinking it's expensive
let b = a.clone();  // same effect but obscures intent; use Rc::clone(&a) by convention
```

### Weak for parent/back-pointer to break cycles

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

// ✅ child holds a Weak reference to parent — parent can be dropped without a cycle
struct Node {
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}

// ❌ both strong — creates a cycle, neither count reaches 0, memory leaks
struct Node {
    parent: RefCell<Rc<Node>>,   // strong back-pointer → cycle
    children: RefCell<Vec<Rc<Node>>>,
}
```

The Book ([ch15-06](https://doc.rust-lang.org/book/ch15-06-reference-cycles.html)): parent→child is `Rc` (ownership); child→parent is `Weak` (non-ownership). `upgrade()` returns `None` once the parent is dropped — no dangling pointer, no leak.

### RefCell runtime-borrow risk

```rust
use std::cell::RefCell;

let data = RefCell::new(vec![1, 2, 3]);

// ✅ guards dropped before second borrow
{
    let mut w = data.borrow_mut();
    w.push(4);
}
let r = data.borrow();  // OK: mutable guard dropped

// ❌ two borrow_mut guards alive simultaneously → panic: "already borrowed"
let mut w1 = data.borrow_mut();
let mut w2 = data.borrow_mut();  // BorrowMutError at runtime
```

The Book ([ch15-05](https://doc.rust-lang.org/book/ch15-05-interior-mutability.html)): RefCell tracks a dynamic borrow count. Acquiring a second mutable guard while one is still live panics — the compile-time borrowing rules are enforced at runtime instead.

---

## Forbidden Patterns

### Forbidden 1 — Rc<RefCell<T>> Cycle with No Weak (Memory Leak)

**Forbidden:**
```rust
// ❌ tail field holds Rc<List> — a.tail points to b, b.tail points to a
// Neither strong_count ever reaches 0; both allocations leak for the lifetime of the process
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}
impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match self { List::Cons(_, t) => Some(t), List::Nil => None }
    }
}
let a = Rc::new(List::Cons(5, RefCell::new(Rc::new(List::Nil))));
let b = Rc::new(List::Cons(10, RefCell::new(Rc::clone(&a))));
if let Some(tail) = a.tail() {
    *tail.borrow_mut() = Rc::clone(&b);  // cycle: a→b→a; strong_count stays at 2
}
```

**Why (Book [ch15-06](https://doc.rust-lang.org/book/ch15-06-reference-cycles.html)):** `Rc<T>` cleans up only when `strong_count` reaches 0. A cycle means each node holds a strong reference to the other; `strong_count` never falls to 0 even after all user-facing bindings are dropped. The heap allocation leaks for the lifetime of the process.

**Fix — change the back-pointer field to `RefCell<Weak<List>>`:**
```rust
// ✅ tail field holds Weak<List> — downgrade doesn't bump strong_count, no cycle
use std::rc::{Rc, Weak};
use std::cell::RefCell;

enum List {
    Cons(i32, RefCell<Weak<List>>),
    Nil,
}
impl List {
    fn tail(&self) -> Option<&RefCell<Weak<List>>> {
        match self { List::Cons(_, t) => Some(t), List::Nil => None }
    }
}
let a = Rc::new(List::Cons(5, RefCell::new(Weak::new())));  // Nil-equivalent tail
let b = Rc::new(List::Cons(10, RefCell::new(Rc::downgrade(&a))));
if let Some(tail) = a.tail() {
    *tail.borrow_mut() = Rc::downgrade(&b);  // Weak back-pointer; strong_count stays at 1
}
// When a and b go out of scope, strong_count reaches 0 and both are dropped correctly
```

```bash
# Detector — files using Rc/RefCell together without any Weak (heuristic; check both annotation and constructor forms)
for f in $(grep -rlE 'Rc<RefCell|RefCell<Rc|Rc::new\(.*RefCell::new|RefCell::new\(.*Rc::new' src/); do
  grep -qE 'Weak<|Rc::downgrade' "$f" || echo "WARN: $f has Rc+RefCell but no Weak — check for cycles"
done
```

---

### Forbidden 2 — RefCell::borrow_mut While Another Borrow Is Live (BorrowMutError Panic)

**Forbidden:**
```rust
// ❌ w1 guard still in scope when w2 is acquired → panic at runtime
let cell = RefCell::new(0i32);
let mut w1 = cell.borrow_mut();
let mut w2 = cell.borrow_mut();  // thread 'main' panicked: already borrowed: BorrowMutError
```

**Why (Book [ch15-05](https://doc.rust-lang.org/book/ch15-05-interior-mutability.html)):** `RefCell<T>` enforces Rust's borrow rules dynamically. The `RefMut<T>` guard increments an internal mutable-borrow counter on acquisition. A second `borrow_mut()` call while the counter is non-zero panics immediately — this is a runtime crash, not a compile error.

**Fix:** Narrow guard lifetimes so they don't overlap. Drop explicitly with `drop(w1)` if needed, or use a block scope.

```bash
# Detector — files with multiple borrow_mut calls (each warrants manual scope review)
grep -rno 'borrow_mut()' src/ | cut -d: -f1 | sort | uniq -d
# Review each flagged file: confirm no two RefMut guards are live simultaneously in the same scope
```

---

### Forbidden 3 — Rc<T> Shared Across Threads (!Send Violation)

**Forbidden:**
```rust
use std::rc::Rc;
use std::thread;

let shared = Rc::new(vec![1, 2, 3]);
let clone = Rc::clone(&shared);
thread::spawn(move || {
    println!("{:?}", clone);  // compile error: Rc<Vec<i32>> cannot be sent between threads safely
});
```

**Why (Book [ch15-04](https://doc.rust-lang.org/book/ch15-04-rc.html), [ch16-04](https://doc.rust-lang.org/book/ch16-04-extensible-concurrency-sync-and-send.html)):** `Rc<T>` is only for single-threaded use. The compiler enforces `!Send` on `Rc<T>` — moving an `Rc` clone to another thread is a hard compile error. As ch16-04 explains, if two threads updated the reference count at the same time, the count could corrupt. The fix is `Arc<T>`, which uses atomic operations and is `Send + Sync` when `T: Send + Sync`.

**Fix:**
```rust
use std::sync::Arc;
let shared = Arc::new(vec![1, 2, 3]);
let clone = Arc::clone(&shared);
thread::spawn(move || println!("{:?}", clone));  // ✅ Arc is Send + Sync
```

```bash
# Detector — files that use Rc AND spawn threads (actionable; low false-positive rate)
# Caveat: Rc is legitimate everywhere in WASM/single-threaded targets (e.g. Leptos islands), so a bare
# "grep Rc::" would be far too noisy; this form only flags Rc in files that also spawn threads.
grep -rln 'Rc::' src/ | xargs grep -lE 'thread::spawn|tokio::spawn' 2>/dev/null
```

---

### Forbidden 4 — Arc<Mutex<T>> Where a Plain &mut or Single Owner Suffices (Needless Sync Overhead)

**Forbidden:**
```rust
// ❌ data is only ever accessed from one place; Arc<Mutex> adds atomic ops and lock contention
async fn process(data: Arc<Mutex<Vec<u8>>>) {
    let mut guard = data.lock().unwrap();
    guard.push(42);
}
```

**Why (Book [ch16-03](https://doc.rust-lang.org/book/ch16-03-shared-state.html)):** `Arc` uses atomic reference counting — more expensive than `Rc` because atomics carry a performance penalty not present in single-threaded counting. `Mutex` adds a lock acquisition on every access. When the value has a single owner at a time, `&mut T` or owned `T` carries the same safety guarantee at zero overhead. As ch16-03 notes, thread safety comes with a performance cost you should only pay when you actually need concurrent access. Unnecessary `Arc<Mutex>` obscures the true ownership model and hides opportunities for better parallelism.

**Fix:** Pass `&mut Vec<u8>` or owned `Vec<u8>` when there is genuinely one concurrent accessor. Introduce `Arc<Mutex<T>>` only when shared mutable state across concurrent tasks is unavoidable.

```bash
# Detector — Arc<Mutex> with no nearby spawn (heuristic; covers annotation and constructor forms)
# Caveat: excludes spawn/rayon/crossbeam files (likely genuinely concurrent). A single-accessor
# Arc<Mutex> inside an async fn with no spawn — like the ❌ above — IS flagged. Review each hit.
grep -rnE 'Arc<Mutex<|Arc::new\(Mutex::new' src/ | grep -vE 'spawn|rayon|crossbeam'
```

---

### Forbidden 5 — Box<T> Where a Plain Value or Borrow Suffices

**Forbidden:**
```rust
// ❌ unnecessary heap allocation — i32 is Copy, stack allocation is free
fn add_one(x: Box<i32>) -> Box<i32> {
    Box::new(*x + 1)
}

// ❌ Box<String> — String already owns heap data; double-indirection wastes a pointer
struct Config {
    name: Box<String>,
}
```

**Why:** `Box<T>` adds a heap allocation whose only benefit is indirection or enabling an unsized type. For `Copy` types or types that already own heap data (`String`, `Vec<T>`), wrapping in `Box` adds a pointer-indirection level with no benefit — `String` already allocates on the heap; the `Box` just adds another pointer hop. The Clippy lint [`clippy::box_collection`](https://rust-lang.github.io/rust-clippy/master/index.html#box_collection) flags `Box<String>`, `Box<Vec<T>>`, and similar double-heap patterns automatically.

**Fix:** Use the type directly. Use `Box<T>` only when the value must live on the heap (recursive type, large struct transferred by ownership across a move-heavy call graph, or `Box<dyn Trait>`).

```bash
# Detector — Box wrapping types that already own heap data (clippy::box_collection also catches these)
grep -rnE 'Box<String>|Box<Vec<|Box<HashMap<|Box<BTreeMap<' src/
```

---

### Forbidden 6 — .clone() on Rc<T> Misread as Deep Clone

**Forbidden:**
```rust
// ❌ looks like a deep copy but is only a ref-count increment — misleads reviewers
let original = Rc::new(expensive_computation());
let copy = original.clone();  // does NOT copy the data
```

**Why (Book [ch15-04](https://doc.rust-lang.org/book/ch15-04-rc.html)):** `Rc::clone` only increments the reference count — O(1) with no heap allocation. Calling `.clone()` on an `Rc<T>` has the same effect but is visually indistinguishable from a deep clone. Code reviewers and profiler annotations will flag `.clone()` as a potential performance concern; `Rc::clone(&val)` makes the intent explicit and silent to analysis tools looking for expensive clones.

**Fix:**
```rust
let copy = Rc::clone(&original);  // ✅ explicit, idiomatic, zero-cost
```

```bash
# Detector — flag all .clone() calls in files that use Rc/Arc (review for intent)
# Catches both type-annotation form (Rc<T>) and call-site form (Rc::new, Rc::clone)
grep -rlnE 'Rc::|Arc::|Rc<|Arc<' src/ | xargs grep -n '\.clone()' 2>/dev/null | grep -vE 'Rc::clone|Arc::clone'
```

---

### Forbidden 7 — Cell<T> or RefCell<T> for Data That Is Never Mutated

**Forbidden:**
```rust
// ❌ data is read-only after construction; Cell/RefCell overhead is wasted
struct Config {
    max_retries: Cell<u32>,
    timeout_ms: RefCell<u64>,
}
```

**Why (Book [ch15-05](https://doc.rust-lang.org/book/ch15-05-interior-mutability.html)):** `Cell<T>` and `RefCell<T>` exist to provide interior mutability — the ability to mutate through a shared (`&self`) reference when the compiler cannot verify the borrow rules statically. If the value is never mutated after construction, the indirection and dynamic borrow tracking are pure overhead. The `Cell`/`RefCell` wrapper also signals to readers that mutation is expected, causing unnecessary cognitive load during code review.

**Fix:** Use plain fields, `const`, or `static` for immutable data. Reserve `Cell`/`RefCell` for fields that genuinely need to be mutated through a shared reference (lazy initialization, mock recorders, arena allocators).

```bash
# Detector — list all Cell/RefCell usages; manually verify each has a corresponding mutation call
grep -rnE 'Cell<|RefCell<' src/
# Then confirm mutation callers exist for each site:
grep -rnE 'borrow_mut\(\)|\.set\(|\.replace\(' src/
```

---

## Book References

| Topic | URL |
|---|---|
| Chapter 15 overview — Smart Pointers | https://doc.rust-lang.org/book/ch15-00-smart-pointers.html |
| 15.1 — Using `Box<T>` to Point to Data on the Heap | https://doc.rust-lang.org/book/ch15-01-box.html |
| 15.4 — `Rc<T>`, the Reference-Counted Smart Pointer | https://doc.rust-lang.org/book/ch15-04-rc.html |
| 15.5 — `RefCell<T>` and the Interior Mutability Pattern | https://doc.rust-lang.org/book/ch15-05-interior-mutability.html |
| 15.6 — Reference cycles and Weak\<T\> | https://doc.rust-lang.org/book/ch15-06-reference-cycles.html |
| 16.3 — Shared-State Concurrency (Arc\<Mutex\<T\>\>) | https://doc.rust-lang.org/book/ch16-03-shared-state.html |
| 16.4 — Extensible Concurrency with `Send` and `Sync` | https://doc.rust-lang.org/book/ch16-04-extensible-concurrency-sync-and-send.html |
| Clippy: box_collection | https://rust-lang.github.io/rust-clippy/master/index.html#box_collection |

---

## Verification Hooks

Run this sweep to catch all seven forbidden patterns in one pass:

```bash
# F1 — Rc+RefCell without Weak (reference cycle risk)
for f in $(grep -rlE 'Rc<RefCell|RefCell<Rc|Rc::new\(.*RefCell::new|RefCell::new\(.*Rc::new' src/); do
  grep -qE 'Weak<|Rc::downgrade' "$f" || echo "WARN F1: $f — Rc+RefCell without Weak"
done

# F2 — files with multiple borrow_mut calls (overlapping guard risk)
grep -rno 'borrow_mut()' src/ | cut -d: -f1 | sort | uniq -d | sed 's/^/WARN F2: /'

# F3 — Rc used in files that also spawn threads (should be Arc)
grep -rln 'Rc::' src/ | xargs grep -lE 'thread::spawn|tokio::spawn' 2>/dev/null | sed 's/^/WARN F3: /'
# Caveat: Rc is legitimate in WASM/single-threaded Leptos island code — review context before flagging

# F4 — Arc<Mutex> with no nearby spawn (needless sync overhead; review each hit)
grep -rnE 'Arc<Mutex<|Arc::new\(Mutex::new' src/ | grep -vE 'spawn|rayon|crossbeam' | sed 's/^/WARN F4: /'

# F5 — Box wrapping already-heap-owning types (run clippy::box_collection for authoritative coverage)
grep -rnE 'Box<String>|Box<Vec<|Box<HashMap<|Box<BTreeMap<' src/ | sed 's/^/WARN F5: /'

# F6 — .clone() on Rc/Arc values (should be Rc::clone / Arc::clone)
grep -rlnE 'Rc::|Arc::|Rc<|Arc<' src/ | xargs grep -n '\.clone()' 2>/dev/null | grep -vE 'Rc::clone|Arc::clone' | sed 's/^/WARN F6: /'

# F7 — Cell/RefCell with no mutation callsite in same file
for f in $(grep -rlE 'Cell<|RefCell<' src/); do
  grep -qE 'borrow_mut\(\)|\.set\(|\.replace\(' "$f" || echo "WARN F7: $f — Cell/RefCell with no mutation call"
done
```

---

## Related Skills

- **rust-ownership-borrowing** — foundational ownership and lifetime rules that smart pointers extend
- **rust-concurrency** — Mutex, RwLock, channels, and Send/Sync bounds that pair with Arc<T>
- **rust-lifetimes** — lifetime annotations that interact with Deref coercions and borrow scopes
- **rust-error-handling** — how `RefCell::borrow_mut` panics propagate through server fn and handler call stacks (see also leptos-hydration-discipline Forbidden 9: `Rc`/`RefCell` held across `.await` must become `Arc`/drop-before-await)

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
