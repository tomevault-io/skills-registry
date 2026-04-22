---
name: rust-tests
description: Rust test organization and patterns. Use when writing tests, reviewing test code, or organizing test files in Rust crates. Use when this capability is needed.
metadata:
  author: legacy3
---

# Rust Tests

Test organization following official Rust best practices (The Rust Book, Chapter 11).

---

## Two Types of Tests

From the Rust Book:

> The Rust community thinks about tests in terms of two main categories: unit tests and integration tests. **Unit tests** are small and more focused, testing one module in isolation at a time, and can test private interfaces. **Integration tests** are entirely external to your library and use your code in the same way any other external code would, using only the public interface and potentially exercising multiple modules per test.

> Writing both kinds of tests is important to ensure that the pieces of your library are doing what you expect them to, separately and together.

| Type                  | Purpose                                    | Location           | Can Test Private? |
| --------------------- | ------------------------------------------ | ------------------ | ----------------- |
| **Unit tests**        | Test one module in isolation               | Inline in `src/`   | Yes               |
| **Integration tests** | Test public API, multiple modules together | `tests/` directory | No                |

---

## Unit Tests

### Location

From the Rust Book:

> You'll put unit tests in the **src** directory in each file with the code that they're testing. **The convention is to create a module named `tests` in each file** to contain the test functions and to annotate the module with `cfg(test)`.

### The `#[cfg(test)]` Annotation

From the Rust Book:

> The `#[cfg(test)]` annotation on the `tests` module tells Rust to compile and run the test code only when you run `cargo test`, not when you run `cargo build`. This saves compile time when you only want to build the library and saves space in the resultant compiled artifact because the tests are not included.

> Because unit tests go in the same files as the code, you'll use `#[cfg(test)]` to specify that they shouldn't be included in the compiled result.

### Testing Private Functions

From the Rust Book:

> There's debate within the testing community about whether or not private functions should be tested directly, and other languages make it difficult or impossible to test private functions. Regardless of which testing ideology you adhere to, **Rust's privacy rules do allow you to test private functions**.

> Tests are just Rust code, and the `tests` module is just another module. [...] items in child modules can use the items in their ancestor modules. In this test, we bring all of the items belonging to the `tests` module's parent into scope with `use super::*`, and then the test can call `internal_adder`.

### Example

```rust
// src/core/event_queue.rs

pub struct EventQueue { /* ... */ }

impl EventQueue {
    pub fn push(&mut self, event: Event) { /* ... */ }

    // Private helper - unit tests CAN test this
    fn rebalance(&mut self) { /* ... */ }
}

#[cfg(test)]
mod tests {
    use super::*;  // Brings all items from parent module into scope

    #[test]
    fn test_push_maintains_order() {
        let mut queue = EventQueue::new();
        queue.push(Event::new(10.0));
        queue.push(Event::new(5.0));
        assert_eq!(queue.peek().unwrap().time, 5.0);
    }

    #[test]
    fn test_rebalance_after_removal() {
        // Can test private function directly - this is allowed
        let mut queue = EventQueue::new();
        queue.rebalance();
        // ...
    }
}
```

### When to Use Unit Tests

- Testing individual functions or methods
- Testing private implementation details
- Testing edge cases of a single component
- Fast, focused tests

---

## Integration Tests

### Purpose

From the Rust Book:

> In Rust, integration tests are entirely external to your library. They use your library in the same way any other code would, which means **they can only call functions that are part of your library's public API**. Their purpose is to test whether many parts of your library work together correctly. Units of code that work correctly on their own could have problems when integrated, so test coverage of the integrated code is important as well.

### The `tests/` Directory

From the Rust Book:

> We create a **tests** directory at the top level of our project directory, next to **src**. Cargo knows to look for integration test files in this directory. We can then make as many test files as we want, and **Cargo will compile each of the files as an individual crate**.

Directory structure:

```
crates/engine/
├── Cargo.toml
├── src/
│   └── lib.rs
└── tests/
    └── integration_test.rs
```

### No `#[cfg(test)]` Needed

From the Rust Book:

> We don't need to annotate any code in tests/integration_test.rs with `#[cfg(test)]`. **Cargo treats the tests directory specially and compiles files in this directory only when we run `cargo test`**.

### Each File is a Separate Crate

From the Rust Book:

> **Each file in the tests directory is a separate crate**, so we need to bring our library into each test crate's scope. For that reason, we add `use adder::add_two;` at the top of the code, which we didn't need in the unit tests.

### Example

