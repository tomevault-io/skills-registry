---
name: tdd-test-generator
description: Automatically generates test boilerplate, edge cases, and unit tests based on function specifications. Use when this capability is needed.
metadata:
  author: jcorpac
---

# TDD Test Generator

This skill focuses on creating robust test suites quickly.

## Principles
- **Boilerplate First**: Get the basic setup out of the way.
- **Exhaustive Edge Cases**: Consider nulls, empty strings, large numbers, and boundary conditions.
- **Table-Driven Tests**: Use data tables for repeatable test logic.

## Recommended Prompts
"Generate a pytest test suite for the following function, including edge cases for empty inputs and invalid types: [code snippet]"

## Templates
Check `resources/templates/` for starting points in Python and JavaScript.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
