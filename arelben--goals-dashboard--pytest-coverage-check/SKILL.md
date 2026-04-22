---
name: pytest-coverage-check
description: > Use when this capability is needed.
metadata:
  author: arelben
---

# Pytest Coverage Check

This skill runs your tests using `pytest` and `pytest-cov`, checks the total coverage percentage, and reports strictly if it falls below the threshold.

## Usage

1. **Ensure dependencies**: Make sure `pytest` and `pytest-cov` are installed.
2. **Run the check**:
   Run the following command in your terminal:

   ```bash
   pytest --cov=. --cov-report=term-missing --cov-fail-under=85
   ```

   - `--cov=.`: Measures coverage for the current directory.
   - `--cov-report=term-missing`: Shows which lines are missing coverage in the terminal report.
   - `--cov-fail-under=85`: Causes the command to fail (exit code 1) if coverage is below 85%.

## Troubleshooting Low Coverage

If coverage is low:
1. Look at the "Missing" column in the output.
2. Identify which files have low coverage (e.g., `services/mock_supabase_service.py` 29%).
3. Write targeted unit tests for those files.
4. Re-run the command to verify improvement.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arelben) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
