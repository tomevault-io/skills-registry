---
name: rust-core-language-versions
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-core-language-versions

Ecosystem identity skill for Rust : editions, channels, MSRV, version matrix. This is the conceptual map. For toolchain mechanics (rustup, cargo, rustc flags) see `[[rust-core-toolchain]]`. For edition-2024 syntactic changes see `[[rust-syntax-edition-2024]]`. For Cargo.toml details including `[lints]` and `rust-version` mechanics see `[[rust-impl-cargo-project]]`.

## Quick Reference

### Identity in one paragraph

Rust is a statically-typed, ahead-of-time-compiled systems language. No garbage collector. Memory safety enforced by ownership at compile time. Zero-cost abstractions via monomorphization. Six-week release cadence on the stable channel. Editions (2015, 2018, 2021, 2024) gate opt-in language evolution without breaking older crates. Current stable target for this skill package : Rust 1.85+, edition 2024.

### Channel matrix

| Channel | Cadence | Purpose | Use when |
|---|---|---|---|
| stable | 6 weeks | Production | ALWAYS for shipping code |
| beta | 6 weeks | Last-mile stabilization candidate | Verifying your crate before a Rust release |
| nightly | Daily | New unstable features | A required feature is `#![feature(...)]`-gated |

### Edition matrix

| Edition | Year | Required Rust | Notable shifts | Default for `cargo new` |
|---|---|---|---|---|
| 2015 | 2015 | 1.0.0 | Original | never (legacy only) |
| 2018 | Dec 2018 | 1.31.0 | `use` paths, NLL, `async`/`await` reserved | no |
| 2021 | Oct 2021 | 1.56.0 | Disjoint closure capture, `IntoIterator` for arrays, panic macro consistency | no |
| 2024 | Feb 2025 | 1.85.0 | RPIT lifetime capture, `unsafe extern`, `unsafe(no_mangle)`, never-type fallback `!`, `expr` matcher includes `const{}` + `_`, resolver 3 default | YES (since 1.85) |

### Version matrix 1.65 to 1.87 (one row per release, condensed)

| Version | Release | Stabilizations of note |
|---|---|---|
| 1.65 | Nov 2022 | GATs (generic associated types), `let else`, generic associated lifetimes |
| 1.66 | Dec 2022 | Explicit discriminants on enums with fields, `core::hint::black_box` |
| 1.67 | Jan 2023 | `{integer}::ilog`, `MutexGuard` is `Sync` when `T: Sync` |
| 1.68 | Mar 2023 | `Cargo` sparse registry stable |
| 1.69 | Apr 2023 | Cargo `--keep-going`, debug-assert improvements |
| 1.70 | Jun 2023 | `OnceCell`/`OnceLock`, `IsTerminal`, sparse registry default |
| 1.71 | Jul 2023 | C-unwind ABI, `Cell::swap` cross-type |
| 1.72 | Aug 2023 | `target-applies-to-host` cargo, `OnceCell::wait` reservation |
| 1.73 | Oct 2023 | `LocalKey::cell` for `Cell`/`RefCell`, better panic output |
| 1.74 | Nov 2023 | `[lints]` table in `Cargo.toml`, `Option::as_slice` |
| 1.75 | Dec 2023 | **AFIT** (async-fn-in-trait), **RPITIT** (return-position impl Trait in trait) |
| 1.76 | Feb 2024 | `Arc<T>::unwrap_or_clone`, `Vec::as_chunks` |
| 1.77 | Mar 2024 | C-string literals `c"..."`, offset-of macro |
| 1.78 | May 2024 | `#[diagnostic::on_unimplemented]`, deterministic realignment for `pointer::is_aligned` |
| 1.79 | Jun 2024 | Inline const blocks `const { ... }`, bounds in associated type position |
| 1.80 | Jul 2024 | `LazyCell`, **`LazyLock`**, exclusive range patterns, `--check-cfg` |
| 1.81 | Sep 2024 | `core::error::Error` in `no_std`, `#[expect(lint)]`, lint reasons, `fs::exists` |
| 1.82 | Oct 2024 | Precise capturing `+ use<..>`, native `&raw const`/`&raw mut`, `#[unsafe(no_mangle)]` syntax, safe items in `unsafe extern` |
| 1.83 | Nov 2024 | References to statics in const contexts, `Option::get_or_insert_default`, `Waker::new` |
| 1.84 | Jan 2025 | **MSRV-aware Cargo resolver** (opt-in via `resolver.incompatible-rust-versions`), new trait solver for coherence, strict-provenance APIs (`with_addr`, `map_addr`, `expose_provenance`, `with_exposed_provenance`), `isqrt`, `Pin::as_deref_mut` |
| 1.85 | Feb 2025 | **Edition 2024 stable**, async closures (`async \|\| { ... }`), `#[diagnostic::do_not_recommend]`, `FromIterator`/`Extend` up to 12-tuples |
| 1.86 | Apr 2025 | **Trait upcasting** (`dyn Sub` to `dyn Super`), `#[target_feature]` on safe fns, `get_disjoint_mut` for slices and `HashMap`, `Vec::pop_if` |
| 1.87 | May 2025 | `asm!` label operands (jumps to Rust code), anonymous pipes `std::io::pipe()`, precise capturing in trait definitions, `Vec::extract_if`, `OsStr::display` |

