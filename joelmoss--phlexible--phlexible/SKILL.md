---
name: test
description: Optional test file path, or path:line for a single test Use when this capability is needed.
metadata:
  author: joelmoss
---

Run Phlexible tests using the Appraisal gem for multi-version testing.

## Instructions

1. If `appraisal` is "all", run: `bundle exec appraisal rails test {file}`
2. Otherwise, run: `bundle exec appraisal {appraisal} rails test {file}`
3. If no `file` is given, omit it to run the full suite for that appraisal.
4. Report pass/fail/skip counts from the test output.
5. If tests fail, summarise which tests failed and why.

---
> Source: [joelmoss/phlexible](https://github.com/joelmoss/phlexible) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
