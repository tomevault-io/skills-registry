---
name: rust-impl-ffi-bindgen
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-ffi-bindgen

C interop has two directions, and each has a generator. To call a C library FROM Rust, write `unsafe extern "C" { ... }` declarations (by hand for small APIs) or run `bindgen` against the header (for everything else). To expose a Rust library TO C, mark the exported `fn` items with `extern "C"` plus `#[unsafe(no_mangle)]` and run `cbindgen` to emit the matching `.h` file. The ABI surface in between is governed by `#[repr(C)]` types, opaque pointers, null-terminated strings (`CString` / `CStr`), function pointers, and Drop guards for foreign-allocated resources. On Unix, file descriptors crossing the boundary use `OwnedFd` (owning, closes on `Drop`) and `BorrowedFd<'_>` (non-owning, lifetime-bounded), never raw `RawFd` for ownership-bearing APIs.

Cross-references: [[rust-syntax-unsafe]] (unsafe operations, UB categories, `unsafe extern` rationale), [[rust-syntax-edition-2024]] (mandatory `unsafe extern` + `#[unsafe(no_mangle)]`), [[rust-impl-build-scripts]] (build.rs to invoke bindgen / cbindgen, `cargo:rustc-link-lib`), [[rust-impl-cargo-project]] (build-dependencies, links key).

---

## When to use this skill

- User writes `extern "C" { ... }` and the compiler rejects it in edition 2024.
- User adds `#[no_mangle]` and gets the `unsafe_attr_outside_unsafe` warning or error.
- User wants to call a C library from Rust and asks "do I need bindgen".
- User wants to expose a Rust API to C / other languages and asks "do I need cbindgen".
- User defines a struct shared with C and the layout is wrong.
- User passes a Rust `String` or `&str` to a C function and the call segfaults.
- User receives a `*const c_char` from C and reads it as a Rust `&str` directly.
- User stores a Rust closure in a struct passed to C and the callback ABI is wrong.
- User passes file descriptors across FFI and asks "is `RawFd` correct" or hits double-close.
- User reaches for `transmute` to convert FFI types instead of using `CStr` / `CString` / `OwnedFd`.

---

## The 2024 edition rules: unsafe extern + unsafe attributes

Two changes ship with edition 2024 (Rust 1.85+). Every FFI declaration MUST use the new syntax:

```rust
// Rust 2024: unsafe is mandatory on the extern block.
unsafe extern "C" {
    // Default: every function in an unsafe extern block is `unsafe fn`,
    // because the compiler cannot verify the foreign code.
    pub fn strlen(p: *const std::ffi::c_char) -> usize;

    // Opt-in `safe fn` (since 1.82) marks an item whose contract the
    // caller can satisfy purely with safe Rust, e.g. pure math.
    pub safe fn sqrt(x: f64) -> f64;
}

// Rust 2024: no_mangle / link_section / export_name MUST be wrapped
// in #[unsafe(...)] because they break global invariants (symbol uniqueness).
#[unsafe(no_mangle)]
pub extern "C" fn my_rust_function(x: i32) -> i32 {
    x * 2
}
```

ALWAYS write `unsafe extern "C" { ... }`, NEVER the bare `extern "C" { ... }`. The pre-2024 form is a hard error in edition 2024 and a future-compat warning in earlier editions. ALWAYS write `#[unsafe(no_mangle)]`, NEVER plain `#[no_mangle]` in edition 2024.

Inside an `unsafe fn` body in edition 2024, the `unsafe_op_in_unsafe_fn` lint is warn-by-default. Wrap each individual `unsafe` operation in its own `unsafe { ... }` block to keep the soundness comment local. See [[rust-syntax-unsafe]] for the full unsafe-block hygiene rules.

---

## #[repr(C)] : the only stable layout for FFI

`#[repr(Rust)]` (the default) is explicitly *unspecified*: the compiler may reorder fields, pick any padding, and change layout between versions. NEVER expose a `#[repr(Rust)]` type across FFI.

