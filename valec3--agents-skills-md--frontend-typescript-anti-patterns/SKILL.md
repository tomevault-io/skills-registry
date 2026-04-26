---
name: frontend-typescript-anti-patterns
description: Standardized guidelines and patterns for Frontend Typescript Anti Patterns. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Typescript Anti Patterns

## When to use this skill
- Code reviews
- Refactoring TypeScript code
- Identifying problematic patterns

## Workflow
- [ ] Search for `any` usage
- [ ] Check for type assertions (`as`)
- [ ] Look for non-null assertions (`!`)
- [ ] Find untyped parameters
- [ ] Identify missing return types

## Instructions

### Anti-Pattern 1: Excessive `any`
```typescript
// ❌ Bad
function process(data: any): any {
  return data.value;
}

// ✅ Good
function process<T extends { value: unknown }>(data: T): T['value'] {
  return data.value;
}
```

### Anti-Pattern 2: Type Assertions
```typescript
// ❌ Bad
const user = data as User;

// ✅ Good
function isUser(data: unknown): data is User {
  return typeof data === 'object' && data !== null && 'id' in data;
}
const user = isUser(data) ? data : null;
```

### Anti-Pattern 3: Non-null Assertions
```typescript
// ❌ Bad
const name = user!.name;

// ✅ Good
const name = user?.name ?? 'Unknown';
```

### Anti-Pattern 4: Ignoring Strict Null Checks
```typescript
// ❌ Bad
function getName(user: User): string {
  return user.name; // Could be undefined
}

// ✅ Good
function getName(user: User): string {
  return user.name ?? 'Unknown';
}
```

## Resources
- Avoid `any` at all costs
- Use type guards instead of assertions
- Enable strict mode

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
