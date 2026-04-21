---
name: typescript-clean-code
description: Use when writing, fixing, editing, reviewing, or refactoring any TypeScript code. Enforces Robert Martin's complete Clean Code catalog—naming, functions, comments, DRY, and boundary conditions.
metadata:
  author: bartoszkwiat
---

# Clean TypeScript: Complete Reference

Enforces all Clean Code principles from Robert C. Martin's Chapter 17, adapted for TypeScript.

## Comments (C1-C5)
- C1: No metadata in comments (use Git)
- C2: Delete obsolete comments immediately
- C3: No redundant comments
- C4: Write comments well if you must
- C5: Never commit commented-out code

## Environment (E1-E2)
- E1: One command to build (`npm install` or `pnpm install`)
- E2: One command to test (`npm test` or `pnpm test`)

## Functions (F1-F4)
- F1: Maximum 3 arguments (use interfaces for more)
- F2: No output arguments (return values)
- F3: No flag arguments (split functions)
- F4: Delete dead functions

## General (G1-G36)
- G1: One language per file
- G2: Implement expected behavior
- G3: Handle boundary conditions
- G4: Don't override safeties
- G5: DRY - no duplication
- G6: Consistent abstraction levels
- G7: Base classes don't know children
- G8: Minimize public interface
- G9: Delete dead code
- G10: Variables near usage
- G11: Be consistent
- G12: Remove clutter
- G13: No artificial coupling
- G14: No feature envy
- G15: No selector arguments
- G16: No obscured intent
- G17: Code where expected
- G18: Prefer instance methods
- G19: Use explanatory variables
- G20: Function names say what they do
- G21: Understand the algorithm
- G22: Make dependencies physical
- G23: Prefer polymorphism to if/else
- G24: Follow conventions (ESLint, Prettier)
- G25: Named constants, not magic numbers
- G26: Be precise
- G27: Structure over convention
- G28: Encapsulate conditionals
- G29: Avoid negative conditionals
- G30: Functions do one thing
- G31: Make temporal coupling explicit
- G32: Don't be arbitrary
- G33: Encapsulate boundary conditions
- G34: One abstraction level per function
- G35: Config at high levels
- G36: Law of Demeter (no train wrecks)

## TypeScript-Specific (TS1-TS10)
These adapt the Java-specific rules (J1-J3) to TypeScript conventions:
- TS1: Use explicit types on public interfaces — TypeScript's equivalent of Java's static typing
- TS2: Use Enums or const objects, not magic constants — same principle as J3
- TS3: Prefer named exports over default exports — clearer imports and better refactoring
- TS4: Always add explicit types everywhere — never rely on type inference for variables, parameters, return types, or callbacks
- TS5: Return statement formatting — ONLY `return;` (no value) inline, `return value` always with braces
- TS6: Interfaces in separate files — never mix interfaces with functions in the same file
- TS7: No underscore for unused parameters — use descriptive name or omit entirely
- TS8: Use `undefined` instead of `null` — consistent absence representation
- TS9: Never use `any` — use `unknown`, generics, or proper types
- TS10: No destructuring — access properties directly via dot notation
- TS11: No single-letter variables — use descriptive names in loops, map, filter, reduce

```typescript
// TS5: Empty return (guard clause) — single line, no braces
// Bad
if (data === undefined) {
    return;
}

// Good - ONLY when return has NO value
if (data === undefined) return;

// TS5: Return with value — ALWAYS use braces (NEVER inline!)
// Bad - returns with values should NEVER be inline
if (data === undefined) return false;
if (items.length === 0) return [];
if (foundIds.has(id)) return false;

// Good - returns with values ALWAYS get braces
if (data === undefined) {
    return false;
}
if (items.length === 0) {
    return [];
}
if (foundIds.has(id)) {
    return false;
}

// TS6: Interfaces in separate files
// Bad - mixing interfaces with functions
// file: userService.ts
interface User {
    id: string;
    name: string;
}
const createUser = (name: string): User => { /* ... */ };

// Good - separate files
// file: types/user.ts
interface User {
    id: string;
    name: string;
}
// file: userService.ts
import { User } from './types/user';
const createUser = (name: string): User => { /* ... */ };

// TS7: No underscore for unused parameters
// Bad
const handleEvent: EventHandler = async (_, context): Promise<void> => {
    await processContext(context);
};

// Good - use descriptive name even if unused
const handleEvent: EventHandler = async (event, context): Promise<void> => {
    await processContext(context);
};

// TS8: Use undefined instead of null
// Bad
const findUser = (id: string): User | null => {
    return users.get(id) ?? null;
};

// Good
const findUser = (id: string): User | undefined => {
    return users.get(id);
};

// TS9: Never use any
// Bad
const processData = (data: any): any => { /* ... */ };

// Good - use unknown and narrow, or generics
const processData = <T>(data: T): T => { /* ... */ };
const parseInput = (data: unknown): ParsedData => {
    if (!isValidInput(data)) {
        throw new Error("Invalid input");
    }
    return data as ParsedData;
};

// TS10: No destructuring — use dot notation
// Bad
const { x, y, z } = vector;
const distance: number = Math.sqrt(x * x + y * y + z * z);

const { name, email, age } = user;
console.log(name, email, age);

// Good - access properties directly
const distance: number = Math.sqrt(
    vector.x * vector.x + vector.y * vector.y + vector.z * vector.z
);

console.log(user.name, user.email, user.age);

// TS11: No single-letter variables — use descriptive names
// Bad
users.map((u) => u.name);
items.filter((i) => i.active);
numbers.reduce((a, b) => a + b, 0);
for (const x of events) { /* ... */ }

// Good
users.map((user: User): string => user.name);
items.filter((item: Item): boolean => item.active);
numbers.reduce((sum: number, value: number): number => sum + value, 0);
for (const event of events) { /* ... */ }
```

