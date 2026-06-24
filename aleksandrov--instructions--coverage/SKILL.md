---
name: coverage
description: Run the requirements coverage report and show which requirements are uncovered by tests. Use when this capability is needed.
metadata:
  author: aleksandrov
---

## Current coverage report

```
!`scripts/coverage-report.sh`
```

Review the UNCOVERED section above. For each uncovered requirement:
1. Identify which test file should cover it
2. Show a suggested test stub with the appropriate `Covers: REQ-XXX-N` comment
3. Ask the user if they want the stubs written

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aleksandrov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
