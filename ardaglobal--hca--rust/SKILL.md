---
name: rust
description: Compile, test, and lint Rust projects Use when this capability is needed.
metadata:
  author: ardaglobal
---

# Rust Development Skill

You are working in a Rust development environment with full toolchain access.

## Available Tools

- `cargo build` - Compile the project
- `cargo build --release` - Compile with optimizations
- `cargo test` - Run tests
- `cargo nextest run` - Run tests with better output (preferred)
- `cargo clippy` - Run linter
- `cargo fmt` - Format code
- `cargo check` - Fast type checking without building
- `cargo doc` - Generate documentation
- `cargo watch -x test` - Watch mode for tests

## Project Detection

Look for these files to identify Rust projects:
- `Cargo.toml` - Project manifest
- `Cargo.lock` - Dependency lock file
- `src/main.rs` - Binary entry point
- `src/lib.rs` - Library entry point

## Common Workflows

### Building
```bash
cargo build 2>&1
```

### Running Tests
```bash
cargo nextest run 2>&1
# or if nextest not available
cargo test 2>&1
```

### Linting
```bash
cargo clippy -- -D warnings 2>&1
```

### Formatting Check
```bash
cargo fmt --check 2>&1
```

## Error Handling

- Compilation errors show file:line:column format
- Use `cargo check` for faster feedback than full build
- Clippy warnings should be treated as errors with `-D warnings`

## Best Practices

1. Always run `cargo fmt --check` before committing
2. Run `cargo clippy` to catch common mistakes
3. Use `cargo nextest` for better test output
4. Check `Cargo.lock` changes for dependency updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ardaglobal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
