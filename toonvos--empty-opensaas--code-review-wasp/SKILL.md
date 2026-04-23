---
name: code-review-wasp
description: Comprehensive code review skill for Wasp applications. Use when reviewing code, PRs, or architectural changes. Covers Wasp-specific patterns, import rules, auth checks, TDD compliance, multi-tenant permissions, and performance. Integrates all CLAUDE.md critical rules. Use when this capability is needed.
metadata:
  author: toonvos
---

# Code Review Wasp Skill

## Quick Reference

**When to use this skill:**

- Reviewing pull requests
- Checking code before commit
- Architectural review of features
- Pre-merge quality checks
- Identifying Wasp-specific issues

**Key Focus Areas:**

1. **Wasp Framework Patterns** - Import rules, operations, entity access
2. **Security** - Server-side auth, multi-tenant isolation, input validation
3. **TDD Compliance** - 2-phase workflow, test quality (5 criteria)
4. **Performance** - N+1 queries, pagination, memoization
5. **Code Quality** - SOLID, DRY, testability

**Review Philosophy:**

This skill catches **Wasp-specific issues** that generic code review tools miss. It enforces the patterns documented in CLAUDE.md and your custom skills.

---

## Review Checklist (10 Critical Categories)

Use this checklist systematically for every code review:

### ✅ Category 1: Import Rules

**Verify correct import syntax based on context:**

```typescript
// ✅ CORRECT - In .ts/.tsx files
import { HttpError } from "wasp/server";
import type { Task } from "wasp/entities";
import { useQuery } from "wasp/client/operations";
import { TaskStatus } from "@prisma/client"; // For enum VALUES

// ❌ WRONG - Common mistakes
import { Task } from "@wasp/entities"; // Wrong prefix
import { helper } from "@src/utils/helper"; // Wrong in .ts/.tsx
import { TaskStatus } from "wasp/entities"; // Wrong for enum values
```

```wasp
// ✅ CORRECT - In main.wasp files
query getTasks {
  fn: import { getTasks } from "@src/server/tasks/operations",
  entities: [Task]
}

// ❌ WRONG - Common mistakes
fn: import { getTasks } from "src/server/tasks/operations", // Missing @
fn: import { getTasks } from "../src/server/tasks/operations", // Relative path
```

**Red Flags:**

- ❌ Using `@wasp/` prefix instead of `wasp/`
- ❌ Using `@src/` in .ts/.tsx files (should be relative paths)
- ❌ Using relative paths in main.wasp (should be `@src/`)
- ❌ Importing enum values from `wasp/entities` (should be `@prisma/client`)

**Cross-reference:** See `wasp-operations` skill for complete import patterns

---

### ✅ Category 2: Type Annotations (Operations)

**CRITICAL: Type annotations are REQUIRED for context.entities access**

```typescript
// ✅ CORRECT - Type annotation present
export const getTasks: GetTasks<void, Task[]> = async (args, context) => {
  return context.entities.Task.findMany(); // Works!
};

// ❌ WRONG - No type annotation
export const getTasks = async (args, context) => {
  return context.entities.Task.findMany(); // context.entities is undefined!
};
```

**Check:**

- [ ] All queries have `Get{Name}<Args, Return>` type annotation
- [ ] All actions have `Create/Update/Delete{Name}<Args, Return>` type annotation
- [ ] Imports include `from "wasp/server/operations"`
- [ ] Args and Return types match actual function signature

**Red Flags:**

- ❌ Operations without type annotations
- ❌ Type annotation doesn't match actual function signature
- ❌ Missing imports for type definitions

**Cross-reference:** See `wasp-operations` skill, lines 88-155

---

### ✅ Category 3: Auth Checks (Security CRITICAL)

**MANDATORY: Auth check must be FIRST line of every operation**

```typescript
// ✅ CORRECT - Auth check first
export const getTasks: GetTasks = async (args, context) => {
  if (!context.user) throw new HttpError(401); // FIRST line
  return context.entities.Task.findMany({ where: { userId: context.user.id } });
};

// ❌ WRONG - Auth check missing or late
export const getTasks: GetTasks = async (args, context) => {
  const tasks = await context.entities.Task.findMany(); // NO AUTH CHECK!
  return tasks;
};
```

**Check:**

