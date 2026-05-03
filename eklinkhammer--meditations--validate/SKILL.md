---
name: validate
description: >- Use when this capability is needed.
metadata:
  author: eklinkhammer
---

# Validate Codebase

Run TypeScript type checking, tests, and build across the meditations monorepo and report a pass/fail summary.

## Default Behavior

Run all three checks in order. Stop and report on first failure.

1. **Types** — `pnpm lint` (runs `tsc --noEmit` via turbo)
2. **Tests** — `pnpm test` (runs `vitest run` via turbo)
3. **Build** — `pnpm build` (compiles all packages via turbo)

## Scoped Validation

`$ARGUMENTS` can scope what gets validated:

- **Package filter**: `shared`, `api-client`, `server` — run checks only on that package
- **Check filter**: `types`, `tests`, `build` — run only that check type
- **Combination**: `shared types` — run only type checking on the shared package

## How to Run

Execute the validation script from the project root:

```bash
.claude/skills/validate/scripts/validate.sh $ARGUMENTS
```

The script accepts `[package] [check-type]` arguments and handles filtering automatically.

### Commands Reference

| Check | Full command | Filtered by package |
|-------|-------------|---------------------|
| Types | `pnpm lint` | `pnpm --filter @meditations/<pkg> lint` |
| Tests | `pnpm test` | `pnpm --filter @meditations/<pkg> test` |
| Build | `pnpm build` | `pnpm --filter @meditations/<pkg> build` |

## Output Format

Report each check as **PASS** or **FAIL**. On failure, include the relevant error output.

After all checks complete, print a summary line: `=== Summary: N passed, M failed ===`

## Post-Validation

- If all checks pass, report success.
- If any fail, suggest fixes based on the error output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eklinkhammer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
