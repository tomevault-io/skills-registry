---
name: rust-syntax-unsafe
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-syntax-unsafe

`unsafe` in Rust does NOT disable the borrow checker, type checker, or any safety analysis. It opts a region of code into eight additional capabilities that the compiler cannot prove sound on its own, and it transfers the proof obligation to the human author. Every unsafe block represents a soundness contract: the author asserts that the invariants the compiler cannot verify hold at this site. Violating that contract is undefined behavior (UB), and UB poisons the entire program, including the safe code that called into it.

This skill is the authoritative entry point for the unsafe surface as of Rust 1.85 / edition 2024. ALWAYS treat the Reference [unsafety][ref-unsafety] and [behavior-considered-undefined][ref-ub] pages as ground truth; NEVER rely on the older "5 superpowers" wording from The Book (it predates edition 2024 and the `target_feature` formalization).

## Quick Reference

### Core rules (deterministic)

1. ALWAYS write a `// SAFETY:` comment on the line immediately preceding every `unsafe { ... }` block and every `#[unsafe(...)]` attribute. The comment MUST enumerate the invariants the caller relies on. NEVER omit it, even when the invariant is obvious to you.
2. NEVER call an `unsafe fn` from inside another `unsafe fn` body without its own inner `unsafe { ... }` block. The `unsafe_op_in_unsafe_fn` lint is `#[warn]`-by-default in edition 2024 and `#[deny]`-by-default in many production lint configs.
3. NEVER write `extern "C" { ... }` in edition 2024 source. ALWAYS write `unsafe extern "C" { ... }`. Each item inside MAY be opted into `safe` per item; the block itself MUST be `unsafe`.
4. NEVER use bare `#[no_mangle]`, `#[link_section = "..."]`, `#[export_name = "..."]`, or `#[used]` in edition 2024. ALWAYS wrap them: `#[unsafe(no_mangle)]`, `#[unsafe(link_section = "...")]`, `#[unsafe(export_name = "...")]`, `#[unsafe(used)]`.
5. NEVER cast a pointer to integer and back with plain `as usize` and `as *const T` in code that must be portable to strict-provenance targets. ALWAYS use `ptr.expose_provenance()` + `core::ptr::with_exposed_provenance(addr)` when you need an int round-trip, or `ptr.with_addr(addr)` / `ptr.map_addr(|a| ...)` when you keep the same provenance.
6. NEVER take a reference (`&` or `&mut`) to a `static mut`. ALWAYS use `&raw const STATIC` / `&raw mut STATIC` (stable native syntax since 1.82) or `core::ptr::addr_of!` / `core::ptr::addr_of_mut!`. The `static_mut_refs` lint is `deny`-by-default in 2024.
7. NEVER `transmute<A, B>` when `size_of::<A>() != size_of::<B>()`. The compiler rejects this at compile time, but the more common silent footgun is `transmute` between two same-sized types with different validity invariants (e.g. `u8` to `bool`, `u32` to `char`, `usize` to a reference). ALWAYS prefer `as` for numeric primitives, `From`/`Into` for ZST/wrapper conversions, and `MaybeUninit::assume_init` for uninit memory.
8. NEVER dereference a `*const T` or `*mut T` without proving four properties at the site: (a) non-null, (b) properly aligned for `T`, (c) points into a single live allocation of at least `size_of::<T>()` bytes, (d) the bytes form a valid value of `T`. The `// SAFETY:` comment MUST address all four.
9. NEVER hold a `&mut T` and dereference a `*mut T` that aliases the same memory inside the same unsafe block. This is breaking pointer aliasing rules (UB category 4) regardless of whether the program "appears to work".
10. ALWAYS run `cargo +nightly miri test` on every crate that contains `unsafe` code, in CI. Miri catches most aliasing, provenance, uninit-read, and OOB-access violations at the MIR level. Miri-clean is the minimum bar for shipping unsafe; the maximum bar adds property-based testing of the public safe API.

### Decision tree: "do I need unsafe?"