- [ ] First line is: `if (!context.user) throw new HttpError(401)`
- [ ] No operations bypass auth (unless explicitly public)
- [ ] Auth check uses `context.user` (NOT custom logic)
- [ ] Operations filter by `userId` or check ownership

**Red Flags:**

- ❌ Missing auth check entirely
- ❌ Auth check after business logic
- ❌ Custom auth logic instead of `context.user`
- ❌ No filtering by userId (potential data leak)

**Cross-reference:** See `wasp-operations` skill, Section 3 (lines 120-220)

---

### ✅ Category 4: Error Sequence (HTTP Status Codes)

**CRITICAL: Enforce correct error sequence**

**Order (MUST follow):**

1. **401 Unauthorized** - No user (unauthenticated)
2. **404 Not Found** - Resource doesn't exist
3. **403 Forbidden** - User lacks permission
4. **400 Bad Request** - Invalid input

```typescript
// ✅ CORRECT - Proper error sequence
export const updateTask: UpdateTask = async (args, context) => {
  // 1. Auth check (401)
  if (!context.user) throw new HttpError(401);

  // 2. Fetch resource
  const taskRecord = await context.entities.Task.findUnique({
    where: { id: args.id },
  });

  // 3. Check existence (404)
  if (!taskRecord) throw new HttpError(404, "Task not found");

  // 4. Check permission (403)
  if (taskRecord.userId !== context.user.id) {
    throw new HttpError(403, "Not authorized");
  }

  // 5. Validate input (400)
  if (!args.data.description?.trim()) {
    throw new HttpError(400, "Description required");
  }

  // 6. Perform update
  return context.entities.Task.update({
    where: { id: args.id },
    data: args.data,
  });
};

// ❌ WRONG - Wrong error order
export const updateTask: UpdateTask = async (args, context) => {
  // Checking permission before auth and existence - WRONG ORDER!
  const taskRecord = await context.entities.Task.findUnique({
    where: { id: args.id },
  });
  if (taskRecord.userId !== context.user.id) throw new HttpError(403);
  // ... rest of checks
};
```

**Check:**

- [ ] 401 comes first (auth)
- [ ] 404 before 403 (existence before permission)
- [ ] 400 after auth/existence/permission checks
- [ ] Error messages are descriptive

**Red Flags:**

- ❌ Wrong error order (e.g., 403 before 404)
- ❌ Using wrong HTTP status code
- ❌ Generic error messages ("Error", "Failed")
- ❌ Not using HttpError class

**Cross-reference:** See `error-handling` skill for complete patterns

---

### ✅ Category 5: Entity Listing (main.wasp)

**CRITICAL: Entities must be listed for context.entities access AND auto-invalidation**

```wasp
// ✅ CORRECT - Entities listed
query getTasks {
  fn: import { getTasks } from "@src/server/tasks/operations",
  entities: [Task]  // Enables context.entities.Task + auto-invalidation
}

action createTask {
  fn: import { createTask } from "@src/server/tasks/operations",
  entities: [Task]  // Same as getTasks → auto-invalidation works!
}

// ❌ WRONG - No entities
query getTasks {
  fn: import { getTasks } from "@src/server/tasks/operations",
  // Missing entities: [] → context.entities undefined!
}

// ❌ WRONG - Different entities prevent auto-invalidation
action createTask {
  entities: [Task, User]  // Extra entity prevents auto-invalidation of getTasks
}
```

**Check:**

- [ ] All queries list entities in main.wasp
- [ ] All actions list entities in main.wasp
- [ ] Entities match actual database access
- [ ] Query + Action share same entities (for auto-invalidation)

**Red Flags:**

- ❌ Missing `entities: []` array
- ❌ Empty entities array when accessing database
- ❌ Query and action have different entities (breaks auto-invalidation)
- ❌ Entities listed but not actually accessed in code

**Cross-reference:** See `wasp-operations` skill, lines 40-87

---

### ✅ Category 6: Client Patterns (Actions)

**DEFAULT: Use direct async/await for actions, NOT useAction**

```typescript
// ✅ CORRECT - Direct call (default pattern)
const handleCreate = async (description: string) => {
  try {
    await createTask({ description }); // Simple + enables auto-invalidation
    toast.success("Task created");
  } catch (err) {
    toast.error(err.message);
  }
};

// ❌ WRONG - useAction by default
const createTaskFn = useAction(createTask);
const handleCreate = async (description: string) => {
  try {
    await createTaskFn({ description }); // Blocks auto-invalidation!
    toast.success("Task created");
  } catch (err) {
    toast.error(err.message);
  }
};
```

