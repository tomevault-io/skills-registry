---
name: ccpm-debugging
description: Systematic debugging with defense-in-depth approach (symptoms → immediate cause → root cause → systemic issues → prevention). Auto-activates when user mentions "error", "failing", "broken", "debug", "bug", "issue" or when /ccpm:verify runs. Uses Observe → Hypothesize → Test → Confirm → Fix → Verify → Document workflow. Updates Linear with findings and automatically logs blockers that require external fixes. Traces root causes instead of patching symptoms. Integrates with ccpm-code-review to verify fixes pass all gates before marking complete. Suggests binary search for intermittent issues and five-whys analysis for complex problems. Use when this capability is needed.
metadata:
  author: neversight
---

# CCPM Debugging

Systematic debugging with Linear integration and structured troubleshooting workflow.

## When to Use

This skill auto-activates when:

- User mentions: **"error"**, **"failing"**, **"broken"**, **"debug"**, **"bug"**, **"issue"**
- Running **`/ccpm:verify`** command
- Tests failing during `/ccpm:verify`
- Build errors during implementation
- Runtime errors or exceptions
- Unexpected behavior

## Core Debugging Philosophy

### Defense-in-Depth Approach

**Multiple layers of investigation**:
1. **Symptoms** - What's observable?
2. **Immediate cause** - What directly caused the error?
3. **Root cause** - Why did it happen?
4. **Systemic issues** - Could this happen elsewhere?
5. **Prevention** - How to prevent recurrence?

### Systematic Process

```
Observe → Hypothesize → Test → Confirm → Fix → Verify → Document
```

Never skip steps. Each step builds on the previous.

## Integration with CCPM

### Use with `/ccpm:verify`

When verification fails, this skill provides structured debugging:

```
User: "/ccpm:verify AUTH-123"

Claude: [ccpm-debugging activates]

Starting systematic debugging for AUTH-123...

Step 1: GATHER SYMPTOMS
Running /ccpm:verify to collect error information...

Found failures:
- 3 failing tests
- 1 TypeScript compilation error
- 0 linting errors

Step 2: ANALYZE FAILURES

Test failures:
1. test/auth/login.test.ts:45
   ❌ "should handle invalid credentials"
   Expected: 401 Unauthorized
   Actual: 500 Internal Server Error

2. test/auth/jwt.test.ts:78
   ❌ "should refresh expired tokens"
   Error: Cannot read property 'id' of undefined

3. test/api/protected.test.ts:23
   ❌ "should reject missing tokens"
   Timeout: Request exceeded 5000ms

TypeScript error:
src/auth/jwt.ts:45:12
- error TS2345: Argument of type 'string | undefined' is not assignable to parameter of type 'string'

Step 3: FORM HYPOTHESES

Hypothesis 1: JWT token handling has type safety issue (TS error)
Hypothesis 2: Error handling missing (test 1 shows 500 instead of 401)
Hypothesis 3: Token refresh logic has null reference bug (test 2)
Hypothesis 4: Protected route timeout suggests authentication middleware hanging (test 3)

Step 4: TEST HYPOTHESES
[Proceeds with systematic testing...]

Step 5: UPDATE LINEAR
Adding debugging findings to AUTH-123...
```

### Linear Integration

**Automatic Linear updates during debugging**:

```typescript
// After each debugging step
Linear.addComment(taskId, {
  title: "Debugging Progress",
  content: `
## Symptoms
- 3 failing tests (login, jwt, protected routes)
- TypeScript type error in jwt.ts:45

## Root Cause
Missing null check before accessing user.id in token refresh

## Fix Applied
Added null guard and proper error handling

## Verification
Re-running tests...
  `
});
```

### Blocker Logging

**When encountering blockers**:

```
Claude: [ccpm-debugging detects blocker]

Found BLOCKER: Redis connection failing in staging

Adding blocker to Linear AUTH-123:
---
🚫 BLOCKER: Redis Connection Issue

Environment: Staging
Error: ECONNREFUSED 127.0.0.1:6379
Impact: Cannot test session management

Investigation:
- Redis service not running in staging
- Docker compose missing Redis container
- Connection string using localhost instead of service name

Temporary workaround:
- Skip Redis-dependent tests in staging
- Test locally with Docker

Resolution needed:
- Update staging docker-compose.yml
- Fix Redis service configuration
---

Blocker logged. Continuing with fixable issues...
```

## Debugging Strategies

### Strategy 1: Error Message Analysis

**Read error messages carefully**:

```
Error: Cannot read property 'id' of undefined

Analysis:
1. What's undefined? → Some object
2. What property? → 'id'
3. Where? → Line number in stack trace
4. When? → During what operation?

Investigation:
- Check stack trace for call site
- Identify which object is undefined
- Trace back to why it's undefined
- Find where it should be defined
```

