---
name: write-unit-tests
description: Enable an agent to write unit tests Use when this capability is needed.
metadata:
  author: pevans
---

# Write unit tests

## Best practices

- Unit tests should go into tests named for the functions being tested.
  - If you have a function `Foo`, you should have a test function named
    `TestFoo`.
  - Methods with receivers should include the name of the receiver's type. A
    method like `func (f *Foo) Bar() {...}` should have a test named
    `TestFooBar`.
- Tests should only test the behavior of the function. Don't exhaustively test
  every possible input.
- Each behavior you test should have a test case. If you have more than two
  test cases, use table-driven tests.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pevans) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
