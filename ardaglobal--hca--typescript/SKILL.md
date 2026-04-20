---
name: typescript
description: Build, test, and lint TypeScript/React projects Use when this capability is needed.
metadata:
  author: ardaglobal
---

# TypeScript Development Skill

You are working in a TypeScript/JavaScript environment with Node.js and Bun.

## Available Runtimes

- `bun` - Fast JavaScript runtime (preferred)
- `node` - Node.js LTS
- `tsx` - TypeScript execution

## Package Managers

- `bun install` - Fastest (preferred)
- `pnpm install` - Fast, disk efficient
- `npm install` - Standard

## Project Detection

Look for these files to identify TypeScript projects:
- `package.json` - Project manifest
- `tsconfig.json` - TypeScript config
- `vite.config.ts` - Vite build config
- `next.config.js` - Next.js config
- `bun.lockb` or `pnpm-lock.yaml` or `package-lock.json`

## Common Workflows

### Install Dependencies
```bash
# Prefer bun, fallback to pnpm/npm
bun install 2>&1 || pnpm install 2>&1 || npm install 2>&1
```

### Type Check
```bash
bun run tsc --noEmit 2>&1
# or
npx tsc --noEmit 2>&1
```

### Run Tests
```bash
bun test 2>&1
# or with vitest
bun run vitest run 2>&1
```

### Build
```bash
bun run build 2>&1
```

### Lint
```bash
bun run lint 2>&1
# or directly
eslint . 2>&1
```

### Format Check
```bash
prettier --check . 2>&1
# or with biome
biome check . 2>&1
```

## Framework-Specific

### Vite Projects
```bash
bun run dev      # Development server
bun run build    # Production build
bun run preview  # Preview production build
```

### Next.js Projects
```bash
bun run dev      # Development
bun run build    # Build
bun run start    # Production server
```

## Error Handling

- TypeScript errors show file:line:column format
- ESLint errors include rule names
- Check `node_modules` exists before running scripts

## Best Practices

1. Always run type check before committing
2. Use `bun` for faster execution when possible
3. Check for lockfile type to determine package manager
4. Run `lint` and `format` before commits

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ardaglobal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
