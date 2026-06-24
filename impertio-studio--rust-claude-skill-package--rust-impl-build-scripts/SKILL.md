---
name: rust-impl-build-scripts
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-build-scripts

The **Cargo build script** skill: how to write a `build.rs` that runs before the package compiles, generates code into `OUT_DIR`, links native libraries, sets `cfg` flags and environment variables for the crate, and triggers correct incremental rebuilds via `rerun-if-changed` / `rerun-if-env-changed` directives.

Cross-references: [[rust-impl-cargo-project]] (where `[build-dependencies]` and the `links` key live in `Cargo.toml`), [[rust-impl-ffi-bindgen]] (running bindgen inside `build.rs` to wrap a C library), [[rust-impl-cross-compile]] (`HOST` vs `TARGET`, `CARGO_CFG_*` for cross builds).

---

## When to use this skill

- User adds a `build.rs` to a crate (compiling C, generating code, detecting features, embedding git commit).
- User asks why their build script seems "cached" or never re-runs after editing a file.
- User links a system library (`-lfoo`) and asks where to put the directive.
- User writes generated Rust code and asks where to put it so `cargo publish` and `cargo clean` still work.
- User wants `#[cfg(has_feature_x)]` driven by runtime detection at build time.
- User runs `bindgen` to wrap a C header and asks how to wire it into the build.
- User sees `cargo:warning=` in build output and asks how to emit one.

For toolchain setup, `[build-dependencies]` syntax, the `links` manifest key, and `package.links` semantics see [[rust-impl-cargo-project]]. For the full FFI / bindgen wrapping flow see [[rust-impl-ffi-bindgen]]. For cross-compilation specifics in `build.rs` (`HOST` vs `TARGET`, `CARGO_CFG_TARGET_*`) see [[rust-impl-cross-compile]].

---

## Mental model: what a build script is

1. `build.rs` lives at the crate root (next to `Cargo.toml`), is compiled by Cargo into an executable, and is then **run before the crate itself compiles**.
2. It communicates with Cargo through **lines printed to stdout**, prefixed with `cargo::KEY=VALUE` (`cargo::` is the modern double-colon spelling; `cargo:` single-colon still works for back-compat).
3. Any stdout line that does NOT start with `cargo:` or `cargo::` is treated as a comment and discarded silently. ALWAYS prefix every meaningful line with `cargo::`.
4. A non-zero exit halts the build and shows the captured stdout/stderr.
5. Its dependencies come from `[build-dependencies]` in `Cargo.toml`. The runtime `[dependencies]` of the crate are NOT yet built and CANNOT be used inside `build.rs`.

```
project/
  Cargo.toml           # has [build-dependencies] + (optional) `links = "foo"`
  build.rs             # this skill
  src/
    lib.rs             # may `include!(concat!(env!("OUT_DIR"), "/gen.rs"))`
```

[Source: Cargo build-scripts reference][cargo-build]

---

## Inputs: environment variables Cargo gives you

Every `build.rs` runs with these env vars set. The full list is in [[references/methods.md]]; the load-bearing ones :

| Variable | Meaning |
|----------|---------|
| `OUT_DIR` | The per-target directory where the script writes generated files. Persists across rebuilds; Cargo does NOT clean it. |
| `CARGO_MANIFEST_DIR` | Crate root path (where `Cargo.toml` lives). |
| `TARGET` | Triple of the platform being built FOR (e.g. `x86_64-unknown-linux-gnu`). |
| `HOST` | Triple of the platform building (different from `TARGET` when cross-compiling). |
| `PROFILE` | `debug` or `release`. |
| `OPT_LEVEL`, `DEBUG`, `NUM_JOBS` | Profile + parallelism hints. |
| `CARGO_CFG_TARGET_OS`, `CARGO_CFG_TARGET_ARCH`, `CARGO_CFG_TARGET_ENV`, `CARGO_CFG_TARGET_FAMILY`, `CARGO_CFG_TARGET_POINTER_WIDTH`, `CARGO_CFG_UNIX`, `CARGO_CFG_WINDOWS` | Target `cfg` values exposed as env vars. |
| `CARGO_FEATURE_<NAME>` | Set to `1` when feature `<name>` is enabled (`-` becomes `_`, uppercased). |
| `DEP_<LINKS>_<KEY>` | Metadata from a build-script dependency that declared `links = "<x>"`. |

