---
name: linting-standards
description: Type safety standards and linting philosophy for TypeScript/Wasp projects. 2-tier approach (production strict, tests pragmatic). Helper patterns, mock patterns, when 'any' is acceptable vs cheating. Use when this capability is needed.
metadata:
  author: toonvos
---

# Linting Standards & Type Safety

**Philosophy:** Type safety in production code (0 tolerance), pragmatism in test infrastructure.

**Reference:** Full documentation at [docs/LINTING-STANDARDS.md](../../docs/LINTING-STANDARDS.md)

---

## Quick Reference

### 2-Tier Standard

| Context         | Standard  | `any` Usage         | Why                      |
| --------------- | --------- | ------------------- | ------------------------ |
| Production code | Strict    | 0 preferred         | Type safety critical     |
| Tests/mocks     | Pragmatic | With inline comment | Practicality over purity |

### Decision Trees

**Helper Function Pattern:**

1. Needs 1-2 entities? → **Delegate pattern** (0 `any`)
2. Needs 3+ entities? → **Context any** (with doc comment)

**Test Mock Pattern:**

1. Try `vi.mocked()` first → **Modern approach** (Vitest 3.x+)
2. Type mismatch? → **`as any` fallback** (with inline comment)

---

## Why We Care About Types

**The danger of `any`:**

```typescript
// ❌ BAD: Silent runtime failures
function processData(data: any) {
  return data.items.map((item) => item.value.toFixed(2));
  // Compiles fine, crashes if data is wrong shape
}

// ✅ GOOD: Compile-time safety
function processData(data: { items: Array<{ value: number }> }) {
  return data.items.map((item) => item.value.toFixed(2));
  // TypeScript catches wrong data shapes immediately
}
```

**Benefits:**

1. Catch bugs early (compile-time, not production)
2. Self-documenting (types serve as inline docs)
3. Refactoring confidence (TypeScript guides safe changes)
4. Team consistency (shared understanding of data)

---

## The 2-Tier Standard

### Tier 1: Production Code (STRICT - 0 tolerance for `any`)

**Applies to:**

- `src/server/*/operations.ts` - Backend operations
- `src/server/*/validators.ts` - Input validation
- `src/server/permissions/*.ts` - Authorization logic
- All React components
- All business logic

**Rules:**

- ❌ NEVER use `any` type
- ❌ NEVER use `@ts-ignore` or `@ts-nocheck`
- ❌ NEVER use broad eslint-disable comments
- ✅ USE proper types: `Prisma.JsonValue`, `unknown`, specific types
- ✅ USE inline eslint-disable ONLY for unavoidable tool limitations (with comment)

### Tier 2: Test Infrastructure (PRAGMATIC)

**Applies to:**

- `src/**/*.test.ts` - Unit tests
- `src/**/*.test.tsx` - Component tests
- `e2e-tests/**/*.spec.ts` - E2E tests

**Rules:**

- ✅ Mock type casts MAY use `as any` with inline comment
- ✅ Test helpers MAY use `unknown` where appropriate
- ❌ Business logic in tests must still be properly typed
- ❌ Test data/assertions should use specific types where possible

**Why different standards?**

- **Mocking** - Test frameworks often have type mismatches
- **Ergonomics** - Tests should be easy to write/maintain
- **Focus** - Test quality matters more than mock infrastructure types

---

## Helper Function Patterns

### 🥇 FIRST CHOICE: Delegate Pattern (0 `any`)

**When:** Helper needs 1-2 specific entities

```typescript
// activityLog.ts - uses delegate pattern (PREFERRED)
export async function logTaskActivity(params: {
  a3Id: string;
  userId: string;
  action: string;
  a3ActivityDelegate: PrismaClient["a3Activity"]; // ✅ Specific delegate
}): Promise<TaskActivity> {
  return taskRecordActivityDelegate.create({
    data: { a3Id: params.a3Id, userId: params.userId, action: params.action },
  });
}

// Called from operation
export const createTask: CreateTask = async (args, context) => {
  await logTaskActivity({
    a3Id: a3.id,
    userId: context.user.id,
    action: "CREATED",
    a3ActivityDelegate: context.entities.TaskActivity, // Pass specific delegate!
  });
};
```

**Why preferred:**

