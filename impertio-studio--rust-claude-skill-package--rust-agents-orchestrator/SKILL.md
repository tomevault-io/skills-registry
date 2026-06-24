---
name: rust-agents-orchestrator
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# Rust Agents Orchestrator

This is the routing brain of the Rust skill package. It does two jobs:

1. **Route**: at the START of a Rust task, map the user prompt to the exact `rust-*` skill (or skills) to load.
2. **Gate**: at the END of a Rust task, run the cross-skill quality checklist before declaring the work done.

ALWAYS run step 1 before writing Rust code. ALWAYS run step 2 before reporting a Rust task complete.

This skill routes and checklists. It does NOT do deep lint review: that is `rust-agents-code-reviewer`. It does NOT iterate on compiler errors: that is `rust-agents-compile-fix`. Treating these three as interchangeable is an anti-pattern.

## The Package: 44 Skills, 5 Categories

| Category | Count | Prefix | Purpose |
|----------|-------|--------|---------|
| core | 6 | `rust-core-` | Cross-cutting concepts, architecture, mental models |
| syntax | 15 | `rust-syntax-` | Language mechanics, API patterns, code form |
| impl | 13 | `rust-impl-` | Development workflows, end-to-end tasks |
| errors | 7 | `rust-errors-` | Diagnosing and fixing compiler/runtime errors |
| agents | 3 | `rust-agents-` | Routing, review, compile-fix orchestration |

## Routing Table: Prompt Pattern to Skill

ALWAYS pick the most specific skill. If a prompt contains a rustc error code (E0xxx) or the word "error"/"fails to compile", route to an `errors-*` skill, NOT a `syntax-*` skill.

### Ownership, borrowing, lifetimes

| User prompt signal | Skill |
|--------------------|-------|
| "move", "value used after move", "who owns this" | [[rust-syntax-ownership]] |
| E0382, "borrow of moved value" | [[rust-errors-borrow-checker]] |
| "`&` vs `&mut`", "borrow", "two-phase borrow", "RefCell" | [[rust-syntax-borrowing]] |
| E0502 / E0596 / E0500, "cannot borrow as mutable" | [[rust-errors-borrow-checker]] |
| "lifetime annotation", "`'a`", "HRTB", "`'static`" | [[rust-syntax-lifetimes]] |
| E0106 / E0623 / E0495 / E0700, "missing lifetime specifier" | [[rust-errors-lifetimes]] |

### Traits, generics, type system

| User prompt signal | Skill |
|--------------------|-------|
| "trait", "default method", "supertrait", "orphan rule", "blanket impl" | [[rust-syntax-traits]] |
| "generic", "type parameter", "`where` clause", "monomorphization", "const generics" | [[rust-syntax-generics]] |
| "`dyn Trait`", "trait object", "object safety", "vtable", "trait upcasting" | [[rust-syntax-trait-objects]] |
| "GAT", "generic associated type", "LendingIterator" | [[rust-syntax-gats]] |
| E0277 / E0599 / E0220, "trait bound not satisfied", "method not found" | [[rust-errors-trait-bounds]] |

### Control flow, iterators, pointers

| User prompt signal | Skill |
|--------------------|-------|
| "`match`", "`if let`", "`let else`", "destructure", "exhaustiveness", "or-pattern" | [[rust-syntax-pattern-matching]] |
| "iterator", "`map`/`filter`/`fold`", "closure", "`Fn`/`FnMut`/`FnOnce`", "`collect`" | [[rust-syntax-iterators-closures]] |
| "`Box`", "`Rc`", "`Arc`", "`Cell`", "`OnceLock`", "`Weak`", "smart pointer" | [[rust-syntax-smart-pointers]] |

### Async

| User prompt signal | Skill |
|--------------------|-------|
| "`async fn`", "`.await`", "async block", "async closure", "AFIT", "RPITIT" | [[rust-syntax-async-await]] |
| "Future trait", "executor", "Poll", "runtime choice", "structured concurrency" (concept) | [[rust-core-async-runtime]] |
| "tokio", "`tokio::spawn`", "`spawn_blocking`", "`select!`", "`JoinSet`", "timeout" | [[rust-impl-async-tokio]] |
| "`!Send` future", "Send across `.await`", async Pin/Unpin runtime error | [[rust-errors-async]] |

