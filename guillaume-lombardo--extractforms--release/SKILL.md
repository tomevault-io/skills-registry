---
name: release
description: Prepare and validate package release workflow for TestPyPI and PyPI. Use when this capability is needed.
metadata:
  author: guillaume-lombardo
---

# Release

## Steps
1. Update `version` in `pyproject.toml`.
2. Run full quality pipeline.
3. Build artifacts with `uv run --with build python -m build`.
4. Check metadata with `uv run --with twine python -m twine check dist/*`.
5. Publish using GitHub Actions tag `vX.Y.Z`.

## Notes
- Release workflow supports manual dispatch to TestPyPI.
- Production publish should use tags and trusted publishing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guillaume-lombardo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