**ONLY use useAction for optimistic UI updates (advanced):**

```typescript
// ✅ CORRECT - useAction ONLY for optimistic updates
const deleteTaskFn = useAction(deleteTask, {
  optimisticUpdates: [
    {
      getQuerySpecifier: () => [getTasks],
      updateQuery: (oldTasks, { id }) => oldTasks.filter((t) => t.id !== id),
    },
  ],
});
```

**Check:**

- [ ] Actions use direct `await action(args)` by default
- [ ] `useAction` ONLY used with `optimisticUpdates` config
- [ ] Client imports from `wasp/client/operations`
- [ ] Error handling present (try/catch)

**Red Flags:**

- ❌ Using `useAction` without optimistic updates
- ❌ Importing operations from wrong location
- ❌ No error handling around action calls
- ❌ Manual cache invalidation (Wasp does this automatically)

**Cross-reference:** See CLAUDE.md, lines 275-304 (Client-Side Action Usage)

---

### ✅ Category 7: Restart Requirements

**MANDATORY: Restart after schema.prisma or main.wasp changes**

**Files that require restart:**

- `schema.prisma` - Database schema changes
- `main.wasp` - Routes, operations, entities changes
- Database migrations - After `wasp db migrate-dev`

**Verify:**

```bash
# After changes, MUST restart with safe-start.sh (multi-worktree safe)
../scripts/safe-start.sh

# ❌ WRONG - Direct wasp start (not multi-worktree safe)
wasp start
```

**Check:**

- [ ] PR mentions restart if schema/main.wasp changed
- [ ] Uses `../scripts/safe-start.sh` (not direct `wasp start`)
- [ ] Migration ran BEFORE restart (if schema changed)
- [ ] Types verified after restart

**Red Flags:**

- ❌ Schema/main.wasp changes without restart mention
- ❌ Using direct `wasp start` (multi-worktree conflicts)
- ❌ Restart before migration (wrong order)
- ❌ Assuming types update without restart

**Cross-reference:** See `wasp-database` skill for complete workflow

---

### ✅ Category 8: Test Coverage (TDD Compliance)

**MANDATORY: ≥80% statements, ≥75% branches**

**Check test quality using 5 MUST PASS criteria:**

**1. Tests Business Logic (NOT Existence):**

```typescript
// ❌ WRONG - Test theater
it("should exist", () => {
  expect(createTask).toBeDefined(); // Meaningless!
});

// ✅ CORRECT - Tests behavior
it("should create task with description", async () => {
  const result = await createTask({ description: "Test" }, mockContext);
  expect(result.description).toBe("Test");
  expect(mockContext.entities.Task.create).toHaveBeenCalledWith({
    data: { description: "Test", userId: "user-1" },
  });
});
```

**2. Meaningful Assertions:**

- ❌ `expect(result).toBeDefined()` - Generic
- ✅ `expect(result.name).toBe('Acme')` - Specific

**3. Tests Error Paths:**

- ✅ 401 (unauthenticated)
- ✅ 400 (validation error)
- ✅ 404 (not found)
- ✅ 403 (no permission)

**4. Tests Edge Cases:**

- ✅ Empty strings, null, undefined
- ✅ Boundary conditions (min/max length)
- ✅ Special characters

**5. Behavior NOT Implementation:**

- ❌ Testing internal variables (`component.state.loading`)
- ✅ Testing observable UI (`screen.getByText('Loaded')`)

**Check:**

- [ ] All 5 quality criteria pass
- [ ] Coverage ≥80% statements, ≥75% branches
- [ ] Tests follow 2-phase TDD (RED → GREEN + REFACTOR)
- [ ] No test theater (existence checks)

**Red Flags:**

- ❌ Low coverage (<80%/<75%)
- ❌ Only happy path tests (no error paths)
- ❌ Existence checks instead of behavior tests
- ❌ Testing implementation details

**Cross-reference:** See `tdd-workflow` skill, lines 226-555

---

### ✅ Category 9: ShadCN Version Lock

**CRITICAL: ONLY use ShadCN v2.3.0**

```bash
# ✅ CORRECT - Version locked
npx shadcn@2.3.0 add button

# ❌ WRONG - Uses latest (Tailwind v4, breaks Wasp)
npx shadcn add button
```