ALWAYS read target info via `env::var("CARGO_CFG_TARGET_OS")` inside `build.rs`. NEVER use the `cfg!(target_os = ...)` macro in `build.rs`: that resolves at compile time of the script (the HOST), not for the TARGET, which is wrong under cross-compilation.

---

## Outputs: the `cargo::` directives

Print each on its own line to stdout. Quoting follows shell-style: values with spaces or `=` need careful escaping (see [[references/methods.md]]).

### Re-run control

| Directive | Effect |
|-----------|--------|
| `cargo::rerun-if-changed=PATH` | Re-run the script when `PATH` (file or directory) changes. |
| `cargo::rerun-if-env-changed=VAR` | Re-run when env var `VAR` changes value. |

**Default rule** (the trap): if the script prints **no** `rerun-if-*` lines at all, Cargo re-runs it whenever ANY file in the package changes. As soon as you print ONE `rerun-if-changed`, you OPT OUT of the default and Cargo only re-runs on the paths/env-vars you list.

ALWAYS list every external file you read inside the script with `rerun-if-changed`. NEVER print a single `rerun-if-changed` and forget another input: the script will silently go stale.

ALWAYS pair `rerun-if-env-changed=VAR` with every `env::var("VAR")` call inside `build.rs`. Without it, changing the env var does not retrigger the script.

### Compile-time `cfg` and env

| Directive | Effect | Use from crate code as |
|-----------|--------|------------------------|
| `cargo::rustc-cfg=KEY` | Enables `#[cfg(KEY)]`. | `#[cfg(KEY)]` |
| `cargo::rustc-cfg=KEY="VAL"` | Enables `#[cfg(KEY = "VAL")]`. | `#[cfg(KEY = "VAL")]` |
| `cargo::rustc-env=NAME=VALUE` | Sets a build-time env var visible via the `env!` macro. | `env!("NAME")` |
| `cargo::rustc-check-cfg=cfg(KEY)` | Registers `KEY` so the `unexpected_cfgs` lint does not fire (Rust 1.80+). | (no runtime effect) |

ALWAYS emit a matching `rustc-check-cfg` for every custom `rustc-cfg` you set (Rust 1.80 and later flag unexpected `cfg`s by default). Skipping it produces a hard `warning` on stable, and `deny(warnings)` projects fail to build.

### Linking native libraries

| Directive | Effect |
|-----------|--------|
| `cargo::rustc-link-lib=[KIND=]NAME` | Link native library `NAME`. `KIND` is `static`, `dylib` (default), or `framework` (macOS). |
| `cargo::rustc-link-search=[KIND=]PATH` | Add `PATH` to the linker search list. `KIND` is `native`, `framework`, `all`, `dependency`, or `crate`. |
| `cargo::rustc-link-arg=ARG` | Append a raw linker flag for all linkable targets. |
| `cargo::rustc-link-arg-bins=ARG` | Same, binaries only. `-bin=NAME=ARG`, `-tests`, `-examples`, `-benches`, `-cdylib` are also available. |

ALWAYS qualify with `KIND=`: `static=foo` for archives, `dylib=foo` for shared libs, `framework=Cocoa` for macOS frameworks. NEVER rely on the implicit default if you actually want static linking.

NEVER hard-code absolute paths in `rustc-link-search` unless they are derived from `OUT_DIR` or a discovered prefix (e.g. via `pkg-config`). Absolute paths break on every other machine.

### Diagnostics

| Directive | Effect |
|-----------|--------|
| `cargo::warning=MESSAGE` | Print a non-fatal warning. Shown only for **path / git dependencies** in normal output, and for every package under `cargo build -vv`. |
| `cargo::error=MESSAGE` | Print a hard error and fail the build (Rust 1.84+). |

ALWAYS emit `cargo::warning=` when a non-fatal config issue is detected (e.g. missing optional system lib, falling back to a slow path). NEVER use `eprintln!` for the same purpose: Cargo captures stderr to a file, the user never sees it.

NEVER call `panic!` or `process::exit` from `build.rs` to surface a config error on stable; prefer `cargo::error=...` (1.84+) or fall back to a `panic!` only if the build truly cannot proceed and you accept a stack trace in the output.

---

## Decision tree: what does this `build.rs` need to do?

