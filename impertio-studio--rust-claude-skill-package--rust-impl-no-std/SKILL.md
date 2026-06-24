---
name: rust-impl-no-std
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-no-std

The **`no_std` Rust** skill: how to opt out of the standard library with `#![no_std]`, work against `core` only, pull in `alloc` when a heap is available, register a `#[panic_handler]` and optional `#[global_allocator]`, and design a hybrid library that builds on both std and no_std consumers via a `default = ["std"]` feature.

Cross-references: [[rust-core-stdlib-overview]] (what std contains that core does not), [[rust-impl-cargo-project]] (features, profiles, `panic = "abort"`), [[rust-syntax-unsafe]] (`#[global_allocator]` involves the unsafe `GlobalAlloc` trait), [[rust-impl-cross-compile]] (`thumbv7em-none-eabihf` and other bare-metal targets).

---

## When to use this skill

- User adds `#![no_std]` to `lib.rs` / `main.rs` and the compiler starts complaining about missing types.
- User targets an embedded board (Cortex-M, RISC-V MCU) and asks how to structure the crate.
- User wants `Vec` / `Box` / `String` in a no_std crate and is unsure whether `alloc` is allowed.
- User writes their first `#[panic_handler]` or hits the "duplicate panic handler" link error.
- User wants to plug in a custom allocator via `#[global_allocator]`.
- User has a library that some users will consume from std applications and others from no_std firmware, and asks how to support both.
- User asks why `std::format!` doesn't compile in their crate.
- User asks why `HashMap` cannot be imported from `alloc`.

For multi-target build matrices and target triples see [[rust-impl-cross-compile]]. For unsafe-trait constraints on `GlobalAlloc` see [[rust-syntax-unsafe]].

---

## Decision tree: do I really need `no_std`?

```
Will this code ever run on a target WITHOUT an operating system?
   NO  -> Keep std. Done.
   YES -> Continue.

Does the target have a heap allocator available?
   YES (embedded with embedded-alloc, OS kernel with own allocator, WASM)
       -> #![no_std] + extern crate alloc;
   NO  (bare-metal with strictly bounded memory)
       -> #![no_std] alone, no alloc. Use heapless / static buffers.

Is this a library that std users ALSO need to consume?
   YES -> Hybrid pattern: default = ["std"], std = [].
          Code is no_std by default, `std` feature re-enables std-only APIs.
   NO  -> Pure no_std crate. Don't add std as a feature.
```

ALWAYS choose pure no_std over the hybrid pattern when the crate has no realistic std consumer: the hybrid pattern doubles testing matrix and `cfg` complexity. NEVER add `#![no_std]` to a binary crate "just in case" without an actual no_std target.

---

## Anatomy of a `#![no_std]` crate

```rust
#![no_std]                       // 1. crate attribute, MUST be at crate root
#![cfg_attr(not(test), no_main)] // 2. embedded bin: no `main` entry point
                                 //    (libraries omit this)

extern crate alloc;              // 3. opt into the alloc crate (heap types)

use core::fmt::Write;            // 4. core replaces std for non-alloc items
use alloc::vec::Vec;             // 5. heap types come from alloc::*
use alloc::string::String;

#[panic_handler]                 // 6. required for non-std binary crates
fn panic(_info: &core::panic::PanicInfo) -> ! {
    loop {}
}

#[global_allocator]              // 7. only if you ship a custom allocator
static GLOBAL: MyAllocator = MyAllocator;
```

[Source: Rust Reference, names/preludes][ref-preludes]

What changes versus a std crate:

| Concern | Without `no_std` (std) | With `#![no_std]` |
|---------|------------------------|-------------------|
| Prelude | `std::prelude::rust_2024` | `core::prelude::rust_2024` |
| Heap types | `std::vec::Vec`, `std::string::String` | `alloc::vec::Vec`, `alloc::string::String` (needs `extern crate alloc;`) |
| Formatting | `std::fmt`, `std::format!` | `core::fmt`, `core::write!`, `core::writeln!` (no `format!` without alloc) |
| Panic | runtime-provided | user MUST provide `#[panic_handler]` (binary / cdylib / dylib) |
| Entry point | `fn main()` | platform-specific (e.g. `cortex-m-rt::entry`) plus `#![no_main]` |
| `println!` | yes | NO (no stdout); use semihosting, defmt, or a serial logger |