```
Does the operation require ANY of the 8 superpowers below?
  NO  -> The compiler refuses. The code is invalid as written.
         Refactor in safe Rust. STOP. Do NOT add unsafe.
  YES -> Can I express the same operation through an existing safe API?
    YES -> Use the safe API. Examples: AtomicX instead of static mut,
           Vec::split_at_mut instead of two raw pointer arithmetic,
           MaybeUninit::write instead of transmute-from-zeroed.
    NO  -> Can I encapsulate the unsafe op behind a SAFE function whose
           preconditions are checked by the type system?
      YES -> Build the safe wrapper. Add `// SAFETY:` to every
             internal unsafe block. Export ONLY the safe function.
      NO  -> The unsafe contract is part of the PUBLIC API.
             Mark the function `unsafe fn`. Document `# Safety`.
             Enumerate every precondition. Add property tests + miri.
```

### The 8 superpowers (post-edition-2024 enumeration)

The Reference unsafety page enumerates these as the operations that require an `unsafe { }` block or `unsafe fn` context. Each row links to its driving UB category in [references/methods.md](references/methods.md).

| # | Superpower | Edition 2024 form | Driving UB risk |
|---|------------|-------------------|-----------------|
| 1 | Dereference a raw pointer | `unsafe { *ptr }` | Dangling / misaligned / wrong-prov |
| 2 | Call an `unsafe fn` | `unsafe { f() }` | Inherited contract |
| 3 | Read / write a mutable (or unsafe external) `static` | `unsafe { COUNTER += 1 }` | Data race |
| 4 | Implement an `unsafe trait` | `unsafe impl Send for T { }` | Auto-trait soundness |
| 5 | Access a `union` field (read or non-assign write) | `unsafe { u.field }` | Invalid value |
| 6 | Declare items inside `unsafe extern { ... }` | `unsafe extern "C" { fn f(); }` | ABI / signature mismatch |
| 7 | Call a `#[target_feature]`-gated function | `unsafe { _mm_add_ps(a, b) }` | Illegal instruction |
| 8 | Apply an unsafe attribute to an item | `#[unsafe(no_mangle)]` | Symbol-name aliasing |

Items 6 and 8 became unsafe-marked in edition 2024 (previously implicit). Item 7 has been carved out since 1.27 but is formalized in the current Reference. The historical "5 superpowers" wording from earlier editions of The Book does NOT cover items 6, 7, or 8 and MUST NOT be quoted in 2024 code reviews.

### Undefined Behavior (UB) enumeration

The Reference [behavior-considered-undefined][ref-ub] page is the ONLY normative list. UB inside `unsafe` code corrupts the entire program. The categories, verbatim from the Reference (paraphrased only for brevity, full text in [references/methods.md](references/methods.md)):

1. **Data races.** Two threads access the same non-atomic location, at least one writes, no happens-before relation.
2. **Dangling or misaligned pointer access.** "Dangling" means not all bytes are part of the same live allocation, including allocations of size zero pointing to past-the-end.
3. **Out-of-bounds place projection.** Indexing past the end of an array even without dereferencing.
4. **Breaking pointer aliasing rules.** `&T` may not be mutated through any path (except `UnsafeCell`); `&mut T` may not be read or written by any pointer not derived from it while the reference lives.
5. **Mutating immutable bytes.** Bytes reachable via `&T` (and transitively through `Box`, etc.) are immutable.
6. **Invoking UB via compiler intrinsics.** Misusing `core::intrinsics::*` or `core::hint::unreachable_unchecked`.
7. **Executing code compiled with platform features the current platform does not support.** The `target_feature` superpower.
8. **Calling a function with the wrong call ABI** or unwinding past a frame that disallows it (e.g. unwinding through `extern "C"` without `-C panic=unwind` semantics).
9. **Producing an invalid value.** Examples: `bool` outside `{0, 1}`, null `fn` pointer, surrogate `char`, `!` ever produced, uninit ints/floats/raw pointers, enum discriminant out of range, null/unaligned/dangling references or `Box<T>`.
10. **Incorrect inline assembly use.** Violating `asm!` register constraints, clobbering preserved registers without listing them.
11. **Violating Rust runtime assumptions.** `longjmp` skipping destructors, exiting via `exit(3)` while threads hold non-mem locks the kernel cares about, etc.
12. **Const-context provenance violations.** Producing a non-comparable pointer in a `const` evaluation.