**After EVERY component installation - Fix import path:**

```diff
// In src/client/components/ui/{component}.tsx
-import { cn } from "s/lib/utils"
+import { cn } from "../../lib/utils"
```

**Check:**

- [ ] Uses `shadcn@2.3.0` (NOT latest)
- [ ] Import path fixed to `../../lib/utils`
- [ ] No Tailwind v4 syntax
- [ ] Component works without import errors

**Red Flags:**

- ❌ Using `shadcn` without version (uses latest)
- ❌ Import path still `s/lib/utils`
- ❌ Tailwind v4 syntax (@theme, etc)
- ❌ Component import errors

**Cross-reference:** See `shadcn-ui` skill and CLAUDE.md lines 82-107

---

### ✅ Category 10: Multi-Tenant Permissions

**CRITICAL: Verify multi-tenant isolation**

**Architecture:**

- Organization → Many Departments (hierarchical via `parentId`)
- Users ↔ Departments = Many-to-Many via `UserDepartment`
- DepartmentRole: MANAGER | MEMBER | VIEWER

**Permission checks REQUIRED:**

```typescript
// ✅ CORRECT - Multi-tenant filtering
export const getTasks: GetTasks = async (args, context) => {
  if (!context.user) throw new HttpError(401);

  // Get user's departments
  const userDepartments = await context.entities.UserDepartment.findMany({
    where: { userId: context.user.id },
  });

  const departmentIds = userDepartments.map((ud) => ud.departmentId);

  // Filter by departments user has access to
  return context.entities.Task.findMany({
    where: {
      departmentId: { in: departmentIds },
    },
  });
};

// ❌ WRONG - No multi-tenant filtering
export const getTasks: GetTasks = async (args, context) => {
  if (!context.user) throw new HttpError(401);
  return context.entities.Task.findMany(); // Returns ALL tasks (data leak!)
};
```

**Check:**

- [ ] Operations filter by organization/department
- [ ] Permission checks use DepartmentRole
- [ ] No raw queries without multi-tenant WHERE clause
- [ ] VIEWER role has read-only access

**Red Flags:**

- ❌ Queries without organization/department filtering
- ❌ Assuming single organization
- ❌ No role-based permission checks
- ❌ Data leaks across organizations

**Cross-reference:** See `permissions` skill for complete patterns

---

## Wasp-Specific Red Flags

These issues are UNIQUE to Wasp - generic tools won't catch them:

### 🚩 Critical Red Flags (BLOCK PR)

1. **Missing type annotations on operations**

   - Symptom: `context.entities` is undefined at runtime
   - Impact: Runtime crash
   - Fix: Add `GetQuery<Args, Return>` type annotation

2. **Auth check not first line**

   - Symptom: Security vulnerability
   - Impact: Unauthorized access to data
   - Fix: Move `if (!context.user)` to first line

3. **Using `@wasp/` prefix**

   - Symptom: Cannot find module error
   - Impact: Build fails
   - Fix: Use `wasp/` (no @)

4. **ShadCN version > 2.3.0**

   - Symptom: Tailwind v4 incompatibility
   - Impact: Styles break, build fails
   - Fix: Uninstall and reinstall with `@2.3.0`

5. **No restart after schema/main.wasp changes**

   - Symptom: Types are stale, imports fail
   - Impact: Development blocked
   - Fix: Stop wasp, run `../scripts/safe-start.sh`

6. **Test theater (5 criteria fail)**
   - Symptom: Tests pass but don't verify behavior
   - Impact: False confidence, bugs in production
   - Fix: Rewrite tests following 5 MUST PASS criteria

### ⚠️ Warning Red Flags (Request Changes)

7. **Using `useAction` by default**

   - Impact: Blocks auto-invalidation
   - Fix: Use direct `await action(args)`

8. **Different entities in query + action**

   - Impact: Manual cache invalidation needed
   - Fix: Use same entities for auto-invalidation

9. **N+1 query problem**

   - Impact: Performance degradation
   - Fix: Use Prisma `include` for relations

10. **No multi-tenant filtering**

    - Impact: Data leaks across organizations
    - Fix: Filter by organization/department in WHERE clause

11. **Wrong error sequence**

    - Impact: Misleading error messages
    - Fix: Follow 401 → 404 → 403 → 400 order

