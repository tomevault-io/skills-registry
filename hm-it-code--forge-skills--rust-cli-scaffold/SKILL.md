---
name: rust-cli-scaffold
description: Bootstraps a clean, idiomatic Rust command-line application. Use when the user says "new Rust CLI", "scaffold a Rust binary", "start a Rust command-line tool", or wants a Rust project set up with argument parsing, error handling, and tests. Produces a buildable project following current Rust conventions. Use when this capability is needed.
metadata:
  author: HM-IT-CODE
---

# Rust CLI Scaffold

Create a Rust CLI the way an experienced Rust engineer would start one: argument parsing with `clap`, ergonomic error handling, a testable core separated from `main`, and CI ready to go.

## Step 1 — Confirm the basics

Ask (or infer) the binary name, a one-line description, and the first subcommand or core operation. Keep the scaffold focused on one real command rather than empty boilerplate.

## Step 2 — Create the project

```bash
cargo new <name> --bin
cd <name>
cargo add clap --features derive
cargo add anyhow
```

Add `thiserror` instead of / alongside `anyhow` if the tool is library-like and needs typed errors. Use `anyhow` for the binary's top level.

## Step 3 — Lay out testable structure

Separate the **CLI shell** from the **logic** so the logic can be unit-tested without spawning a process:

```
src/
├── main.rs        # parse args, call run(), map errors to exit codes
├── cli.rs         # clap Parser/Subcommand definitions
└── lib logic...   # pure functions that do the actual work
```

`main.rs` stays thin:

```rust
use anyhow::Result;
use clap::Parser;

mod cli;

fn main() -> Result<()> {
    let args = cli::Cli::parse();
    cli::run(args)
}
```

`cli.rs` holds the `#[derive(Parser)]` struct, the `Subcommand` enum, and a `run()` that dispatches to pure functions. Those pure functions are what your tests target.

## Step 4 — Apply conventions

- Use `#[derive(Parser)]` with `#[command(version, about)]` so `--help` / `--version` work for free.
- Return `Result<T>` and use `?`; never `unwrap()`/`expect()` on recoverable errors in shipped paths.
- Add `#[derive(Debug)]` to public types.
- Read config from flags first, env second, defaults last.
- Map errors to meaningful process exit codes in `main` if the tool is meant to be scripted.

## Step 5 — Tests and CI

- Add `#[cfg(test)]` unit tests for the pure logic functions (the easy wins).
- Add one integration test under `tests/` using `assert_cmd` + `predicates` to exercise the built binary end-to-end. Run `cargo add --dev assert_cmd predicates`.
- Generate a `cargo fmt --check` + `cargo clippy -D warnings` + `cargo test` GitHub Actions workflow (hand off to the `ci-pipeline` skill if available).

## Step 6 — Verify

Run `cargo build` and `cargo test` and confirm both pass. Show the user the tree, the `--help` output shape, and the next command to implement. Hand off to `tdd-loop` to build the real behavior test-first.

---
> Source: [HM-IT-CODE/forge-skills](https://github.com/HM-IT-CODE/forge-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
