---
name: fix
description: Run Prettier and ESLint to format and fix code Use when this capability is needed.
metadata:
  author: sammii-hk
---

# Fix Code Style

Run Prettier and ESLint to automatically format and fix code issues.

## Steps

1. **Run ESLint with auto-fix**:

   ```bash
   pnpm lint
   ```

2. **Run Prettier** (if needed for non-JS/TS files):
   ```bash
   pnpm exec prettier --write "**/*.{json,css,md,yml,yaml}"
   ```

## For Specific Files

If user specifies a file or pattern:

```bash
pnpm exec prettier --write <path>
npx eslint --fix <path>
```

## Notes

- ESLint handles .ts/.tsx/.js/.jsx files
- Prettier handles JSON, CSS, Markdown, YAML
- Both are run by lint-staged on commit automatically

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