```rust
// CORRECT: C-compatible, fields in declaration order, C padding rules.
#[repr(C)]
pub struct Point {
    pub x: f64,
    pub y: f64,
}

// CORRECT: single-field newtype with identical layout to inner.
#[repr(transparent)]
pub struct Handle(*mut std::ffi::c_void);

// CORRECT: enum with explicit discriminant size matches a C int.
#[repr(C)]
pub enum Status {
    Ok = 0,
    InvalidArg = 1,
    NotFound = 2,
}

// AVOID: #[repr(packed)] across FFI unless the C side also packs;
// packed fields require &raw const / &raw mut (1.82+) to read, never &.
```

Choose `#[repr(C)]` for shared structs and shared enums with explicit discriminants. Choose `#[repr(transparent)]` for wrapper types that must keep the inner ABI (e.g. `NonNull<T>` newtypes). Choose `#[repr(u8)]` / `#[repr(i32)]` for C-style enums whose tag size MUST be fixed.

---

## bindgen workflow: C headers to Rust

`bindgen` is invoked from `build.rs` and writes a generated `bindings.rs` into `OUT_DIR`. The crate consumes it via `include!(concat!(env!("OUT_DIR"), "/bindings.rs"))`.

```toml
# Cargo.toml
[package]
name = "mylib-sys"
edition = "2024"
links = "mylib"   # one *-sys crate per native lib (uniqueness rule)

[build-dependencies]
bindgen = "0.71"
```

```rust
// build.rs
use std::env;
use std::path::PathBuf;

fn main() {
    println!("cargo:rustc-link-lib=mylib");
    println!("cargo:rerun-if-changed=wrapper.h");

    let bindings = bindgen::Builder::default()
        .header("wrapper.h")
        .clang_arg("-I/usr/local/include")
        // Restrict surface area: allowlist prevents pulling in libc symbols.
        .allowlist_function("mylib_.*")
        .allowlist_type("mylib_.*")
        .allowlist_var("MYLIB_.*")
        .blocklist_type("FILE")
        .derive_default(true)
        // unsafe extern is generated automatically for edition 2024+.
        .parse_callbacks(Box::new(bindgen::CargoCallbacks::new()))
        .generate()
        .expect("bindgen failed");

    let out = PathBuf::from(env::var("OUT_DIR").unwrap());
    bindings
        .write_to_file(out.join("bindings.rs"))
        .expect("write bindings");
}
```

```rust
// src/lib.rs
#![allow(non_camel_case_types, non_snake_case, non_upper_case_globals)]
include!(concat!(env!("OUT_DIR"), "/bindings.rs"));
```

ALWAYS use `allowlist_*` to keep the binding small: every type bindgen emits is part of your ABI surface. ALWAYS pair the `-sys` crate with a safe wrapper crate that exposes idiomatic Rust types; never expose raw bindings as the crate's public API. See `references/methods.md` for the full builder option list.

---

## cbindgen workflow: Rust API to C header

`cbindgen` walks the public Rust API and emits a C / C++ header. Run it either from `build.rs` (for inclusion in your own build) or as a one-shot CLI before publishing.

```toml
# cbindgen.toml at crate root
language = "C"
header = "/* Generated by cbindgen. Do not edit. */"
include_guard = "MYLIB_H"
autogen_warning = "/* Warning, this file is autogenerated by cbindgen. */"
no_includes = false
sys_includes = ["stdint.h", "stddef.h"]
cpp_compat = true
style = "type"   # use `typedef struct Foo Foo;` form

[export]
prefix = "mylib_"

[export.rename]
"Status" = "mylib_status_t"

[enum]
prefix_with_name = true
```

```rust
// build.rs
fn main() {
    let crate_dir = std::env::var("CARGO_MANIFEST_DIR").unwrap();
    let out_dir = std::env::var("OUT_DIR").unwrap();
    cbindgen::Builder::new()
        .with_crate(crate_dir)
        .with_config(cbindgen::Config::from_file("cbindgen.toml").unwrap())
        .generate()
        .expect("cbindgen failed")
        .write_to_file(format!("{out_dir}/mylib.h"));
}
```

