---
name: testing
description: Guide for testing practices and frameworks Use when this capability is needed.
metadata:
  author: 5t111111
---

# Testing Skill

This skill provides a guide for testing practices and frameworks.

## Testing frameworks

- Use Deno's built-in testing framework for writing and running tests

## Writing Tests

- Write tests in separate files with the `.test.ts` extension in the same
  directory as the code being tested
- All public functions and methods must have corresponding tests
- Use descriptive names for test cases to clearly indicate their purpose
- Should cover edge cases and error handling in tests

## Running Tests

- Use the command `mise run test` to run all tests in the project
- Run tests before committing code changes to ensure no tests are failing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/5t111111) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
