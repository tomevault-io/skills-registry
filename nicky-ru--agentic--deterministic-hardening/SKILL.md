---
name: deterministic-hardening
description: 7-layer deterministic validation framework for TypeScript projects. Use this skill to harden codebases against AI hallucinations with automated, deterministic tools. Use when this capability is needed.
metadata:
  author: nicky-ru
---

# Deterministic Hardening

Framework for constraining AI-generated code with deterministic validation layers. The goal: make trust in AI irrelevant through automated verification.

## Why This Matters

AI-generated code is non-deterministic. Code that "looks right" can fail at runtime. These layers create a validation funnel where code must pass increasingly strict gates before deployment.

## Layer Overview

| Layer | Tool | Catches | Priority |
|-------|------|---------|----------|
| 1 | TypeScript strict | Type errors, null crashes | **High** |
| 2 | ast-grep | Logical errors, API misuse | **High** |
| 3 | Zod | Runtime type mismatches | **High** |
| 4 | Assertions | State violations | Medium |
| 5 | Pact | API contract breaks | Medium |
| 6 | Stryker | Weak test suites | Medium |
| 7 | fast-check | Edge case logic errors | Medium |

**Start with layers 1-3** — they catch 80% of AI hallucinations with minimal setup.

---

## Layer 1: TypeScript Strict Mode

Maximizes compile-time safety. AI often generates code without type annotations or null handling.

### Detection

Check `tsconfig.json` for:

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

### Setup

Add to `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictBindCallApply": true,
    "strictFunctionTypes": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "useUnknownInCatchVariables": true,
    "exactOptionalPropertyTypes": true
  }
}
```

### Key Flags

- **`noImplicitAny`**: Forces explicit types. AI often omits annotations.
- **`strictNullChecks`**: Forces null/undefined handling. AI frequently forgets API responses can be null.

### npm Script

```json
{
  "scripts": {
    "typecheck": "tsc --noEmit"
  }
}
```

---

## Layer 2: Semantic Linting (ast-grep)

Pattern matching on AST, not text. Catches logical errors that look syntactically correct.

### Detection

Check for `.ast-grep.yaml` or `ast-grep` in `package.json` devDependencies.

### Setup

```bash
npm install --save-dev @ast-grep/cli
```

Create `.ast-grep.yaml`:

```yaml
# Catch await inside Promise.all (common AI mistake)
id: no-await-in-promise-all
language: TypeScript
rule:
  pattern: Promise.all($PROMISES)
  has:
    pattern: await $_
    stopBy: end
message: "Avoid await inside Promise.all - use map instead"
severity: error

---
# Catch dangerous type assertions
id: dangerous-type-assertion
language: TypeScript
rule:
  pattern: $VAR as $TYPE
message: "Avoid type assertions - use type guards instead"
severity: warning

---
# Catch missing error handling
id: unhandled-async
language: TypeScript
rule:
  pattern: async function $NAME($ARGS) { $BODY }
  not:
    has:
      pattern: try { $$ } catch { $$ }
message: "Async functions should have error handling"
severity: warning
```

### npm Script

```json
{
  "scripts": {
    "lint:semantic": "ast-grep scan",
    "lint": "biome check --write && npm run lint:semantic"
  }
}
```

---

## Layer 3: Runtime Validation (Zod)

TypeScript types are erased at runtime. External data (API responses, user input) needs runtime validation.

### Detection

Check for `zod` in `package.json` dependencies.

### Setup

```bash
npm install zod
```

### Patterns

**API Response Validation:**

```typescript
import { z } from 'zod';

const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1),
  email: z.string().email(),
});

type User = z.infer<typeof UserSchema>;

async function getUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  return UserSchema.parse(data); // Throws if invalid
}
```

**Environment Variables:**

```typescript
const EnvSchema = z.object({
  DATABASE_URL: z.string().url(),
  API_KEY: z.string().min(32),
  PORT: z.coerce.number().int().default(3000),
});

export const env = EnvSchema.parse(process.env);
```

**Request Body Validation:**

