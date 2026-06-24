---
name: test
description: Execute the project test suite using Deno Use when this capability is needed.
metadata:
  author: sachitv
---

## What I do

- Run `deno task test` to execute the complete test suite
- Can also run a single test file using `deno test <filename>.test.ts`
- All tests run on Deno's built-in harness
- Test fixtures are stored under `test-data/`

## When to use me

Use this when you want to verify code correctness:

- After implementing new features
- Before committing changes
- As part of the CI/CD pipeline
- When debugging failing tests

## Notes

- The test task is defined in `deno.jsonc` and runs with `--allow-read`
- This is the baseline for any change to the codebase
- Use `deno task test:ci` for CI behavior with JUnit output and coverage flags

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sachitv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