### Strategy 2: Reproduce Minimally

**Create minimal reproduction**:

```
Full test failing:
- 100 lines of setup
- Multiple database calls
- Complex state management

Minimal reproduction:
- 10 lines
- Mock database
- Isolated function call

Result: Easier to identify exact issue
```

### Strategy 3: Binary Search

**For intermittent or complex issues**:

```
Problem: Feature works sometimes, fails other times

Binary search approach:
1. Identify working commit (git bisect start)
2. Test middle commit
3. Mark as good or bad
4. Repeat until culprit commit found

Result: Exact commit that introduced bug
```

### Strategy 4: Rubber Duck Debugging

**Explain the problem out loud**:

```
User: "I don't understand why this is failing"

Claude: [ccpm-debugging activates]

Let's walk through this step by step:

1. What are you trying to do?
   → "Log in a user with JWT"

2. What do you expect to happen?
   → "Return 200 with token"

3. What actually happens?
   → "Returns 500 error"

4. What's different between expectation and reality?
   → "Error handling is missing"

5. Why would error handling be missing?
   → "Forgot to wrap async call in try-catch"

Often explaining the problem reveals the solution!
```

## Root-Cause Tracing

### The 5 Whys Technique

**Keep asking "why?" until you find root cause**:

```
Problem: Tests failing in CI but passing locally

Why? → Database connection timeout in CI
Why? → Database takes longer to start in CI
Why? → No health check waiting for database
Why? → Docker Compose doesn't have healthcheck configured
Why? → Template missing this configuration

Root cause: Missing healthcheck in docker-compose.yml template

Fix: Add healthcheck to template, not just local override
```

### Trace Backwards

**Start from symptom, trace backwards**:

```
Symptom: User sees "Internal Server Error"
       ↓
Application log: TypeError: Cannot read property 'email' of null
       ↓
Code: const email = user.email
       ↓
user comes from: await db.findUser(id)
       ↓
findUser returned: null (user not found)
       ↓
Why null? User ID was: undefined
       ↓
ID came from: req.params.userId
       ↓
Route defined as: /api/users/:id (not :userId)
       ↓
Root cause: Route parameter mismatch
```

## Common Debugging Patterns

### Pattern 1: Null/Undefined Issues

```typescript
// ❌ Crash waiting to happen
function getEmail(user) {
  return user.email;  // Crashes if user is null
}

// ✅ Defensive
function getEmail(user) {
  if (!user) {
    throw new Error('User is required');
  }
  return user.email;
}

// ✅ Even better with TypeScript
function getEmail(user: User | null): string {
  if (!user) {
    throw new Error('User is required');
  }
  return user.email;
}
```

### Pattern 2: Async/Await Errors

```typescript
// ❌ Unhandled promise rejection
async function login(email, password) {
  const user = await db.findUser(email);  // Could throw
  return generateToken(user);
}

// ✅ Proper error handling
async function login(email, password) {
  try {
    const user = await db.findUser(email);
    if (!user) {
      throw new UnauthorizedError('Invalid credentials');
    }
    return generateToken(user);
  } catch (error) {
    if (error instanceof DatabaseError) {
      logger.error('Database error during login', error);
      throw new ServiceUnavailableError();
    }
    throw error;
  }
}
```

### Pattern 3: Race Conditions

```typescript
// ❌ Race condition
let counter = 0;
async function increment() {
  const current = counter;
  await delay(10);
  counter = current + 1;  // Lost updates!
}

// ✅ Atomic operation
let counter = 0;
const lock = new Mutex();
async function increment() {
  await lock.acquire();
  try {
    counter++;
  } finally {
    lock.release();
  }
}
```

## Debugging Workflows

### Workflow 1: Test Failure

```
1. Read test failure message carefully
2. Identify what's expected vs actual
3. Find the code being tested
4. Add console.log or debugger
5. Re-run test in isolation
6. Step through with debugger
7. Identify exact line causing failure
8. Fix the issue
9. Verify test passes
10. Update Linear with fix
```

### Workflow 2: Runtime Error

```
1. Capture full error message + stack trace
2. Identify error location from stack trace
3. Reproduce error consistently
4. Add error handling/logging at error site
5. Trace backwards to root cause
6. Fix root cause (not just symptom)
7. Add test to prevent regression
8. Update Linear with findings
```

### Workflow 3: Performance Issue

```
1. Measure baseline performance
2. Profile to find bottleneck
3. Hypothesize cause
4. Test hypothesis (enable/disable features)
5. Confirm bottleneck
6. Optimize bottleneck
7. Measure improvement
8. Document in Linear
```

## Integration with Other CCPM Skills

### Works with `ccpm-code-review`

