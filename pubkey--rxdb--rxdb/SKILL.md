---
name: verify
description: Verifies code changes by running tests and generation scripts Use when this capability is needed.
metadata:
  author: pubkey
---

# Verify Changes

This skill verifies that recent code changes are correct and do not break existing functionality.

## Steps

1. Run the fast memory tests to ensure core functionality is working.
   ```bash
   npm run test:fast:memory
   ```



2. (Optional) Run linting to check for style issues.
   ```bash
   npm run lint
   ```

---
> Source: [pubkey/rxdb](https://github.com/pubkey/rxdb) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
