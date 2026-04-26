---
name: frontend-typescript-types-advanced
description: Standardized guidelines and patterns for Frontend Typescript Types Advanced. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Typescript Types Advanced

## When to use this skill
- Building type-safe libraries
- Creating complex type utilities
- Advanced type constraints

## Workflow
- [ ] Use generics for reusable types
- [ ] Apply conditional types when needed
- [ ] Leverage mapped types
- [ ] Use template literal types
- [ ] Implement branded types for safety

## Instructions

### Conditional Types
```typescript
type IsString<T> = T extends string ? true : false;

type A = IsString<string>; // true
type B = IsString<number>; // false
```

### Mapped Types
```typescript
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type Optional<T> = {
  [P in keyof T]?: T[P];
};
```

### Template Literal Types
```typescript
type EventName = 'click' | 'focus' | 'blur';
type Handler = `on${Capitalize<EventName>}`;
// 'onClick' | 'onFocus' | 'onBlur'
```

### Utility Types
```typescript
// Pick
type UserPreview = Pick<User, 'id' | 'name'>;

// Omit
type UserWithoutPassword = Omit<User, 'password'>;

// Partial
type PartialUser = Partial<User>;

// Required
type RequiredUser = Required<PartialUser>;

// Record
type Roles = Record<string, Permission[]>;
```

### Branded Types
```typescript
type UserId = string & { __brand: 'UserId' };
type ProductId = string & { __brand: 'ProductId' };

function getUser(id: UserId) { }
function getProduct(id: ProductId) { }

const userId = 'user-123' as UserId;
getUser(userId); // OK
// getProduct(userId); // Error
```

## Resources
- Use generics for flexibility
- Conditional types for logic
- Mapped types for transformations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
