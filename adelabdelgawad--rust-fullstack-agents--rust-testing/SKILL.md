---
name: rust-testing
description: > Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Test Writing (Rust)

## When to Use

- Before writing any `#[test]` function for the first time in a module.
- When a test failure message is unhelpful (the diff says nothing about *why*).
- When investigating a `#[should_panic]` test that passes even though the wrong panic fired.
- When deciding whether a test belongs in `#[cfg(test)] mod tests` or in `tests/`.
- When adding shared helper logic for integration tests.
- When writing a test that calls fallible code (`?` is available in tests).

This skill covers **how to write** tests. For **running** the full gate suite (fmt, clippy, sqlx
offline, migration safety, hydration lints) see `quality-gates`.

---

## Unit Tests vs. Integration Tests

| Concern | Unit Test | Integration Test |
|---|---|---|
| Location | `#[cfg(test)] mod tests { }` inside `src/` file | `tests/<name>.rs` at crate root |
| `#[cfg(test)]` needed? | Yes — keeps test code out of production build | No — Cargo knows the `tests/` dir |
| Accesses private functions? | Yes — child module sees parent's private items | No — uses only `pub` API |
| Compiled as | Same crate as the code under test | Separate crate per file |
| `use` of the crate | `use super::*;` | `use mycrate::whatever;` |
| Purpose | Test a single function/module in isolation | Verify that modules work together end-to-end |

> "Writing both kinds of tests is important to ensure that the pieces of your library are doing
> what you expect them to, separately and together." — The Book, Ch. 11.3

---

## Core Idioms

### Idiom 1 — Return `Result<(), E>` and propagate with `?`

Returning `Result` from a test enables `?` inside the body. This is cleaner than chaining
`.unwrap()` on every fallible call and produces a structured failure on `Err`.

```rust
// CORRECT: propagate with ?; failure message shows the Err value
#[test]
fn parses_valid_port() -> Result<(), std::num::ParseIntError> {
    let port: u16 = "8080".parse()?;
    assert_eq!(port, 8080);
    Ok(())
}
```

```rust
// BAD: .unwrap() panics with a generic message; no structured Err info on failure
#[test]
fn parses_valid_port_bad() {
    let port: u16 = "8080".parse().unwrap();
    assert_eq!(port, 8080);
}
```

Note: you cannot combine `#[should_panic]` with a `Result`-returning test. To assert that an
operation returns `Err`, use `assert!(result.is_err())` inside a `Result`-returning test:

```rust
#[test]
fn rejects_non_numeric() -> Result<(), String> {
    let result = "xyz".parse::<u16>();
    assert!(result.is_err());
    Ok(())
}
```

---

### Idiom 2 — `#[should_panic(expected = "...")]` with a substring

Always provide `expected =` so the test fails when code panics for the *wrong* reason.

```rust
// CORRECT: test fails if the panic message does not contain "less than or equal to 100"
#[test]
#[should_panic(expected = "less than or equal to 100")]
fn rejects_over_range() {
    Guess::new(200);
}
```

```rust
// BAD: test passes on ANY panic — including an unrelated internal assertion or a panic
// injected by a dependency. You have no idea the right condition fired.
#[test]
#[should_panic]
fn rejects_over_range_bad() {
    Guess::new(200);
}
```

---

### Idiom 3 — `assert_eq!` / `assert_ne!` with a custom message

Custom messages are formatted with `format!` and printed only on failure. They document
*what* was expected, not just *that* equality failed.

```rust
// CORRECT: failure message explains the semantic expectation
#[test]
fn greeting_includes_name() {
    let msg = greeting("Carol");
    assert!(
        msg.contains("Carol"),
        "expected greeting to contain the user's name, got: `{msg}`",
    );
}

#[test]
fn totals_match() {
    let got = compute_total(&items);
    assert_eq!(
        got, 42,
        "compute_total returned {got} but expected 42 for input {:?}", &items,
    );
}
```

```rust
// BAD: bare assert!(a == b) produces "assertion failed: a == b" — no values printed
#[test]
fn totals_match_bad() {
    assert!(compute_total(&items) == 42);
}
```