CRITICAL clarification from the Reference: "Rust code is incorrect if it exhibits any of the behaviors in the following list. This includes code within `unsafe` blocks and `unsafe` functions. `unsafe` only means that avoiding undefined behavior is on the programmer." See [references/methods.md](references/methods.md) for the per-category contract language.

### Strict provenance APIs (Rust 1.84+, stable)

| API | Signature | Use case |
|-----|-----------|----------|
| `<*const T>::addr(self)` | `-> usize` | Inspect the integer address WITHOUT exposing provenance. Pure observation. |
| `<*mut T>::addr(self)` | `-> usize` | Same, on `*mut T`. |
| `<*const T>::expose_provenance(self)` | `-> usize` | Inspect integer AND publish provenance into the global pool. Required before round-trip via `with_exposed_provenance`. |
| `<*mut T>::expose_provenance(self)` | `-> usize` | Same, on `*mut T`. |
| `core::ptr::with_exposed_provenance::<T>(addr: usize)` | `-> *const T` | Materialize a pointer with provenance previously exposed by `expose_provenance`. Use this for int-to-ptr round-trips. |
| `core::ptr::with_exposed_provenance_mut::<T>(addr: usize)` | `-> *mut T` | Mutable variant. |
| `<*const T>::with_addr(self, addr: usize)` | `-> *const T` | Build a new pointer keeping THIS pointer's provenance but adopting a different address. |
| `<*const T>::map_addr(self, f: impl FnOnce(usize) -> usize)` | `-> *const T` | Same, in closure form. |
| `core::ptr::addr_of!(place)` | `-> *const T` | Take a raw pointer to a place WITHOUT first creating a reference. Critical for `static mut`, packed-struct fields, and unaligned memory. |
| `core::ptr::addr_of_mut!(place)` | `-> *mut T` | Mutable variant. |
| `&raw const place` | `-> *const T` | Native syntax for `addr_of!`, stable since 1.82. |
| `&raw mut place` | `-> *mut T` | Native syntax for `addr_of_mut!`, stable since 1.82. |

The strict-provenance model treats pointers as `(provenance, address)` pairs. Plain `as usize` ptr-to-int casts discard provenance silently and produce non-round-trippable values on hardware with hardware-enforced provenance (e.g. CHERI). ALWAYS use the strict-provenance APIs when you genuinely need an integer view of a pointer.

## Patterns

### Pattern: writing a sound unsafe block (`// SAFETY:` discipline)

```rust
use core::slice;

/// Returns a shared slice that views the same memory as `src` reinterpreted
/// as `u8`. SAFE wrapper: caller cannot break invariants from safe Rust.
pub fn as_bytes<T>(src: &[T]) -> &[u8] {
    let len_bytes = core::mem::size_of_val(src);
    let ptr = src.as_ptr().cast::<u8>();
    // SAFETY:
    //  - `ptr` is derived from a live shared reference `src`, so it is
    //    non-null, properly aligned for `u8` (any address), and points
    //    into a single allocation of `size_of_val(src)` bytes.
    //  - `len_bytes` equals that size, so the resulting slice is in-bounds.
    //  - The returned slice borrows `src` (lifetime elided), so aliasing
    //    rules are upheld by the borrow checker.
    //  - `u8` has no validity invariants beyond "a byte exists",
    //    satisfied because the source bytes are initialized.
    unsafe { slice::from_raw_parts(ptr, len_bytes) }
}
```

EVERY unsafe block has a `// SAFETY:` paragraph enumerating each invariant the unsafe API requires. The block is the smallest scope that needs unsafe. The wrapper exports only safe behavior.

### Pattern: `unsafe_op_in_unsafe_fn` in edition 2024

```rust
// Pre-2024: an unsafe fn body let you call unsafe ops bare.
// Edition 2024: warn-by-default. Wrap every unsafe op in its own block.

/// # Safety
/// `i < slice.len()` MUST hold. UB otherwise.
pub unsafe fn read_unchecked<T>(slice: &[T], i: usize) -> &T {
    // SAFETY: precondition `i < slice.len()` is delegated to the caller.
    unsafe { slice.get_unchecked(i) }
}
```