```typescript
const CreateUserSchema = z.object({
  name: z.string().min(1).max(100),
  email: z.string().email(),
  password: z.string().min(8),
});

// In route handler
const user = CreateUserSchema.parse(req.body);
```

### Integration Points

Validate at boundaries:

1. API responses (external services)
2. Environment variables (startup)
3. Request bodies (incoming data)
4. Database results (if schema can drift)

---

## Layer 4: Assertion Functions

Use TypeScript's `asserts` keyword for critical code paths.

### Setup

```typescript
// src/lib/assertions.ts
export function invariant<T>(
  condition: T | null | undefined,
  message: string
): asserts condition is T {
  if (!condition) {
    throw new Error(`Invariant failed: ${message}`);
  }
}
```

### Usage

```typescript
function processOrder(userId: string | null) {
  invariant(userId, 'User ID required');
  // TypeScript now knows userId is string
}
```

---

## Layer 5: Contract Testing (Pact)

Validates that service integrations match expected contracts.

### Detection

Check for `@pact-foundation/pact` in devDependencies.

### Setup

```bash
npm install --save-dev @pact-foundation/pact
```

### When to Add

Add when:

- Integrating with external APIs
- Building microservices that communicate
- API shape assumptions have caused production bugs

---

## Layer 6: Mutation Testing (Stryker)

Tests the tests. Introduces deliberate errors to verify tests catch them.

### Detection

Check for `@stryker-mutator/core` in devDependencies.

### Setup

```bash
npm install --save-dev @stryker-mutator/core @stryker-mutator/typescript-checker
```

### When to Add

Add when:

- Test coverage is high but bugs still slip through
- Validating test quality before major releases
- Team suspects tests are "coverage theater"

### npm Script

```json
{
  "scripts": {
    "test:mutation": "stryker run"
  }
}
```

---

## Layer 7: Property-Based Testing (fast-check)

Generates hundreds of randomized inputs to find edge cases.

### Detection

Check for `fast-check` in devDependencies.

### Setup

```bash
npm install --save-dev fast-check
```

### Example

```typescript
import fc from 'fast-check';

test('sort maintains all elements', () => {
  fc.assert(
    fc.property(fc.array(fc.integer()), (arr) => {
      const sorted = sort(arr);
      expect(sorted.length).toBe(arr.length);
      expect(sorted.sort()).toEqual(arr.sort());
    })
  );
});
```

### When to Add

Add when:

- Testing pure functions with many possible inputs
- Algorithm correctness is critical
- Unit tests feel incomplete but you can't think of more cases

---

## Layer Detection Summary

For agents to detect which layers are present:

| Layer | Detection Method |
|-------|------------------|
| 1 | `tsconfig.json` has `"strict": true` |
| 2 | `.ast-grep.yaml` exists OR `@ast-grep/cli` in devDeps |
| 3 | `zod` in dependencies |
| 4 | Search for `asserts` keyword in `src/**/*.ts` |
| 5 | `@pact-foundation/pact` in devDeps |
| 6 | `@stryker-mutator/core` in devDeps |
| 7 | `fast-check` in devDeps |

---

## Recommended npm Scripts

Full hardened setup:

```json
{
  "scripts": {
    "typecheck": "tsc --noEmit",
    "lint": "biome check --write && ast-grep scan",
    "lint:check": "biome check && ast-grep scan",
    "test": "bun test",
    "test:mutation": "stryker run",
    "test:e2e": "playwright test",
    "validate": "npm run typecheck && npm run lint:check && npm run test"
  }
}
```

---

## Gradual Hardening Strategy

For projects with no layers:

1. **Week 1**: Add Layer 1 (TypeScript strict) — fix type errors
2. **Week 2**: Add Layer 3 (Zod) — validate external data boundaries
3. **Week 3**: Add Layer 2 (ast-grep) — catch semantic patterns
4. **Ongoing**: Add layers 4-7 based on specific pain points

Don't try to add all layers at once. Each layer requires fixing existing violations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicky-ru) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