---

### Idiom 4 — Unit tests in `#[cfg(test)] mod tests` next to the code

The standard location for unit tests is a `tests` submodule inside the same file. The
`#[cfg(test)]` attribute means the module is compiled only during `cargo test` — it does not
inflate your production binary.

```rust
// src/math.rs
pub fn add_two(n: i32) -> i32 { n + 2 }

fn internal_double(n: i32) -> i32 { n * 2 }

#[cfg(test)]
mod tests {
    use super::*;   // imports both pub and private items

    #[test]
    fn add_two_basic() {
        assert_eq!(add_two(3), 5);
    }

    #[test]
    fn internal_double_basic() {
        assert_eq!(internal_double(4), 8);  // private fn reachable here
    }
}
```

---

### Idiom 5 — Integration tests in `tests/` for black-box, public-API coverage

Each file in `tests/` is compiled as its own crate. It can only call `pub` items.
Use this for end-to-end flow tests that cross module boundaries.

```
my_crate/
├── src/
│   └── lib.rs
└── tests/
    ├── common/
    │   └── mod.rs     ← shared helpers; NOT treated as a test file
    └── accounts.rs    ← integration test file; no #[cfg(test)] needed
```

```rust
// tests/accounts.rs
use my_crate::accounts::{create_account, find_account};
mod common;

#[test]
fn round_trip_create_find() -> Result<(), Box<dyn std::error::Error>> {
    let db = common::setup_test_db();
    let id = create_account(&db, "alice")?;
    let acc = find_account(&db, id)?;
    assert_eq!(acc.name, "alice");
    Ok(())
}
```

```rust
// tests/common/mod.rs  — use this path, NOT tests/common.rs
// Files in subdirectories of tests/ are NOT compiled as test binaries.
pub fn setup_test_db() -> TestDb { /* ... */ }
```

---

### Idiom 6 — Descriptive test names that read as sentences

Test names appear verbatim in `cargo test` output. A descriptive name is the first
line of the failure report.

```rust
// CORRECT: names communicate the scenario and expected outcome
#[test] fn larger_rectangle_can_hold_smaller() { ... }
#[test] fn parse_port_rejects_zero() { ... }
#[test] fn create_user_returns_conflict_on_duplicate_email() { ... }

// BAD: names that communicate nothing
#[test] fn test1() { ... }
#[test] fn it_works() { ... }
#[test] fn check() { ... }
```

---

## Forbidden Patterns

### Forbidden 1 — `#[should_panic]` Without `expected =`

```rust
// BAD: passes on any panic, including the wrong one
#[test]
#[should_panic]
fn bad_input_rejected() {
    process_input(-1);
}
```

**Why (Book Ch. 11.1):** Without `expected =`, the test passes even when a completely
unrelated panic fires (e.g., an index-out-of-bounds in a dependency, a debug assertion, an
`.unwrap()` on an unrelated value). You get a false green.

**Fix:** Add `expected = "<substring of the panic message>"`.

```rust
// CORRECT
#[test]
#[should_panic(expected = "input must be non-negative")]
fn bad_input_rejected() {
    process_input(-1);
}
```

```bash
# Detector — #[should_panic] lines NOT followed by (expected =
grep -n '#\[should_panic\]' src/ tests/ -r | grep -v 'expected'
```

Caveat: the grep catches the attribute form `#[should_panic]`; the equivalent
`#[should_panic(expected = "...")]` is on the same line and won't match.

---

### Forbidden 2 — `.unwrap()` Chains in Tests Instead of Returning `Result`

```rust
// BAD: five .unwrap() calls — first failure gives "called unwrap on Err"; no context
#[test]
fn full_flow() {
    let conn = connect().unwrap();
    let id = insert_user(&conn, "alice").unwrap();
    let user = find_user(&conn, id).unwrap();
    assert_eq!(user.name, "alice");
}
```

**Why (Book Ch. 11.1):** `?` in a `Result`-returning test propagates the *actual* error
value, giving a useful failure message. A chain of `.unwrap()` panics with "called
`unwrap()` on an `Err` value" and swallows the inner `Err`.

