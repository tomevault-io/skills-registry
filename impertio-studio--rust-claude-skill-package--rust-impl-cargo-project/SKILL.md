---
name: rust-impl-cargo-project
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-cargo-project

The **single-crate Cargo project setup** skill: how to scaffold a crate, structure its `Cargo.toml`, declare dependencies safely, design features that obey the additive principle, tune profiles, override third-party code with `[patch]` / `[replace]`, configure package-level lints via the `[lints]` table, and opt into the MSRV-aware resolver introduced in Rust 1.84.

Cross-references: [[rust-impl-workspaces]] (multi-crate layout with `[workspace]`), [[rust-core-toolchain]] (rustup channels, components, `rust-toolchain.toml`), [[rust-core-language-versions]] (which features stabilized in which release), [[rust-impl-build-scripts]] (`build.rs`, `OUT_DIR`, `cargo::` directives).

---

## When to use this skill

- User runs `cargo new` or `cargo init` and asks lib vs bin, what edition, where files go.
- User edits `Cargo.toml` and asks about `[package]`, `[dependencies]`, `[dev-dependencies]`, `[build-dependencies]`, or target-specific dependencies.
- User asks about version requirements: caret vs tilde vs exact vs wildcard.
- User designs a feature flag, hits a non-additive design, or asks about `dep:` / `?` syntax.
- User tunes `[profile.release]`, defines a custom profile, or asks about LTO, `opt-level`, `codegen-units`, `strip`, `panic`.
- User wants to override a transitive dependency via `[patch.crates-io]` or `[replace]`.
- User wants project-wide lint configuration via the `[lints]` table.
- User wants `cargo add` to pick versions compatible with their MSRV (Rust 1.84+ resolver).

For multi-crate workspaces (`[workspace]`, shared dependencies, `default-members`) see [[rust-impl-workspaces]]. For `build.rs` and the `cargo::` directives see [[rust-impl-build-scripts]].

---

## Decision tree: `cargo new` vs `cargo init`

```
Need a fresh directory?
   YES -> cargo new <name>            (creates ./<name>/)
   NO  -> cargo init [<path>]         (initialises current dir or path)

Binary or library?
   Binary (executable, main.rs)         -> default, or `--bin` explicitly
   Library (lib.rs, no main)            -> `--lib`
   Both (mixed lib + bin in one crate)  -> create lib, then add src/main.rs

Need a different VCS than git?
   git (default) | hg | pijul | fossil | none   -> `--vcs <name>`
```

ALWAYS pass `--lib` when the crate is library-first. NEVER hand-edit `Cargo.toml` to switch crate type after `cargo new`; rerun with the right flag in a clean directory or add a `[lib]` / `[[bin]]` target explicitly.

`cargo new` in Rust 1.85+ writes `edition = "2024"` and uses resolver 3 by default. ALWAYS check the generated edition string when scaffolding inside an existing repo that pinned an older toolchain.

---

## Cargo.toml top-level structure

```toml
[package]                    # crate identity + metadata
[lib]            / [[bin]]   # explicit target overrides (rare)
[features]                   # feature flags
[dependencies]               # runtime deps
[dev-dependencies]           # tests / examples / benches only
[build-dependencies]         # deps used by build.rs
[target.<triple>.dependencies]   # platform-specific deps
[profile.<name>]             # compile profile tuning
[patch]                      # override registry sources
[replace]                    # legacy override (prefer [patch])
[lints]                      # package-level lint config
[workspace]                  # workspace declaration (see rust-impl-workspaces)
```

| Section | Visibility downstream | Triggers rebuild of |
|---------|-----------------------|---------------------|
| `[dependencies]` | yes, exported through the crate | the crate + all consumers |
| `[dev-dependencies]` | NO | tests / examples / benches only |
| `[build-dependencies]` | NO | `build.rs` |
| `[target.cfg(unix).dependencies]` | yes, but only when `cfg` matches | the crate on matching targets |

ALWAYS keep `proptest`, `criterion`, `tokio-test`, `tempfile` in `[dev-dependencies]`. NEVER put them in `[dependencies]`: they bloat downstream builds and pollute the public dependency graph.

ALWAYS keep `bindgen`, `cc`, `pkg-config`, `cmake` in `[build-dependencies]`. NEVER duplicate them in `[dependencies]` "just in case": that doubles compile time.

---

## `[package]` metadata: required + strongly recommended fields

```toml
[package]
name        = "my-crate"             # required, kebab-case, [a-z0-9-_]+
version     = "0.1.0"                # SemVer
edition     = "2024"                 # 2015 / 2018 / 2021 / 2024
rust-version = "1.85"                # MSRV; enables MSRV-aware resolver
license     = "MIT OR Apache-2.0"    # SPDX 2.3 expression; required for crates.io
description = "What this crate does, one paragraph."   # required for crates.io
repository  = "https://github.com/org/repo"
keywords    = ["cli", "parser"]      # max 5, each <=20 chars (crates.io rules)
categories  = ["command-line-utilities"]
readme      = "README.md"            # auto-detected; set false to suppress
```