ALWAYS reach for `core::` first when writing no_std code. Only escalate to `alloc::` when the algorithm needs the heap, and only escalate further (`std::`) inside `#[cfg(feature = "std")]` blocks.

NEVER write `extern crate std;` in a no_std crate. NEVER call `std::format!`, `std::println!`, `std::eprintln!`, or anything under `std::fs` / `std::net` / `std::process` from no_std code.

---

## `core` vs `alloc` vs `std`: what lives where

```
core   = language minimum. Always available. No heap, no OS.
alloc  = adds heap-backed types. Requires a global allocator.
std    = adds OS abstractions (threads, files, network, time, env, process).
         std re-exports everything from core and alloc, plus its own additions.
```

[Source: alloc crate documentation][alloc-docs]

`core` contains (selected, no allocation needed):

- `core::option::Option`, `core::result::Result` (re-exported via prelude).
- `core::iter::Iterator`, `IntoIterator`, adapters (`map`, `filter`, `take`).
- `core::fmt::{Debug, Display, Write, Formatter}` and the format-string machinery used by `write!`.
- `core::mem` (`size_of`, `align_of`, `swap`, `replace`, `take`, `ManuallyDrop`).
- `core::ptr` (raw pointer ops, `NonNull`, `addr_of!`, `addr_of_mut!`).
- `core::cell::{Cell, RefCell, OnceCell, UnsafeCell}`.
- `core::sync::atomic::*` (`AtomicUsize`, `AtomicBool`, `Ordering`, ...).
- `core::marker::{PhantomData, Send, Sync, Sized, Copy}`.
- `core::error::Error` (stable in `no_std` since 1.81).
- `core::panic::PanicInfo` for `#[panic_handler]`.

`alloc` adds (heap-backed types, requires an allocator):

- `alloc::boxed::Box`, `alloc::vec::Vec`, `alloc::string::String`.
- `alloc::rc::{Rc, Weak}`, `alloc::sync::{Arc, Weak}`.
- `alloc::collections::{BTreeMap, BTreeSet, VecDeque, BinaryHeap, LinkedList}`.
- `alloc::format!` macro (heap-allocating formatting).
- `alloc::borrow::Cow`, `alloc::borrow::ToOwned`.
- `alloc::slice::SliceConcatExt` extras.

`std` adds (OS-bound):

- `std::collections::HashMap` / `HashSet` (use `hashbrown` in no_std crates instead).
- `std::fs`, `std::io`, `std::net`, `std::process`, `std::thread`, `std::time::SystemTime`, `std::env`.
- `std::sync::{Mutex, RwLock, Once, OnceLock, LazyLock}` (these are NOT in `core::sync`; `core::sync` is atomics only).

ALWAYS check the docs.rs page for a crate before depending on it from no_std. Look for a `default-features = false` example and a `no_std` feature.

NEVER assume `std::collections::HashMap` has an alloc-only counterpart: it does not. Use `hashbrown::HashMap` (the same backing implementation std uses) with `default-features = false`.

---

## `#[panic_handler]`: required signature

> "must appear _once_ in the dependency graph of a binary / dylib / cdylib crate"
> [Source: Rustonomicon, panic-handler][nomicon-ph]

The exact required signature, verified against the Rustonomicon:

```rust
use core::panic::PanicInfo;

#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

| Constraint | Rule |
|------------|------|
| Argument | exactly one: `&core::panic::PanicInfo` |
| Return type | `!` (the never type); function must diverge |
| Visibility | private (`fn`, no `pub`) |
| Frequency | exactly ONE in the entire dependency graph of the final artefact |
| Location | typically the binary crate; libraries SHOULD NOT define one |

Production-grade handlers:

```rust
// Option A: halt the CPU (Cortex-M).
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    cortex_m::asm::udf()       // undefined instruction; debugger trap
}

// Option B: rely on the panic-halt crate (no allocation, no print).
// In Cargo.toml: panic-halt = "0.2"
extern crate panic_halt;       // its #[panic_handler] is picked up automatically