12. **Direct `wasp start` instead of `safe-start.sh`**
    - Impact: Multi-worktree port conflicts
    - Fix: Use `../scripts/safe-start.sh`

---

## Code Quality Criteria

### SOLID Principles

**Single Responsibility:**

- ✅ Operations do ONE thing (get, create, update, delete)
- ❌ Operations mixing CRUD operations
- ❌ Operations handling multiple entities

**Open/Closed:**

- ✅ Helper functions for reusable logic
- ❌ Copy-pasted auth checks
- ❌ Copy-pasted validation logic

**Liskov Substitution:**

- ✅ Consistent return types
- ❌ Sometimes returns entity, sometimes null
- ❌ Inconsistent error handling

**Interface Segregation:**

- ✅ Minimal operation arguments
- ❌ Operations accepting huge config objects
- ❌ Optional parameters that are actually required

**Dependency Inversion:**

- ✅ Using Wasp operations abstraction
- ❌ Direct Prisma imports in client code
- ❌ Tight coupling to specific implementations

### DRY (Don't Repeat Yourself)

**Extract common patterns:**

```typescript
// ✅ CORRECT - Extracted helper
function requireAuth(context: any) {
  if (!context.user) throw new HttpError(401);
  return context.user;
}

export const getTasks: GetTasks = async (args, context) => {
  requireAuth(context); // Reused
  return context.entities.Task.findMany();
};

// ❌ WRONG - Repeated auth check
export const getTasks: GetTasks = async (args, context) => {
  if (!context.user) throw new HttpError(401); // Repeated
  // ...
};

export const getTask: GetTask = async (args, context) => {
  if (!context.user) throw new HttpError(401); // Repeated
  // ...
};
```

**Check:**

- [ ] Auth checks extracted to helper
- [ ] Validation logic reused
- [ ] Permission checks centralized
- [ ] No copy-pasted code

### Testability

**Code should be easy to test:**

- ✅ Pure functions (input → output, no side effects)
- ✅ Dependencies injected (via context)
- ✅ Minimal mocking required
- ❌ Global state access
- ❌ Hard-coded values
- ❌ Untestable private logic

---

## Security Review

### Server-Side Authorization

**CRITICAL: Never trust client**

```typescript
// ❌ WRONG - Client-side check only
if (user.role !== "ADMIN") return <div>Access denied</div>;
// Easy to bypass! Client-side is cosmetic only.

// ✅ CORRECT - Server-side enforcement
export const sensitiveOperation: Operation = async (args, context) => {
  if (!context.user) throw new HttpError(401);
  if (context.user.role !== "ADMIN") throw new HttpError(403);
  // Safe - enforced server-side
};
```

**Check:**

- [ ] All permissions checked server-side
- [ ] Operations throw 403 if unauthorized
- [ ] Client checks are cosmetic only (UX)
- [ ] No sensitive data leaked to client

### Input Validation

**Validate ALL user input:**

```typescript
// ✅ CORRECT - Validation
if (!args.description?.trim()) {
  throw new HttpError(400, "Description required");
}
if (args.description.length > 500) {
  throw new HttpError(400, "Description too long");
}

// ❌ WRONG - No validation
const taskRecord = await context.entities.Task.create({
  data: { description: args.description }, // Accepts empty string, null, etc.
});
```

**Check:**

- [ ] Required fields validated
- [ ] Length constraints enforced
- [ ] Format validation (email, URL, etc.)
- [ ] Special characters handled

### SQL Injection Prevention

**Use Prisma (auto-escapes):**

```typescript
// ✅ CORRECT - Prisma auto-escapes
const tasks = await context.entities.Task.findMany({
  where: { status: args.status }, // Safe
});

// ❌ WRONG - Raw SQL (dangerous!)
const tasks = await context.entities.$queryRaw`
  SELECT * FROM Task WHERE status = ${args.status}
`; // SQL injection risk!
```

**Check:**

- [ ] All queries use Prisma (not raw SQL)
- [ ] No string concatenation in queries
- [ ] User input never directly in SQL

---

## Performance Review

### N+1 Query Problem

**CRITICAL: Avoid multiple queries in loop**

