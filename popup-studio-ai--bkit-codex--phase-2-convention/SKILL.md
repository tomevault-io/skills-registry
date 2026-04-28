---
name: phase-2-convention
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 2: Coding Conventions

> Establish coding standards before writing application code.

## Purpose

Phase 2 sets the rules everyone (human and AI) follows. Consistent conventions prevent confusion and reduce code review friction.

## Actions

| Action | Description | Example |
|--------|-------------|---------|
| `start` | Begin Phase 2 | `$phase-2-convention start` |
| `generate` | Generate convention doc | `$phase-2-convention generate` |
| `lint` | Check convention compliance | `$phase-2-convention lint` |

## Deliverables

1. **Convention Document** - `docs/01-plan/convention.md`
2. **ESLint/Prettier Config** - Automated enforcement
3. **File Naming Rules** - Directory and file naming patterns
4. **Component Naming Rules** - UI component conventions
5. **Git Commit Conventions** - Commit message format

## Process

### Step 1: Language & Framework Conventions

```markdown
## TypeScript Conventions
- Strict mode enabled
- No `any` type (use `unknown` if needed)
- Interfaces over Types for object shapes
- Enum for fixed value sets
- Async/await over .then() chains
```

### Step 2: Naming Rules

| Element | Convention | Example |
|---------|-----------|---------|
| Files (components) | PascalCase | `UserProfile.tsx` |
| Files (utilities) | camelCase | `formatDate.ts` |
| Files (styles) | kebab-case | `user-profile.module.css` |
| Variables | camelCase | `userName`, `isActive` |
| Constants | UPPER_SNAKE | `MAX_RETRY_COUNT` |
| Types/Interfaces | PascalCase | `UserProfile`, `ApiResponse` |
| Hooks | camelCase with use | `useAuth`, `useQuery` |
| Components | PascalCase | `UserCard`, `NavBar` |
| API routes | kebab-case | `/api/user-profile` |
| Database | snake_case | `user_profile`, `created_at` |

### Step 3: Directory Structure Rules

- Group by feature, not by type
- Max 3 levels deep
- index.ts for barrel exports
- __tests__/ next to source files

### Step 4: Git Conventions

```
<type>(<scope>): <description>

Types: feat, fix, docs, style, refactor, test, chore
Scope: component or module name
Description: imperative mood, lowercase, no period

Examples:
- feat(auth): add social login support
- fix(api): handle null response from user endpoint
```

### Step 5: Code Style Enforcement

```json
{
  "extends": ["next/core-web-vitals", "prettier"],
  "rules": {
    "no-console": "warn",
    "no-unused-vars": "error",
    "@typescript-eslint/no-explicit-any": "error"
  }
}
```

## Convention Patterns

See `references/naming-conventions.md` for detailed patterns.

## Output Location

```
docs/01-plan/
├── convention.md      # Full convention document
project/
├── .eslintrc.json     # ESLint config
├── .prettierrc        # Prettier config
└── .editorconfig      # Editor config
```

## Next Phase

When conventions are established, proceed to **$phase-3-mockup** for UI/UX design.

## Common Mistakes

| Mistake | Solution |
|---------|----------|
| No automated enforcement | Set up ESLint + Prettier from day 1 |
| Too many rules | Start minimal, add rules as needed |
| Inconsistent naming | Use a naming decision table |
| No commit conventions | Use commitlint with husky |
| Mixing conventions | One language = one convention set |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