**Fix:** Declare `-> Result<(), YourErrorType>` and use `?`.

```rust
// CORRECT
#[test]
fn full_flow() -> Result<(), Box<dyn std::error::Error>> {
    let conn = connect()?;
    let id = insert_user(&conn, "alice")?;
    let user = find_user(&conn, id)?;
    assert_eq!(user.name, "alice");
    Ok(())
}
```

```bash
# Detector — .unwrap() inside test functions (heuristic; review matches manually)
grep -rn '\.unwrap()' src/ tests/ | grep -v '#\[cfg(test\|// @ok-in-test'
```

Note: `unwrap()` is acceptable for constructing test fixtures where the value is
hard-coded and obviously valid (e.g., `"127.0.0.1".parse().unwrap()`). The smell is
*chains* of `.unwrap()` on production code paths. See `rust-error-handling` — that skill
explicitly permits `.unwrap()` in `#[cfg(test)]` blocks for fixture setup.

---

### Forbidden 3 — `assert!(a == b)` Instead of `assert_eq!(a, b)`

```rust
// BAD: failure says "assertion failed: left == right" — no values shown
#[test]
fn computes_total_bad() {
    assert!(compute_total(&items) == 42);
}
```

**Why (Book Ch. 11.1):** `assert_eq!` and `assert_ne!` print both values on failure:
```
assertion `left == right` failed
  left: 37
 right: 42
```
`assert!(a == b)` shows only the source expression. On a CI log without a debugger, the
difference between "got 37, expected 42" and "assertion failed" can take minutes.

**Fix:** Replace `assert!(a == b)` with `assert_eq!(a, b)` (and optionally add a message).
Types must implement `PartialEq + Debug`; add `#[derive(PartialEq, Debug)]` if needed.

```rust
// CORRECT
#[test]
fn computes_total() {
    assert_eq!(compute_total(&items), 42, "unexpected total for items: {:?}", &items);
}
```

```bash
# Detector
grep -rn 'assert!(.* == ' src/ tests/ | grep -v '//'
grep -rn 'assert!(.* != ' src/ tests/ | grep -v '//'
```

Caveat: the pattern substring-matches `debug_assert!(a == b)`, which is a distinct macro
(stripped in release builds) and may appear legitimately. Review matches to confirm the
hit is `assert!` rather than `debug_assert!`. Lines with a trailing `//` comment may
slip through `grep -v '//'`; inspect borderline cases manually.

---

### Forbidden 4 — Test Body With No Assertion (Always Passes)

```rust
// BAD: this test can never fail — it just calls the code and drops the result
#[test]
fn creates_user() {
    let _ = create_user("alice");
}
```

**Why (convention — not a named Book rule; the closest anchor is the assertion-macros section in Book Ch. 11.1):** A test with no assertion (no `assert!`, `assert_eq!`, `assert_ne!`, `panic!`,
or returned `Err`) is an always-green placeholder. It wastes CI time and provides false
confidence. The function could return any garbage value and the test still reports `ok`.

**Fix:** Assert the return value, or if testing for no-panic, document that explicitly
and at minimum assert a property of the returned value.

```rust
// CORRECT
#[test]
fn creates_user() -> Result<(), AppError> {
    let user = create_user("alice")?;
    assert_eq!(user.name, "alice");
    Ok(())
}
```

```bash
# Detector — test functions with no assert!/assert_eq!/assert_ne! and no ? or Err return
# Collects 30 lines after each #[test] marker; checks the block for assertion keywords.
# Heuristic: 30 lines covers almost all real test bodies; review any reported match manually.
grep -rn -A30 '#\[test\]' src/ tests/ \
  | awk '
      /^[^-].*#\[test\]/ { capture=1; block=$0; next }
      /^--$/ {
        if (capture && block !~ /assert[_!]|Ok\(|Err\(|\?/) print block
        capture=0; block=""; next
      }
      capture { block = block "\n" $0 }
      END {
        if (capture && block !~ /assert[_!]|Ok\(|Err\(|\?/) print block
      }
    ' 2>/dev/null \
  || echo "run manually: check each #[test] fn for at least one assertion"
```

