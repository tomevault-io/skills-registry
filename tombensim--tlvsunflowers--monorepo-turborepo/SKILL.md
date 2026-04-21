---
name: monorepo-turborepo
description: pnpm workspaces + Turborepo patterns for TypeScript monorepos. This skill should be used when configuring the monorepo, adding packages, setting up build pipelines, or managing shared dependencies. Triggers on tasks involving pnpm workspaces, turbo, shared packages, build configuration, or CI/CD setup. Use when this capability is needed.
metadata:
  author: tombensim
---

# Monorepo with pnpm + Turborepo

Best practices for managing a TypeScript monorepo with pnpm workspaces and Turborepo.

## When to Apply

Reference these guidelines when:

- Scaffolding the monorepo structure
- Adding new packages or apps
- Configuring Turborepo task pipelines
- Setting up shared TypeScript types and Zod schemas
- Managing dependencies across packages
- Configuring CI/CD build steps

## Project Structure

```
tennis-team/
├── pnpm-workspace.yaml
├── turbo.json
├── package.json              # Root: devDependencies + scripts
├── tsconfig.base.json        # Shared TS config
├── packages/
│   └── shared/               # Shared types + Zod schemas
│       ├── package.json
│       ├── tsconfig.json
│       └── src/
│           ├── types/        # TypeScript interfaces
│           ├── schemas/      # Zod validation schemas
│           └── index.ts      # Barrel export
├── apps/
│   ├── backend/
│   │   ├── package.json
│   │   ├── tsconfig.json     # extends ../../tsconfig.base.json
│   │   └── src/
│   └── frontend/
│       ├── package.json
│       ├── tsconfig.json
│       └── src/
└── docker/
```

## Rules

### Workspace Configuration

#### workspace-pnpm-yaml

Define workspace packages in `pnpm-workspace.yaml`.

```yaml
packages:
  - 'packages/*'
  - 'apps/*'
```

#### workspace-shared-deps

Install shared devDependencies at the root. Package-specific deps go in each package.

```bash
# Root devDependencies
pnpm add -Dw typescript eslint prettier vitest

# App-specific dependencies
pnpm add --filter @tennis/backend express pg
pnpm add --filter @tennis/frontend react react-dom
```

### Turborepo Configuration

#### turbo-task-pipeline

Configure turbo.json with correct task dependencies.

```json
{
  "$schema": "https://turbo.build/schema.json",
  "globalDependencies": [".env"],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "dev": {
      "dependsOn": ["^build"],
      "cache": false,
      "persistent": true
    },
    "test": {
      "dependsOn": ["build"],
      "cache": false
    },
    "test:unit": {
      "dependsOn": ["^build"]
    },
    "lint": {},
    "typecheck": {
      "dependsOn": ["^build"]
    },
    "clean": {
      "cache": false
    }
  }
}
```

Key: `^build` means "build my dependencies first". `build` without `^` means "my own build task".

### Shared Package

#### shared-package-exports

Export types and schemas from the shared package with proper package.json configuration.

```json
{
  "name": "@tennis/shared",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "exports": {
    ".": {
      "import": "./dist/index.js",
      "types": "./dist/index.d.ts"
    },
    "./schemas": {
      "import": "./dist/schemas/index.js",
      "types": "./dist/schemas/index.d.ts"
    },
    "./types": {
      "import": "./dist/types/index.js",
      "types": "./dist/types/index.d.ts"
    }
  },
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch"
  }
}
```

#### shared-zod-schemas

Define Zod schemas in the shared package. Use them for both frontend form validation and backend request validation.

```typescript
// packages/shared/src/schemas/event.schema.ts
import { z } from 'zod';

export const createEventSchema = z.object({
  title: z.string().min(1).max(100),
  courtId: z.string().uuid(),
  startTime: z.string().datetime(),
  endTime: z.string().datetime(),
  capacity: z.number().int().min(2).max(50),
  skillLevelMin: z.number().min(1).max(7).optional(),
  skillLevelMax: z.number().min(1).max(7).optional(),
  costPerPlayer: z.number().min(0).optional(),
  isRecurring: z.boolean().default(false),
  recurrenceRule: z.string().optional(),
});

export type CreateEventInput = z.infer<typeof createEventSchema>;
```

### TypeScript Configuration

#### tsconfig-base

Use a shared base tsconfig extended by all packages.

```json
// tsconfig.base.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "resolveJsonModule": true
  }
}
```

```json
// apps/backend/tsconfig.json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"],
  "references": [{ "path": "../../packages/shared" }]
}
```

### Scripts

#### scripts-root-package

Define common scripts at the root for convenience.

```json
{
  "scripts": {
    "dev": "turbo dev",
    "build": "turbo build",
    "test": "turbo test",
    "test:unit": "turbo test:unit",
    "lint": "turbo lint",
    "typecheck": "turbo typecheck",
    "clean": "turbo clean",
    "db:migrate": "pnpm --filter @tennis/backend run db:migrate",
    "db:seed": "pnpm --filter @tennis/backend run db:seed",
    "docker:dev": "docker compose -f docker/docker-compose.yml -f docker/docker-compose.dev.yml up",
    "docker:infra": "docker compose -f docker/docker-compose.yml up postgres redis"
  }
}
```

### Dev Tooling

#### tooling-lint-staged

Configure Husky + lint-staged for pre-commit quality checks.

```json
// package.json (root)
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md,yml,yaml}": ["prettier --write"]
  }
}
```

```bash
# .husky/pre-commit
pnpm lint-staged
```

### CI/CD

#### ci-turbo-cache

Use Turborepo's remote caching or local caching in CI for faster builds.

```yaml
# .github/workflows/test.yml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm build
      - run: pnpm test
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tombensim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