ALWAYS keep `cbindgen.toml` under source control. NEVER hand-edit the generated header: regenerate. Mark public functions with both `#[unsafe(no_mangle)]` and `pub extern "C"` so cbindgen emits them.

---

## Opaque types: hide Rust internals behind a pointer

A C consumer holds an opaque pointer; the Rust struct's fields are not visible to C. Two patterns exist; both are stable and ABI-safe.

```rust
// Pattern A: zero-sized marker on the C side, real type only in Rust.
// In Rust:
pub struct Engine {
    cfg: Vec<u8>,
    state: SomeState,
}

#[unsafe(no_mangle)]
pub extern "C" fn engine_new() -> *mut Engine {
    Box::into_raw(Box::new(Engine { cfg: Vec::new(), state: SomeState::init() }))
}

#[unsafe(no_mangle)]
pub unsafe extern "C" fn engine_free(p: *mut Engine) {
    if p.is_null() { return; }
    drop(unsafe { Box::from_raw(p) });
}
```

cbindgen emits a forward declaration so the C side sees `struct Engine;` as an incomplete type and can only handle `Engine *`.

```rust
// Pattern B: hand-written `pub struct Foo { _private: [u8; 0] }`
// when writing the C header by hand instead of using cbindgen.
#[repr(C)]
pub struct Foo {
    _private: [u8; 0],
    _marker: core::marker::PhantomData<(*mut u8, core::marker::PhantomPinned)>,
}
```

ALWAYS pair `Box::into_raw` with a free-function that calls `Box::from_raw` exactly once. NEVER let C call `free()` on a pointer that Rust allocated, and NEVER let Rust `Box::from_raw` a pointer that C `malloc`-ed.

---

## Strings across FFI: CString, CStr, c_char

Rust strings are not null-terminated and not necessarily valid for C consumption. `std::ffi::{CString, CStr}` are the bridge types.

```rust
use std::ffi::{CStr, CString, c_char};

// Rust to C: owned, null-terminated, must not contain interior nulls.
pub fn pass_to_c(s: &str) -> Result<(), std::ffi::NulError> {
    let owned = CString::new(s)?;          // adds terminator, errors on \0
    unsafe { c_takes_string(owned.as_ptr()) }; // *const c_char, valid for the call
    Ok(())                                    // owned dropped here, ptr now dangling
}

unsafe extern "C" {
    fn c_takes_string(s: *const c_char);
    fn c_returns_static() -> *const c_char;
}

// C to Rust: borrow a pointer the C side owns; lifetime tied to caller.
pub fn read_from_c() -> Result<String, std::str::Utf8Error> {
    // SAFETY: caller guarantees the pointer is valid, null-terminated, and
    // its bytes outlive this CStr borrow.
    let cstr = unsafe { CStr::from_ptr(c_returns_static()) };
    cstr.to_str().map(|s| s.to_owned())     // copy to owned Rust String
}
```

ALWAYS construct `CString::new(...)` before sending a Rust string to C, and keep the `CString` alive for the entire duration of the C call. NEVER cast `&str` directly to `*const c_char`: a Rust `&str` lacks the trailing `\0`. NEVER take ownership of a `*const c_char` returned by a C function unless its API explicitly transfers ownership; if it does, wrap with `CString::from_raw` and let Rust free it.

---

## File descriptors: BorrowedFd, OwnedFd, AsFd

On Unix, `std::os::fd` distinguishes ownership at the type level. ALWAYS prefer these over raw `RawFd` for any API that transfers or borrows an fd.

| Type | Owns? | Closes on `Drop`? | Lifetime | Use for |
|------|-------|-------------------|----------|---------|
| `RawFd` (`= i32`) | no | no | none | The integer at the very edge of FFI, never as an API type. |
| `BorrowedFd<'fd>` | no | no | `'fd` | A short-lived non-owning view of an fd that someone else owns. |
| `OwnedFd` | yes | yes | static | Returning an fd from a constructor; storing an fd in a struct. |