---

### Forbidden 5 — Integration-Style Tests Reaching Private Internals via `#[cfg(test)]`

```rust
// BAD: this is in src/billing.rs — it imports a crate-internal helper from src/payments/mod.rs
// to test a multi-module flow. This crosses module boundaries via crate-internal visibility
// and makes the test brittle to internal refactoring.
#[cfg(test)]
mod tests {
    use super::*;
    use crate::payments::internal_charge_helper; // accessible because internal_charge_helper
                                                  // is pub(crate) — crossing module boundaries
                                                  // via crate-internal visibility is the smell

    #[test]
    fn full_billing_flow() {
        internal_charge_helper(42);
        // ...
    }
}
```

**Why (Book Ch. 11.3):** Unit tests placed with `#[cfg(test)]` in a source file legitimately
access that file's private helpers. But if your test is exercising behavior that crosses
multiple modules via internal symbols, it belongs in `tests/` as a black-box integration
test against the `pub` API. Reaching deeply into other modules' `pub(crate)` implementations
from a unit-test module creates false coupling and hides design problems.

**Fix:** Move cross-module flow tests to `tests/` and call only the public interface.
If the flow cannot be tested without accessing crate-internal helpers, those helpers probably
need to be promoted to fully `pub` with intent, or the design needs refactoring.

```rust
// CORRECT: tests/billing_flow.rs — calls only the public API; no cross-module internals
use my_crate::billing::process_billing;
use my_crate::payments::ChargeResult;

#[test]
fn full_billing_flow_succeeds() -> Result<(), Box<dyn std::error::Error>> {
    let result: ChargeResult = process_billing(42)?;
    assert!(result.is_success());
    Ok(())
}
```

```bash
# Detector — use crate:: inside a #[cfg(test)] block (heuristic for cross-module reach)
# Finds files that contain both #[cfg(test)] and a use crate:: import referencing another module.
grep -rln '#\[cfg(test)\]' src/ | while read -r file; do
  if grep -qE 'use crate::[a-z_]+::[a-z_]' "$file"; then
    grep -n 'use crate::' "$file"
  fi
done
```

---

### Forbidden 6 — Shared Mutable Global State Across Tests

```rust
// BAD: tests share a global counter; run order determines whether they pass
static COUNTER: std::sync::Mutex<i32> = std::sync::Mutex::new(0);

#[test]
fn increments_counter() {
    *COUNTER.lock().unwrap() += 1;
    assert_eq!(*COUNTER.lock().unwrap(), 1);  // fails if another test ran first
}

#[test]
fn counter_starts_at_zero() {
    assert_eq!(*COUNTER.lock().unwrap(), 0);  // fails if increments_counter ran first
}
```

**Why (Book Ch. 11.2):** Cargo runs tests in parallel by default (`--test-threads` defaults
to the number of logical CPUs). Tests sharing mutable global state race with each other and
produce order-dependent results — green locally (serial run), red on CI (parallel run), or
intermittently red depending on the scheduler.

**Fix options:**
1. Give each test its own local state (preferred).
2. Run the affected tests serially with `cargo test -- --test-threads=1` (documents the
   constraint; slow; use only as a last resort).
3. Protect shared state with a `Mutex` *and* reset it at test start — but this couples
   tests to a teardown contract that is fragile.

```rust
// CORRECT: each test owns its state
#[test]
fn increments_counter() {
    let mut counter = 0i32;
    counter += 1;
    assert_eq!(counter, 1);
}
```

```bash
# Detector — static mutable or static Mutex in files with #[test]
grep -rn 'static .*Mutex\|static mut ' src/ tests/ \
  | grep -v '// @test-global-ok'
```

Caveat: this heuristic matches all `static Mutex`/`static mut` globals, including
production registries (`OnceLock<Mutex<_>>`, metrics counters, connection pools) that
are safe and intentional. Review each hit; flag only those declared inside a `#[cfg(test)]`
module or a test helper file, not every prod-code global.

---

### Forbidden 7 — `#[ignore]` Without a Reason Comment