```
Debugging fixes issue
       ↓
ccpm-code-review verifies fix
       ↓
- Tests now passing?
- Build successful?
- Issue documented?
       ↓
Only then mark as complete
```

### Works with `sequential-thinking`

```
Complex debugging scenario
       ↓
sequential-thinking structures investigation
       ↓
Thought 1: Gather symptoms
Thought 2: Form hypotheses
Thought 3: Test hypothesis A
Thought 4: Root cause identified
Thought 5: Fix applied
Thought 6: Verification complete
```

### Works with `pm-workflow-guide`

```
Debugging started
       ↓
pm-workflow-guide suggests /ccpm:verify
       ↓
ccpm-debugging provides systematic approach
       ↓
After fix: pm-workflow-guide suggests /ccpm:verify
```

## Examples

### Example 1: Failing Tests

```
User: "Tests are failing, help me debug"

Claude: [ccpm-debugging activates]

Starting systematic debugging...

Step 1: GATHER SYMPTOMS
Running tests to collect failure information...

Found 2 failures:
1. test/api/users.test.ts:34
   "GET /api/users should return user list"
   Expected status 200, got 404

2. test/api/users.test.ts:45
   "POST /api/users should create user"
   Expected status 201, got 404

Step 2: PATTERN RECOGNITION
Both tests hitting /api/users route → routing issue likely

Step 3: HYPOTHESIS
Route might not be registered or path is wrong

Step 4: INVESTIGATION
Checking route registration...

Found in src/app.ts:
```typescript
app.use('/api/user', userRouter);  // ← Missing 's'
```

Should be:
```typescript
app.use('/api/users', userRouter);
```

Step 5: ROOT CAUSE
Typo in route registration path

Step 6: FIX
Updated route path to '/api/users'

Step 7: VERIFICATION
Re-running tests...
✅ All tests passing (2/2)

Step 8: LINEAR UPDATE
Adding fix summary to Linear task...

Issue: Route registration typo
Fix: Corrected /api/user → /api/users
Tests: Now passing
Commit: abc123f

Debugging complete! ✅
```

### Example 2: Memory Leak

```
User: "Application memory usage keeps growing"

Claude: [ccpm-debugging activates]

Investigating memory issue...

Step 1: MEASURE BASELINE
Starting memory: 150MB
After 1 hour: 450MB
After 2 hours: 750MB
Pattern: Linear growth → memory leak

Step 2: PROFILE
Using Node.js --inspect and Chrome DevTools...

Heap snapshot shows:
- EventEmitter listeners growing
- WebSocket connections not being cleaned up

Step 3: HYPOTHESIS
Event listeners not being removed after WebSocket disconnect

Step 4: CODE INVESTIGATION
Found in src/websocket/handler.ts:
```typescript
wss.on('connection', (ws) => {
  ws.on('message', handleMessage);
  ws.on('close', () => {
    console.log('Connection closed');
    // ❌ Not removing listeners!
  });
});
```

Step 5: FIX
```typescript
wss.on('connection', (ws) => {
  const messageHandler = handleMessage.bind(null, ws);
  ws.on('message', messageHandler);
  ws.on('close', () => {
    ws.removeListener('message', messageHandler);
    ws.removeAllListeners();
    console.log('Connection closed and cleaned up');
  });
});
```

Step 6: VERIFICATION
Running memory test for 2 hours...
Memory stable at ~160MB ✅

Step 7: LINEAR UPDATE
Blocker resolved: Memory leak in WebSocket handler
Fix: Proper cleanup of event listeners
Testing: 2-hour stability test passed

Debugging complete! ✅
```

## Tips for Effective Debugging

### Do's

- ✅ Read error messages completely
- ✅ Check stack traces for exact line numbers
- ✅ Reproduce issue consistently
- ✅ Create minimal reproduction
- ✅ Test one hypothesis at a time
- ✅ Document findings in Linear
- ✅ Add regression tests
- ✅ Fix root cause, not symptoms

### Don'ts

- ❌ Make random changes hoping to fix it
- ❌ Skip error messages
- ❌ Change multiple things at once
- ❌ Ignore warnings
- ❌ Forget to verify the fix
- ❌ Leave debugging code in production
- ❌ Skip documentation

## Summary

This skill provides:

- ✅ Systematic debugging approach
- ✅ Root-cause tracing
- ✅ Linear integration for tracking
- ✅ Blocker logging
- ✅ Defense-in-depth investigation
- ✅ Integration with CCPM verification workflow

**Philosophy**: Systematic over random, root-cause over symptoms, document for future.

---

**Source**: Adapted from [claudekit-skills/debugging](https://github.com/mrgoonie/claudekit-skills)
**License**: MIT
**CCPM Integration**: `/ccpm:verify`, `/ccpm:sync`, Linear blocker tracking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
