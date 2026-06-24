---
name: setup-wikipedia
description: | Use when this capability is needed.
metadata:
  author: aymenfurter
---

# Wikipedia Package Setup

Install the `wikipedia` Python package to enable Wikipedia lookups.

## Step 1 -- Install the Package

```bash
pip install wikipedia
```

## Step 2 -- Verify Installation

```bash
python3 -c "import wikipedia; print('wikipedia package version:', wikipedia.__version__)"
```

If this prints a version string, the package is ready.

## Step 3 -- Quick Test

```bash
python3 -c "import wikipedia; print(wikipedia.summary('Python (programming language)', sentences=1))"
```

If this returns a sentence about Python, everything is working.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aymenfurter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
