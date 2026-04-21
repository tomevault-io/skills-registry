---
name: clean-names
description: Use when naming, renaming, or fixing names of variables, functions, classes, or modules in TypeScript. Enforces Clean Code principles—descriptive names, appropriate length, no encodings.
metadata:
  author: bartoszkwiat
---

# Clean Names

## N1: Choose Descriptive Names

Names should reveal intent. If a name requires a comment, it doesn't reveal its intent.

```typescript
// Bad - what is d?
const d = 86400;

// Good - obvious meaning
const SECONDS_PER_DAY: number = 86400;

// Bad - what does this function do?
function proc(lst: number[]): number[] {
    return lst.filter(x => x > 0);
}

// Good - intent is clear
const filterPositiveNumbers = (numbers: number[]): number[] => {
    return numbers.filter((n: number): boolean => n > 0);
};
```

## N2: Choose Names at the Appropriate Level of Abstraction

Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the class or function.

```typescript
// Bad - too implementation-specific
function getMapOfUserIdsToNames(): Map<string, string> {
    // ...
}

// Good - abstracts the data structure
const getUserDirectory = (): UserDirectory => {
    // ...
};
```

## N3: Use Standard Nomenclature Where Possible

Use terms from the domain, design patterns, or well-known conventions.

```typescript
// Good - uses pattern name
class UserFactory {
    create(data: UserData): User { /* ... */ }
}

// Good - uses domain term
const calculateAmortization = (principal: number, rate: number, term: number): number => {
    // ...
};
```

## N4: Unambiguous Names

Choose names that make the workings of a function or variable unambiguous.

```typescript
// Bad - ambiguous
function rename(old: string, newName: string): void {
    // ...
}

// Good - clear what's being renamed
const renameFile = (oldPath: string, newPath: string): void => {
    // ...
};
```

## N5: Use Longer Names for Longer Scopes

Short names are fine for tiny scopes. Longer scopes need longer, more descriptive names.

```typescript
// Good - short name for tiny scope
const total: number = numbers.reduce(
    (sum: number, value: number): number => sum + value,
    0
);

// Good - longer name for module-level constant
const MAX_RETRY_ATTEMPTS_BEFORE_FAILURE: number = 5;

// Bad - short name at module level
const MAX = 5;
```

## N6: Avoid Encodings

Don't encode type or scope information into names. Modern editors make this unnecessary.

```typescript
// Bad - Hungarian notation
const strName = "Alice";
const arrUsers: User[] = [];
const nCount = 0;

// Good - clean names
const name: string = "Alice";
const users: User[] = [];
const count: number = 0;

// Bad - interface prefix
interface IUserRepository {
    // ...
}

// Good - just name it
interface UserRepository {
    // ...
}
```

## N7: Names Should Describe Side Effects

If a function does something beyond what its name suggests, the name is misleading.

```typescript
// Bad - name doesn't mention file creation
function getConfig(): Config {
    if (!fs.existsSync(configPath)) {
        fs.writeFileSync(configPath, "{}");  // Hidden side effect!
    }
    return JSON.parse(fs.readFileSync(configPath, "utf-8"));
}

// Good - name reveals behavior
const getOrCreateConfig = (): Config => {
    if (!fs.existsSync(configPath)) {
        fs.writeFileSync(configPath, "{}");
    }
    return JSON.parse(fs.readFileSync(configPath, "utf-8"));
};
```

## Quick Reference

| Rule | Principle | Example |
|------|-----------|---------|
| N1 | Descriptive names | `SECONDS_PER_DAY` not `d` |
| N2 | Right abstraction level | `getUserDirectory()` not `getMapOf...` |
| N3 | Standard nomenclature | `UserFactory`, `calculateAmortization` |
| N4 | Unambiguous | `renameFile(oldPath, newPath)` |
| N5 | Length matches scope | Short for loops, long for globals |
| N6 | No encodings | `users` not `arrUsers` |
| N7 | Describe side effects | `getOrCreateConfig()` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bartoszkwiat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