```
Are you compiling/linking native code?
  YES  -> rustc-link-lib (+ optional rustc-link-search), use cc / pkg-config crates.
  NO   -> next.

Are you generating Rust code?
  YES  -> write into $OUT_DIR/<file>.rs, include! it from src/.
          ALSO print rerun-if-changed for every input read.
  NO   -> next.

Are you detecting platform features (libc version, available syscalls, ...)?
  YES  -> rustc-cfg=KEY (+ matching rustc-check-cfg).
  NO   -> next.

Are you embedding metadata (git commit, build time, version)?
  YES  -> rustc-env=NAME=VALUE.

Do you read files outside the crate?
  YES  -> rerun-if-changed=<each path>.

Do you read env vars (CC, PKG_CONFIG_PATH, custom)?
  YES  -> rerun-if-env-changed=<each var>.

Final answer:
  - If none of the above: probably no need for build.rs at all.
  - Otherwise: ensure rerun-if-* lines cover every input.
```

---

## Pattern 1: generate Rust code into `OUT_DIR`

`build.rs` :

```rust
use std::env;
use std::fs;
use std::path::PathBuf;

fn main() {
    // Inputs that should retrigger this script.
    println!("cargo::rerun-if-changed=build.rs");
    println!("cargo::rerun-if-changed=data/protocol.txt");

    let out_dir = PathBuf::from(env::var("OUT_DIR").expect("OUT_DIR not set"));
    let spec    = fs::read_to_string("data/protocol.txt")
        .expect("failed to read data/protocol.txt");

    let mut generated = String::from("// AUTO-GENERATED. DO NOT EDIT.\n");
    for (i, line) in spec.lines().enumerate() {
        generated.push_str(&format!(
            "pub const OP_{i}: &str = {line:?};\n"
        ));
    }
    fs::write(out_dir.join("ops.rs"), generated)
        .expect("failed to write ops.rs");
}
```

`src/lib.rs` :

```rust
include!(concat!(env!("OUT_DIR"), "/ops.rs"));
```

ALWAYS write generated code under `$OUT_DIR`, NEVER under `src/`. Writing into `src/` breaks `cargo publish` (the directory may be read-only), pollutes git diffs, and produces stale checked-in code when the script changes.

ALWAYS include via `concat!(env!("OUT_DIR"), "/<file>")` so the path is resolved at compile time. NEVER hard-code a relative path like `"../target/.../ops.rs"`.

---

## Pattern 2: compile and link a small C library

`Cargo.toml` :

```toml
[package]
name  = "my-crate"
links = "myhelper"           # at most one crate per links value

[build-dependencies]
cc = "1"
```

`build.rs` :

```rust
fn main() {
    println!("cargo::rerun-if-changed=src/helper.c");
    println!("cargo::rerun-if-changed=src/helper.h");

    cc::Build::new()
        .file("src/helper.c")
        .compile("myhelper");     // emits libmyhelper.a in OUT_DIR

    // cc already emits rustc-link-lib=static=myhelper + rustc-link-search,
    // so no extra println! is needed here.
}
```

ALWAYS declare `links = "myhelper"` in `[package]` when the crate exposes a unique native symbol set. Cargo enforces a single crate per `links` value, preventing duplicate-symbol link errors at the workspace level.

NEVER pass `cc::Build::new().compile("foo")` without `rerun-if-changed` for every source/header file. The `cc` crate does NOT emit those automatically for arbitrary inputs.

---

## Pattern 3: bindgen workflow

`Cargo.toml` :

```toml
[build-dependencies]
bindgen = "0.71"
```

`build.rs` :

```rust
use std::env;
use std::path::PathBuf;

fn main() {
    println!("cargo::rerun-if-changed=wrapper.h");

    let out_dir = PathBuf::from(env::var("OUT_DIR").unwrap());
    let bindings = bindgen::Builder::default()
        .header("wrapper.h")
        // Invalidate cached bindings when headers change.
        .parse_callbacks(Box::new(bindgen::CargoCallbacks::new()))
        .generate()
        .expect("bindgen failed");

    bindings
        .write_to_file(out_dir.join("bindings.rs"))
        .expect("could not write bindings.rs");
}
```

`src/lib.rs` :

```rust
#![allow(non_camel_case_types, non_snake_case, non_upper_case_globals)]
include!(concat!(env!("OUT_DIR"), "/bindings.rs"));
```

ALWAYS attach `bindgen::CargoCallbacks::new()` so every `#include`d header gets a `cargo::rerun-if-changed=` emitted automatically. NEVER hand-maintain that list: includes are transitive and easy to miss.

For the full FFI wrapping discussion (safe wrappers, `extern "C"`, `#[repr(C)]`, ownership of `OwnedFd`) see [[rust-impl-ffi-bindgen]].

