---
name: maverick-rust-errors
description: Rust error handling with Result, Error trait, anyhow, thiserror Use when this capability is needed.
metadata:
  author: get2knowio
---

# Rust Errors Skill

## Result Type
```rust
fn parse_number(s: &str) -> Result<i32, ParseIntError> {
    s.parse()
}

// Use ? operator for propagation
fn process() -> Result<(), Error> {
    let num = parse_number("42")?;
    Ok(())
}
```

## Custom Errors with thiserror
```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum MyError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),

    #[error("Parse error: {0}")]
    Parse(String),
}
```

## Avoid unwrap/expect in Production
```rust
// BAD
let value = some_option.unwrap();

// GOOD
let value = some_option.ok_or(MyError::MissingValue)?;

// OR provide default
let value = some_option.unwrap_or_default();
```

## Review Severity
- **CRITICAL**: unwrap/expect without justification
- **MAJOR**: Missing error handling
- **MINOR**: Could use ? operator

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get2knowio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