- ✅ Zero `any` types (100% type safety)
- ✅ Explicit dependencies (clear what entities helper needs)
- ✅ Easy to test (mock specific delegates)
- ✅ OpenSaaS recommended pattern

### ⚠️ FALLBACK: Context Any (When delegate becomes impractical)

**When:** Helper needs 3+ entities

```typescript
// permissions/index.ts - context any with documentation

/**
 * Permission Helper Functions
 *
 * These helpers accept Wasp operation context (`context: any`) because:
 * 1. Needs 3+ entities (UserDepartment, User, Organization)
 * 2. Passing all delegates would create verbose function signatures
 * 3. Operations calling these ARE properly typed (GetTask, CreateTask, etc.)
 *
 * This is a Wasp framework limitation - operations get automatic type inference,
 * but helper functions outside operations lose this inference.
 */

export async function canAccessDepartment(
  userId: string,
  departmentId: string,
  context: any, // eslint-disable-line @typescript-eslint/no-explicit-any -- Wasp context varies by operation, needs 3+ entities (see file header)
): Promise<boolean> {
  const membership = await context.entities.UserDepartment.findUnique({
    where: { userId_departmentId: { userId, departmentId } },
  });
  return membership !== null;
}
```

**When acceptable:**

- Helper needs 3+ entities (delegate pattern too verbose)
- Complex cross-entity queries
- File header documents justification
- Inline eslint-disable with comment explaining reason
- Operations calling helpers ARE properly typed (safety at operation boundary)

**Wasp 0.20+ Type Inference:**

- Operations with type annotations (`GetTask<Args, Return>`) get automatic context typing
- Helper functions outside operations lose this inference (framework limitation)
- Reevaluate helper patterns when Wasp adds official typing for helpers

---

## Test Mock Patterns

### ✅ PREFERRED: vi.mocked() (Vitest 3.x+)

```typescript
import { vi } from "vitest";

// ✅ Try this first - proper type inference
const mockLogActivity = vi.mocked(activityLog.logTaskActivity);

it("should log activity after creation", async () => {
  mockLogActivity.mockResolvedValue({ id: "activity-1" });
  // Proper type inference for mock methods
});
```

### ✅ FALLBACK: Mock Type Cast (Complex cases)

```typescript
// ✅ ACCEPTABLE - If vi.mocked() has type mismatches
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- Mock cast: Vitest types don't match Prisma delegates
const mockLogActivity = activityLog.logTaskActivity as any;

it("should log activity after creation", async () => {
  mockLogActivity.mockResolvedValue({ id: "activity-1" });
  // Test logic uses proper types
});
```

**Why this approach:**

- `vi.mocked()` provides proper typing in most cases (Vitest 3.x+)
- `as any` fallback for edge cases (Prisma delegate types, complex generics)
- Isolated to test infrastructure
- Documented reason when using fallback
- Doesn't compromise test quality

---

## When `any` is Acceptable

### ✅ ACCEPTABLE: CLI Scripts & Seed Data

```typescript
// Context: seed.ts (CLI script)
/* eslint-disable no-console */
// Seed script - console.log is appropriate for CLI output

export async function seedMultiTenant() {
  console.log("Seeding multi-tenant data...");
  // ... seed logic
}
```

**Why acceptable:**

- Script runs once during development
- Console output is the intended behavior
- Not part of production runtime

### ✅ ACCEPTABLE: Type Guards with Unknown

```typescript
// ✅ GOOD: Use unknown at boundaries
function validateData(data: unknown): void {
  if (!data || typeof data !== "object") {
    throw new Error("Invalid data");
  }
  // Type narrowed to object here
}
```

---

## When `any` is Cheating

### ❌ CHEATING: Hiding Production Code Issues

```typescript
// ❌ WRONG: Using any to avoid proper typing
export const updateTask = async (args: { data: any }, context: any) => {
  return context.entities.TaskDocument.update({
    where: { id: args.id },
    data: args.data, // Could be anything!
  });
};

// ✅ RIGHT: Proper Prisma types
import { Prisma } from "@prisma/client";

export const updateTask = async (
  args: { data: Prisma.TaskDocumentUpdateInput },
  context: Context,
) => {
  return context.entities.TaskDocument.update({
    where: { id: args.id },
    data: args.data, // TypeScript validates structure
  });
};
```

