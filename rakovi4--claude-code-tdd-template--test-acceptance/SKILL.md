---
name: test-acceptance
description: Run acceptance tests (backend API or frontend UI). Use when user wants to run E2E acceptance tests or mentions /test-acceptance command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Run Acceptance Tests

Backend must be running first (`/run-backend`).

## Action

All tests:
```bash
cd acceptance/app-acceptance && ./gradlew.bat test --rerun-tasks
```

Backend tests only:
```bash
cd acceptance/app-acceptance && ./gradlew.bat backendTest --rerun-tasks
```

Frontend tests only:
```bash
cd acceptance/app-acceptance && ./gradlew.bat frontendTest --rerun-tasks
```

With argument:
- `backend` → use `backendTest`
- `frontend` → use `frontendTest`
- other → use `test --tests "*{argument}*"`

## Output

Report the test results from output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
