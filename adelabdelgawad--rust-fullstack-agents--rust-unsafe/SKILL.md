---
name: rust-unsafe
description: Disciplined unsafe Rust — five superpowers (dereference raw pointer *const/*mut, call unsafe fn/method, access/modify mutable static, implement unsafe trait, access union fields), SAFETY comment invariants, minimizing unsafe scope, wrapping unsafe in safe abstractions, undefined behavior (UB) taxonomy (null deref, dangling pointer, data race, invalid transmute, aliasing violation), static mut → OnceLock/atomic migration, std::mem::transmute misuse detection, unsafe impl Send/Sync soundness, FFI/extern boundary discipline, union field access. Auto-triggers on ANY `unsafe` block/fn, any raw-pointer deref (*const/*mut), any `static mut`, any `std::mem::transmute`, any FFI/`extern` boundary, any `unsafe impl`. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Unsafe Rust (Discipline)

`unsafe` is an escape hatch that transfers compiler correctness guarantees onto you. Use it only when the five safe-alternative table entries (below) are exhausted, keep every block surgically small, and prove every invariant with a `// SAFETY:` comment. In this codebase `unsafe` is exceedingly rare — flag **every instance** for human review.

## When to Use

Invoke before writing, reviewing, or modifying **any** of the following:
- An `unsafe` block, `unsafe fn`, or `unsafe impl`.
- Raw pointer creation or dereference (`*const T`, `*mut T`).
- `static mut` access or modification.
- `std::mem::transmute` or `std::mem::transmute_copy`.
- Any `extern "C"` block or `#[no_mangle]` export.
- `union` field reads.

---

## The Five Unsafe Superpowers

*The Rust Programming Language*, Chapter 20.1 — "Unsafe Rust":

> "You can take five actions in unsafe Rust that you can't in safe Rust, which we call *unsafe superpowers*."

| # | Superpower | What it unlocks | Primary risk |
|---|---|---|---|
| 1 | Dereference a raw pointer | Read/write through `*const T` / `*mut T` | Null deref, dangling pointer, aliasing UB |
| 2 | Call an unsafe function or method | Invoke code whose preconditions the compiler cannot verify | Invariant violation propagates silently |
| 3 | Access or modify a mutable static variable | Read/write a `static mut` | Data race across threads |
| 4 | Implement an unsafe trait | Implement `Send`, `Sync`, or custom `unsafe trait` | Unsound trait contract; cross-thread UB |
| 5 | Access fields of a `union` | Read a union field as a specific type | Type confusion; reading uninitialised bytes |

**Critical invariant** (Book ch20.1):

> "`unsafe` doesn't turn off the borrow checker or disable any of Rust's other safety checks: If you use a reference in unsafe code, it will still be checked."

`unsafe` grants only the five superpowers above. Lifetime rules still apply; the borrow checker still runs.

---

## Safe Alternatives — Prefer These First

| Temptation | Safe alternative |
|---|---|
| `static mut` counter / flag | `std::sync::atomic::AtomicU64` / `AtomicBool` |
| Lazily-initialised global | `std::sync::OnceLock<T>` or `once_cell::sync::Lazy<T>` |
| `transmute` between numeric types | `f32::from_bits(u)` / `f64::to_bits()` / `u32::from_ne_bytes(bytes)` |
| `transmute` for `#[repr(C)]` structs | `bytemuck::cast` / `bytemuck::cast_ref` |
| Splitting `&mut [T]` into disjoint slices | `slice::split_at_mut` (safe stdlib fn) |
| Calling C stdlib functions | Wrap in a crate that already provides safe bindings (`libc`, `nix`) |
| `union` for type punning | `#[repr(C)]` struct + `bytemuck` |

---

## Core Idioms

### ✅ Minimal unsafe scope — shrink the block to the single statement that needs it

```rust
fn read_raw(ptr: *const u32) -> u32 {
    // SAFETY: ptr non-null, aligned, valid for one u32 read; no live alias.
    unsafe { *ptr }
}
```

```rust
// BAD: marks the whole body unsafe, spreading implicit trust to safe arithmetic.
unsafe fn read_raw_bad(ptr: *const u32) -> u32 { let x = *ptr; x + 1 }
```

### ✅ `// SAFETY:` comment — state the upheld invariant, not what the code does

```rust
// GOOD: states the contract
// SAFETY: buf allocated for len bytes; no concurrent reference; allocator alignment.
let slice = unsafe { std::slice::from_raw_parts(buf, len) };

// BAD: describes the operation, not why it is sound
// Safety: calling from_raw_parts
let slice = unsafe { std::slice::from_raw_parts(buf, len) };
```

### ✅ Wrap unsafe in a safe public API — contain the blast radius

```rust
pub fn split_at_mid(slice: &mut [u32]) -> (&mut [u32], &mut [u32]) {
    let mid = slice.len() / 2;
    let ptr = slice.as_mut_ptr();
    // SAFETY: [0,mid) and [mid,len) are disjoint; lifetimes bound to `slice`.
    unsafe {
        (std::slice::from_raw_parts_mut(ptr, mid),
         std::slice::from_raw_parts_mut(ptr.add(mid), slice.len() - mid))
    }
}
```

### ✅ Replace `static mut` with an atomic or `OnceLock`

```rust
// BAD: data race across threads
static mut COUNTER: u32 = 0;

// GOOD: no unsafe at call sites
static COUNTER: std::sync::atomic::AtomicU32 = std::sync::atomic::AtomicU32::new(0);
```

### ✅ Prefer `&T` / `&[T]` over raw pointers — reach for `*const`/`*mut` only when references cannot express the lifetime

```rust
// BAD: raw pointer when a reference works
fn sum(ptr: *const u32, len: usize) -> u32 { ... }

// GOOD: borrow checker enforces validity and lifetime automatically
fn sum(slice: &[u32]) -> u32 { slice.iter().sum() }
```

Raw pointers opt out of lifetime tracking and alias analysis. Every `*const`/`*mut` that could be a `&T`/`&mut T` or `&[T]` is an unnecessary unsafe surface.

---

## Forbidden Patterns

### Forbidden 1 — `unsafe` block with NO `// SAFETY:` comment

**Forbidden:**
```rust
unsafe { *ptr }           // ← no SAFETY comment
unsafe { COUNTER += 1; }  // ← no SAFETY comment
```

**Required:**
```rust
// SAFETY: ptr is non-null and valid for a u32 read; exclusive access
// guaranteed by the calling invariant.
unsafe { *ptr }
```

**Why (Book ch20.1):** "Whenever we perform an unsafe operation, it is idiomatic to write a comment starting with `SAFETY` to explain how the safety rules are upheld." Without it every reviewer must reconstruct the invariant from scratch.

```bash
# Detector (grep matches the same line only; preceding-line SAFETY comments are manual review)
grep -rn 'unsafe {' src/ | grep -v '// SAFETY'
grep -rnE '^unsafe fn|    unsafe fn' src/ | grep -v '// SAFETY'
```

---

### Forbidden 2 — Oversized unsafe scope (entire function marked `unsafe` when only one statement needs it)

```rust
// BAD: whole body trusted; arithmetic and collect() inherit the unsafe label.
unsafe fn process(ptr: *const u32, len: usize) -> Vec<u32> {
    std::slice::from_raw_parts(ptr, len).iter().copied().collect()
}

// GOOD: one line is unsafe; everything else remains borrow-checked.
fn process(ptr: *const u32, len: usize) -> Vec<u32> {
    // SAFETY: caller guarantees ptr valid for len aligned u32 reads; no concurrent mutation.
    let slice = unsafe { std::slice::from_raw_parts(ptr, len) };
    slice.iter().copied().collect()
}
```

**Why (Book ch20.1):** "Keep `unsafe` blocks small; you'll be thankful later when you investigate memory bugs." A whole-function `unsafe` signals callers bear the full precondition burden, inflating the unsafe surface needlessly.

```bash
# Detector — top-level unsafe fn (these may be legitimate; review each one)
grep -rnE '^pub unsafe fn|^    pub unsafe fn|^unsafe fn|^    unsafe fn' src/
```

---

### Forbidden 3 — `std::mem::transmute` where a safe cast fits

**Forbidden:**
```rust
let bits: u32 = unsafe { std::mem::transmute(1.0f32) };
let value: u32 = unsafe { std::mem::transmute::<[u8; 4], u32>(bytes) };
```

**Required:**
```rust
let bits: u32 = 1.0f32.to_bits();          // safe stdlib method
let value: u32 = u32::from_ne_bytes(bytes); // safe stdlib method
// For #[repr(C)] struct casts: bytemuck::cast(&my_struct)
```

**Why (Book ch20.1 + Rustonomicon):** `transmute` reinterprets raw bytes as a different type, bypassing alignment, validity, niche, and drop-glue invariants simultaneously — one wrong type parameter is silent UB. The safe alternatives cover virtually all legitimate use cases.

```bash
# Detector (SAFETY comments appear on the preceding line; this grep does NOT confirm annotations are absent — all matches require manual inspection)
grep -rnE 'std::mem::transmute|mem::transmute' src/
```

---

### Forbidden 4 — `static mut` access (data race hazard)

**Forbidden:**
```rust
static mut REGISTRY: Vec<String> = Vec::new();

fn register(name: String) {
    unsafe { REGISTRY.push(name); } // data race if called from multiple threads
}
```

**Required:**
```rust
// Option A — atomic (no init, no lock)
static COUNTER: std::sync::atomic::AtomicU32 = std::sync::atomic::AtomicU32::new(0);

// Option B — lazily-initialised collection
static REGISTRY: std::sync::OnceLock<std::sync::Mutex<Vec<String>>> =
    std::sync::OnceLock::new();
fn registry() -> &'static std::sync::Mutex<Vec<String>> {
    REGISTRY.get_or_init(|| std::sync::Mutex::new(Vec::new()))
}
```

**Why (Book ch20.1):** "If two threads are accessing the same mutable global variable, it can cause a data race." `OnceLock`, atomics, and `Mutex`-wrapped statics provide thread-safe alternatives with no unsafe call sites.

```bash
# Detector
grep -rn 'static mut ' src/
```

---

### Forbidden 5 — Raw pointer dereference without null/dangling justification in `// SAFETY:`

**Forbidden:**
```rust
// SAFETY: this should be fine   ← vague; does not address null, alignment, aliasing
unsafe { *ptr }
```

**Required:** the `// SAFETY:` comment must address non-null (or null checked above), alignment for `T`, memory validity and initialisation, no concurrent mutable alias, and that the resulting reference lifetime does not outlive the allocation.

```rust
// SAFETY: ptr from Box::into_raw — non-null, T-aligned, valid for full T layout;
// sole owner (no clones); reference does not escape this function.
let val = unsafe { &*ptr };
```

**Why (Book ch20.1):** Raw pointers "aren't guaranteed to point to valid memory" and "are allowed to be null." Dereferencing an invalid pointer is immediate UB — wrong results, memory corruption, or SIGSEGV.

```bash
# Detector (heuristic — `*ptr`/`*raw` also match safe derefs where the variable is a `&T`/`Box`, not a raw pointer; grep -v filters the deref line only — a preceding-line `// SAFETY:` is NOT suppressed, so every match needs manual review)
grep -rnE '\*ptr\b|\*raw\b' src/ | grep -v '// SAFETY'
# Detector (heuristic — high false-positive rate; confirm the dereferenced variable is a raw pointer, not a reference or Box)
grep -rn 'unsafe {' src/ -A3 | grep '\*[a-z_]'
```

---

### Forbidden 6 — `unsafe impl Send` or `unsafe impl Sync` without soundness justification

**Forbidden:**
```rust
struct MyHandle(*mut Inner);

unsafe impl Send for MyHandle {}  // ← no comment; reviewer cannot verify
unsafe impl Sync for MyHandle {}  // ← same
```

**Required:**
```rust
// SAFETY: MyHandle has exclusive ownership of the Inner allocation;
// raw pointer never aliased; all mutation behind the Inner Mutex.
unsafe impl Send for MyHandle {}

// SAFETY: &MyHandle read-only access safe across threads; mutable state
// is behind a Mutex inside Inner.
unsafe impl Sync for MyHandle {}
```

**Why (Book ch20.1):** "Rust can't verify that our type upholds the guarantees that it can be safely sent across threads or accessed from multiple threads; therefore, we need to do those checks manually and indicate as such with `unsafe`." An unjustified `unsafe impl Send/Sync` opens a soundness hole — any `Send` type can enter `thread::spawn`; any `Sync` type can be shared via `Arc`. Wrong invariant = data race = silent memory corruption.

```bash
# Detector (matches the impl line only; preceding-line SAFETY comments require manual review — every match needs human inspection regardless)
grep -rnE 'unsafe impl Send|unsafe impl Sync' src/
```

---

### Forbidden 7 — Using `unsafe` under the mistaken belief it "bypasses the borrow checker"

**Forbidden belief:** "The borrow checker rejected this, so I'll wrap it in `unsafe {}`."

**Why (Book ch20.1):** "`unsafe` doesn't turn off the borrow checker or disable any of Rust's other safety checks." A borrow/lifetime compile error does not disappear inside `unsafe {}`. What *does* happen: raw pointer operations that alias a live mutable reference become UB the compiler is no longer obligated to detect.

```rust
let r1 = &mut data;
let r2: *mut _ = r1 as *mut _;
// UB: r1 (live mutable reference) and r2 alias the same memory.
unsafe { *r2 = 42; }
```

**Fix:** Restructure ownership to satisfy the borrow checker, or use `RefCell`/`Mutex` for interior mutability.

```bash
# Detector (heuristic — raw pointer creation via `as *mut`/`as *const` is a safe operation; dereferencing is the unsafe superpower, not the cast; this detector flags review candidates, not defects — many matches will be legitimate FFI-prep or pointer-arithmetic sites)
grep -rnE 'as \*mut|as \*const' src/ | grep -v '// SAFETY'
```

---

## Book References

- **The Rust Programming Language, Chapter 20.1 — "Unsafe Rust"**
  https://doc.rust-lang.org/book/ch20-01-unsafe-rust.html
  Primary source for all five superpowers, SAFETY comment convention, static mut dangers, raw pointer semantics, unsafe traits, union fields, and FFI.

- **The Rustonomicon — "The Dark Arts of Unsafe Rust"**
  https://doc.rust-lang.org/nomicon/
  Authoritative deep reference: aliasing rules, variance, PhantomData, drop order, unwinding across FFI, the full UB taxonomy. Consult before writing any non-trivial unsafe abstraction.

- **Miri — undefined behaviour detector**
  `cargo +nightly miri test` — dynamic analysis tool that detects null/dangling dereferences, aliasing violations, and invalid transmutes at runtime. Not a substitute for the SAFETY proof, but catches what static analysis misses.

---

## Related Skills

- **rust-smart-pointers** — `Box::into_raw` / `from_raw` patterns, `Rc`/`Arc` raw counts, `Deref` coercions; the safe wrappers that `unsafe` code typically produces.
- **rust-concurrency** — `Send`/`Sync` soundness, `Arc<Mutex<T>>` vs `Arc<RwLock<T>>`, data-race prevention; directly relevant to Forbidden 4 (`static mut`) and Forbidden 6 (`unsafe impl Send/Sync`).

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
