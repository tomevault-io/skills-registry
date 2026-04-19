---
name: ci
description: Run the CI pipeline locally (brakeman, rubocop, importmap audit, tests). Use when asked to run CI, check code quality, or verify code before pushing. Use when this capability is needed.
metadata:
  author: lylo
---

# CI

Run the CI pipeline locally before pushing.

## Steps

Run these commands in sequence, stopping on first failure:

1. **Brakeman** (security scan)
   ```bash
   bundle exec brakeman --quiet --no-pager --ensure-latest
   ```

2. **Rubocop** (style check)
   ```bash
   bundle exec rubocop
   ```

3. **Importmap Audit** (JS dependency check)
   ```bash
   bin/importmap audit
   ```

4. **Unit Tests**
   ```bash
   bin/rails test
   ```

5. **System Tests**
   ```bash
   bin/rails test:system
   ```

## Behavior

- Run all checks in sequence
- Stop and report on first failure
- Summarize results at the end as a table:

| Step | Status |
|------|--------|
| Brakeman (security) | ✅ Passed |
| Rubocop (style) | ✅ Passed |
| Importmap Audit (JS deps) | ✅ Passed |
| Unit Tests | ✅ X tests passed |
| System Tests | ✅ X tests passed |

- If all pass, confirm the code is ready to push

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lylo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