## Names (N1-N7)
- N1: Choose descriptive names
- N2: Right abstraction level
- N3: Use standard nomenclature
- N4: Unambiguous names
- N5: Name length matches scope
- N6: No encodings
- N7: Names describe side effects

## Tests (T1-T9)
- T1: Test everything that could break
- T2: Use coverage tools
- T3: Don't skip trivial tests
- T4: Ignored test = ambiguity question
- T5: Test boundary conditions
- T6: Exhaustively test near bugs
- T7: Look for patterns in failures
- T8: Check coverage when debugging
- T9: Tests must be fast (< 100ms each)

## Quick Reference Table

| Category | Rule | One-Liner |
|----------|------|-----------|
| **Comments** | C1 | No metadata (use Git) |
| | C3 | No redundant comments |
| | C5 | No commented-out code |
| **Functions** | F1 | Max 3 arguments |
| | F3 | No flag arguments |
| | F4 | Delete dead functions |
| **General** | G5 | DRY—no duplication |
| | G9 | Delete dead code |
| | G16 | No obscured intent |
| | G23 | Polymorphism over if/else |
| | G25 | Named constants, not magic numbers |
| | G30 | Functions do one thing |
| | G36 | Law of Demeter (one dot) |
| **Names** | N1 | Descriptive names |
| | N5 | Name length matches scope |
| **Tests** | T5 | Test boundary conditions |
| | T9 | Tests must be fast |
| **TypeScript** | TS1 | Explicit types on public interfaces |
| | TS4 | Always add explicit types everywhere |
| | TS5 | ONLY `return;` inline, `return value` with braces |
| | TS6 | Interfaces in separate files |
| | TS7 | No underscore for unused parameters |
| | TS8 | Use `undefined` not `null` |
| | TS9 | Never use `any` |
| | TS10 | No destructuring, use dot notation |
| | TS11 | No single-letter variables |

## Anti-Patterns (Don't → Do)

| ❌ Don't | ✅ Do |
|----------|-------|
| Comment every line | Delete obvious comments |
| Helper for one-liner | Inline the code |
| `import * as x` | Explicit named imports |
| Magic number `86400` | `const SECONDS_PER_DAY: number = 86400` |
| `process(data, true)` | `processVerbose(data)` |
| Deep nesting | Guard clauses, early returns |
| `obj.a.b.c.value` | `obj.getValue()` |
| 100+ line function | Split by responsibility |
| `const x = 5` | `const x: number = 5` |
| `(val) => val > 0` | `(val: number): boolean => val > 0` |
| `if (x) { return; }` | `if (x) return;` |
| `if (x) return false;` | `if (x) { return false; }` |
| Interface + function in one file | Separate `types/` files for interfaces |
| `(_, ctx) => ...` | `(event, ctx) => ...` |
| `User \| null` | `User \| undefined` |
| `data: any` | `data: unknown` or generics |
| `const { x, y } = obj` | `obj.x`, `obj.y` |
| `.map((u) => ...)` | `.map((user: User) => ...)` |
| `.reduce((a, b) => ...)` | `.reduce((sum, value) => ...)` |

## AI Behavior

When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").
When fixing or editing code, report what was fixed (e.g., "Fixed: extracted magic number to `SECONDS_PER_DAY` (G25)").

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bartoszkwiat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
