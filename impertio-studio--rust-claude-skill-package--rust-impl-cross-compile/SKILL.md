---
name: rust-impl-cross-compile
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-cross-compile

The **cross-compilation** skill: how to build a Rust crate for a target other than the host. Covers target triple format and common triples, `rustup target add` to install standard library artifacts, `cargo build --target <triple>`, the `cross` crate for container-based toolchains, conditional compilation via `cfg(target_os = ...)` and related predicates, the `cfg_attr` attribute for selective attribute emission, and per-target linker / runner configuration via `.cargo/config.toml`.

Cross-references: [[rust-impl-cargo-project]] (single-crate Cargo.toml + profiles), [[rust-impl-build-scripts]] (`build.rs` that detects target via env vars), [[rust-impl-ffi-bindgen]] (cross-compile C deps and adjust sysroot), [[rust-core-toolchain]] (rustup channels, components, target lists).

---

## When to use this skill

- User runs `cargo build` and gets `linker 'cc' not found` or `error: target may not be installed` after switching `--target`.
- User wants a portable static Linux binary and asks `musl` vs `gnu`.
- User builds for Windows from Linux and asks `pc-windows-msvc` vs `pc-windows-gnu`.
- User builds for macOS ARM (Apple Silicon) from x86_64 host.
- User asks how to build a `.wasm` module and asks `wasm32-unknown-unknown` vs `wasm32-wasip1`.
- User writes platform-conditional code with `#[cfg(target_os = "...")]` or `cfg!(...)`.
- User wants a single attribute (e.g. `#[repr(C)]`, `#[link(...)]`) to appear only on one target.
- User configures a custom linker for a target via `[target.<triple>] linker = "..."` in `.cargo/config.toml`.
- User asks about the `cross` tool because installing native toolchains is tedious.

For host-only profile / lints / dependency tuning see [[rust-impl-cargo-project]]. For build-time target detection in `build.rs` see [[rust-impl-build-scripts]].

---

## Target triple format

Rust target triples follow the shape `<arch>-<vendor>-<os>-<env>` where each field is a fixed identifier (see [platform-support][platform]):

| Field | Examples | Meaning |
|-------|----------|---------|
| `arch` | `x86_64`, `aarch64`, `i686`, `armv7`, `thumbv7em`, `riscv64gc`, `wasm32`, `wasm64` | CPU instruction set. |
| `vendor` | `unknown`, `apple`, `pc`, `none`, `nvidia` | Convention only; `unknown` is the placeholder for non-Apple / non-PC. |
| `os` | `linux`, `darwin`, `windows`, `none`, `wasi`, `freebsd`, `android` | Operating system. `none` for bare metal / freestanding. |
| `env` | `gnu`, `musl`, `msvc`, `eabihf`, `androideabi`, `wasip1` (1.81+), absent | ABI / libc / runtime flavour. May be absent (`aarch64-apple-darwin`). |

ALWAYS write the full triple. NEVER abbreviate to `linux` or `windows`. NEVER assume a host target; query with `rustc -vV | grep host`.

```text
x86_64-unknown-linux-gnu      arch=x86_64  vendor=unknown  os=linux    env=gnu
x86_64-unknown-linux-musl     arch=x86_64  vendor=unknown  os=linux    env=musl
aarch64-apple-darwin          arch=aarch64 vendor=apple    os=darwin   env=(none)
x86_64-pc-windows-msvc        arch=x86_64  vendor=pc       os=windows  env=msvc
x86_64-pc-windows-gnu         arch=x86_64  vendor=pc       os=windows  env=gnu
wasm32-unknown-unknown        arch=wasm32  vendor=unknown  os=unknown  env=(none)
wasm32-wasip1                 arch=wasm32  vendor=unknown  os=wasi     env=p1
thumbv7em-none-eabihf         arch=thumbv7em vendor=none   os=none     env=eabihf
```

NOTE: `wasm32-wasi` was renamed to `wasm32-wasip1` in Rust 1.81 ([rust181]). `wasm32-wasi` still works as a legacy alias but new code should use `wasm32-wasip1` (or `wasm32-wasip2` for WASIp2).

---

## Common target triples

Decision rules per use case. Tier classification is from [platform-support][platform]: Tier 1 has CI guarantees + host tools, Tier 2 with-host-tools has guaranteed std + tools, Tier 2 without-host-tools has guaranteed std only, Tier 3 is best-effort.