## Decision Trees

### "Which edition should I use?"

```
Is this a new crate?
├── YES → ALWAYS edition 2024 (default since cargo 1.85). Set rust-version = "1.85".
└── NO  → Existing crate?
         ├── On 2015/2018/2021 with no migration cost → keep until you need a 2024 feature.
         ├── Want async closures, never-fallback `!`, `unsafe extern` ergonomics → migrate.
         └── Migration command : `cargo fix --edition` then bump `edition = "2024"` in Cargo.toml.
```

NEVER edit `edition = "..."` manually before running `cargo fix --edition`. The fix command rewrites code to be valid in both old and new editions, then the manual edit is just the field bump. Manual bump without `cargo fix` produces compile errors that look like language bugs but are migration debt.

### "What channel should I use?"

```
Is feature stable on current stable?
├── YES → ALWAYS stable.
└── NO  → Is the feature gated behind a `#![feature(...)]` attribute?
         ├── YES, and the crate is yours alone → nightly is acceptable, document the dependency.
         └── YES, and the crate is published → NEVER nightly. Wait for stabilization or design around.
```

NEVER publish to crates.io a crate that requires nightly. Consumers cannot use it on their stable toolchain without breaking their build.

### "How do I set MSRV?"

```
Are you a library author?
├── YES → Pick a target MSRV behind current stable (typically N-6 weeks to N-12 months).
│        Set rust-version = "1.<N>" in [package]. Test in CI with that toolchain.
│        Bumping MSRV is a minor-version bump under semver convention (NOT major).
└── NO (binary or internal app) → Set rust-version to the toolchain you actually use.
        ALWAYS pin via rust-toolchain.toml as well so contributors match.
```

ALWAYS set `rust-version` when publishing. Without it, dependency resolution may pick crate versions that require a newer Rust than your stated MSRV, and the failure surfaces at someone else's `cargo build`.

### "Do I need the MSRV-aware resolver?"

```
Cargo version >= 1.84 AND `rust-version` is set?
├── YES → Opt in by setting `resolver.incompatible-rust-versions = "fallback"`
│         in .cargo/config.toml. Cargo then prefers older crate versions that satisfy MSRV
│         over newer versions that require a higher Rust.
└── NO  → Default resolver picks newest semver-compatible crate, may break your MSRV silently.
```

The MSRV-aware resolver is OPT-IN in 1.84. NEVER assume it is on by default. For published libraries with a stated MSRV, ALWAYS enable it.

### "Which version stabilized feature X?"

Use the version matrix above. If the feature is not listed there, consult the release blog index : https://blog.rust-lang.org/ and search by version. ALWAYS cite the release date when recommending a feature : "available since 1.75 (Dec 2023)" not just "recent Rust".

## Patterns

### Stability commitment in practice

The stable channel guarantees that code compiling on Rust 1.N continues to compile on Rust 1.N+M for any M >= 0, within the same edition. The escape hatch is the edition mechanism : opting into a new edition is the only way to receive breaking changes, and that opt-in is per-crate via `edition = "2024"` in `Cargo.toml`. Editions never break dependency interop : a 2024-edition crate can depend on a 2015-edition crate and vice versa.

Soundness fixes are the documented exception. If a previously-compiling program was unsound, the compiler may begin to reject it in a future stable release. This is rare and announced.

### `rust-version` field semantics

```toml
[package]
name = "my-crate"
version = "0.1.0"
edition = "2024"
rust-version = "1.85"
```

`rust-version` declares the minimum Rust the crate is guaranteed to compile on. Three effects :

1. `cargo build` and `cargo publish` reject the crate if the active toolchain is below the declared MSRV.
2. With the MSRV-aware resolver enabled (1.84+), dependency selection respects this floor.
3. crates.io shows the MSRV badge on the crate page.

ALWAYS bump `rust-version` in lockstep with using a newer-Rust-only feature in your code. NEVER ship code that uses 1.86 features with `rust-version = "1.85"`.

### Channel switching

```bash
# Install and use stable (default)
rustup default stable

