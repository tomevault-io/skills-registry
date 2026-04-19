---
name: coverage
description: Collect and enforce code coverage using Deno Use when this capability is needed.
metadata:
  author: sachitv
---

## What I do

- Run `deno task coverage` to collect coverage data for the codebase
- Can also collect coverage for a specific file using
  `deno task coverage <filename>`
- Use `deno task coverage:check` to enforce coverage thresholds defined in
  `.denocoveragerc`
- Use `deno task serve:coverage` to serve an HTML coverage report in the browser

## When to use me

Use this when you want to measure or verify test coverage:

- After adding new tests
- Before submitting a pull request (coverage must meet 100% thresholds)
- When debugging which areas of the code are under-tested
- To inspect coverage gaps interactively

## Notes

- Coverage thresholds are defined in `.denocoveragerc` (100% for lines,
  branches, and functions)
- Every test change must be accompanied by coverage that satisfies these
  thresholds
- The HTML report is helpful for visualizing failing areas in complex
  serialization paths

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sachitv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
