---
name: validate-code
description: Validate code before committing. Use before commits or when fixing linting/type errors. Use when this capability is needed.
metadata:
  author: jamesjfoong
---

# Validate Code

## When to Use

- Before committing any changes
- When fixing linting or type errors
- When code quality checks fail

## Quick Validation

```bash
npm run validate
```

Runs: type-check + lint + format-check

## Fix Issues

```bash
# Fix linting
npm run lint:fix

# Fix formatting
npm run format

# Re-validate
npm run validate
```

## Individual Checks

```bash
npm run type-check    # TypeScript only
npm run lint          # ESLint only
npm run format:check  # Prettier only
```

## Common Type Errors

| Error              | Fix                                       |
| ------------------ | ----------------------------------------- |
| Implicit any       | Add explicit type annotations             |
| Type mismatch      | Verify schema matches type                |
| Missing properties | Use optional `?` if needed                |
| Optional values    | Use `?` instead of `\| null \| undefined` |

## Debugging Steps

1. Run `npm run type-check` to see all errors
2. Check Zod schemas match expected structure
3. Ensure types exported from `types.ts`
4. Verify optional properties use `?`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamesjfoong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
