---
name: ng-lib
description: | Use when this capability is needed.
metadata:
  author: tdp1999
---

# Angular Library Generator

Generate Angular libraries following project conventions with proper scoping and module boundaries.

## Quick Reference

| Scope           | Directory Pattern             | Tags                                  | Import Path                         |
| --------------- | ----------------------------- | ------------------------------------- | ----------------------------------- |
| Global shared   | `libs/shared/{name}`          | `scope:shared`, `type:{name}`         | `@portfolio/shared/{name}`          |
| Landing shared  | `libs/landing/shared/{type}`  | `scope:landing`, `type:shared-{type}` | `@portfolio/landing/shared/{type}`  |
| Landing feature | `libs/landing/feature-{name}` | `scope:landing`, `type:feature`       | `@portfolio/landing/feature-{name}` |

## Workflow

1. **Gather input** - Ask user for library name and purpose
2. **Classify** - Determine scope (global-shared, landing-shared, landing-feature)
3. **Prefill** - Auto-generate all options based on classification
4. **Confirm** - Show user the command before execution
5. **Execute** - Run nx generate command

## Interactive Questions

Ask user:

1. "What is the library name?" (e.g., `projects`, `data-access`, `ui`)
2. "What is the library's purpose?" (helps classify scope)
3. "Which scope?" - Offer options based on purpose:
   - **Feature** - Landing page section (projects, skills, experience, hero)
   - **Landing Shared** - Shared within landing app (data-access, ui, util, types)
   - **Global Shared** - Shared across FE+BE (types, utils, testing)

## Generator Options

Reference: `references/generator-options.md`

### Project Defaults (from nx.json)

```
linter: eslint
unitTestRunner: jest
style: scss (for components)
standalone: true
```

### Computed Options by Scope

**Global Shared** (`libs/shared/{name}`):

```bash
nx g @nx/angular:library {name} \
  --directory=libs/shared/{name} \
  --importPath=@portfolio/shared/{name} \
  --tags="scope:shared,type:{name}" \
  --prefix=shared \
  --standalone \
  --style=scss
```

**Landing Shared** (`libs/landing/shared/{type}`):

```bash
nx g @nx/angular:library {type} \
  --directory=libs/landing/shared/{type} \
  --importPath=@portfolio/landing/shared/{type} \
  --tags="scope:landing,type:shared-{type}" \
  --prefix=landing \
  --standalone \
  --style=scss
```

**Landing Feature** (`libs/landing/feature-{name}`):

```bash
nx g @nx/angular:library feature-{name} \
  --directory=libs/landing/feature-{name} \
  --importPath=@portfolio/landing/feature-{name} \
  --tags="scope:landing,type:feature" \
  --prefix=landing \
  --standalone \
  --style=scss
```

## Post-Generation

After generating, do the following:

1. Update `tsconfig.base.json` paths if not auto-added
2. Verify tags in `project.json`
3. **Fix `tsconfig.spec.json`** — The Nx generator defaults to `"module": "commonjs"` and `"moduleResolution": "node10"`, which breaks Angular package resolution (e.g., `@angular/core/testing`). Update to:
   ```json
   "module": "es2015",
   "moduleResolution": "bundler"
   ```
   This only applies to Angular/frontend libs. Pure backend or non-Angular shared libs (types, utils, errors) can keep `node10`.
4. Run `pnpm lint` to verify module boundaries

## Module Boundary Rules

Reference: `references/module-boundaries.md` for ESLint enforce-module-boundaries configuration.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tdp1999) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
