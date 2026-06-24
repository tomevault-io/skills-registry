---
name: test-writer
description: Generate comprehensive test coverage for existing code. Use when writing or expanding tests. Use when this capability is needed.
metadata:
  author: nimbalyst
---

# /write-tests Command

Generate comprehensive test coverage for existing code.

## What This Command Does

1. Analyzes code to test
2. Identifies test cases (happy path, edge cases, errors)
3. Generates test code in appropriate framework
4. Includes setup/teardown as needed

## Usage

`/write-tests [file or function]`

## Test Generation Approach

- Uses existing test framework in project
- Follows project test patterns
- Includes meaningful test names
- Covers edge cases and error conditions
- Adds necessary mocks/fixtures

## Best Practices

- Review generated tests for accuracy
- Adjust assertions as needed
- Run tests to verify they pass
- Add tests incrementally for large files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nimbalyst) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
