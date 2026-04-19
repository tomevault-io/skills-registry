---
name: coverage-checker
description: Measure test coverage and identify untested areas. Use for coverage checks. Use when this capability is needed.
metadata:
  author: cvprun
---

# Coverage Checker

Measure test coverage and suggest improvements.

## Commands

```bash
./pytest.sh                    # Full coverage
./pytest.sh tester/{module}/   # Module coverage
```

## Results

- HTML: `build/cov/html/index.html`
- XML: `build/cov.xml`

## Goals

- Line coverage: 80%+
- Branch coverage: 70%+

## Report Format

Output as markdown table:
- Line/branch coverage percentages
- Per-file breakdown
- Untested functions list
- Improvement suggestions

For untested areas, suggest using `/test-generator` skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cvprun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