### ❌ CHEATING: Broad eslint-disable

```typescript
// ❌ WRONG: Blanket disable
/* eslint-disable @typescript-eslint/no-explicit-any */

function validateData(data: any) {
  /* ... */
}
function processData(input: any) {
  /* ... */
}
function storeData(record: any) {
  /* ... */
}

// ✅ RIGHT: Specific fixes or inline disables
function validateData(data: unknown) {
  if (typeof data !== "object" || !data) {
    throw new Error("Invalid data");
  }
  // Type guard narrows to object
}
```

---

## Inline eslint-disable Best Practices

**ALWAYS include a comment explaining why:**

```typescript
// ✅ GOOD: Explains the limitation
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- Mock cast: Vitest types don't match Prisma delegates
const mockFn = module.func as any;

// ❌ BAD: No explanation
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const mockFn = module.func as any;
```

**Comment format:**

```
// eslint-disable-next-line <rule> -- <reason why disable is needed>
```

**Good reasons:**

- "Mock cast: Framework types don't match"
- "CLI script: console output is intentional"
- "Type guard: narrowing from unknown"
- "Wasp context varies by operation, needs 3+ entities"

**Bad reasons:**

- "Too hard to type"
- "Faster this way"
- "Will fix later"

---

## Decision Trees

### Helper Function Decision

```
Does helper need database access?
├─ NO → Use proper TypeScript types (no context)
└─ YES → How many entities?
    ├─ 1-2 entities → Delegate pattern (PREFERRED)
    │   Example: logTaskActivity(params, a3ActivityDelegate)
    └─ 3+ entities → Context any with doc comment
        Example: complexHelper(data, context: any)
        ⚠️  Requires: File header + inline comment
```

### Test Mock Decision

```
Need to mock a function?
├─ Try: vi.mocked(module.function)
│   ├─ Works? → ✅ Use it
│   └─ Type mismatch?
│       └─ Use: module.function as any
│           ⚠️  Requires: inline eslint-disable with comment
```

### Type Safety Decision

```
Got 'any' type?
├─ Production code?
│   ├─ YES → ❌ MUST FIX (0 tolerance)
│   │   Options:
│   │   ├─ Use Prisma.* types
│   │   ├─ Use unknown with type guards
│   │   └─ Use proper TypeScript types
│   └─ NO → Test infrastructure?
│       ├─ Mock cast? → ✅ OK with inline comment
│       └─ Business logic? → ❌ MUST FIX
```

---

## Common Type Errors & Fixes

### "Property X does not exist on type Y"

**After schema.prisma or main.wasp changes:**

```bash
# Restart Wasp (regenerates types)
./scripts/safe-start.sh

# If still fails, clean build
wasp clean
./scripts/safe-start.sh
```

**Missing type annotation:**

```typescript
// ❌ WRONG - No type annotation
export const getTask = async (args, context) => {
  // context.entities is undefined!
};

// ✅ CORRECT - Type annotation
export const getTask: GetTask<{ id: string }, TaskDocument> = async (
  args,
  context,
) => {
  // context.entities is typed
};
```

### "Cannot find module 'wasp/...'"

```bash
# Restart Wasp to regenerate types
./scripts/safe-start.sh
```

### "Unexpected any"

**Option 1: Fix with proper types**

```typescript
// ✅ PREFERRED
import { Prisma } from "@prisma/client";
const data: Prisma.TaskDocumentCreateInput = {
  /* ... */
};
```

**Option 2: Inline eslint-disable (tests only)**

```typescript
// ✅ ACCEPTABLE in tests
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- Mock cast: Vitest types don't match
const mockFn = module.func as any;
```

---

## ESLint Configuration

### Why `no-undef` is Disabled for TypeScript

**Problem:** ESLint's `no-undef` doesn't understand TypeScript types (flags `HTMLSelectElement`, etc.)

**Official TypeScript ESLint Recommendation:**

> "We strongly recommend you do NOT use the `no-undef` rule on TypeScript projects. TypeScript provides better checking."

**Configuration:**

```javascript
// eslint.config.js (ESLint v9+)
{
  files: ["**/*.ts", "**/*.tsx"],
  rules: {
    "no-undef": "off"  // TypeScript compiler handles this
  }
}
```

**Why safe:**

