---
name: fsd-check
description: Validate Feature-Sliced Design architecture compliance. Checks layer imports, public API usage, and cross-slice violations. Use when asked to "check FSD", "validate architecture", "check imports", or "FSD audit". Use when this capability is needed.
metadata:
  author: riedel28
---

# FSD Architecture Checker

Validates Feature-Sliced Design compliance for this codebase.

## FSD Layer Hierarchy

The project follows strict FSD methodology:

```
app → pages → widgets → features → entities → shared
```

**Rules:**

- Layers can ONLY import from layers below them
- No cross-imports within the same layer (features cannot import other features)
- All imports must go through public API (`index.ts`)
- Use absolute imports with `@/*` alias

## Checks Performed

### 1. Layer Import Violations

Check that imports respect the hierarchy:

- `app` can import: pages, widgets, features, entities, shared
- `pages` can import: widgets, features, entities, shared
- `widgets` can import: features, entities, shared
- `features` can import: entities, shared
- `entities` can import: shared, other entities (same layer allowed)
- `shared` can only import: shared

### 2. Public API Violations

All imports from `entities`, `features`, `pages`, `widgets` must go through `index.ts`

```typescript
// CORRECT
import { Article } from '@/entities/Article';

// WRONG - bypasses public API
import { Article } from '@/entities/Article/model/types';
```

### 3. Cross-Slice Violations

Within the same layer, slices should not import each other directly:

```typescript
// In features/AuthByUsername - WRONG
import { something } from '@/features/AnotherFeature';
```

### 4. Missing Public API

Check if slices have proper `index.ts` exports.

## How to Run Checks

### Option A: Run ESLint FSD Rules

```bash
npx eslint "src/**/*.{ts,tsx}" --rule 'ulbi-tv-plugin/layer-imports: error' --rule 'ulbi-tv-plugin/public-api-imports: error' --rule 'ulbi-tv-plugin/path-checker: error'
```

### Option B: Manual Analysis

1. Use Grep to find problematic import patterns
2. Check specific layers or slices

## Analysis Workflow

When user invokes `/fsd-check`:

1. **If argument provided** (e.g., `/fsd-check features`):
   - Focus analysis on that specific layer or path
   - Run targeted checks

2. **If no argument**:
   - Ask user what to check: full codebase, specific layer, or specific slice
   - Or run quick overview of all layers

3. **For each check**:
   - Search for violation patterns using Grep
   - Report findings in format: `file:line - violation description`

4. **Output summary**:
   - Total violations by category
   - Most problematic files/slices
   - Suggested fixes

## Common Violation Patterns to Search

### Layer violations (Grep patterns)

```
# Features importing from pages/widgets
path: src/features/
pattern: from ['"]@/(pages|widgets)/

# Entities importing from features/pages/widgets
path: src/entities/
pattern: from ['"]@/(features|pages|widgets)/

# Shared importing from any other layer
path: src/shared/
pattern: from ['"]@/(entities|features|pages|widgets|app)/
```

### Public API violations

```
# Deep imports into slices (more than 2 segments after @/)
pattern: from ['"]@/(entities|features|pages|widgets)/[^'"]+/[^'"]+
```

### Cross-slice violations

```
# Feature importing another feature (check current file path vs import)
# This requires analyzing file location context
```

## Output Format

Report findings as:

```
## FSD Violations Found

### Layer Import Violations (X found)
- `src/features/Auth/ui/LoginForm.tsx:15` - features cannot import from widgets
- `src/entities/User/model/slice.ts:3` - entities cannot import from features

### Public API Violations (X found)
- `src/widgets/Sidebar/ui/Sidebar.tsx:8` - import should be `@/entities/User` not `@/entities/User/model/types`

### Cross-Slice Violations (X found)
- `src/features/Auth/ui/Form.tsx:12` - feature should not import from another feature

### Summary
- Total: X violations
- Critical: X (layer violations)
- Fixable: X (public API - has autofix via ESLint)
```

## Quick Commands

For the user's reference:

```bash
# Run all FSD ESLint rules
npm run lint:ts -- --rule 'ulbi-tv-plugin/layer-imports: error'

# Check specific layer
npx eslint "src/features/**/*.{ts,tsx}" --rule 'ulbi-tv-plugin/layer-imports: error'

# Auto-fix public API imports
npx eslint "src/**/*.{ts,tsx}" --rule 'ulbi-tv-plugin/public-api-imports: error' --fix
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riedel28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