[Source: Cargo manifest reference][cargo-manifest]

ALWAYS declare `rust-version` once the crate is published or shared. Without it the MSRV-aware resolver cannot help downstream users.

NEVER omit `description` and `license` on a crate destined for crates.io: publication will fail.

ALWAYS prefer the SPDX expression syntax (`"MIT OR Apache-2.0"`) over `license-file` unless the license has no SPDX identifier.

---

## Version requirements (semver)

| Syntax | Meaning | Compatible range | Use when |
|--------|---------|------------------|----------|
| `"1.2.3"` | caret, implicit | `>=1.2.3, <2.0.0` | default; allows minor + patch updates |
| `"^1.2.3"` | caret, explicit | `>=1.2.3, <2.0.0` | same as above, spelled out |
| `"~1.2.3"` | tilde | `>=1.2.3, <1.3.0` | pin minor, allow patch only |
| `"=1.2.3"` | exact | `=1.2.3` only | reproducing a bug; library code should AVOID |
| `"1.2"` | caret on shorter version | `>=1.2.0, <2.0.0` | allow any patch + future minors |
| `"1"` | caret on major | `>=1.0.0, <2.0.0` | broadest stable range |
| `"*"` | wildcard | any version | NEVER in published crates |
| `">=1.2, <1.5"` | multiple | `>=1.2.0, <1.5.0` | excluding a known-bad range |

ALWAYS use bare caret (`"1.2.3"`) in library crates so consumers can dedupe transitive versions.

NEVER use `"=x.y.z"` in a published library: it forces every downstream graph to that exact version and breaks dedup.

NEVER use `"*"` outside throwaway scripts: it implies "any future major version is fine", which is never true.

---

## Features: the additive principle

> "A consequence of this is that features should be _additive_. That is, enabling a feature should not disable functionality, and it should usually be safe to enable any combination of features."
> [Source: Cargo features reference][cargo-features]

This single rule drives every other feature-design decision.

```toml
[features]
default = ["std"]            # auto-enabled unless --no-default-features
std     = []                 # enables std support
serde   = ["dep:serde"]      # private dep, no implicit feature
async   = ["dep:tokio", "serde?/derive"]   # weak: only if serde is ALSO enabled
full    = ["std", "serde", "async"]        # aggregator feature
```

| Syntax | Effect | Stabilised |
|--------|--------|------------|
| `feature = []` | a bare feature flag, gates `#[cfg(feature = "...")]` | always |
| `feature = ["other"]` | enabling `feature` also enables `other` | always |
| `optional = true` on a dep | implicitly creates a feature of the same name | always |
| `feature = ["dep:foo"]` | enables optional dep `foo` WITHOUT creating an implicit feature | 1.60 |
| `feature = ["foo?/bar"]` | enables `bar` feature of `foo` ONLY if `foo` is enabled elsewhere | 1.60 |

ALWAYS phrase a flag as "enables X". NEVER phrase it as "disables X" (e.g. an `no_std` feature is the wrong direction; use an opt-in `std` feature instead).

NEVER design two features that must not be enabled together. Feature unification means any consumer in the dep graph can flip either one on, and Cargo will refuse to fail loudly.

ALWAYS use `dep:foo` when an optional dependency name should NOT become a public feature flag. Otherwise users can `--features foo` directly, locking you into that name forever.

---

## Profiles: compile-time knobs

Built-in profiles and their inheritance:

```
dev  --(inherited by)--> test
release --(inherited by)--> bench
```

[Source: Cargo profiles reference][cargo-profiles]

| Setting | `dev` default | `release` default | Override scope |
|---------|---------------|-------------------|----------------|
| `opt-level` | `0` | `3` | per package |
| `debug` | `true` | `false` | per package |
| `debug-assertions` | `true` | `false` | per package |
| `overflow-checks` | `true` | `false` | per package |
| `lto` | `false` | `false` | profile-wide only |
| `panic` | `"unwind"` | `"unwind"` | profile-wide only |
| `incremental` | `true` | `false` | profile-wide only |
| `codegen-units` | `256` | `16` | per package |
| `strip` | `"none"` | `"none"` | per package |

LTO modes: `false` (thin-local LTO on the current crate), `"thin"`, `true` / `"fat"` (cross-crate full LTO), `"off"` (disabled).

Panic strategies: `"unwind"` (default, supports `catch_unwind`), `"abort"` (smaller binaries, no unwinding).

```toml
# Faster release builds, smaller binaries
[profile.release]
lto           = "thin"
codegen-units = 1
strip         = "symbols"

# Custom profile inherits another
[profile.release-debug]
inherits = "release"
debug    = true

# Speed up dependencies in dev builds
[profile.dev.package."*"]
opt-level = 2
```

ALWAYS set `panic = "abort"` only AFTER verifying no dependency relies on `std::panic::catch_unwind`. NEVER set it just to shrink binaries by reflex.

NEVER override `panic`, `lto`, or `rpath` in per-package overrides: only profile-wide settings accept those. Cargo errors out otherwise.

---

## `[patch]` and `[replace]`: overriding sources