// Option C: log + abort (panic-probe + defmt).
// extern crate panic_probe;
```

ALWAYS provide the panic handler in the BINARY crate, never inside a reusable library. NEVER write `panic!()` inside a `#[panic_handler]`: re-entry into the panic machinery is undefined.

NEVER copy the handler into multiple library crates; the linker fails with "duplicate lang item `panic_impl`" (error E0152) when more than one is found.

---

## `#[global_allocator]`: hooking the heap

Once `extern crate alloc;` is in scope, `Box`, `Vec`, `String`, and friends call into a single global allocator. For std targets that allocator is provided automatically. In no_std you provide it yourself.

```rust
// Minimal Cortex-M heap using the embedded-alloc crate.
use embedded_alloc::Heap;

#[global_allocator]
static HEAP: Heap = Heap::empty();

fn init_heap() {
    use core::mem::MaybeUninit;
    const HEAP_SIZE: usize = 1024;
    static mut MEMORY: [MaybeUninit<u8>; HEAP_SIZE] = [MaybeUninit::uninit(); HEAP_SIZE];
    unsafe { HEAP.init(MEMORY.as_ptr() as usize, HEAP_SIZE) }
}
```

[Source: Rust Embedded Book][embedded-book]

| Rule | Detail |
|------|--------|
| Trait implemented | `core::alloc::GlobalAlloc` (this is an `unsafe trait`) |
| Frequency | exactly ONE `#[global_allocator]` static per final artefact |
| Lifetime | `'static`; usually a `static` variable |
| Initialization | allocator may need a runtime `init()` call BEFORE the first allocation |

ALWAYS call the allocator's init routine before the first `Box::new` / `Vec::push` / heap-allocating call. NEVER allocate from inside `#[panic_handler]` or other interrupt-context code unless the allocator documents it as safe.

NEVER expose `#[global_allocator]` from a library crate: it is a global, application-level choice. Libraries depend on `alloc`, applications pick the allocator.

---

## Hybrid std + no_std library pattern

The canonical Cargo recipe, verified against the Cargo features reference:

```toml
[package]
name        = "mylib"
edition     = "2024"
rust-version = "1.85"

[features]
default = ["std"]                     # std users get std automatically
std     = ["alloc", "serde?/std"]     # std implies alloc
alloc   = []                          # opt in to heap types separately

[dependencies]
serde = { version = "1", optional = true, default-features = false }
```

```rust
#![cfg_attr(not(feature = "std"), no_std)]

#[cfg(feature = "alloc")]
extern crate alloc;

#[cfg(feature = "alloc")]
use alloc::string::String;

#[cfg(feature = "std")]
use std::io;       // only compiled in for std consumers
```

Three feature tiers, in dependency order:

```
(none)   -> pure core. No heap, no OS. Most restrictive.
alloc    -> core + heap. Vec, Box, String, BTreeMap available.
std      -> core + alloc + OS. Files, network, threads, HashMap available.
```

ALWAYS gate `extern crate alloc;` behind `#[cfg(feature = "alloc")]` when the crate also supports pure-core builds. NEVER make `std` the only opt-in: by following the additive-features principle, the default must include everything most users want, and the absence of a feature must REMOVE functionality, not break compilation for the default case.

NEVER write `default = []` for a hybrid library: every existing std consumer's build would break the moment they upgrade.

---

## Testing a `#[no_std]` crate

`cargo test` defaults to building a `test` harness that depends on `std`. Three paths:

1. **Std-only tests** : tests use `std`, library still compiles for no_std targets.

   ```rust
   #[cfg(test)]
   mod tests {
       // std is implicitly available here; this still works in a #![no_std] crate
       use super::*;
   }
   ```

   Verify the no_std path separately:

   ```bash
   cargo build --no-default-features --target thumbv7em-none-eabihf
   ```

2. **Integration tests in a separate std crate**: workspace member with `[dev-dependencies] mylib = { path = "..", default-features = false }`.

3. **`defmt-test` or `embedded-test`** for on-target testing (out of scope for this skill; see embedded-book chapter on testing).