| Target Triple | Tier | When to use it |
|---------------|------|----------------|
| `x86_64-unknown-linux-gnu` | 1 | Default 64-bit Linux. Dynamic linking against glibc 2.17+. |
| `aarch64-unknown-linux-gnu` | 1 | ARM64 Linux servers (AWS Graviton, RPi 64-bit, etc.). |
| `x86_64-unknown-linux-musl` | 2-host | Fully static Linux binary, no glibc dependency. |
| `aarch64-unknown-linux-musl` | 2-host | Static ARM64 Linux binary. |
| `aarch64-apple-darwin` | 1 | macOS on Apple Silicon (M1+). |
| `x86_64-apple-darwin` | 1 | macOS on Intel. |
| `x86_64-pc-windows-msvc` | 1 | Windows native, MSVC toolchain (needs Visual Studio Build Tools). |
| `x86_64-pc-windows-gnu` | 1 | Windows via MinGW-w64 (cross-compile-friendly from Linux). |
| `aarch64-pc-windows-msvc` | 1 | Windows on ARM64. |
| `wasm32-unknown-unknown` | 2 | Browser WebAssembly. No OS. Requires `wasm-bindgen` for browser glue. |
| `wasm32-wasip1` | 2 | WebAssembly with WASI Preview 1 (filesystem, env, clock). |
| `wasm32-wasip2` | 2 | WebAssembly with WASIp2 (component model). |
| `thumbv7em-none-eabihf` | 2 | Cortex-M4F / M7F embedded (no_std only, hardfloat). |

### Linux: gnu vs musl

```
Need a portable static binary that runs on any Linux distro (no glibc dep)?
   YES -> *-unknown-linux-musl
   NO  -> *-unknown-linux-gnu (default, dynamic glibc, smaller binary, glibc 2.17+)

Will the binary load shared libraries with dlopen at runtime?
   YES -> *-gnu (musl has known dlopen quirks)
   NO  -> either is fine, choose by static vs dynamic preference

Is the binary going into a `FROM scratch` Docker image?
   YES -> *-musl is the only sane choice
```

### Windows: msvc vs gnu

```
Compiling on Windows with Visual Studio Build Tools installed?
   YES -> *-pc-windows-msvc (recommended, matches Microsoft ecosystem)

Cross-compiling from Linux / macOS to Windows?
   *-pc-windows-gnu is easier (just install mingw-w64 cross gcc).
   *-pc-windows-msvc also works via `cargo-xwin` or LLVM tooling but heavier setup.

Linking against a precompiled `.lib` from a vendor?
   Match the vendor's ABI. MSVC libs require msvc; MinGW libs require gnu.
```

### WebAssembly: unknown vs wasi

```
Browser target with JS interop?  -> wasm32-unknown-unknown + wasm-bindgen.
CLI / serverless WASI runtime (wasmtime, wasmer)?  -> wasm32-wasip1 (or wasip2 for component model).
Pure compute, no I/O, no DOM?    -> wasm32-unknown-unknown (smaller, no OS shims).
```

---

## Cross-compile workflow

### Step 1 : install the target standard library

```bash
rustup target add x86_64-unknown-linux-musl
```

`rustup target add` installs ONLY the precompiled standard library for the target ([rustup-cross][rustup-cross]). It does NOT install a linker, libc, or any platform SDK. The host toolchain (compiler) is reused; only `libstd` / `libcore` / `liballoc` are added.

List available targets : `rustup target list`. List installed : `rustup target list --installed`. Remove : `rustup target remove <triple>`.

### Step 2 : ensure a target-compatible linker exists

| Target | Linker needed on Linux host |
|--------|------------------------------|
| `x86_64-unknown-linux-musl` | `musl-gcc` (Alpine native) or via `cross` |
| `aarch64-unknown-linux-gnu` | `gcc-aarch64-linux-gnu` (Debian/Ubuntu) |
| `aarch64-unknown-linux-musl` | `musl-cross` toolchain or `cross` |
| `x86_64-pc-windows-gnu` | `gcc-mingw-w64-x86-64` |
| `x86_64-pc-windows-msvc` | MSVC SDK + `cargo-xwin`, or `cross` |
| `aarch64-apple-darwin` | macOS SDK (must build on macOS host or use osxcross) |
| `wasm32-unknown-unknown` | none required, LLVM ships its own. |
| `wasm32-wasip1` | none required. |

NEVER expect `rustup target add` to install gcc / clang / lld. ALWAYS install the host-side cross toolchain separately, or delegate to `cross`.

