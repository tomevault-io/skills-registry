---
name: frontend-typescript-standards
description: Standardized guidelines and patterns for Frontend Typescript Standards. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Typescript Standards

## When to use this skill
- Setting up TypeScript in a new project
- Establishing coding standards
- Code reviews for TypeScript quality

## Workflow
- [ ] Enable strict mode in tsconfig.json
- [ ] Use interfaces for object shapes
- [ ] Prefer `type` for unions and intersections
- [ ] Always type function parameters and returns
- [ ] Avoid `any`, use `unknown` instead

## Instructions

### tsconfig.json
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### Naming Conventions
- Interfaces: PascalCase with `I` prefix optional
- Types: PascalCase
- Enums: PascalCase
- Generics: Single capital letter or PascalCase

### Type vs Interface
```typescript
// Use interface for object shapes
interface User {
  id: string;
  name: string;
}

// Use type for unions, intersections
type Status = 'pending' | 'active' | 'inactive';
type Admin = User & { role: 'admin' };
```

## Resources
- Enable all strict flags
- Type everything explicitly
- Use utility types (Partial, Pick, Omit)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