ALWAYS run `cargo build --no-default-features` for every published no_std crate as part of CI, and ALSO `cargo build --no-default-features --features alloc` if the hybrid pattern is used. NEVER assume `cargo test` proves the no_std path: it does not.

---

## Cargo profile: pair `no_std` with `panic = "abort"`

> "`panic = "abort"` ... Required for some `no_std` targets."
> [Source: vooronderzoek §19, verified against Cargo profiles reference][cargo-profiles]

```toml
[profile.release]
panic         = "abort"        # no unwinding tables
lto           = "fat"
codegen-units = 1
opt-level     = "z"            # size, common for embedded
strip         = "symbols"

[profile.dev]
panic = "abort"
```

| Setting | Reason for no_std |
|---------|-------------------|
| `panic = "abort"` | unwinding requires `eh_personality` lang item; abort skips it entirely |
| `opt-level = "z"` or `"s"` | embedded flash is small; size beats speed |
| `lto = "fat"` | cross-crate inlining removes unused std-shim code |
| `strip = "symbols"` | smaller binary; debug info goes to a separate `.elf` |

ALWAYS set `panic = "abort"` for both `[profile.dev]` AND `[profile.release]` in pure embedded binaries: tests would otherwise mismatch.

NEVER mix `panic = "unwind"` with no_std targets that lack `eh_personality`: link fails with "undefined reference to `rust_eh_personality`".

---

## Quick recipes

### Add the alloc crate to an existing no_std lib

```rust
// lib.rs
#![no_std]
extern crate alloc;

use alloc::vec::Vec;
use alloc::string::String;
```

### A minimal Cortex-M binary, no allocator

```rust
#![no_std]
#![no_main]

use core::panic::PanicInfo;
use cortex_m_rt::entry;

#[entry]
fn main() -> ! {
    loop {}
}

#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    cortex_m::asm::udf()
}
```

### Replace `format!` with `core::write!` to a heapless buffer

```rust
use core::fmt::Write;
use heapless::String;                  // heapless = "0.8"

let mut s: String<32> = String::new();
write!(s, "value = {}", 42).unwrap();  // no heap, fixed-capacity buffer
```

### Conditional `std::error::Error` impl

```rust
#![cfg_attr(not(feature = "std"), no_std)]

// Works on both std and no_std since 1.81 (core::error::Error is stable).
impl core::error::Error for MyError {}
```

---

## Validation checklist

Before publishing or merging:

1. `cargo build --no-default-features` succeeds (proves the pure-core / pure-no_std path).
2. `cargo build --no-default-features --features alloc` succeeds when the alloc tier exists.
3. `cargo build` succeeds (proves the default std path still works for std consumers).
4. The binary crate has exactly ONE `#[panic_handler]` in the final dependency graph.
5. The binary crate has at most ONE `#[global_allocator]` in the final dependency graph.
6. No `use std::...` lines exist outside `#[cfg(feature = "std")]` blocks.
7. `cargo build --target <embedded-triple>` succeeds when an embedded target is supported.
8. Profile has `panic = "abort"` if the target requires it.
9. `core::error::Error` (not `std::error::Error`) is used for error trait impls in no_std-friendly code.

---

## Reference links

- [[references/methods.md]] : full inventory of what lives in `core` vs `alloc` vs `std`, complete `#[panic_handler]` + `#[global_allocator]` signatures, every relevant `#[cfg]` predicate, the no_std prelude per edition.
- [[references/examples.md]] : full crate templates : minimal Cortex-M binary, no_std library with alloc, hybrid std + no_std library with three feature tiers, custom allocator implementation, heapless string formatting.
- [[references/anti-patterns.md]] : seven concrete anti-patterns with diagnostics, error codes (E0152, undefined `rust_eh_personality`, missing `alloc` import), and the exact fix per case.

[ref-preludes]: https://doc.rust-lang.org/reference/names/preludes.html
[alloc-docs]: https://doc.rust-lang.org/alloc/
[embedded-book]: https://doc.rust-lang.org/embedded-book/
[nomicon-ph]: https://doc.rust-lang.org/nomicon/panic-handler.html
[cargo-profiles]: https://doc.rust-lang.org/cargo/reference/profiles.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
