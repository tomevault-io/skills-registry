---
name: code-flow-analysis
description: Trace code execution path before implementing fixes. Forces understanding of fn(args, deps) flows, Result types, and workflow composition. Prevents guessing by requiring file:line references and execution diagrams. Use when this capability is needed.
metadata:
  author: jagreehal
---

# Code Flow Analysis Protocol

Trace execution paths before implementing. Understand the flow, then fix.

## Core Principle

**Map the execution path before changing code.** When fixing bugs, implementing features, or refactoring, you must first understand how the code currently flows through your `fn(args, deps)` functions, Result types, and workflows.

## When This Activates

Before:
- Fixing bugs
- Implementing features
- Refactoring
- Starting TDD cycles
- Debugging issues

**Skip for:** Typos, formatting, documentation-only changes.

## The Protocol (3 Quick Steps)

**Keep it lightweight** - This isn't detailed planning, just enough to guide implementation. 5-10 lines max for diagrams.

### 1. Trace the Execution Path

Answer these with **file:line references**:

- **Entry point:** Which event/request triggers this? (`src/api/routes.ts:45`)
- **Function chain:** Which `fn(args, deps)` functions are called? (`src/domain/get-user.ts:12`)
- **Error location:** Where does the failure occur? (`src/domain/get-user.ts:28`)
- **Workflow composition:** Does it use `createWorkflow()`? Which steps? (`src/workflows/load-user-data.ts:23`)
- **Result flow:** How does the Result propagate? (`src/api/handlers.ts:67`)

### 2. Quick Diagram

Simple class.method() flow with relevant data and Result types:

```
Event: POST /users/:id
  ↓ (args: { userId: string })
Handler.getUser() [src/api/handlers.ts:45]
  ↓ (validates with Zod)
getUser(args, deps) [src/domain/get-user.ts:12]
  ↓ (deps: { db, logger })
deps.db.findUser() [src/infra/database.ts:89]
  ↓ (returns: User | null)
Result check [src/domain/get-user.ts:28] ← 💥 Error here
  ↓ (if null) → err('NOT_FOUND')
  ↓ (if user) → ok(user)
resultToResponse() [src/api/handlers.ts:67]
  ↓
HTTP 200 or 404
```

**Keep it short** - 5-10 lines max. Focus on the relevant path.

**Key elements:**
- Show `fn(args, deps)` signatures
- Show Result types (`ok()` vs `err()`)
- Show workflow steps if applicable
- Mark error location with 💥
- Include relevant data fields in flow

### 3. Verify Understanding

Ask: "Here's the flow: [diagram]. The error occurs at [file:line] when [condition]. Correct?"

**Wait for confirmation, then proceed.**

**Skip for trivial changes** - Typos, formatting, docs-only changes don't need this protocol.

## Integration with Patterns

### fn(args, deps) Pattern

When tracing, identify:
- Which functions receive which `args`
- Which `deps` are injected at each step
- Where dependencies are wired (composition root)

```typescript
// Trace shows:
getUser(args: { userId }, deps: { db, logger })
  ↓
createUser(args: { name, email }, deps: { db, logger, mailer })
  ↓
// Question: Why does createUser need mailer but getUser doesn't?
// Answer: Each function declares only what it uses (per fn-args-deps)
```

### Result Types

Show Result propagation:

```
getUser() → Result<User, 'NOT_FOUND' | 'DB_ERROR'>
  ↓
createWorkflow({ getUser })
  ↓
step(() => getUser(...)) → unwraps Result
  ↓
if err → short-circuits, returns err
if ok → continues to next step
```

### Workflow Composition

Trace through `createWorkflow()`:

```
workflow(async (step) => {
  const user = await step(() => getUser(...));      // Step 1
  const posts = await step(() => getPosts(...));    // Step 2 (skipped if Step 1 err)
  return { user, posts };                           // Only if all ok
})
```

## Example: Bug Fix

**Problem:** `getUser` returns `err('DB_ERROR')` but database is working.

**❌ WRONG (guessing):**
"I'll add retry logic to getUser."

**✅ RIGHT (tracing first):**

1. **Trace:**
   - Entry: `GET /users/:id` → `Handler.getUser` [src/api/handlers.ts:45]
   - Calls: `getUser(args, deps)` [src/domain/get-user.ts:12]
   - Error: `catch` block returns `err('DB_ERROR')` [src/domain/get-user.ts:28]
   - But: `deps.db.findUser` is mocked in tests, real DB in integration

2. **Diagram:**
   ```
   Handler.getUser() [src/api/handlers.ts:45]
     ↓
   getUser(args, deps) [src/domain/get-user.ts:12]
     ↓
   deps.db.findUser() [src/infra/database.ts:89]
     ↓ (throws Error)
   catch block [src/domain/get-user.ts:28] ← 💥 Returns err('DB_ERROR')
     ↓
   But: Error message is "Connection timeout", not "Connection refused"
   ```

3. **Verify:** "The error occurs in the catch block, but the actual error is a timeout, not a connection failure. The function is catching all errors as 'DB_ERROR'. Should we distinguish timeout vs connection errors?"

**Now the fix is clear:** Add specific error handling for timeout vs connection errors, or add timeout to the workflow level (per resilience patterns).

## Example: Feature Implementation

**Request:** "Add email validation to user creation."

**❌ WRONG:**
"I'll add validation inside createUser function."

**✅ RIGHT:**

1. **Trace current flow:**
   ```
   POST /users
     ↓
   Handler.createUser() [src/api/handlers.ts:67]
     ↓
   createUserSchema.safeParse() [src/api/handlers.ts:70] ← Validation happens here
     ↓
   createUser(args, deps) [src/domain/create-user.ts:15]
     ↓
   deps.db.saveUser() [src/infra/database.ts:102]
   ```

2. **Diagram shows:** Validation already happens at boundary (per validation-boundary pattern).

3. **Verify:** "Email validation should be in the Zod schema at the boundary, not inside createUser. The schema is at [file:line]. Should I add email validation there?"

## Rules

1. **Always trace before implementing** - No exceptions for non-trivial changes
2. **Use file:line references** - Not abstractions like "the handler" or "some function"
3. **Show Result flow** - How errors propagate through the system
4. **Show deps injection** - Where dependencies come from
5. **Get confirmation** - Don't proceed without user verification
6. **Keep diagrams concise** - 5-15 lines, focus on the relevant path

## Anti-Patterns

❌ **"I'll fix the validation error"** (didn't trace where it occurs)
✅ **"Let me trace where validation fails... [diagram]. The error is at [file:line] when [condition]. Correct?"**

❌ **"I'll add retry logic"** (didn't check if retry exists at workflow level)
✅ **"Tracing the flow... [diagram]. Retry should be at workflow level per resilience patterns, not inside the function. Correct?"**

## Integration with Other Skills

- **debugging-methodology:** Use this protocol BEFORE adding instrumentation
- **tdd-workflow:** Use this to understand what to test in RED phase
- **result-types:** Always show Result propagation in diagrams
- **resilience:** Identify where retry/timeout should be added (workflow level)

## Quick Reference

| Situation | What to Trace |
|-----------|--------------|
| Bug fix | Error location, Result flow, deps chain |
| Feature | Entry point, function chain, where to add code |
| Refactor | Current flow, what changes, impact on callers |
| Performance | Slow path, external calls, workflow steps |

**Remember:** Understanding the flow makes the solution obvious. Guessing wastes time.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jagreehal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
