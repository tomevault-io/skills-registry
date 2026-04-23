---
name: biome
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

# Biome Skill

## When to Use

Use this skill when:
- Setting up Biome for the first time
- Configuring linting rules
- Setting up formatting standards
- Integrating with CI/CD
- Fixing linting errors
- Configuring import sorting
- Setting up pre-commit hooks with Biome

## Critical Patterns

### Biome Configuration

**ALWAYS use a single `biome.json` in monorepo root:**

```json
{
  "$schema": "https://biomejs.dev/schemas/1.4.1/schema.json",
  "organizeImports": {
    "enabled": true
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "correctness": {
        "noUnusedVariables": "error",
        "useExhaustiveDependencies": "warn"
      },
      "style": {
        "noNonNullAssertion": "warn",
        "useImportType": "error"
      },
      "suspicious": {
        "noExplicitAny": "error",
        "noConsoleLog": "warn"
      }
    }
  },
  "formatter": {
    "enabled": true,
    "formatWithErrors": false,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "trailingComma": "es5",
      "semicolons": "asNeeded",
      "arrowParentheses": "asNeeded"
    }
  }
}
```

### Import Organization

**Biome organizes imports automatically:**

```typescript
// Before Biome
import { useState } from 'react'
import type { Quiz } from '@/types'
import { Button } from '@/components/ui/button'
import { calculateScore } from './utils'

// After Biome (organized)
import { useState } from 'react'

import { Button } from '@/components/ui/button'
import type { Quiz } from '@/types'

import { calculateScore } from './utils'
```

**Order enforced by Biome:**
1. Side-effect imports
2. External dependencies
3. Internal absolute imports
4. Relative imports
5. Type-only imports (grouped separately)

### Linting Rules for TypeScript

**ALWAYS enable strict TypeScript rules:**

```json
{
  "linter": {
    "rules": {
      "suspicious": {
        "noExplicitAny": "error",
        "noUnsafeDeclarationMerging": "error"
      },
      "correctness": {
        "noUnusedVariables": "error",
        "useExhaustiveDependencies": "warn"
      },
      "style": {
        "useImportType": "error",
        "noNonNullAssertion": "warn"
      }
    }
  }
}
```

### Formatter Configuration

**Consistent formatting across packages:**

```json
{
  "formatter": {
    "indentWidth": 2,
    "lineWidth": 100,
    "indentStyle": "space"
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "semicolons": "asNeeded",
      "trailingComma": "es5",
      "arrowParentheses": "asNeeded"
    }
  }
}
```

**Why these choices:**
- `quoteStyle: "single"` - Consistent with most JS projects
- `semicolons: "asNeeded"` - Clean code, ASI-safe
- `trailingComma: "es5"` - Better git diffs
- `arrowParentheses: "asNeeded"` - Cleaner arrow functions
- `lineWidth: 100` - Balance readability and screen width

## Code Examples

### Package Scripts

**Add to each package.json:**

```json
{
  "scripts": {
    "lint": "biome check .",
    "lint:fix": "biome check --apply .",
    "format": "biome format --write .",
    "format:check": "biome format .",
    "check": "biome check --apply ."
  }
}
```

**Root package.json (runs on all packages):**

```json
{
  "scripts": {
    "lint": "turbo run lint",
    "lint:fix": "turbo run lint:fix",
    "format": "turbo run format",
    "format:check": "turbo run format:check"
  }
}
```

### CI Integration

**GitHub Actions example:**

```yaml
name: Code Quality

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      
      - run: pnpm install
      - run: pnpm lint
      - run: pnpm format:check
```

### Pre-commit Hooks

**Using husky + lint-staged:**

```json
// package.json
{
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": [
      "biome check --apply --no-errors-on-unmatched"
    ]
  }
}
```

```bash
# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

pnpm lint-staged
```

### Ignoring Files

**biome.json:**

```json
{
  "files": {
    "ignore": [
      "node_modules",
      "dist",
      ".next",
      "build",
      "coverage",
      "*.config.js"
    ]
  }
}
```

### VSCode Integration

**.vscode/settings.json:**

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "biomejs.biome",
  "editor.codeActionsOnSave": {
    "quickfix.biome": "explicit",
    "source.organizeImports.biome": "explicit"
  },
  "[typescript]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "biomejs.biome"
  }
}
```

## Common Patterns

### Fixing Common Issues

**Issue: noExplicitAny errors**

```typescript
// ❌ Bad
function handleData(data: any) {
  return data.value
}

