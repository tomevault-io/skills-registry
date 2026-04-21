---
name: lint
description: Run linting and type checking across the Terrae codebase Use when this capability is needed.
metadata:
  author: alamenai
---

# Lint Skill

Run linting and type checking across the Terrae codebase.

## Instructions

1. **Run Type Checking**

   ```bash
   npx tsc --noEmit
   ```

   This checks TypeScript types without emitting files.

2. **Run ESLint**

   ```bash
   npm run lint
   ```

   This runs ESLint on the codebase.

3. **Run Prettier Check**

   ```bash
   npm run format:check
   ```

   This checks if files are properly formatted.

4. **Report Results**
   - List any type errors with file locations
   - List any linting errors/warnings
   - List any formatting issues

5. **Offer to Fix**
   If issues are found, ask the user if they want to:
   - Auto-fix linting issues: `npm run lint -- --fix`
   - Auto-format files: `npm run format`
   - Manually address type errors (show each error)

6. **Re-verify**
   After fixes, run the checks again to confirm all issues are resolved.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alamenai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