This is NOT busywork. The inner `unsafe { }` exists to mark precisely WHICH operations in the body are unsafe; the outer `unsafe fn` only marks the function's PUBLIC contract. Pre-2024 conflated these, which hurt review.

### Pattern: raw pointers from `static mut` via strict provenance

```rust
static mut COUNTER: u64 = 0;

pub fn bump() -> u64 {
    // SAFETY: single-threaded program. No other thread accesses COUNTER.
    //  - `addr_of_mut!` does NOT materialize a `&mut`, so we never
    //    violate aliasing rules for the static.
    //  - The read-modify-write is uninterrupted in single-threaded code.
    unsafe {
        let p = core::ptr::addr_of_mut!(COUNTER);
        let cur = p.read();
        p.write(cur + 1);
        cur + 1
    }
}
```

NEVER write `unsafe { let r = &mut COUNTER; *r += 1; }`: it forms a `&mut` to a `static mut`, which the `static_mut_refs` lint rejects at deny-by-default. In multi-threaded code, the correct answer is `AtomicU64`, not raw pointers. See [[rust-impl-concurrency]].

### Pattern: `MaybeUninit<T>` for safe uninitialized memory

```rust
use core::mem::MaybeUninit;

pub fn make_array<T: Copy + Default, const N: usize>() -> [T; N] {
    let mut uninit: [MaybeUninit<T>; N] =
        // SAFETY: an array of `MaybeUninit<T>` is itself defined to be
        // valid in any bit pattern, including uninitialized memory.
        unsafe { MaybeUninit::uninit().assume_init() };
    for slot in &mut uninit {
        slot.write(T::default());
    }
    // SAFETY: every slot was written exactly once above, so every
    // element is initialized. The transmute below is size- and
    // alignment-compatible (same layout for the wrapping array).
    unsafe { (&uninit as *const _ as *const [T; N]).read() }
}
```

The dance here is exactly the pattern from the standard library's own internals: the OUTER array is `MaybeUninit<T>` (so the bit pattern is always valid), each slot is written exactly once, then the whole array is read out. NEVER call `MaybeUninit::assume_init` on partially-initialized memory.

### Pattern: `UnsafeCell<T>` for sound interior mutability

```rust
use core::cell::UnsafeCell;

pub struct MyCell<T> { inner: UnsafeCell<T> }

impl<T> MyCell<T> {
    pub fn new(value: T) -> Self { Self { inner: UnsafeCell::new(value) } }
    pub fn replace(&self, value: T) -> T {
        // SAFETY: `MyCell` is single-threaded (not Send). We hold the
        // only reference to `inner` at this exact moment because the
        // returned `&mut T` does not escape this function call.
        unsafe { core::mem::replace(&mut *self.inner.get(), value) }
    }
}
// `MyCell` is `!Sync` because `UnsafeCell` is `!Sync`. The compiler
// derives this automatically; we MUST NOT `unsafe impl Sync` for it.
```

`UnsafeCell<T>` is the ONLY way to obtain `&mut T` from `&T` soundly in Rust. Every interior-mutability primitive in `std` (`Cell`, `RefCell`, `Mutex`, `RwLock`, `OnceCell`, atomics) wraps `UnsafeCell` internally. NEVER cast `&T` to `&mut T` via `transmute` or pointer cast; ALWAYS go through `UnsafeCell::get`.

### Pattern: `unsafe extern` in edition 2024

```rust
unsafe extern "C" {
    // Audited: caller-supplied buffer, no aliasing precondition,
    // returns the constant-defined length. Marked safe per-item.
    pub safe fn strlen(s: *const core::ffi::c_char) -> usize;
    // Standard unsafe FFI: dereferences arbitrary memory.
    pub unsafe fn memcpy(
        dst: *mut core::ffi::c_void,
        src: *const core::ffi::c_void,
        n: usize,
    ) -> *mut core::ffi::c_void;
}
```