// ✅ Good
function handleData(data: unknown) {
  if (typeof data === 'object' && data !== null && 'value' in data) {
    return (data as { value: string }).value
  }
  throw new Error('Invalid data')
}

// ✅ Better with Zod
import { z } from 'zod'

const DataSchema = z.object({
  value: z.string(),
})

function handleData(data: unknown) {
  const parsed = DataSchema.parse(data)
  return parsed.value
}
```

**Issue: noUnusedVariables**

```typescript
// ❌ Bad
function calculateScore(answers: Answer[], quizId: string) {
  return answers.filter(a => a.isCorrect).length
  // quizId is unused
}

// ✅ Good - remove unused param
function calculateScore(answers: Answer[]) {
  return answers.filter(a => a.isCorrect).length
}

// ✅ Good - prefix with _ if intentionally unused
function calculateScore(answers: Answer[], _quizId: string) {
  return answers.filter(a => a.isCorrect).length
}
```

**Issue: useImportType**

```typescript
// ❌ Bad
import { Quiz } from '@/types'

const quiz: Quiz = { ... }

// ✅ Good
import type { Quiz } from '@/types'

const quiz: Quiz = { ... }
```

### Auto-fix Workflow

```bash
# 1. Check issues
pnpm lint

# 2. Auto-fix what can be fixed
pnpm lint:fix

# 3. Format code
pnpm format

# 4. Check if there are remaining issues
pnpm lint
```

## Commands Reference

```bash
# Check linting (no changes)
biome check .

# Check and auto-fix
biome check --apply .

# Format files (write)
biome format --write .

# Format files (check only)
biome format .

# Check specific files
biome check src/**/*.ts

# Run with detailed output
biome check --verbose .

# Check with specific config
biome check --config-path ./custom-biome.json .
```

## Best Practices

### ALWAYS:
- Run `biome check --apply` before committing
- Use `noExplicitAny: "error"` to prevent `any` types
- Enable `organizeImports` for consistent import order
- Configure pre-commit hooks to run Biome
- Use single `biome.json` in monorepo root

### NEVER:
- Disable rules without documenting why
- Use `// @ts-ignore` instead of `// biome-ignore`
- Commit code that fails `biome check`
- Mix Biome with other formatters (Prettier, ESLint)
- Use `any` type (Biome will catch this)

### Biome vs ESLint/Prettier

**Why Biome:**
- ✅ **Fast:** 10-100x faster than ESLint
- ✅ **All-in-one:** Linter + Formatter in one tool
- ✅ **Zero config:** Works out of the box
- ✅ **TypeScript-first:** Native TS support
- ✅ **Import sorting:** Built-in organize imports

**Migration from ESLint/Prettier:**
1. Remove ESLint and Prettier dependencies
2. Remove `.eslintrc.*` and `.prettierrc.*` files
3. Add `biome.json` configuration
4. Update scripts to use Biome
5. Update pre-commit hooks
6. Update CI/CD pipelines

## Monorepo Setup

**Root biome.json (shared config):**

```json
{
  "$schema": "https://biomejs.dev/schemas/1.4.1/schema.json",
  "organizeImports": { "enabled": true },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  },
  "formatter": {
    "enabled": true,
    "indentWidth": 2,
    "lineWidth": 100
  }
}
```

**Package-specific overrides (if needed):**

```json
// frontend/biome.json
{
  "extends": ["../biome.json"],
  "linter": {
    "rules": {
      "suspicious": {
        "noConsoleLog": "off"  // Allow console in frontend dev
      }
    }
  }
}
```

## Troubleshooting

### Issue: Biome not formatting on save

**Solution:**
1. Install Biome VSCode extension
2. Set as default formatter in `.vscode/settings.json`
3. Enable format on save

### Issue: Import organization not working

**Solution:**
```json
{
  "organizeImports": {
    "enabled": true
  }
}
```

Run: `biome check --apply .`

### Issue: Conflicts with existing ESLint config

**Solution:**
1. Remove ESLint completely or
2. Use ESLint only for custom rules Biome doesn't support
3. Don't mix formatters

## Resources

- **Biome Docs:** https://biomejs.dev
- **Configuration:** https://biomejs.dev/reference/configuration/
- **VSCode Extension:** https://marketplace.visualstudio.com/items?itemName=biomejs.biome
- **Migration Guide:** https://biomejs.dev/guides/migrate-eslint-prettier/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
