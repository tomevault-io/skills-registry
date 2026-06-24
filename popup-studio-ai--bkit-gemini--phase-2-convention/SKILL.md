---
name: phase-2-convention
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 2: Convention Definition

> Establish coding standards and conventions

## Categories

### 1. Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Components | PascalCase | `UserProfile.tsx` |
| Functions | camelCase | `getUserById()` |
| Constants | UPPER_SNAKE | `MAX_RETRY_COUNT` |
| Files | kebab-case | `user-service.ts` |
| CSS Classes | kebab-case | `user-card` |

### 2. File Structure

```
src/
├── components/     # UI components
├── hooks/          # Custom hooks
├── lib/            # Utilities
├── services/       # API services
├── types/          # TypeScript types
└── utils/          # Helper functions
```

### 3. Git Conventions

```
feat: Add user login
fix: Resolve password reset bug
docs: Update README
style: Format code
refactor: Extract auth service
test: Add login tests
chore: Update dependencies
```

### 4. Code Style

- Max line length: 100
- Indentation: 2 spaces
- Quotes: Single quotes
- Semicolons: Required
- Trailing commas: ES5

## Output

Save to: `docs/01-plan/conventions.md`

## Next Phase

After completion: `/phase-3-mockup`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