### Macros, unsafe, FFI

| User prompt signal | Skill |
|--------------------|-------|
| "`macro_rules!`", "declarative macro", "fragment specifier", "macro hygiene" | [[rust-syntax-macros-declarative]] |
| "proc macro", "derive macro", "`syn`", "`quote`", "`proc_macro2`" | [[rust-syntax-macros-procedural]] |
| "`unsafe`", "raw pointer", "UB", "strict provenance", "`unsafe extern`", "miri" | [[rust-syntax-unsafe]] |
| "FFI", "`extern \"C\"`", "bindgen", "cbindgen", "`#[repr(C)]`", "C interop" | [[rust-impl-ffi-bindgen]] |

### Edition, toolchain, core concepts

| User prompt signal | Skill |
|--------------------|-------|
| "edition 2024", "migrate to 2024", "edition migration", "edition idioms", "`gen` block" | [[rust-syntax-edition-2024]] |
| "rustup", "channels", "components", "clippy setup", "rustfmt config", "MSRV management" | [[rust-core-toolchain]] |
| "which Rust version", "edition system", "stability", "`rust-version`", "release matrix" | [[rust-core-language-versions]] |
| "type system", "ZST", "`repr`", "niche optimization", "never type", "type-state" | [[rust-core-type-system]] |
| "memory model", "stack vs heap", "drop scope", "RAII", "Send/Sync overview" | [[rust-core-memory-model]] |
| "stdlib", "which std module", "prelude", "`std::collections`", "`std::sync`" | [[rust-core-stdlib-overview]] |

### Cargo and project workflows

| User prompt signal | Skill |
|--------------------|-------|
| "`cargo new`", "Cargo.toml", "features", "profiles", "`[lints]` table" | [[rust-impl-cargo-project]] |
| "workspace", "member crate", "virtual manifest", "workspace inheritance" | [[rust-impl-workspaces]] |
| "build.rs", "build script", "`OUT_DIR`", "code generation", "`rerun-if-changed`" | [[rust-impl-build-scripts]] |
| "cross-compile", "target triple", "`rustup target add`", "`cross` crate", "`cfg`" | [[rust-impl-cross-compile]] |

### Error handling design and error skills

| User prompt signal | Skill |
|--------------------|-------|
| "`Result`/`Option` idioms", "`?` operator", "custom error type", "error design" | [[rust-impl-error-handling]] |
| "`thiserror`", "`anyhow`", "library vs application errors", "error context" | [[rust-errors-thiserror-anyhow]] |
| "panic", "`RUST_BACKTRACE`", "abort vs unwind", "index out of bounds at runtime" | [[rust-errors-runtime]] |
| "linker error", "missing native lib", "version conflict", "`cargo update`", MSRV mismatch | [[rust-errors-build-link]] |

### Concurrency, common crates, testing

| User prompt signal | Skill |
|--------------------|-------|
| "`Arc<Mutex>`", "`RwLock`", "atomics", "`Ordering`", "deadlock", "scoped thread" | [[rust-impl-concurrency]] |
| "channel", "`mpsc`", "`oneshot`", "`broadcast`", "`watch`", "backpressure" | [[rust-impl-channels]] |
| "serde", "`Serialize`/`Deserialize`", "JSON/TOML/YAML", "`#[serde(...)]`" | [[rust-impl-serde]] |
| "clap", "CLI args", "`derive(Parser)`", "subcommand", "shell completion" | [[rust-impl-clap-cli]] |
| "`#[test]`", "integration test", "doc test", "`nextest`", "`criterion`", "bench" | [[rust-impl-testing]] |
| "`#![no_std]`", "embedded", "`#[panic_handler]`", "`#[global_allocator]`" | [[rust-impl-no-std]] |

### Agent / meta skills

