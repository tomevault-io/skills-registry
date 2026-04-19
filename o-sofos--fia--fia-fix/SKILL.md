---
name: fia-fix
description: Fix common Fia pattern issues. Use when debugging reactive code, fixing signal usage errors, or correcting anti-patterns. Use when this capability is needed.
metadata:
  author: o-sofos
---

# Fia Fix Skill

Automatically detects and fixes common Fia pattern issues, anti-patterns, and mistakes in reactive code.

## Usage

```bash
/fia-fix [file-path]
```

## Arguments

- `$0` (optional): File path to analyze and fix
  - If provided, fixes issues in that specific file
  - If omitted, analyzes the current file or recently modified Fia files

## What This Skill Fixes

### 1. Missing `.value` in Reactive Contexts

**Detects:**

- Signals accessed without `.value` in computed signals
- Signals accessed without `.value` in effects
- Signals used directly in expressions

**Fixes:**

```typescript
// Before
const doubled = $(() => count * 2);

// After
const doubled = $(() => count.value * 2);
```

### 2. Event Handler Issues

**Detects:**

- Using `e.target` instead of `e.currentTarget`
- Missing type annotations on event handlers
- Incorrect event handler signatures

**Fixes:**

```typescript
// Before
oninput: (e) => (value.value = e.target.value);

// After
oninput: (e) => (value.value = e.currentTarget.value);
```

### 3. Missing Effect Cleanup

**Detects:**

- Effects with timers that don't return cleanup
- Effects with event listeners that don't remove them
- Effects with subscriptions that don't dispose

**Fixes:**

```typescript
// Before
$e(() => {
  const timer = setInterval(() => tick(), 1000);
});

// After
$e(() => {
  const timer = setInterval(() => tick(), 1000);
  return () => clearInterval(timer);
});
```

### 4. Static vs. Reactive Content

**Detects:**

- Static string interpolation that should be reactive
- Signals used in template literals without computed
- Props that should be reactive but aren't

**Fixes:**

```typescript
// Before
p({ textContent: `Count: ${count.value}` });

// After
p({ textContent: $(() => `Count: ${count.value}`) });
```

### 5. Unbatched Updates

**Detects:**

- Multiple sequential signal assignments
- Related state changes in sequence
- Effects that could run fewer times

**Fixes:**

```typescript
// Before
state.field1 = value1;
state.field2 = value2;
state.field3 = value3;

// After
batch(() => {
  state.field1 = value1;
  state.field2 = value2;
  state.field3 = value3;
});
```

### 6. Effect for Derived State

**Detects:**

- Effects that only update another signal
- Unnecessary signal assignments in effects
- Derived values not using computed

**Fixes:**

```typescript
// Before
const doubled = $(0);
$e(() => {
  doubled.value = count.value * 2;
});

// After
const doubled = $(() => count.value * 2);
```

### 7. Context Issues

**Detects:**

- Elements created outside execution context
- Missing parent context setup
- Elements created in wrong scope

**Fixes:**
Wraps elements in proper execution context or moves them to correct location.

### 8. Type Errors

**Detects:**

- Missing SmartElement types
- Incorrect prop type validation
- Missing generic parameters

**Fixes:**
Adds appropriate type annotations and generic parameters.

## How It Works

1. **Scan File** - Reads the target file and parses TypeScript
2. **Pattern Matching** - Uses regex and AST patterns from [patterns/common-fixes.md](patterns/common-fixes.md)
3. **Issue Detection** - Identifies all fixable issues
4. **Apply Fixes** - Uses Edit tool to apply corrections
5. **Verify** - Runs `bun tsc --noEmit` to ensure no type errors
6. **Report** - Shows what was fixed and any remaining issues

## Fix Categories

### Automatic Fixes (Applied without confirmation)

- Missing `.value` on signals
- `e.target` → `e.currentTarget`
- Static text → reactive text
- Simple type annotations

### Suggested Fixes (Require confirmation)

- Adding effect cleanup (may need custom logic)
- Converting effects to computed (may change behavior)
- Batching updates (need to verify scope)
- Refactoring context usage

### Manual Fixes (Reported but not automatically fixed)

- Complex type issues
- Architectural changes
- Performance optimizations
- Custom business logic

## Output Format

```
# Fia Fix Report

## File: src/components/MyComponent.ts

### Fixed Automatically ✅

1. **Missing .value in computed (line 42)**
   - Changed: `$(() => count * 2)`
   - To: `$(() => count.value * 2)`

2. **Event handler using e.target (line 58)**
   - Changed: `e.target.value`
   - To: `e.currentTarget.value`

### Suggested Fixes ⚠️

1. **Effect for derived state (line 72)**
   - Consider using computed signal instead
   - Current: Effect updates `doubled` signal
   - Suggested: `const doubled = $(() => count.value * 2);`
   - Apply this fix? [Y/n]

### Manual Review Needed 🔍

1. **Complex effect cleanup (line 95)**
   - Effect creates WebSocket connection
   - Add cleanup: `return () => ws.close();`
   - Review and add manually

## Summary
- 2 issues fixed automatically
- 1 suggested fix pending
- 1 manual review needed
- Type check: ✅ Passed
```

## Examples

### Fix current file

```bash
/fia-fix
```

### Fix specific file

```bash
/fia-fix src/components/Counter.ts
```

### Fix all components

```bash
/fia-fix src/components/*.ts
```

## Integration with Other Skills

- Use `/fia-component` to create new components with correct patterns
- Use `/fia-optimize` after fixing to improve performance
- Use `/fia-docs` to document the fixed code
- Run pattern-validator agent to verify all patterns are correct

## Reference

See [patterns/common-fixes.md](patterns/common-fixes.md) for the complete list of detectable patterns and their fixes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/o-sofos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