See [[rust-impl-ffi-bindgen]] for the broader FFI ownership story (`BorrowedFd`, `OwnedFd`, `#[repr(C)]`, opaque pointers). See [[rust-syntax-edition-2024]] for the migration mechanics.

## Edge Cases

- Type-state on raw pointers: `*const T` is `Send` and `Sync` if `T: Sync`; `*mut T` is `!Send` and `!Sync`. NEVER move raw pointers across threads without explicit `unsafe impl Send`/`unsafe impl Sync` for a wrapper newtype, accompanied by `// SAFETY:` rationale.
- `&` and `&mut` carry lifetimes; raw pointers do NOT. Converting `&'a T` to `*const T` is implicit (`as_ptr()`); converting back to `&'a T` requires `unsafe { &*ptr }` and the responsibility for `'a`-respecting validity transfers to you.
- `core::hint::unreachable_unchecked()` is an UB-on-reach intrinsic; the compiler is permitted to assume the call site is unreachable and may delete code paths leading to it. NEVER call it unless you can prove the path is impossible.
- `core::mem::zeroed::<T>()` is UB for many `T` (any type with a niche that cannot be zero, e.g. `&T`, `Box<T>`, `NonZeroU32`, enums whose discriminant is non-zero). ALWAYS prefer `MaybeUninit::zeroed().assume_init()` only when you have proved every byte-zero pattern is a valid `T`.
- Miri runs only via `cargo +nightly miri`. Edition 2024 + Miri requires a recent enough nightly; track [the Miri page][miri] for the supported stable feature surface.
- The Stacked Borrows / Tree Borrows model is what Miri checks: borrowing rules are stricter than the Reference (which is intentionally underspecified). Miri-failure is conclusive evidence of UB; Miri-pass is not proof of soundness (but is the strongest automated check available).

## Cross-References

- [[rust-impl-ffi-bindgen]] : `unsafe extern`, `#[repr(C)]`, ABI bindings, `BorrowedFd`/`OwnedFd`.
- [[rust-syntax-edition-2024]] : `unsafe extern` migration, unsafe-attribute syntax, `unsafe_op_in_unsafe_fn` lint, `static_mut_refs` migration paths.
- [[rust-core-memory-model]] : aliasing rules, `Send`/`Sync` derivation, interior-mutability category.
- [[rust-syntax-borrowing]] : the safe complement; what `unsafe` is allowed to violate that safe Rust isn't.
- [[rust-impl-concurrency]] : atomics as the safe replacement for `static mut` raw access.
- [[rust-core-toolchain]] : how to install Miri (`rustup component add miri --toolchain nightly`).

## Reference Links

- Full UB category contracts + strict-provenance API surface + Miri usage : [references/methods.md](references/methods.md)
- Working before/after snippets for every superpower and pattern : [references/examples.md](references/examples.md)
- Anti-patterns and how to recover : [references/anti-patterns.md](references/anti-patterns.md)

## Official Sources

- The Rust Reference : Unsafety : https://doc.rust-lang.org/reference/unsafety.html
- The Rust Reference : Behavior considered undefined : https://doc.rust-lang.org/reference/behavior-considered-undefined.html
- The Rustonomicon : https://doc.rust-lang.org/nomicon/
- `std::ptr` (strict provenance) : https://doc.rust-lang.org/std/ptr/index.html
- `core::mem::MaybeUninit` : https://doc.rust-lang.org/core/mem/union.MaybeUninit.html
- `core::cell::UnsafeCell` : https://doc.rust-lang.org/core/cell/struct.UnsafeCell.html
- Rust 1.84.0 release (strict provenance APIs stabilized) : https://blog.rust-lang.org/2025/01/09/Rust-1.84.0/
- Rust 1.85.0 release (edition 2024 ; unsafe attributes ; `unsafe extern`) : https://blog.rust-lang.org/2025/02/20/Rust-1.85.0/
- Miri project : https://github.com/rust-lang/miri

[ref-unsafety]: https://doc.rust-lang.org/reference/unsafety.html
[ref-ub]: https://doc.rust-lang.org/reference/behavior-considered-undefined.html
[miri]: https://github.com/rust-lang/miri

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