# Install and use a specific stable version
rustup toolchain install 1.85.0
rustup default 1.85.0

# Temporarily use nightly for one invocation
rustup run nightly cargo build

# Pin per-project via rust-toolchain.toml at repo root
# [toolchain]
# channel = "1.85.0"
# components = ["rustfmt", "clippy", "rust-src"]
```

`rust-toolchain.toml` overrides `rustup default` for any cargo invocation under the project tree. ALWAYS commit `rust-toolchain.toml` for reproducible builds across contributors.

### Edition migration workflow

```bash
# 1. Ensure the crate compiles cleanly on its current edition
cargo build --all-targets
cargo test

# 2. Run the automated edition migration
cargo fix --edition

# 3. Manually bump the edition in Cargo.toml
# edition = "2021"   ->   edition = "2024"

# 4. Verify
cargo build --all-targets
cargo test

# 5. Apply edition-aware idiom fixes (optional)
cargo fix --edition-idioms
```

`cargo fix --edition` only rewrites code to remain valid across the edition boundary. The manual bump is the second step. NEVER bump first and rely on the compiler to surface migration errors : the error messages are not designed as a migration guide.

### Identifying "what version added X?"

ALWAYS use the release blog as primary source : https://blog.rust-lang.org/ and the version-specific URL pattern `https://blog.rust-lang.org/<YEAR>/<MM>/<DD>/Rust-1.<N>.0/`. The "Stable features" section lists every API and language stabilization in that release. The `RELEASES.md` file in the rust-lang/rust repository is the authoritative changelog.

NEVER guess a stabilization version from memory. Always verify against the release blog before recommending a feature with a version annotation.

### Telling stable from nightly features

```rust
// Stable feature : compiles on stable Rust.
let x: u32 = 0;
let _y = x.isqrt(); // stable since 1.84

// Nightly-only feature : would require `#![feature(...)]`.
// #![feature(generic_const_exprs)]   // still nightly at 1.87
```

If a code example needs `#![feature(...)]` at the crate root, it is nightly-only. ALWAYS warn the user that the example does not compile on stable.

## Reference Links

- `references/methods.md` : full edition mechanics, MSRV resolver flags, channel commands, complete `cargo fix --edition` workflow, RELEASES.md pointer.
- `references/examples.md` : worked `Cargo.toml` examples per edition, `rust-toolchain.toml` examples, MSRV-aware resolver config, edition migration log.
- `references/anti-patterns.md` : real failure modes (manual edition bump without `cargo fix`, mixing stable and nightly in one project, MSRV without `rust-version`, version-claim without source).

### Cross-skill references

- `[[rust-syntax-edition-2024]]` : the 13 language changes in edition 2024 with code examples.
- `[[rust-core-toolchain]]` : rustup, cargo, rustc, clippy, rustfmt mechanics.
- `[[rust-impl-cargo-project]]` : `Cargo.toml` complete reference including `[lints]` and workspace inheritance.

### Source URLs (verified 2026-05-19)

- Edition Guide : https://doc.rust-lang.org/edition-guide/
- Rust 1.65.0 release (GATs) : https://blog.rust-lang.org/2022/11/03/Rust-1.65.0/
- Rust 1.75.0 release (AFIT/RPITIT) : https://blog.rust-lang.org/2023/12/28/Rust-1.75.0/
- Rust 1.80.0 release (LazyLock) : https://blog.rust-lang.org/2024/07/25/Rust-1.80.0/
- Rust 1.81.0 release : https://blog.rust-lang.org/2024/09/05/Rust-1.81.0/
- Rust 1.82.0 release (precise capturing) : https://blog.rust-lang.org/2024/10/17/Rust-1.82.0/
- Rust 1.83.0 release : https://blog.rust-lang.org/2024/11/28/Rust-1.83.0/
- Rust 1.84.0 release (MSRV resolver) : https://blog.rust-lang.org/2025/01/09/Rust-1.84.0/
- Rust 1.85.0 release (edition 2024 stable) : https://blog.rust-lang.org/2025/02/20/Rust-1.85.0/
- Rust 1.86.0 release (trait upcasting) : https://blog.rust-lang.org/2025/04/03/Rust-1.86.0/
- Rust 1.87.0 release (asm! jumps) : https://blog.rust-lang.org/2025/05/15/Rust-1.87.0/
- Cargo manifest (`rust-version` field) : https://doc.rust-lang.org/cargo/reference/manifest.html
- Edition 2024 RPIT lifetime capture : https://doc.rust-lang.org/edition-guide/rust-2024/rpit-lifetime-capture.html
- Edition 2024 never-type fallback : https://doc.rust-lang.org/edition-guide/rust-2024/never-type-fallback.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