### Step 3 : point Cargo at the linker via `.cargo/config.toml`

```toml
# .cargo/config.toml (project-local or in $CARGO_HOME)
[target.aarch64-unknown-linux-gnu]
linker = "aarch64-linux-gnu-gcc"

[target.x86_64-unknown-linux-musl]
linker = "musl-gcc"

[target.x86_64-pc-windows-gnu]
linker = "x86_64-w64-mingw32-gcc"
```

`[target.<triple>]` keys ([cargo-config][cargo-config]) :

| Key | Purpose | Env var override |
|-----|---------|------------------|
| `linker` | Linker binary path. | `CARGO_TARGET_<TRIPLE>_LINKER` |
| `runner` | Wrapper to run the produced executable (e.g. emulator for `cargo run --target`). | `CARGO_TARGET_<TRIPLE>_RUNNER` |
| `rustflags` | Extra `rustc` flags for this target only. | `CARGO_TARGET_<TRIPLE>_RUSTFLAGS` |
| `rustdocflags` | Extra `rustdoc` flags. | `CARGO_TARGET_<TRIPLE>_RUSTDOCFLAGS` |

Triple names in env vars uppercase and replace dashes with underscores : `CARGO_TARGET_X86_64_UNKNOWN_LINUX_GNU_LINKER`.

### Step 4 : build

```bash
cargo build --target x86_64-unknown-linux-musl --release
# Binary in target/x86_64-unknown-linux-musl/release/<name>
```

ALWAYS include `--release` for distribution builds. The target dir is nested under `target/<triple>/`, so cross builds never overwrite host builds.

### Pin a default target for a project

```toml
# .cargo/config.toml
[build]
target = "x86_64-unknown-linux-musl"
```

Now bare `cargo build` cross-builds without `--target`. NEVER do this in a library crate (forces all downstream users to install that target).

---

## The `cross` tool (container-based)

`cross` ([cross-rs][cross]) is a community wrapper that runs `cargo` inside a per-target Docker / Podman image. Each image bundles the cross linker + libc + sysroot, so the host needs only Docker.

```bash
cargo install cross --git https://github.com/cross-rs/cross
cross build --target aarch64-unknown-linux-gnu --release
```

Use `cross` when :

- Host toolchain setup is awkward (musl-cross on Debian, mingw-w64 on macOS, etc.).
- You build many targets in CI and want hermetic, reproducible toolchains.
- You want Windows / Android / BSD targets without installing each SDK.

NEVER use `cross` for `aarch64-apple-darwin` from a non-macOS host : Apple's SDK license forbids redistribution, so no Docker image exists. ALWAYS build Darwin targets on macOS hardware or via macOS CI runners.

`cross` reads `Cross.toml` for per-target image overrides, mounted volumes, and pre-build hooks. See [[references/methods.md]] for syntax.

---

## Conditional compilation with `cfg`

`cfg` predicates select code that is compiled only when the predicate holds ([reference-cfg][ref-cfg]). All cfg keys are evaluated at compile time against the current target.

### Common cfg keys

| Key | Values | Notes |
|-----|--------|-------|
| `target_os` | `linux`, `macos`, `windows`, `android`, `ios`, `freebsd`, `netbsd`, `openbsd`, `wasi`, `none`, ... | OS identifier. |
| `target_arch` | `x86_64`, `aarch64`, `arm`, `x86`, `wasm32`, `riscv64`, ... | Instruction-set arch. |
| `target_family` | `unix`, `windows`, `wasm` | Coarse-grained OS family. |
| `target_env` | `gnu`, `musl`, `msvc`, `sgx`, empty | Environment / ABI. |
| `target_endian` | `little`, `big` | Byte order. |
| `target_pointer_width` | `16`, `32`, `64` | Pointer size in bits. |
| `target_feature` | `"sse2"`, `"avx2"`, `"neon"`, `"crt-static"`, ... | CPU / runtime features (must be quoted strings). |
| `unix` / `windows` | (boolean keys) | Shortcut for `target_family`. |

```rust
#[cfg(target_os = "linux")]
fn platform() -> &'static str { "linux" }

#[cfg(target_os = "macos")]
fn platform() -> &'static str { "macos" }

#[cfg(target_os = "windows")]
fn platform() -> &'static str { "windows" }
```

### Combining predicates

```rust
#[cfg(all(target_os = "linux", target_arch = "x86_64"))]
fn fast_linux_x64() {}

#[cfg(any(target_os = "linux", target_os = "macos"))]
fn unix_like() {}

#[cfg(not(target_family = "windows"))]
fn not_windows() {}
```

