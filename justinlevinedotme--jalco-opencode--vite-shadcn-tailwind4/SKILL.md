---
name: vite-shadcn-tailwind4
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Protocol for initializing shadcn/ui with Tailwind CSS v4 in a **Vite** project.

</overview>

<constraints>

This skill is Vite-specific due to:
- Vite's solution-style `tsconfig.json` (references pattern)
- `@tailwindcss/vite` plugin requirement
- CSS entry file conventions (`src/index.css`)

For Next.js, Remix, or other frameworks, MUST use their respective shadcn installation guides.

</constraints>

<guidelines>

## Question Tool Usage

- **Batching Rule**: MUST use only for 2+ related questions; single questions use plain text.
- **Syntax Constraints**: header MUST be max 12 chars, labels MUST be 1-5 words, SHOULD mark defaults with `(Recommended)`.
- **Purpose**: Clarify component selection, style preferences, and optional AI elements before running shadcn CLI.

</guidelines>

<workflow>

<phase name="verify-prerequisites">

## Step 1: Verify Prerequisites

Check that the project is ready:
- `vite.config.ts` MUST exist.
- `package.json` MUST contain `tailwindcss` (v4+) and `@tailwindcss/vite`.
- TypeScript MUST be configured.

If prerequisites are missing, MUST help the user set up Tailwind v4 first.

</phase>

<phase name="fix-typescript-aliases">

## Step 2: Fix TypeScript Aliases

The shadcn CLI fails if paths aren't in the root `tsconfig.json`. Vite uses a solution-style config with references, but shadcn doesn't parse those.

**Action**: MUST update `tsconfig.json` to include `compilerOptions`:

```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

</phase>

<phase name="verify-vite-config">

## Step 3: Verify Vite Config

MUST confirm `vite.config.ts` has the Tailwind plugin and path alias:

```ts
import tailwindcss from "@tailwindcss/vite";
import path from "path";

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});
```

</phase>

<phase name="initialize-shadcn">

## Step 4: Initialize Shadcn

MUST instruct the user to run:
```bash
npx shadcn@latest init
```

SHOULD recommend these settings:
- Style: New York
- Base Color: Neutral or Zinc
- CSS Variables: Yes

**MUST wait for user confirmation before continuing.**

</phase>

<phase name="add-components">

## Step 5: Add Components

MUST instruct the user to run:
```bash
npx shadcn@latest add
```

SHOULD instruct them to select all components (`a` then Enter).

**MUST wait for user confirmation before continuing.**

</phase>

<phase name="add-extensions">

## Step 6: Add Extensions (Optional)

If user wants AI elements, MUST instruct them to run:
```bash
npx shadcn@latest add @ai-elements/all
```

SHOULD instruct them to answer **NO** to all overwrite prompts.

**MUST wait for user confirmation before continuing.**

</phase>

<phase name="install-missing-dependencies">

## Step 7: Install Missing Dependencies

The CLI MAY miss dependencies. MUST check `package.json` and install any missing.

**Required packages**:
- `tw-animate-css` (devDep) - v4 replacement for `tailwindcss-animate`
- `tailwind-merge` (dep) - used by `cn()` utility
- `clsx` (dep) - used by `cn()` utility
- `class-variance-authority` (dep) - used by shadcn components

**MUST run if any are missing**:
```bash
npm install tailwind-merge clsx class-variance-authority
npm install -D tw-animate-css
```

</phase>

<phase name="clean-css">

## Step 8: Clean CSS for v4 Compliance

MUST rewrite `src/index.css` to match strict v4 structure:

```css
@import "tailwindcss";
@import "tw-animate-css";

@custom-variant dark (&:is(.dark *));

@theme inline {
  /* Variable mappings: --color-X: var(--X); */
}

:root {
  /* Token definitions using oklch() */
}

.dark {
  /* Dark mode tokens using oklch() */
}

@layer base {
  * {
    @apply border-border outline-ring/50;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

MUST remove these if present:
- `@media (prefers-color-scheme)` blocks
- Duplicate `@theme` blocks (keep only `@theme inline`)
- `@config` directives

</phase>

<phase name="verify-setup">

## Step 9: Verify Setup

MUST run typecheck to catch any issues:
```bash
npm run lint
```

MUST fix any TypeScript errors before marking setup complete.

</phase>

</workflow>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
