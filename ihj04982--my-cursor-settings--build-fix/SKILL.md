---
name: build-fix
description: description: Incrementally fix TypeScript and build errors. Invokes build-error-resolver agent. Use when build fails, type errors occur, or the user asks to fix the build. Use when this capability is needed.
metadata:
  author: ihj04982
---
---
name: build-fix
description: Incrementally fix TypeScript and build errors. Invokes build-error-resolver agent. Use when build fails, type errors occur, or the user asks to fix the build.
disable-model-invocation: true
---

# Build and Fix Command

This command invokes the **build-error-resolver** agent to fix TypeScript, compilation, and build errors with minimal diffs.

## What This Command Does

1. **Diagnose** - Run tsc, eslint, npm run build
2. **Fix incrementally** - One error at a time, minimal changes
3. **No architecture changes** - Only fix errors, no refactoring
4. **Verify** - Re-run build after each fix

## When to Use

Use `/build-fix` when:
- Build fails (TypeScript, compilation, module resolution)
- Type errors occur
- Import/export or dependency issues
- Configuration errors (tsconfig, webpack, Next.js)

## How It Works

The build-error-resolver agent uses tsc --noEmit, ESLint, and project build commands to diagnose and fix errors with the smallest possible changes. It does not refactor or redesign.

## Related

- Agent: `agents/build-error-resolver.md`
- Use **build-fix** proactively when build is red

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihj04982) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
