---
name: rust-errors-build-link
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# Rust Build and Link Errors

Diagnose and fix linker errors, native-library failures, duplicate-symbol
conflicts, MSRV mismatches, and feature-unification surprises. These errors
come from Cargo, rustc, the system linker, and build scripts. Each has a
distinct root cause and a deterministic fix. Blind `cargo clean` is never the
first move.

## Quick Reference

| Symptom | Root cause | Fix |
|---------|-----------|-----|
| `linker `cc` not found` | No C toolchain installed | Install build-essential / Xcode CLI / MSVC build tools |
| `undefined reference to '...'` | Missing native lib or wrong link order | `cargo::rustc-link-lib` in build.rs, install `-dev` package |
| `could not find native static library X` | Search path not set | `cargo::rustc-link-search=native=PATH` or `*_LIB_DIR` env var |
| `symbol '...' is already defined` (links crate) | Two major versions of a `links` crate | `cargo tree -d`, unify versions |
| `package X requires rustc 1.YY` | Dependency MSRV exceeds toolchain | Update toolchain, or pin dependency older |
| A feature is unexpectedly on | Feature unification across the build graph | `cargo tree -f`, find the consumer that enabled it |

## ALWAYS / NEVER

- ALWAYS read the FULL error block. The `note:` lines after `error: linking with
  cc failed` contain the actual linker diagnostic. The top line is generic.
- ALWAYS run `cargo tree -d` when you see a duplicate-symbol or "multiple
  versions" error. Diagnose before changing dependencies.
- ALWAYS prefer a targeted `cargo update -p <crate> --precise <version>` over a
  global `cargo update`. One conflict does not justify bumping every dependency.
- ALWAYS install the missing `-dev` (Debian) / `-devel` (Fedora) package when an
  `undefined reference` traces to a system library. This is not a Rust bug.
- ALWAYS treat feature unification as designed behavior: enabling a feature in
  one place enables it for ALL consumers of that crate in the build graph.
- NEVER run `cargo clean` as a routine response to a build error. It deletes
  `target/` and forces a full rebuild, masking the real cause and wasting time.
- NEVER pin `=x.y.z` to dodge a version conflict. It over-constrains the lock
  file and blocks every future compatible upgrade.
- NEVER set `LD_LIBRARY_PATH` at runtime to paper over a link-configuration
  problem. Fix the search path or RPATH at build time.

## Decision Tree: Which Build Error Is This

```
Build fails. Read the error category.
|
+-- "linker `cc` not found" / "linker `link.exe` not found"
|     -> No C toolchain on the machine.
|     -> Install per OS (see table below). [[rust-core-toolchain]]
|
+-- "undefined reference to `<symbol>`" / "note: ld returned 1 exit status"
|     -> A symbol the linker needs is not in any linked object.
|     -> Missing native library OR wrong link order OR missing build.rs link directive.
|     -> See "Undefined Reference" section.
|
+-- "could not find native static library `X`"
|     -> rustc has the -l flag but no -L search path containing libX.a.
|     -> Add cargo::rustc-link-search, or set the -sys crate's *_LIB_DIR env var.
|
+-- "symbol `...` is already defined" / "multiple definition of"
|     -> Two versions of a crate that uses `links` are both in the tree.
|     -> cargo tree -d -> unify versions. See "Duplicate Symbols" section.
|
+-- "package `X v1.2.3` cannot be built because it requires rustc 1.YY"
|     -> Dependency MSRV is newer than the installed toolchain.
|     -> Update toolchain OR pin the dependency older. See "MSRV Mismatch".
|
+-- Code compiles a feature you did not enable
      -> Feature unification: a transitive dependency enabled it.
      -> cargo tree -f to find which consumer. See "Feature Unification".
```

## Missing Linker (`linker 'cc' not found`)

Rust compiles to object files, then invokes the system C linker to produce the
final binary. If no C toolchain is installed, linking fails before it starts.

| OS / target | Install command |
|-------------|-----------------|
| Debian / Ubuntu | `sudo apt install build-essential` |
| Fedora / RHEL | `sudo dnf install gcc` (or `@development-tools`) |
| Arch Linux | `sudo pacman -S base-devel` |
| Alpine | `apk add build-base` |
| macOS | `xcode-select --install` |
| Windows (MSVC) | Install "Visual Studio Build Tools" with the C++ workload |
| Windows (GNU) | `rustup default stable-gnu` plus a MinGW-w64 toolchain |
| musl static | `rustup target add x86_64-unknown-linux-musl` plus `musl-tools` |

On Windows the default toolchain is `*-pc-windows-msvc`, which needs the MSVC
linker `link.exe`. The error `linker `link.exe` not found` means the Build Tools
are missing. See [[rust-core-toolchain]] for cross-compilation linker setup.

## Undefined Reference (`undefined reference to`)

The linker found every Rust object but a referenced symbol is in none of them.
Three root causes, in order of likelihood:

1. **Missing native library.** An FFI declaration (`extern "C"`) names a function
   that lives in a C library not passed to the linker. Fix: tell Cargo to link
   it. In `build.rs`:
   ```rust
   println!("cargo::rustc-link-lib=dylib=z");      // links libz
   println!("cargo::rustc-link-lib=static=foo");   // links libfoo.a
   ```
   Per the Cargo Book, "the `rustc-link-lib` instruction tells Cargo to link the
   given library using the compiler's `-l` flag." The `KIND` is `dylib`,
   `static`, or `framework`.
2. **Missing `-dev` package.** The library exists at runtime but the headers and
   the linkable `.so`/`.a` symlink are in a separate package. Install
   `libfoo-dev` (Debian) or `foo-devel` (Fedora). This is a system-packaging
   issue, NOT a Rust bug.
