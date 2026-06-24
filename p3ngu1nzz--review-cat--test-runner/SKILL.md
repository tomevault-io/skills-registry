---
name: test-runner
description: Runs tests by suite using the repo's canonical test layout (test/). Use when asked to run unit/integration/bench tests, troubleshoot failures, or validate the green gate.
metadata:
  author: p3ngu1nzz
---

# Skill: test-runner

## What this skill does

Runs tests via `./scripts/test.sh`.

## How to use

- Unit tests: `./scripts/test.sh --unit`
- Integration tests: `./scripts/test.sh --integration`
- Benchmarks: `./scripts/test.sh --bench --bench-output build/bench.json`
- All: `./scripts/test.sh --all`

## Outputs

- Exit code: 0 on success, non-zero on failure
- Optional JUnit XML: `--junit-output <path>`
- Optional bench JSON: `--bench-output <path>`

## Related

- `docs/specs/dev/components/TestDirectory.md`
- `docs/specs/dev/TESTING_STRATEGY.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/p3ngu1nzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
