---
name: upgrade-nextjs
description: Guided Next.js upgrade workflow for this portfolio project. Triggers on 'upgrade Next.js', 'update Next.js', 'migrate Next.js', 'bump Next.js', or 'update dependencies'. Encodes the exact upgrade order, breaking changes, and project-specific pitfalls discovered during past migrations. Use when this capability is needed.
metadata:
  author: tipyaf
---

# Upgrade Next.js

## Overview

Step-by-step workflow for upgrading Next.js and its ecosystem in this portfolio project (Next.js + React + next-intl + Sanity + framer-motion + ESLint + Tailwind). This skill encodes lessons learned from past migrations to avoid known pitfalls.

Check `references/migration-history.md` for details on past upgrades.

## Pre-flight Checks

Before starting any upgrade:

1. **Check current versions**: `cat package.json | grep -E "next|react|sanity|framer|eslint|next-intl|tailwind"`
2. **Check Node.js version**: `node -v` (Sanity 5+ requires Node.js >=20.19)
3. **Ensure clean git state**: `git status` — commit or stash any pending changes
4. **Create upgrade branch**: `git checkout -b core/update-nextjs` (or similar)
5. **Check for official codemods**: `npx @next/codemod@latest` (Next.js provides codemods for major version upgrades)

## Upgrade Order

Upgrade dependencies in this exact order to avoid peer dependency conflicts. Run `npm install` after each group.

### Group 1: Core (Next.js + React)

```bash
npm install next@latest react@latest react-dom@latest
npm install -D @types/react@latest @types/react-dom@latest @types/node@latest
```

These must be upgraded together — Next.js major versions require matching React versions.

### Group 2: Sanity Ecosystem

```bash
npm install sanity@latest next-sanity@latest @sanity/image-url@latest @sanity/vision@latest
npm install -D @portabletext/react@latest
```

Sanity packages have tight peer dependency ranges. Upgrade them as a group, **after** React is already at the required version.

### Group 3: Framer Motion

```bash
npm install framer-motion@latest
```

Framer Motion major versions often have breaking API changes. Upgrade separately so issues are isolated.

### Group 4: ESLint + Config

```bash
npm install -D eslint@latest eslint-config-next@latest eslint-config-prettier@latest eslint-plugin-prettier@latest
```

ESLint major versions require config file format changes (e.g., `.eslintrc.json` → `eslint.config.mjs`).

### Group 5: Everything Else

```bash
npm install -D prettier-plugin-tailwindcss@latest tailwindcss@latest typescript@latest
npm install next-intl@latest @vercel/analytics@latest @vercel/speed-insights@latest usehooks-ts@latest
```

These generally have fewer breaking changes but check release notes.

## Breaking Changes Checklist

### Next.js

| Change | What to do |
|--------|-----------|
| `swcMinify` removed | Delete from `next.config.mjs` — it's the default now |
| Empty `webpack()` config | Remove if only returning `config` unchanged |
| `next lint` removed (v16+) | Change `package.json` scripts: `"lint": "eslint ."`, `"lint:fix": "eslint . --fix"` |
| `middleware.ts` → `proxy.ts` (v16+) | Rename the file. Build output shows `ƒ Proxy (Middleware)` when correct |
| Async `params` in layouts/pages | `params` is now `Promise<{...}>`. Must `await params` in layouts, pages, `generateMetadata` |
| `images.qualities` config | New option in v16+. Add `qualities: [75, 100]` if using multiple quality levels |
| `tsconfig.json` changes | Add `"jsx": "react-jsx"`, `"target": "ES2017"`, add `.next/dev/types/**/*.ts` to `include` |
| `.lintstagedrc.js` | Update from `next lint --fix --file` to `eslint --fix` |

### React 19

| Change | What to do |
|--------|-----------|
| `JSX.Element` removed from global namespace | Use `React.JSX.Element` or just `ReactNode` |
| `ref` is a regular prop | No need for `forwardRef` — ref comes as a prop directly |
| `useRef<T>(null)` returns `RefObject<T>` | Was `MutableRefObject<T\|null>`. Check ref type annotations match the element: e.g., `useRef<HTMLDivElement>(null)` for a `<div>`, NOT `<HTMLImageElement>` |

### Framer Motion 12