3. **Wrong link order.** The C linker resolves symbols left to right; a library
   must appear AFTER the object that references it. With multiple
   `cargo::rustc-link-lib` lines, order them so dependers precede dependees.

See [[rust-impl-ffi-bindgen]] for declaring the `extern` block and
[[rust-impl-build-scripts]] for the full build.rs directive set.

## Could Not Find Native Static Library

`rustc` was told to link `libX` (via `-l`) but no `-L` search path contains it.
Fix by adding the directory:

```rust
// build.rs
println!("cargo::rustc-link-search=native=/usr/local/lib");
println!("cargo::rustc-link-lib=static=X");
```

The `KIND` for `rustc-link-search` is one of `dependency`, `crate`, `native`,
`framework`, or `all`. Use `native` for system C libraries.

When the failing crate is a `-sys` crate (for example `openssl-sys`,
`libsqlite3-sys`), it usually reads an environment variable for the library
location instead of guessing. Set the variable the crate documents, commonly
`<NAME>_LIB_DIR` and `<NAME>_INCLUDE_DIR`:

```bash
export OPENSSL_LIB_DIR=/usr/local/opt/openssl/lib
export OPENSSL_INCLUDE_DIR=/usr/local/opt/openssl/include
cargo build
```

A `-sys` crate that uses `links` also exports `DEP_<LINKS>_<KEY>` env vars to its
immediate dependents. See `references/methods.md` for the full convention.

## Duplicate Symbols (Multiple Major Versions)

Cargo allows two semver-incompatible major versions of the same crate to coexist
(for example `rand 0.8` and `rand 0.9`). This is by design and is harmless for
pure-Rust crates: each version gets mangled symbols.

The duplicate-symbol LINK error happens specifically with crates that use the
`links` manifest key (they own a native library). Two versions both try to link
the same native lib, producing `symbol already defined`.

Diagnose first:

```bash
cargo tree -d            # lists every crate present in multiple versions
cargo tree -i <crate>    # inverted tree: who depends on <crate>
```

`cargo tree -d` shows "only dependencies which come in multiple versions". Per
the Cargo Book, you "then investigate if the package that depends on the
duplicate with the older version can be updated to the newer version."

Fixes, in order of preference:

1. **Bump the lagging dependency.** Update the crate that pulls the OLD version
   so it pulls the new one. `cargo update -p <lagging-crate>`.
2. **Targeted precise update.** `cargo update -p <crate> --precise <version>` to
   pull a specific version into the lock file.
3. **`[patch.crates-io]`.** When you must force one version tree-wide, patch it
   in the workspace `Cargo.toml`. Use sparingly; it is a global override.

Never globally `cargo update` to fix one conflict, and never pin `=x.y.z`. See
`references/anti-patterns.md`.

## MSRV Mismatch

`error: package `X v1.2.3` cannot be built because it requires rustc 1.YY` means
a dependency's `rust-version` (its MSRV) is newer than your installed toolchain.

Options:

1. **Update the toolchain** (preferred): `rustup update stable`.
2. **Pin the dependency older**: `cargo update -p X --precise <older-version>`
   to a release whose MSRV your toolchain satisfies.
3. **MSRV-aware resolver** (Rust 1.84+): set `resolver = "3"` and a
   `rust-version` in your manifest. Cargo will then prefer dependency versions
   compatible with your declared MSRV instead of erroring late.

See [[rust-core-toolchain]] for `rustup` and [[rust-impl-cargo-project]] for the
`rust-version` field and resolver configuration.

## Feature Unification

Cargo unifies features: "When a dependency is used by multiple packages, Cargo
will use the union of all features enabled on that dependency when building it."
A feature that appears unexpectedly on is almost always enabled by a transitive
dependency, NOT a compiler bug.

Diagnose with the feature-annotated tree:

```bash
cargo tree -f "{p} {f}"       # shows enabled features per package
cargo tree -e features        # shows which features each edge enables
```

Find the consumer that enabled the surprising feature, then decide:

- If you do not want it, set `default-features = false` on YOUR direct
  dependency and re-enable only what you need.
- You cannot disable a feature another crate in the graph requires. Features are
  additive by contract.

The resolver-2/3 behavior (default in edition 2024) does avoid unifying
build-dependency and proc-macro features into normal dependencies. A feature
leaking from a build-dependency under an old resolver is fixed by upgrading the
resolver. See [[rust-impl-cargo-project]].

## When `cargo clean` Is the Answer

`cargo clean` is correct ONLY for genuine cache corruption: a panicking
incremental-compilation cache, a `target/` directory copied between
incompatible toolchains, or a stale `OUT_DIR` after editing a `build.rs`. It is
NEVER the fix for a linker error, a missing library, a version conflict, or an
MSRV mismatch. For a stale build script specifically, `cargo build` already
re-runs it when `rerun-if-changed` paths change; prefer fixing the directives
over nuking the cache.

## Reference Files

- `references/methods.md` : full command and directive reference (cargo tree
  flags, build.rs link directives, `*-sys` env-var convention, resolver).
- `references/examples.md` : worked diagnostic sessions for each error class.
- `references/anti-patterns.md` : six anti-patterns with WHY they fail and the
  correct fix.

## Related Skills

- [[rust-impl-cargo-project]] : manifest, `rust-version`, resolver, `[lints]`.
- [[rust-impl-build-scripts]] : authoring `build.rs` and its directives.
- [[rust-impl-ffi-bindgen]] : `extern` blocks, `bindgen`, `-sys` crate patterns.
- [[rust-core-toolchain]] : `rustup`, targets, cross-compilation linkers.
- [[rust-agents-compile-fix]] : orchestrated compile-error remediation.

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
