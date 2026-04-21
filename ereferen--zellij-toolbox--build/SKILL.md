---
name: build
description: Build the toolbox project (CLI or WASM plugin) Use when this capability is needed.
metadata:
  author: ereferen
---

# /build - Build the project

Build the toolbox project.

## Usage

- `/build` - Build all crates in debug mode
- `/build release` - Build all crates in release mode
- `/build cli` - Build only the CLI
- `/build wasm` - Build the Zellij plugin (WASM)

## Instructions

When the user runs this skill:

1. Parse the argument to determine what to build
2. Run the appropriate cargo command:
   - No args or "all": `cargo build`
   - "release": `cargo build --release`
   - "cli": `cargo build -p toolbox-cli`
   - "wasm": `cargo build -p toolbox-zellij --target wasm32-wasip1 --release`
3. Report any errors clearly
4. On success, show the built artifacts location

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ereferen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
