---
name: test
description: Run unit tests, optionally for specific files Use when this capability is needed.
metadata:
  author: sammii-hk
---

# Run Tests

Run Jest unit tests for the codebase or specific files.

## Usage

**Run all unit tests:**

```bash
pnpm test
```

**Run tests for a specific file or pattern** (if user provides an argument):

```bash
pnpm test -- <pattern>
```

Examples:

- `pnpm test -- numerology` - tests matching "numerology"
- `pnpm test -- __tests__/unit/blog` - all blog unit tests
- `pnpm test -- --watch` - watch mode

## Output

Report test results including:

- Number of tests passed/failed
- Failed test details with file paths and error messages
- Coverage summary if available

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
