---
name: coverage-check
description: Check test coverage and identify files below 80% threshold. Use before committing to avoid SonarCloud failures. Use when this capability is needed.
metadata:
  author: pavelzbornik
---

# Coverage Check

Run the test suite with coverage reporting and identify gaps.

## Steps

1. Run: `uv run pytest --cov=app --cov-report=term-missing --cov-fail-under=80`
2. Parse the output to find files below 80% coverage
3. For each file below threshold, list the uncovered line ranges
4. Summarize: total coverage, files below 80%, and which code paths need tests
5. If coverage is below 80%, suggest specific test cases to write

Report the results concisely. Do not write tests unless asked — just identify the gaps.

---
> Source: [pavelzbornik/whisperX-FastAPI](https://github.com/pavelzbornik/whisperX-FastAPI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
