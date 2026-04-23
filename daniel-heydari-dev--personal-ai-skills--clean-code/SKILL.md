---
name: clean-code
description: Principles for writing readable, maintainable, and simple code. Use when writing new functions, refactoring existing code, doing code reviews, or when user asks about "naming conventions", "code readability", "clean code", or "code quality". Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Clean Code

Write code that is easy to read, understand, and maintain.

## Naming

### Rules

- ✅ DO: Use descriptive, intention-revealing names
- ✅ DO: Use verbs for functions (`getUserById`, `calculateTotal`)
- ✅ DO: Use nouns for classes and variables (`user`, `orderList`)
- ✅ DO: Use consistent naming conventions (camelCase, PascalCase)
- ❌ DON'T: Use abbreviations (`btn`, `msg`, `usr`, `idx`)
- ❌ DON'T: Use single letters except in short loops (`i`, `j` ok in loops)
- ❌ DON'T: Use Hungarian notation (`strName`, `intCount`)
- ❌ DON'T: Add unnecessary context (`userUserName` → `user.name`)

### Examples

```typescript
// ❌ Bad
const d = new Date(); // What is d?
const ymd = formatDate(d); // Cryptic
function proc(u: any) {} // Unclear

// ✅ Good
const currentDate = new Date();
const formattedDate = formatDate(currentDate);
function processUser(user: User) {}
```

## Functions

### Rules

- ✅ DO: Keep functions small (under 20 lines ideally)
- ✅ DO: Do one thing (single responsibility)
- ✅ DO: Use early returns to reduce nesting
- ✅ DO: Limit parameters to 3 or fewer (use object for more)
- ❌ DON'T: Mix abstraction levels
- ❌ DON'T: Use flag arguments (split into two functions)
- ❌ DON'T: Have side effects that aren't obvious from the name

### Examples

```typescript
// ❌ Bad - does multiple things, deep nesting
function processOrder(order: Order) {
  if (order) {
    if (order.items.length > 0) {
      if (order.user) {
        // validate
        // calculate
        // save
        // send email
      }
    }
  }
}

// ✅ Good - single responsibility, early returns
function processOrder(order: Order) {
  validateOrder(order);
  const total = calculateTotal(order);
  saveOrder(order, total);
  notifyUser(order.user);
}

function validateOrder(order: Order) {
  if (!order) throw new Error("Order is required");
  if (order.items.length === 0) throw new Error("Order has no items");
  if (!order.user) throw new Error("Order has no user");
}
```

## Comments

### Rules

- ✅ DO: Write self-documenting code instead of comments
- ✅ DO: Use comments for "why", not "what"
- ✅ DO: Document public APIs with JSDoc
- ✅ DO: Add TODO/FIXME with ticket numbers
- ❌ DON'T: Comment obvious code
- ❌ DON'T: Leave commented-out code
- ❌ DON'T: Write redundant comments

### Examples

```typescript
// ❌ Bad - obvious comment
// increment counter by 1
counter += 1;

// ❌ Bad - commented code
// const oldValue = calculateOldWay(x);
const value = calculateNewWay(x);

// ✅ Good - explains why
// Using floor to ensure integer for pagination offset
const offset = Math.floor(page * limit);

// ✅ Good - JSDoc for public API
/**
 * Calculates the total price including tax.
 * @param items - Array of items with price property
 * @param taxRate - Tax rate as decimal (e.g., 0.08 for 8%)
 * @returns Total price with tax applied
 */
function calculateTotal(items: Item[], taxRate: number): number {
  // ...
}
```

## Code Organization

### Rules

- ✅ DO: Group related code together
- ✅ DO: Order: imports, types, constants, main code, helpers
- ✅ DO: Keep files focused (one component/class per file)
- ✅ DO: Use consistent file naming
- ❌ DON'T: Mix unrelated functionality
- ❌ DON'T: Create god files with thousands of lines

## Magic Numbers & Strings

### Rules

- ✅ DO: Extract magic numbers to named constants
- ✅ DO: Use enums for related constants
- ❌ DON'T: Hardcode values that have meaning

### Examples

```typescript
// ❌ Bad
if (user.age >= 18) {
}
if (status === 1) {
}
setTimeout(fn, 86400000);

// ✅ Good
const MINIMUM_AGE = 18;
if (user.age >= MINIMUM_AGE) {
}

enum OrderStatus {
  Pending = 1,
  Completed = 2,
}
if (status === OrderStatus.Pending) {
}

const ONE_DAY_MS = 24 * 60 * 60 * 1000;
setTimeout(fn, ONE_DAY_MS);
```

## Simplicity (KISS)

### Rules

- ✅ DO: Choose the simplest solution that works
- ✅ DO: Avoid premature optimization
- ✅ DO: Avoid premature abstraction
- ❌ DON'T: Over-engineer for hypothetical future needs
- ❌ DON'T: Add complexity without clear benefit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