```rust
use std::os::fd::{AsFd, AsRawFd, BorrowedFd, FromRawFd, IntoRawFd, OwnedFd, RawFd};

unsafe extern "C" {
    fn dup(fd: RawFd) -> RawFd;
    fn close(fd: RawFd) -> i32;
}

// CORRECT: take a borrow, the caller keeps ownership.
pub fn duplicate(fd: BorrowedFd<'_>) -> std::io::Result<OwnedFd> {
    // SAFETY: dup returns -1 on failure or a new owned fd on success.
    let raw = unsafe { dup(fd.as_raw_fd()) };
    if raw < 0 {
        Err(std::io::Error::last_os_error())
    } else {
        // SAFETY: raw is a valid new fd that nobody else owns.
        Ok(unsafe { OwnedFd::from_raw_fd(raw) })
    }
}

// Convert an fd-bearing object (File, TcpStream, ...) to a borrowed view.
fn pass_borrowed<T: AsFd>(t: &T) {
    let _b: BorrowedFd<'_> = t.as_fd();
    // ...
}
```

ALWAYS return `OwnedFd` from any function that produces a new fd, and ALWAYS take `BorrowedFd<'_>` (or `&impl AsFd`) for functions that only need to read or operate on an fd. NEVER store a `RawFd` in a struct alongside other state: there is no Drop to close it, and ownership becomes ambiguous. NEVER call `close()` on an fd you hold a `BorrowedFd` to: closure is the owner's job.

---

## Callbacks: function pointers, not closures with captures

Rust closures are types with hidden state; they have no stable C ABI. The portable form is `extern "C" fn(...) -> ...` plus a separate `*mut c_void` user-data pointer for state.

```rust
use std::ffi::c_void;

type Callback = extern "C" fn(event: i32, user: *mut c_void);

unsafe extern "C" {
    fn register_callback(cb: Callback, user: *mut c_void);
}

#[repr(C)]
struct MyState { hits: u32 }

extern "C" fn on_event(event: i32, user: *mut c_void) {
    // SAFETY: register_callback was called with a *mut MyState.
    let state = unsafe { &mut *(user as *mut MyState) };
    state.hits = state.hits.saturating_add(1);
    let _ = event;
}

pub fn install(state: &mut MyState) {
    unsafe { register_callback(on_event, state as *mut MyState as *mut c_void) };
}
```

ALWAYS use a plain `extern "C" fn` for the callback type and pass closure state via a `void*` user-data parameter. NEVER cast a `Box<dyn Fn>` or a closure-with-captures to a function pointer: the ABI does not match, even if the cast compiles.

A panic that unwinds across an `extern "C"` boundary is undefined behavior. Since Rust 1.81 the language *aborts* on an uncaught panic in `extern "C"` rather than continuing into C, which is safer but still terminates the process. Wrap the callback body with `std::panic::catch_unwind` if recovery matters; see `references/examples.md`.

---

## Errors across FFI: status codes, out-parameters, or Result inside Rust

C has no `Result<T, E>`. Three idioms cover essentially every case:

1. **Return an integer status code** (`0` = ok, negative or named enum for errors). Most ergonomic for callers in any language.
2. **Out-parameter for the value, status for the error.** `int32_t fn(InputT in, OutputT* out)`.
3. **Translate at the boundary.** Inside Rust, work with `Result<T, E>`; at the `extern "C"` entry point, catch errors and convert to a status code, optionally setting a thread-local "last error" string.

```rust
#[repr(C)]
pub enum MylibStatus { Ok = 0, InvalidArg = 1, Internal = 2 }

#[unsafe(no_mangle)]
pub extern "C" fn mylib_compute(in_: i32, out: *mut i32) -> MylibStatus {
    if out.is_null() { return MylibStatus::InvalidArg; }
    match do_compute(in_) {
        Ok(v) => { unsafe { *out = v }; MylibStatus::Ok }
        Err(_) => MylibStatus::Internal,
    }
}
```

NEVER let a Rust panic escape an `extern "C" fn`. ALWAYS `catch_unwind` if the function calls into Rust code that may panic, and translate the panic to a status code. See [[rust-impl-error-handling]] for the Rust-side `Result` design.

---

## Drop guards for foreign resources

