---
name: ds-notebook-refactor
description: Bridging the gap between exploratory Jupyter notebooks and production-ready Python modules. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Notebook Refactor

Jupyter notebooks are great for research but risky for production. This skill helps you safely migrate code out of cells and into testable modules.

## The Refactor Checklist
1. **Identify Logic**: Find the cells that actually perform data transformations (ignore the plotting cells for now).
2. **Extract Functions**: Move cell code into well-named functions with type hints.
3. **Handle Config**: Extract hard-coded paths and variables into a `config.yaml` or `.env` file.
4. **Parameterize**: Ensure your main script can take arguments (e.g., input path).
5. **Unit Test**: Write a small test for your new function using representative dummy data.

## Before vs. After
- **Before**: A notebook with 50 cells, global variables, and `print()` statements everywhere.
- **After**: A single `pipeline.py` that imports functions from `utils.py` and logs progress.

## Best Practices
- Use a `.ipynb` to `.py` exporter for a head start, but always manually clean the result.
- Keep your plotting logic separate from your transformation logic.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