| User prompt signal | Skill |
|--------------------|-------|
| "review my Rust code", "is this idiomatic", deep clippy lint review | [[rust-agents-code-reviewer]] |
| "fix this compile error", iterate on rustc output, follow `--explain` suggestions | [[rust-agents-compile-fix]] |
| "which skill", "route this task", "run the quality checklist" | this skill |

## Decision Tree: Ambiguous Prompt to Skill

```
Rust task arrives
├── Contains an E0xxx code or "does not compile"? ......... ROUTE to errors-*
│   ├── E0382 / E0502 / E0596 / E0500 ............. rust-errors-borrow-checker
│   ├── E0106 / E0623 / E0495 / E0700 ............. rust-errors-lifetimes
│   ├── E0277 / E0599 / E0220 ..................... rust-errors-trait-bounds
│   ├── linker / native lib / version clash ....... rust-errors-build-link
│   ├── async `!Send` / Pin across .await ......... rust-errors-async
│   └── panic / OOB / abort at runtime ............ rust-errors-runtime
├── "How do I build / set up X"? ...................... ROUTE to impl-*
├── "Why does Rust work like X" (concept)? ............ ROUTE to core-*
├── "What is the syntax for X" (language form)? ....... ROUTE to syntax-*
├── "Review / is this idiomatic"? ..................... rust-agents-code-reviewer
└── Multiple skills plausible? ........................ load the NARROWEST
    that names the exact construct, then load its core-* concept skill
    only if the user needs the mental model, not just the syntax.
```

ALWAYS prefer the narrowest match. NEVER load a `core-*` overview when the user asked a concrete `syntax-*` or `impl-*` question. Load 1 to 3 skills, not the whole package.

## Cross-Skill Quality Checklist

Run this BEFORE declaring ANY Rust task complete. Every item is a hard gate.

| # | Check | Command / inspection | Pass criterion |
|---|-------|----------------------|----------------|
| a | Edition + MSRV | read `edition` and `rust-version` in Cargo.toml | code uses only features available at the stated MSRV; edition is 2024 unless the project pins older |
| b | Clippy | `cargo clippy --all-targets -- -D warnings` | no warnings; correctness + suspicious + perf clean |
| c | Formatting | `cargo fmt --all --check` | exits 0, no diff |
| d | No panics on fallible paths | grep `.unwrap()` / `.expect()` in non-test code | every remaining one is on a genuinely infallible path or has a justifying comment |
| e | Async hygiene | inspect `.await` points | no blocking call on the async executor; no `!Send` value held across `.await` in a spawned task |
| f | Doc coverage | `cargo doc` + inspect public items | every public item has a `///` doc; add a doctest where it clarifies usage |
| g | Unsafe is justified | grep `unsafe` blocks | every `unsafe` block has a `// SAFETY:` comment stating the invariant |
| h | Error type contract | inspect library error types | error types implement `std::error::Error` + `Display`; a library does NOT leak `anyhow::Error` in its public API |
| i | Tests | `cargo test --all-targets` and `cargo test --doc` | all unit, integration, and doc tests pass |

If any item fails, fix it and re-run the failing check. NEVER report a Rust task done with an unchecked item.

See `references/methods.md` for the full command set and CI wiring, `references/examples.md` for worked routing and checklist runs, `references/anti-patterns.md` for routing and gating mistakes.

## Quick Reference

- Routing happens FIRST, the checklist happens LAST. Never skip either.
- Error code present means an `errors-*` skill, never a `syntax-*` skill.
- `cargo build` passing is NOT the quality gate. `cargo clippy` plus `cargo fmt --check` plus `cargo test` is.
- MSRV is set by `rust-version` in Cargo.toml. A new dependency must not raise it silently.
- This skill routes and gates. Deep review is `rust-agents-code-reviewer`. Error iteration is `rust-agents-compile-fix`.

## Reference Links

- Rust API Guidelines: https://rust-lang.github.io/api-guidelines/
- Clippy lint reference: https://rust-lang.github.io/rust-clippy/master/
- `references/methods.md`: command set, clippy lint groups, CI configuration
- `references/examples.md`: worked routing and checklist examples
- `references/anti-patterns.md`: routing and quality-gate anti-patterns

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