Foreign resources (file descriptors, library handles, malloc-ed buffers) escape Rust's RAII unless wrapped in a type with a `Drop` impl that calls the C deallocator.

```rust
pub struct CBuffer { ptr: *mut u8, len: usize }

impl CBuffer {
    pub fn new(len: usize) -> Option<Self> {
        // SAFETY: malloc returns null on failure or a valid len-byte allocation.
        let ptr = unsafe { libc::malloc(len) as *mut u8 };
        (!ptr.is_null()).then_some(Self { ptr, len })
    }
    pub fn as_slice(&self) -> &[u8] {
        // SAFETY: ptr is valid for `len` bytes and not mutably aliased.
        unsafe { core::slice::from_raw_parts(self.ptr, self.len) }
    }
}

impl Drop for CBuffer {
    fn drop(&mut self) {
        // SAFETY: ptr came from libc::malloc, not from Rust's allocator.
        unsafe { libc::free(self.ptr as *mut _) }
    }
}
```

ALWAYS pair every foreign allocator with the matching deallocator in a `Drop` impl. NEVER panic from `Drop`; if cleanup fails, log it. See [[rust-syntax-unsafe]] §"Drop and panic" for the full rationale.

---

## Quick decision tree

1. **C library to call from Rust ?**
   - Header is small (under ~30 symbols) and stable -> hand-write `unsafe extern "C" { ... }`.
   - Header is large or generated -> `bindgen` from `build.rs`. Always pair with a safe wrapper crate.
2. **Rust library to expose to C ?**
   - Mark exports with `#[unsafe(no_mangle)] pub extern "C" fn ...`.
   - Generate `mylib.h` with `cbindgen`. Keep `cbindgen.toml` in the repo.
3. **Shared struct ?** `#[repr(C)]`. Single-field wrapper that must match inner ABI -> `#[repr(transparent)]`.
4. **Hide Rust struct from C ?** Box it into `*mut T`, expose only opaque pointer + new / free pair.
5. **Strings ?** `CString::new(...)` Rust to C; `CStr::from_ptr(...)` C to Rust. Always document who owns the bytes.
6. **File descriptors ?** `OwnedFd` if owning, `BorrowedFd<'_>` if borrowing, `AsFd` for generic input. Never `RawFd` in API types.
7. **Callbacks ?** `extern "C" fn(..., *mut c_void)`, user-data `void*` for closure state. Never cast a Rust closure to a fn pointer.
8. **Errors ?** Status-code return or out-parameter pattern, `catch_unwind` around any Rust body that may panic.

---

## Reference files

- `references/methods.md` : full `bindgen::Builder` and `cbindgen::Config` option index, `std::os::fd` API table, `std::ffi` API table, ABI-relevant attributes (`#[repr(...)]`, `#[unsafe(...)]`, `#[link]`, `extern "ABI"`).
- `references/examples.md` : end-to-end `-sys` crate, end-to-end Rust-to-C crate, opaque-type round-trip, callback with `catch_unwind`, fd-passing round-trip.
- `references/anti-patterns.md` : the seven mandatory anti-patterns plus six more drawn from real GitHub issues (linkage cycles, missing `links = ...`, transmuting `&str` to `*const c_char`, etc.).

---

## Sources

- Rust Reference, External blocks : https://doc.rust-lang.org/reference/items/external-blocks.html
- Rust Edition Guide, Unsafe extern : https://doc.rust-lang.org/edition-guide/rust-2024/unsafe-extern.html
- Rust Edition Guide, Unsafe attributes : https://doc.rust-lang.org/edition-guide/rust-2024/unsafe-attributes.html
- Rustonomicon, FFI chapter : https://doc.rust-lang.org/nomicon/ffi.html
- `std::os::fd` module : https://doc.rust-lang.org/std/os/fd/index.html
- `std::ffi` module : https://doc.rust-lang.org/std/ffi/index.html
- bindgen user guide : https://rust-lang.github.io/rust-bindgen/
- cbindgen documentation : https://github.com/mozilla/cbindgen
- Cargo `links` key : https://doc.rust-lang.org/cargo/reference/build-scripts.html#the-links-manifest-key

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