---

## Pattern 4: platform / feature detection -> `rustc-cfg`

`build.rs` :

```rust
use std::env;

fn main() {
    println!("cargo::rerun-if-changed=build.rs");
    println!("cargo::rerun-if-env-changed=MYCRATE_FORCE_SIMD");

    // Always register the cfg names we may set (Rust 1.80+).
    println!("cargo::rustc-check-cfg=cfg(has_simd)");
    println!("cargo::rustc-check-cfg=cfg(has_io_uring)");

    let target_os   = env::var("CARGO_CFG_TARGET_OS").unwrap();
    let target_arch = env::var("CARGO_CFG_TARGET_ARCH").unwrap();

    if target_arch == "x86_64" || target_arch == "aarch64" {
        println!("cargo::rustc-cfg=has_simd");
    }
    if target_os == "linux" && kernel_supports_io_uring() {
        println!("cargo::rustc-cfg=has_io_uring");
    }
}

fn kernel_supports_io_uring() -> bool { /* probe ... */ true }
```

Then in `src/`:

```rust
#[cfg(has_simd)]
mod simd_impl;
#[cfg(not(has_simd))]
mod scalar_impl;
```

ALWAYS emit a matching `rustc-check-cfg=cfg(KEY)` for every `rustc-cfg=KEY` you set. NEVER ship a build script that triggers `unexpected_cfgs` warnings on stable Rust.

---

## Pattern 5: embed metadata via `rustc-env`

```rust
use std::process::Command;

fn main() {
    println!("cargo::rerun-if-changed=.git/HEAD");
    println!("cargo::rerun-if-changed=.git/refs/heads");

    let sha = Command::new("git")
        .args(["rev-parse", "--short", "HEAD"])
        .output()
        .ok()
        .and_then(|o| String::from_utf8(o.stdout).ok())
        .map(|s| s.trim().to_string())
        .unwrap_or_else(|| "unknown".to_string());

    println!("cargo::rustc-env=GIT_COMMIT={sha}");
}
```

Then in `src/main.rs`:

```rust
fn main() {
    println!("build: {}", env!("GIT_COMMIT"));
}
```

ALWAYS list both `.git/HEAD` and `.git/refs/heads` in `rerun-if-changed` for git-derived metadata. NEVER assume `.git/HEAD` alone suffices: a `git pull` updates `refs/heads/<branch>` first.

---

## Quick recipes

### Minimal build.rs that ONLY watches one file

```rust
fn main() {
    println!("cargo::rerun-if-changed=build.rs");
}
```

(This is also the cheapest way to opt out of the default "rerun on any package change".)

### See what your build script printed

```bash
cargo build -vv 2>&1 | grep -E '^\s*\[my-crate .*]'
# or read the captured output directly
cat target/debug/build/my-crate-*/output
```

### Force-rerun a build script

```bash
cargo clean -p my-crate
# or touch a file the script watches
touch build.rs
```

---

## Validation checklist

Before merging a `build.rs` change, verify :

1. `cargo build` followed by a second `cargo build` shows no rebuild for unchanged inputs.
2. `cargo build -vv` shows the `cargo::` directives you expect.
3. Every file read inside the script has a matching `rerun-if-changed`.
4. Every env var read with `env::var(...)` has a matching `rerun-if-env-changed`.
5. Every `rustc-cfg=KEY` has a matching `rustc-check-cfg=cfg(KEY)` (Rust 1.80+).
6. Generated files live under `$OUT_DIR`, not `src/`.
7. `cargo clean && cargo build` reproduces the same artifacts.
8. The `[build-dependencies]` table contains every crate used in `build.rs` (and only those).

---

## Reference links

- [[references/methods.md]] : full directive reference, every env var Cargo sets for `build.rs`, the `links` manifest key and its metadata interface (`DEP_<LINKS>_<KEY>`).
- [[references/examples.md]] : complete `build.rs` files for the five patterns (code-gen, cc, bindgen, cfg detection, git metadata) plus a combined example.
- [[references/anti-patterns.md]] : missing `rerun-if-changed`, writing into `src/`, raw `eprintln!` instead of `cargo::warning`, absolute paths in `rustc-link-search`, missing `rustc-check-cfg`, using `cfg!(target_os = ...)` inside `build.rs`.

[cargo-build]: https://doc.rust-lang.org/cargo/reference/build-scripts.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