Use `all(...)`, `any(...)`, `not(...)` for boolean composition. NEVER nest `cfg` attributes (`#[cfg(a)] #[cfg(b)]`) when you mean `all` ; while it works, `cfg(all(a, b))` makes intent explicit.

### Runtime `cfg!(...)` macro

```rust
if cfg!(target_os = "linux") {
    // executed only on Linux. Code is still type-checked on every target.
}
```

`cfg!(...)` returns a `bool` constant. Both arms of the `if` are compiled on every target, so they must type-check everywhere. For mutually exclusive code, prefer `#[cfg(...)]` on items.

### `cfg_attr` for conditional attributes

`#[cfg_attr(predicate, attr1, attr2, ...)]` emits the listed attributes ONLY when the predicate is true ([reference-cfg]).

```rust
#[cfg_attr(target_os = "linux", repr(C))]
struct Header {
    magic: u32,
    flags: u32,
}

// On Linux, expands to: #[repr(C)] struct Header { ... }
// On other targets, no repr attribute is applied.

#[cfg_attr(not(test), inline)]
fn hot_path() { /* ... */ }
```

ALWAYS use `cfg_attr` when an attribute itself should be conditional. NEVER copy-paste the whole item under `#[cfg(...)]` just to vary one attribute.

---

## Quick-start workflow recipes

### Static Linux binary from Linux host

```bash
sudo apt install musl-tools                  # provides musl-gcc
rustup target add x86_64-unknown-linux-musl
# Add to .cargo/config.toml: [target.x86_64-unknown-linux-musl] linker = "musl-gcc"
cargo build --release --target x86_64-unknown-linux-musl
file target/x86_64-unknown-linux-musl/release/<name>     # statically linked
```

### Windows .exe from Linux host (MinGW)

```bash
sudo apt install gcc-mingw-w64-x86-64
rustup target add x86_64-pc-windows-gnu
# Add to .cargo/config.toml: [target.x86_64-pc-windows-gnu] linker = "x86_64-w64-mingw32-gcc"
cargo build --release --target x86_64-pc-windows-gnu
```

### WebAssembly (WASI) from any host

```bash
rustup target add wasm32-wasip1
cargo build --release --target wasm32-wasip1
wasmtime target/wasm32-wasip1/release/<name>.wasm
```

### Many targets with `cross`

```bash
cargo install cross --git https://github.com/cross-rs/cross
cross build --release --target aarch64-unknown-linux-gnu
cross build --release --target armv7-unknown-linux-gnueabihf
cross build --release --target x86_64-pc-windows-gnu
```

---

## Validation checklist

Before claiming a cross-build is done :

1. `rustup target list --installed | grep <triple>` shows the target.
2. Linker is on `PATH` or pinned in `.cargo/config.toml`.
3. `cargo build --target <triple>` exits 0.
4. `file target/<triple>/release/<bin>` reports the expected ELF / PE / Wasm format.
5. For musl : `ldd target/<triple>/release/<bin>` reports `not a dynamic executable`.
6. For Wasm : `wasm-objdump -h <bin>.wasm` lists sections without errors.
7. Platform-conditional code paths were exercised on the actual target (or under an emulator) ; `cfg!` only verifies the predicate, not the runtime correctness.

---

## Reference links

- [[references/methods.md]] : full rustup / cargo / cross command catalogue, all `[target.<triple>]` keys, full cfg-key list with valid values, env-var equivalents.
- [[references/examples.md]] : end-to-end recipes (musl static, Windows MinGW from Linux, macOS ARM, WASI, Cortex-M no_std, `Cross.toml`, GitHub Actions matrix).
- [[references/anti-patterns.md]] : missing `rustup target add`, choosing musl for the wrong reason, `wasm32-unknown-unknown` for WASI workloads, hard-coding linker path, missing `cfg_attr`, untested conditional code paths.

[platform]: https://doc.rust-lang.org/rustc/platform-support.html
[cargo-config]: https://doc.rust-lang.org/cargo/reference/config.html
[rustup-cross]: https://rust-lang.github.io/rustup/cross-compilation.html
[cross]: https://github.com/cross-rs/cross
[ref-cfg]: https://doc.rust-lang.org/reference/conditional-compilation.html
[rust181]: https://blog.rust-lang.org/2024/09/05/Rust-1.81.0/

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
