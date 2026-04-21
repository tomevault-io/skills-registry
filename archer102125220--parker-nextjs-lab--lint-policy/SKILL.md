---
name: lint-policy-error-handling
description: Guidelines for handling lint errors and when to use disable comments Use when this capability is needed.
metadata:
  author: archer102125220
---

# Lint Policy & Error Handling

## 🎯 When to Use This Skill

Use this skill when:
- Encountering ESLint warnings or errors
- Encountering TypeScript errors
- Deciding whether to fix or suppress lint errors
- Adding lint disable comments
- **Confused about whether to fix or suppress**
- Receiving lint feedback from IDE

## 📋 Decision Tree

### Step 1: Can the issue be fixed properly?

**Ask yourself**:
- Is this a legitimate code issue?
- Can I fix it without breaking functionality?
- Is the fix straightforward?

- **YES** → Fix it (preferred) - Go to Step 3
- **NO** → Continue to Step 2

---

### Step 2: Ask user for permission

**NEVER add disable comments without explicit user instruction.**

**Template**:
```
AI: "I encountered a lint warning: [description]
     
     Options:
     1. Fix it properly (recommended)
     2. Add a disable comment with justification
     
     Which approach should I take?"
```

**Wait for user response before proceeding.**

---

### Step 3: Fix properly (Preferred)

Apply the appropriate fix based on the lint rule.

---

## ✅ Correct Examples

### Example 1: Fix Properly (Preferred)

#### Scenario: Prefer const

```typescript
// ❌ Lint warning: Prefer const
let value = 10;

// ✅ Fixed
const value = 10;
```

#### Scenario: Unused variable

```typescript
// ❌ Lint warning: 'data' is declared but never used
const data = fetchData();

// ✅ Fixed - Remove unused variable
// (or use it if it was meant to be used)
```

#### Scenario: Missing await

```typescript
// ❌ Lint warning: Promise returned in function is not awaited
async function loadData() {
  fetchData();  // ❌ Missing await
}

// ✅ Fixed
async function loadData() {
  await fetchData();
}
```

#### Scenario: Unsafe any

```typescript
// ❌ Lint warning: Unsafe use of any
function process(data: any) {
  return data.value;
}

// ✅ Fixed - Use proper types
interface Data {
  value: string;
}

function process(data: Data) {
  return data.value;
}
```

---

### Example 2: Disable with User Permission

**Only after user explicitly approves:**

```typescript
// ✅ CORRECT - After user approval
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const legacyData: any = externalLibrary.getData();
// Reason: External library returns untyped data, will be fixed in v2
```

```typescript
// ✅ CORRECT - With justification
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const _debugInfo = process.env.DEBUG ? data : null;
// Reason: Used only in debug mode
```

```typescript
// ✅ CORRECT - Temporary workaround
// @ts-expect-error - Type mismatch in third-party library
const result = legacyLib.process(data);
// TODO: Remove when library updates to v3
```

---

## ❌ Common Mistakes

### Mistake 1: Auto-adding Disable Comments

```typescript
// ❌ WRONG - Added without asking user
// eslint-disable-next-line
const data: any = getData();

// ✅ CORRECT - Ask user first, then add with justification
AI: "Should I add a disable comment for the 'any' type warning?"
// (Wait for user approval)
```

---

### Mistake 2: Disabling Without Justification

```typescript
// ❌ WRONG - No explanation
// eslint-disable-next-line
const value = data.value;

// ✅ CORRECT - With justification
// eslint-disable-next-line @typescript-eslint/no-non-null-assertion
const value = data.value!;
// Reason: Value is guaranteed to exist after validation
```

---

### Mistake 3: Disabling Entire File

```typescript
// ❌ WRONG - Disabling for entire file
/* eslint-disable @typescript-eslint/no-explicit-any */

// ✅ CORRECT - Disable only specific lines
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const data: any = legacyLib.getData();
```

---

### Mistake 4: Using Generic Disable

```typescript
// ❌ WRONG - Generic disable (disables ALL rules)
// eslint-disable-next-line
const value = data.value;

// ✅ CORRECT - Specific rule
// eslint-disable-next-line @typescript-eslint/no-non-null-assertion
const value = data.value!;
```

---

## 📋 Common Lint Errors & Fixes

### TypeScript Errors

| Error | Fix |
|-------|-----|
| `any` type | Use specific types or `unknown` |
| Unused variable | Remove or prefix with `_` |
| Missing await | Add `await` keyword |
| Non-null assertion | Add null check or use optional chaining |
| Type mismatch | Fix type or use type assertion |

### ESLint Errors

| Error | Fix |
|-------|-----|
| Prefer const | Change `let` to `const` |
| No console | Remove or use proper logging |
| No unused vars | Remove or prefix with `_` |
| Require await | Add `await` or remove `async` |
| No explicit any | Use specific types |

---

## 📝 Checklist

### Before Adding Disable Comment

- [ ] Attempted to fix lint error properly first
- [ ] Verified fix is not straightforward
- [ ] Asked user for permission
- [ ] Added specific rule (not generic `eslint-disable-next-line`)
- [ ] Added justification comment explaining why

### When Fixing Lint Errors

- [ ] Identified the root cause
- [ ] Applied the recommended fix
- [ ] Verified fix doesn't break functionality
- [ ] Tested the change
- [ ] Checked if similar issues exist elsewhere

---

## 💡 Pro Tips

### Tip 1: Fix First, Suppress Last

Always try to fix the issue properly before considering a disable comment.

**Priority order**:
1. ✅ Fix the code properly
2. ✅ Refactor to avoid the issue
3. ⚠️ Ask user for permission to suppress
4. ❌ Never auto-suppress

---

### Tip 2: Use Specific Disable Rules

```typescript
// ❌ BAD - Generic disable
// eslint-disable-next-line

// ✅ GOOD - Specific rule
// eslint-disable-next-line @typescript-eslint/no-explicit-any
```

**Why**: Generic disables hide other potential issues.

---

### Tip 3: Add Justification Comments

Always explain WHY you're disabling a rule:

```typescript
// ✅ GOOD
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const data: any = legacyLib.getData();
// Reason: Legacy library returns untyped data, will be fixed in v2
```

---

### Tip 4: Prefer `@ts-expect-error` over `@ts-ignore`

```typescript
// ❌ BAD - Silently ignores error
// @ts-ignore
const result = legacyLib.process(data);

// ✅ GOOD - Fails if error is fixed
// @ts-expect-error - Type mismatch in third-party library
const result = legacyLib.process(data);
```

**Why**: `@ts-expect-error` will fail if the error is fixed, reminding you to remove the comment.

---

### Tip 5: Temporary vs Permanent Suppressions

**Temporary** (with TODO):
```typescript
// @ts-expect-error - Type mismatch in third-party library
const result = legacyLib.process(data);
// TODO: Remove when library updates to v3
```

**Permanent** (with explanation):
```typescript
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const legacyData: any = externalLib.getData();
// Reason: External library will never provide types
```

---

## 🔗 Related Rules

- `.agent/rules/lint-policy.md`
- `GEMINI.md` - Lint Disable Comments section
- `CLAUDE.md` - Lint Policy section

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archer102125220) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