```rust
// tests/simulation.rs
use wowlab_engine::{SimConfig, Simulation, parse_rotation};

#[test]
fn test_simulation_produces_valid_results() {
    let config = SimConfig::default();
    let rotation = parse_rotation(include_str!("fixtures/basic.json")).unwrap();

    let results = Simulation::new(config, rotation).run();

    assert!(results.dps > 0.0);
    assert!(results.total_damage > 0.0);
}
```

### Shared Test Helpers (Submodules)

From the Rust Book:

> As you add more integration tests, you might want to make more files in the tests directory to help organize them. [...] However, this means files in the tests directory don't share the same behavior as files in src do.

The problem with `tests/common.rs`:

> Having `common` appear in the test results with `running 0 tests` displayed for it is not what we wanted. We just wanted to share some code with the other integration test files.

The solution - use `tests/common/mod.rs`:

> To avoid having `common` appear in the test output, instead of creating tests/common.rs, we'll create **tests/common/mod.rs**. [...] This is the older naming convention that Rust also understands. **Naming the file this way tells Rust not to treat the `common` module as an integration test file.**

> **Files in subdirectories of the tests directory don't get compiled as separate crates or have sections in the test output.**

Correct structure:

```
crates/engine/
├── src/
│   └── lib.rs
└── tests/
    ├── common/
    │   └── mod.rs       # NOT treated as a test file
    └── integration_test.rs
```

Usage:

```rust
// tests/integration_test.rs
mod common;

#[test]
fn test_with_setup() {
    common::setup();
    // ...
}
```

### Binary Crates

From the Rust Book:

> If our project is a binary crate that only contains a src/main.rs file and doesn't have a src/lib.rs file, **we can't create integration tests in the tests directory** and bring functions defined in the src/main.rs file into scope with a `use` statement.

> This is one of the reasons Rust projects that provide a binary have a straightforward src/main.rs file that calls logic that lives in the src/lib.rs file. Using that structure, integration tests **can** test the library crate.

### When to Use Integration Tests

- Testing multiple modules working together
- Testing the public API as external users would
- End-to-end workflows
- Ensuring modules integrate correctly

---

## Test Naming

Use descriptive names:

```rust
// Good - describes behavior
#[test]
fn test_event_queue_maintains_fifo_at_same_timestamp() { }

#[test]
fn test_parse_returns_error_on_invalid_json() { }

// Bad - too vague
#[test]
fn test_basic() { }

#[test]
fn test_it_works() { }
```

---

## Test Body Structure

Follow Arrange-Act-Assert:

```rust
#[test]
fn test_resource_pool_caps_at_maximum() {
    // Arrange
    let mut pool = ResourcePool::new(100);
    pool.set_current(80);

    // Act
    pool.gain(50);

    // Assert
    assert_eq!(pool.current(), 100);
}
```

---

## Async Tests

Use `#[tokio::test]` for async code:

```rust
#[tokio::test]
async fn test_fetch_spell_data() {
    let client = TestClient::new();
    let spell = client.get_spell(123).await.unwrap();
    assert_eq!(spell.id, 123);
}
```

---

## Running Tests

```bash
# Run all tests in a crate
cd crates/engine
cargo test

# Run unit tests only (in src/)
cargo test --lib

# Run integration tests only (in tests/)
cargo test --test simulation

# Run specific test function
cargo test test_event_queue_ordering

# Run with output visible
cargo test -- --nocapture

# Run all workspace tests
cargo test --workspace
```

---

## Summary

From the Rust Book:

> Rust's testing features provide a way to specify how code should function to ensure that it continues to work as you expect, even as you make changes. **Unit tests exercise different parts of a library separately and can test private implementation details. Integration tests check that many parts of the library work together correctly**, and they use the library's public API to test the code in the same way external code will use it.

---

## Quick Reference

| Test Type   | Location          | Imports                | Private Access | `#[cfg(test)]` |
| ----------- | ----------------- | ---------------------- | -------------- | -------------- |
| Unit        | `src/*.rs` inline | `use super::*;`        | Yes            | Required       |
| Integration | `tests/*.rs`      | `use crate_name::...;` | No             | Not needed     |

| Pattern          | Location                                      |
| ---------------- | --------------------------------------------- |
| Unit test module | `#[cfg(test)] mod tests { }` in source file   |
| Integration test | `tests/<name>.rs`                             |
| Shared helpers   | `tests/common/mod.rs` (NOT `tests/common.rs`) |
| Test fixtures    | `tests/fixtures/*`                            |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/legacy3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