```toml
# Replace a registry dep with a local fork while debugging
[patch.crates-io]
serde = { git = "https://github.com/me/serde", branch = "fix-1234" }

# Or a path override
[patch.crates-io]
serde = { path = "../serde" }
```

ALWAYS prefer `[patch]` over the legacy `[replace]`. `[patch]` is workspace-aware and respects semver; `[replace]` is deprecated.

ALWAYS keep `[patch]` entries on a feature branch, not on `main`. NEVER ship a published crate that patches a registry dep: downstream cannot inherit `[patch]` from a dependency.

---

## `[lints]` table: package-level lint configuration

```toml
[lints.rust]
unsafe_code        = "forbid"
unused_imports     = "deny"
missing_docs       = { level = "warn", priority = -1 }

[lints.clippy]
enum_glob_use      = "deny"
pedantic           = { level = "warn", priority = -1 }
```

Levels: `forbid`, `deny`, `warn`, `allow`. Tool name comes from the part before `::` in the lint name (`unsafe_code` -> `lints.rust`; `clippy::enum_glob_use` -> `lints.clippy`).

> "These will only affect local development of the current package. Cargo only applies these to the current package and not to dependencies."
> [Source: Cargo manifest reference][cargo-manifest]

`priority` lets a group (e.g. `clippy::pedantic` at `priority = -1`) be overridden by individual lints (default `priority = 0`).

ALWAYS use `[lints]` in `Cargo.toml` for new projects instead of `#![deny(...)]` at the crate root: the manifest form is grep-friendly, machine-editable, and inheritable across a workspace via `lints.workspace = true` (see [[rust-impl-workspaces]]).

NEVER set `unsafe_code = "forbid"` in a crate that contains `unsafe` blocks: `forbid` cannot be overridden by inner attributes, the build will fail.

---

## MSRV-aware resolver (Rust 1.84+)

Three activation paths:

1. **`Cargo.toml`**: `[package] resolver = "3"` (requires the host toolchain to be >= 1.84).
2. **`.cargo/config.toml`**: `[resolver] incompatible-rust-versions = "fallback"`.
3. **Edition 2024 default**: a fresh `cargo new` on Rust 1.85+ scaffolds with resolver 3 already enabled.

[Source: Rust 1.84.0 release notes][rust184]

Effect: when `rust-version` is set, `cargo add` and `cargo update` prefer dependency versions that still build on that MSRV instead of the absolute latest.

```toml
[package]
name         = "demo"
version      = "0.1.0"
edition      = "2024"
rust-version = "1.85"        # MSRV
resolver     = "3"           # opt into MSRV-aware behaviour
```

Override for CI runs that should test against the latest dep versions:

```bash
CARGO_RESOLVER_INCOMPATIBLE_RUST_VERSIONS=allow cargo update
```

ALWAYS declare `rust-version` before opting into resolver 3, otherwise the resolver has no MSRV to honour.

NEVER raise `rust-version` casually: every bump is a breaking change for downstream pinned at the old MSRV.

---

## Quick recipes

### Add a runtime dep with explicit features (no defaults)

```bash
cargo add tokio --no-default-features --features rt,macros,sync
```

### Add a dev-only dep

```bash
cargo add --dev proptest
```

### Add an optional dep gated behind a feature

```bash
cargo add serde --optional --features derive
# Then in Cargo.toml [features]:
# serde = ["dep:serde"]
```

### Patch a transitive dep on a branch

```toml
[patch.crates-io]
libc = { git = "https://github.com/rust-lang/libc", branch = "main" }
```

### Test the crate under all feature permutations

```bash
cargo install cargo-hack
cargo hack check --feature-powerset --no-dev-deps
```

---

## Validation checklist

Before committing a `Cargo.toml` change, verify:

1. `cargo metadata --format-version 1 > /dev/null` succeeds (manifest parses).
2. `cargo build` and `cargo build --release` both succeed.
3. `cargo build --no-default-features` succeeds when the crate claims to support that.
4. `cargo build --all-features` succeeds (features are truly additive).
5. `rust-version` is set when the crate is shared or published.
6. Dev / build / runtime deps are in the correct section.
7. `[lints]` block has no `forbid` on a lint the code violates.

---

## Reference links

- [[references/methods.md]] : full `Cargo.toml` key reference (every `[package]` field, every dependency-table key, every profile setting with type).
- [[references/examples.md]] : complete `Cargo.toml` recipes (binary crate, library crate, FFI crate with `build.rs`, no_std crate, MSRV-pinned crate, custom profile, `[patch]` override).
- [[references/anti-patterns.md]] : non-additive features, dev-deps in `[dependencies]`, `panic = "abort"` without verification, exact-version pinning in libraries, missing `rust-version`, copying release profile blindly.

[cargo-manifest]: https://doc.rust-lang.org/cargo/reference/manifest.html
[cargo-features]: https://doc.rust-lang.org/cargo/reference/features.html
[cargo-profiles]: https://doc.rust-lang.org/cargo/reference/profiles.html
[rust184]: https://blog.rust-lang.org/2025/01/09/Rust-1.84.0/

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