- TypeScript compiler already checks undefined variables
- `no-undef` was designed for JavaScript, not TypeScript
- Modern TS projects (React, Vue, Angular) all disable it

---

## Real-World Examples

### Example 1: Production Operation (STRICT)

```typescript
// ✅ CORRECT - Wasp operation with proper types
import type { UpdateTask } from "wasp/server/operations";
import { Prisma } from "@prisma/client";

export const updateTask: UpdateTask = async (args, context) => {
  if (!context.user) throw new HttpError(401);

  const updateData: Prisma.TaskDocumentUpdateInput = {};
  if (args.data.title) updateData.title = args.data.title;

  return context.entities.TaskDocument.update({
    where: { id: args.id },
    data: updateData, // ✅ Type-safe!
  });
};
```

### Example 2: Helper with Delegate Pattern

```typescript
// ✅ CORRECT - Delegate pattern (0 any)
export async function auditTaskChange(params: {
  a3Id: string;
  userId: string;
  changes: Record<string, unknown>;
  auditLogDelegate: PrismaClient["auditLog"];
}): Promise<AuditLog> {
  return auditLogDelegate.create({
    data: {
      entityType: "TaskDocument",
      entityId: params.a3Id,
      userId: params.userId,
      changes: params.changes,
    },
  });
}
```

### Example 3: Test Mock (PRAGMATIC)

```typescript
// ✅ CORRECT - Modern Vitest approach
import { vi } from "vitest";

// Try vi.mocked first
const mockAudit = vi.mocked(auditLog.auditTaskChange);

// Fallback if needed
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- Mock cast: Complex Prisma delegate types
const mockAudit = auditLog.auditTaskChange as any;

it("audits Task changes", async () => {
  mockAudit.mockResolvedValue({ id: "audit-1" });
  // Test logic properly typed
});
```

---

## FAQ

### Q: Should I use `vi.mocked()` or `as any` for test mocks?

**A:** Try `vi.mocked()` first (Vitest 3.x+), fall back to `as any` if needed.

```typescript
// ✅ Try this first
const mockFn = vi.mocked(module.function);

// ✅ Use when vi.mocked() has type mismatches
// eslint-disable-next-line @typescript-eslint/no-explicit-any -- Mock cast: reason
const mockFn = module.function as any;
```

### Q: When should I use `unknown` vs `any`?

**Production code:**

- ✅ `unknown` - Forces type checking before use
- ❌ `any` - Disables all type checking

**Test mocks:**

- ✅ `vi.mocked()` - Try first (Vitest 3.x+)
- ✅ `as any` - Fallback for complex type mismatches (with inline comment)
- ❌ `as unknown` - Doesn't solve mock type issues

### Q: Can I use `@ts-ignore`?

**A:** Almost never. Use:

1. Proper types first
2. Inline `eslint-disable` with comment if unavoidable
3. `@ts-expect-error` if testing error handling

`@ts-ignore` silences errors without explanation.

### Q: Helper needs 2 entities - delegate or context any?

**A:** Delegate pattern (0 `any`). Only use context any for 3+ entities.

---

## Summary

**Production Operations:** Properly typed with Wasp's generated types

**Helper Functions:**

1. 🥇 **Delegate pattern** (0 any) - Pass specific delegates (1-2 entities)
2. ⚠️ **Context any** with doc - Only for complex helpers needing 3+ entities

**Test Mocking:**

1. ✅ Try `vi.mocked()` first (Vitest 3.x+)
2. ✅ Fallback to `as any` for complex cases (with inline comment)
3. Test business logic still properly typed

**When in doubt:**

1. Helper needs 1-2 entities? → Delegate pattern
2. Helper needs 3+ entities? → Context any with doc
3. Test mock? → Try `vi.mocked()` first
4. Does this compromise operation type safety? If no, pragmatism wins.

**Key Principle:** Wasp 0.20 operations get automatic type inference. Helpers outside operations don't - use delegate pattern where possible, document when context any is needed.

---

## References

- **Full documentation:** [docs/LINTING-STANDARDS.md](../../docs/LINTING-STANDARDS.md)
- **Code quality procedures:** `code-quality` skill
- **Wasp operations:** `wasp-operations` skill
- **Troubleshooting:** `troubleshooting-guide` skill
- **ESLint config:** `app/eslint.config.js`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
