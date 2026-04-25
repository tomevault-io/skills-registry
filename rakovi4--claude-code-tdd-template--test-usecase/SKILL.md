---
name: test-usecase
description: Run use-case module tests quickly. Use when user wants to run use-case tests or mentions /test-usecase command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Run Use-Case Tests

## Action

```bash
cd backend && ./gradlew.bat :usecase:test --rerun-tasks
```

With argument (test filter):
```bash
cd backend && ./gradlew.bat :usecase:test --tests "*{argument}*" --rerun-tasks
```

## Output

Report the test results from output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