| Change | What to do |
|--------|-----------|
| `AnimationProps` type removed | Remove the type annotation, use plain objects for animation config |
| `motion()` → `motion.create()` | Replace `motion(Component)` with `motion.create(Component)` |
| `motion.create()` must be outside render | Move `const MotionLink = motion.create(Link)` to module scope, not inside component body |

### Sanity 5 / @sanity/image-url v2

| Change | What to do |
|--------|-----------|
| Node.js >=20.19 required | Verify with `node -v` before upgrading |
| `imageUrlBuilder` → `createImageUrlBuilder` | In `utils/url-for.ts`: `import { createImageUrlBuilder } from '@sanity/image-url'` |
| `SanityImageSource` export moved | Import from `@sanity/image-url` directly, not `@sanity/image-url/lib/types/types` |
| Dynamic `apiVersion` breaks caching | Use a fixed date: `apiVersion: '2025-01-01'` in `sanity/sanity.client.ts` |
| `@portabletext/react` v6 | Peer dep bump — install alongside sanity packages |

### ESLint 9 (Flat Config)

| Change | What to do |
|--------|-----------|
| `.eslintrc.json` → `eslint.config.mjs` | Delete `.eslintrc.json`, create flat config |
| Config format | Use `import` + array of config objects instead of `extends` |
| `eslint-config-next` | Import as `nextConfig` and spread: `...nextConfig` |
| `eslint-plugin-prettier` | Import `eslint-plugin-prettier/recommended` |
| `// eslint-disable-next-line` comments | May become unnecessary — review and remove stale ones |

**Template for `eslint.config.mjs`:**
```js
import nextConfig from 'eslint-config-next';
import prettierPlugin from 'eslint-plugin-prettier/recommended';

const config = [
  ...nextConfig,
  prettierPlugin,
  {
    ignores: ['.next/', 'node_modules/'],
  },
];

export default config;
```

### next-intl

next-intl generally stays compatible across Next.js versions. Key things to check:

| Change | What to do |
|--------|-----------|
| `hasLocale()` helper | Prefer over manual `includes()` check for locale validation |
| `setRequestLocale()` | Still required for static rendering in layouts/pages |
| Import reorg | `hasLocale` from `next-intl`, `getRequestConfig`/`setRequestLocale` from `next-intl/server` |

## Project-Specific Files to Check

These files need manual review during any upgrade:

| File | What to check |
|------|---------------|
| `next.config.mjs` | Removed/renamed options, plugin compatibility |
| `proxy.ts` (was `middleware.ts`) | Still recognized by Next.js, i18n routing works |
| `eslint.config.mjs` (was `.eslintrc.json`) | Flat config format, plugins load correctly |
| `.lintstagedrc.js` | Lint command matches new setup (`eslint` vs `next lint`) |
| `package.json` scripts | `lint`/`lint:fix` commands updated |
| `tsconfig.json` | `jsx`, `target`, `include` paths match Next.js expectations |
| `app/[locale]/layout.tsx` | `params` is async, metadata generation works |
| `utils/url-for.ts` | Image URL builder API matches `@sanity/image-url` version |
| `sanity/sanity.client.ts` | `apiVersion` is a fixed date string |
| `components/utils/Button.tsx` | `motion.create()` at module scope |
| `components/utils/AnimatedPortrait.tsx` | No `AnimationProps` type usage |
| `components/ProjectCard.tsx` | `useRef` type matches actual element |

## Verification

After completing all upgrades:

1. **Run `/verify-site`** — This checks lint, build, TypeScript, all routes, i18n, hreflang, and Studio isolation
2. **Start dev server and test manually**: `npm run dev`
   - `http://localhost:3000/` → 200 (EN)
   - `http://localhost:3000/fr` → 200 (FR)
   - `http://localhost:3000/en` → 307 redirect to `/`
   - `http://localhost:3000/studio` → Sanity Studio loads
3. **Check browser console** for runtime errors or warnings
4. **Check build output** for the `ƒ Proxy (Middleware)` line confirming proxy.ts is recognized

## Post-Upgrade

1. **Update migration history**: Append an entry to `references/migration-history.md`
2. **Update README.md**: If tech stack versions, features, or architecture changed
3. **Update CLAUDE.md**: If commands, architecture, or conventions changed
4. **Update MEMORY.md**: If new patterns or learnings should be remembered

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tipyaf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
