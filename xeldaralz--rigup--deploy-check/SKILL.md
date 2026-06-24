---
name: deploy-check
description: Pre-deployment checklist Use when this capability is needed.
metadata:
  author: xeldaralz
---

# /deploy-check Skill

Run a pre-deployment checklist to catch common issues before deploying.

## Usage
- `/deploy-check` — run all checks

## Checks

1. **Git status**: No uncommitted changes, on correct branch
2. **Tests pass**: Run `npm test` / test command from package.json
3. **Build succeeds**: Run `npm run build` / build command
4. **Type check**: Run TypeScript compiler with `--noEmit`
5. **No TODO/FIXME in diff**: Check recent changes for unfinished work
6. **No console.log in diff**: Check for debugging leftovers
7. **Environment variables**: Verify .env.example matches required vars
8. **Dependencies**: No security vulnerabilities (`npm audit`)
9. **Bundle size**: Check for unexpected size increases
10. **Migration status**: Check for pending database migrations

## Output Format
```
Pre-deployment Check
====================
[pass/fail/skip] Git status clean
[pass/fail/skip] Tests passing
[pass/fail/skip] Build succeeds
...

Result: READY / NOT READY (N issues found)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xeldaralz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