```typescript
// ❌ WRONG - N+1 queries
const tasks = await context.entities.Task.findMany();
for (const task of tasks) {
  const user = await context.entities.User.findUnique({
    where: { id: task.userId },
  }); // N queries!
}

// ✅ CORRECT - Single query with include
const tasks = await context.entities.Task.findMany({
  include: { user: true }, // 1 query with JOIN
});
```

**Check:**

- [ ] No queries inside loops
- [ ] Related data fetched with `include`
- [ ] Uses Prisma select for specific fields

### Pagination

**Required for large lists:**

```typescript
// ❌ WRONG - Loading all records
const tasks = await context.entities.Task.findMany(); // Could be millions!

// ✅ CORRECT - Paginated
const tasks = await context.entities.Task.findMany({
  skip: page * pageSize,
  take: pageSize,
  orderBy: { createdAt: "desc" },
});
```

**Check:**

- [ ] Lists use pagination (skip/take)
- [ ] Default page size reasonable (20-50)
- [ ] Returns total count for UI

### React Re-renders

**Memoization for expensive components:**

```typescript
// ✅ CORRECT - Memoized
const ExpensiveComponent = memo(({ data }) => {
  const processed = useMemo(() => expensiveCalc(data), [data]);
  return <div>{processed}</div>;
});

// ❌ WRONG - Re-renders every time
function ExpensiveComponent({ data }) {
  const processed = expensiveCalc(data); // Runs on every render!
  return <div>{processed}</div>;
}
```

**Check:**

- [ ] Expensive components use `memo`
- [ ] Expensive calculations use `useMemo`
- [ ] Stable callbacks use `useCallback`

---

## PR Review Template

Use this template for structured feedback:

````markdown
## Code Review Summary

**Overall:** [APPROVE / REQUEST CHANGES / BLOCK]

**Wasp-Specific Issues:** [count] found
**Security Issues:** [count] found
**Performance Issues:** [count] found
**Test Quality:** [PASS / FAIL - specify criteria]

---

## 🚨 Critical Issues (MUST FIX)

### Issue 1: [Title]

**Location:** `app/src/server/tasks/operations.ts:45`

**Problem:**
Missing type annotation on operation. `context.entities` will be undefined at runtime.

**Current Code:**

```typescript
export const getTasks = async (args, context) => {
  return context.entities.Task.findMany(); // FAILS
};
```
````

**Fix:**

```typescript
export const getTasks: GetTasks<void, Task[]> = async (args, context) => {
  return context.entities.Task.findMany(); // Works
};
```

**Why:** Type annotations are CRITICAL for Wasp operations to enable `context.entities` access.

**Reference:** `wasp-operations` skill, lines 88-155

---

## ⚠️ Warnings (Request Changes)

### Issue 2: [Title]

[Same structure as Critical Issues]

---

## ✅ What Went Well

- Auth checks present and correct
- Test coverage excellent (87%/82%)
- Code follows DRY principle
- Multi-tenant filtering correct

---

## 📚 Additional Resources

- [CLAUDE.md](../../CLAUDE.md) - Project guidelines
- [wasp-operations skill](../wasp-operations/SKILL.md) - Operation patterns
- [tdd-workflow skill](../tdd-workflow/SKILL.md) - Test quality criteria
- [Troubleshooting Guide](../../docs/TROUBLESHOOTING-GUIDE.md) - Common issues

---

## 🎯 Action Items

- [ ] Fix all CRITICAL issues (block PR)
- [ ] Address all WARNING issues
- [ ] Verify test coverage ≥80%/≥75%
- [ ] Run `../scripts/safe-start.sh` after schema/main.wasp changes
- [ ] Update PR description with testing notes

```

---

## Summary

**This skill provides comprehensive Wasp-specific code review.**

**Key Features:**

- ✅ Catches Wasp-specific issues (generic tools miss these)
- ✅ Enforces all CLAUDE.md critical rules
- ✅ Integrates with existing skills (cross-references)
- ✅ Provides actionable feedback (not just "good/bad")
- ✅ Context-efficient (activates only when explicitly asked)

**When to activate:**

- Reviewing PRs before merge
- Pre-commit quality checks
- Architectural reviews
- Identifying framework-specific issues

**Expected outcome:**

Structured review with:

1. Critical issues (block PR)
2. Warnings (request changes)
3. What went well (positive feedback)
4. Actionable fixes (code examples)
5. Cross-references (related skills)

**For complete patterns:** See cross-referenced skills listed throughout this document.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