```rust
// BAD: no one knows why this is ignored or when it can be re-enabled
#[test]
#[ignore]
fn slow_integration_test() {
    // ...
}
```

**Why (Book Ch. 11.2):** `#[ignore]` is a legitimate tool for tests that require external
resources or take minutes to run. Without a reason, ignored tests become permanently invisible
dead weight — no one re-enables them and coverage silently drops.

**Fix:** Add a `// ignore: <reason>` comment immediately above `#[ignore]`, stating why and
the condition under which it should be re-enabled.

```rust
// CORRECT
// ignore: requires a live Postgres instance; run with INTEGRATION=1 cargo test -- --ignored
#[test]
#[ignore]
fn slow_integration_test() {
    // ...
}
```

```bash
# Detector — #[ignore] lines not immediately preceded by a reason comment
# Uses awk to check whether the line immediately before #[ignore] contains a reason.
grep -rn -B1 '#\[ignore\]' src/ tests/ \
  | awk '
      /\/\/ ignore:|@ignore-ok/ { skip=1; next }
      /#\[ignore\]/ { if (!skip) print; skip=0; next }
      { skip=0 }
    '
```

---

## Running Tests — Quick Reference

> Full gate (fmt + clippy + sqlx + hydration lints): see `quality-gates`.

```bash
# Run all tests
cargo test

# Show stdout from passing tests (suppressed by default)
cargo test -- --show-output

# Disable parallelism (required when tests share mutable global state)
cargo test -- --test-threads=1

# Run a single test by name (substring match)
cargo test parse_port_rejects_zero

# Run all tests whose name contains "account"
cargo test account

# Run only a specific integration test file
cargo test --test accounts

# Run only tests marked #[ignore]
cargo test -- --ignored

# Run all tests including ignored
cargo test -- --include-ignored
```

**Argument separation:** arguments before `--` are for `cargo test`; arguments after `--`
are passed to the test binary. Example: `cargo test --test accounts -- --show-output`.

---

## Doc Tests

Doc tests live in `///` doc comments and are run by `cargo test` as a third category
(separate section in output). They serve as living documentation.

```rust
/// Adds two to the given number.
///
/// # Examples
///
/// ```
/// let result = my_crate::add_two(3);
/// assert_eq!(result, 5);
/// ```
pub fn add_two(n: i32) -> i32 { n + 2 }
```

Rules:
- Doc tests compile and run as their own mini-crate — they test the `pub` API only.
- They run even in library crates that have no `tests/` directory.
- `# ignored_setup_line` (lines starting with `#`) are included in compilation but hidden
  in rendered docs.
- Mark a block `no_run` if it requires runtime resources: ```` ```no_run ````.

---

## Book References

All section titles and quotes are from *The Rust Programming Language* (doc.rust-lang.org/book):

- **Ch. 11.0** — "Writing Automated Tests" (chapter intro overview)
  https://doc.rust-lang.org/book/ch11-00-testing.html

- **Ch. 11.1** — "How to Write Tests": test lifecycle (set up → run → assert), `#[test]`,
  assertion macros, `#[should_panic(expected=)]`, `Result<T,E>` tests, custom failure messages
  https://doc.rust-lang.org/book/ch11-01-writing-tests.html

- **Ch. 11.2** — "Controlling How Tests Are Run": `--test-threads`, `--show-output`,
  name filtering, `#[ignore]`, `--ignored`
  https://doc.rust-lang.org/book/ch11-02-running-tests.html

- **Ch. 11.3** — "Test Organization": unit tests + `#[cfg(test)]`, private function testing,
  integration tests in `tests/`, `tests/common/mod.rs`, doc tests
  https://doc.rust-lang.org/book/ch11-03-test-organization.html

---

## Related Skills

- **`quality-gates`** — runs `cargo test` (and fmt/clippy/sqlx/hydration gates) as the
  final verification step. This skill tells you *what* to write; `quality-gates` tells you
  *when and how to run* it.
- **`rust-error-handling`** — companion for `Result<T, E>` test patterns; clarifies that
  `.unwrap()` is acceptable in `#[cfg(test)]` fixture setup (Forbidden 2 caveat above).

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
