---
name: test
description: Run tests with optional filter Use when this capability is needed.
metadata:
  author: mgoodman60
---

Run ForemanOS tests. Use optional argument to filter.

## Usage

- `/test` - Run all tests
- `/test rag` - Run tests matching "rag"
- `/test __tests__/lib/rag.test.ts` - Run specific file

## Commands

```bash
# All tests
npm test -- --run

# Filtered
npm test -- $ARGUMENTS --run

# Single file
npm test -- __tests__/lib/$ARGUMENTS.test.ts --run
```

## Expected Output

Report:
- Tests passed/failed/skipped
- Failed test details
- Coverage summary if available

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mgoodman60) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
