---
name: tidy-first
description: This skill should be used when the user asks to "refactor", "clean up code", "restructure", "rename", "extract method", "improve code quality", or when separating structural changes from behavioral changes. Enforces Tidy First approach to keep commits clean and focused. Use when this capability is needed.
metadata:
  author: kaneshin
---

# Tidy First Skill

## Purpose

Enforce separation of structural changes (tidying/refactoring) from behavioral changes (new functionality) following the "Tidy First" methodology. Ensure each commit contains either structural OR behavioral changes, never both, and structural changes always come first.

## When to Use This Skill

Apply Tidy First approach when:
- About to add a feature but code structure makes it difficult
- Refactoring existing code to improve clarity or maintainability
- Renaming variables, methods, or classes
- Extracting methods or introducing abstractions
- Reorganizing code files or modules
- Preparing to commit changes

## Core Principle: Separate Structure from Behavior

### Two Types of Changes

**1. Structural Changes (Tidying)**
Rearranging code without changing what it does:
- Renaming variables, methods, classes
- Extracting methods or functions
- Moving code to different files
- Introducing abstractions
- Reorganizing imports
- Reformatting code
- Removing dead code

**2. Behavioral Changes (Features/Fixes)**
Changing what the code does:
- Adding new functionality
- Fixing bugs
- Modifying algorithms
- Changing business logic
- Adding or removing features
- Altering output or side effects

### The Golden Rule

**NEVER mix structural and behavioral changes in the same commit.**

Each commit should be:
- Pure structural (behavior stays exactly the same), OR
- Pure behavioral (structure stays the same or is only minimally affected)

## The Tidy First Workflow

### When to Tidy First

**Before adding a feature:**
1. Look at the code where the feature will go
2. If structure makes feature difficult to add cleanly, tidy first
3. Commit structural changes
4. Then add the feature
5. Commit behavioral changes separately

**Example scenario:**

Need to add discount calculation to order processing, but the `processOrder` method is 50 lines long and unclear.

**Tidy first:**
- Extract pricing logic into separate methods
- Rename unclear variables
- Commit: "Refactor: Extract pricing methods from processOrder"

**Then add feature:**
- Add discount calculation using the tidier structure
- Commit: "Add discount calculation to order processing"

### When NOT to Tidy First

**If structure is already clean:**
- Skip tidying if code is already well-organized
- Just add the feature directly

**If tidying doesn't help the current feature:**
- Don't tidy unrelated code "while you're there"
- Focus tidying on code relevant to current work

**When fixing urgent bugs:**
- Fix the bug first (minimal change)
- Tidy later if needed
- Exception: If tidying makes the fix obvious, tidy first

## Validation: Ensuring Structural Changes Don't Alter Behavior

After making structural changes, ALWAYS validate behavior is preserved:

### Run All Tests

**Before structural changes:**
```bash
npm test  # All tests should pass
```

**After structural changes:**
```bash
npm test  # All tests should STILL pass
```

If tests fail after structural changes, either:
- Fix the refactoring to preserve behavior, or
- Revert the changes and try a different approach

**Critical rule:** NEVER commit structural changes if tests fail.

### Validation Checklist

Before committing structural changes:

- [ ] All tests were passing before changes
- [ ] All tests still pass after changes
- [ ] No compiler warnings introduced
- [ ] No linter warnings introduced
- [ ] Code behavior is identical (verified by tests)
- [ ] Only structure changed, not behavior

### When Tests Don't Exist

If code lacks tests (legacy code):

1. **Add characterization tests first:**
   - Tests that document current behavior
   - Even if behavior is buggy, capture it first
   - Commit tests separately

2. **Then perform structural changes:**
   - Refactor with test protection
   - Verify tests still pass

3. **Then fix bugs if needed:**
   - Now that structure is clean
   - Tests will catch if fix breaks something

## Commit Discipline

### Commit Message Format

Clearly indicate commit type in message:

**Structural commits:**
```
Refactor: Extract calculateTax method from processOrder
Refactor: Rename confusing variable names in checkout flow
Refactor: Move validation logic to separate module
```

Start with "Refactor:" prefix to indicate structural change.

**Behavioral commits:**
```
Add discount calculation to order processing
Fix tax calculation for international orders
Update checkout flow to handle multiple payment methods
```

No "Refactor:" prefix—message describes the behavioral change.

### Commit Sequencing

When both structural and behavioral changes are needed:

**Correct sequence:**
```
1. Commit: "Refactor: Extract pricing logic"
2. Commit: "Add discount calculation"
```

Structural changes FIRST, behavioral changes SECOND.

**Incorrect sequence:**
```
1. Commit: "Add discount calculation and refactor pricing"  ❌
```

Never mix both in one commit.

### Atomic Commits

**Each commit should be independently valuable:**

- Structural commits leave code in working state (tests pass)
- Behavioral commits add one logical feature or fix
- Reviewer can understand each commit in isolation
- Easy to revert either commit without breaking the other

**Example: Adding Caching Feature**

Bad (mixed):
```
Commit: "Add caching and refactor data access layer"
- Renamed database methods
- Extracted query builder
- Added caching logic
- Updated error handling
```

Good (separated):
```
Commit 1: "Refactor: Extract query builder from data access"
Commit 2: "Refactor: Standardize database method naming"
Commit 3: "Add caching layer to data access"
```

Each commit is focused and reviewable.

## Identifying Structural vs Behavioral Changes

### Structural Change Examples

**Extract method:**
```diff
-function processOrder(order) {
-  const tax = order.total * 0.08;
-  const shipping = order.total > 50 ? 0 : 5.99;
-  return order.total + tax + shipping;
-}
+function processOrder(order) {
+  return order.total + calculateTax(order) + calculateShipping(order);
+}
+
+function calculateTax(order) {
+  return order.total * 0.08;
+}
+
+function calculateShipping(order) {
+  return order.total > 50 ? 0 : 5.99;
+}
```

**Rename variable:**
```diff
-function calc(n) {
-  const t = n * 0.08;
-  return n + t;
+function calculateTotalWithTax(subtotal) {
+  const tax = subtotal * 0.08;
+  return subtotal + tax;
}
```

**Move code to module:**
```diff
-// orders.js
-function validateEmail(email) { ... }
-function processOrder(order) { ... }

+// validation.js
+function validateEmail(email) { ... }

+// orders.js
+import { validateEmail } from './validation';
+function processOrder(order) { ... }
```

All these preserve exact behavior—output is identical.

### Behavioral Change Examples

**Add feature:**
```diff
function calculateTotal(order) {
-  return order.subtotal + calculateTax(order);
+  const discount = order.discountCode ? applyDiscount(order) : 0;
+  return order.subtotal + calculateTax(order) - discount;
}
```

**Fix bug:**
```diff
function calculateShipping(order) {
-  return order.total > 50 ? 0 : 5.99;
+  return order.subtotal > 50 ? 0 : 5.99;  // Fixed: should use subtotal
}
```

**Change algorithm:**
```diff
function findUser(users, id) {
-  for (const user of users) {
-    if (user.id === id) return user;
-  }
+  return users.find(user => user.id === id);
}
```

Wait—is this structural or behavioral? **Behavioral**, because the algorithm change could affect performance or edge cases (though output may be same).

## Gray Areas and Guidelines

Some changes are ambiguous. Use these guidelines:

### Performance Optimizations

**Usually behavioral:**
```diff
-function sum(numbers) {
-  return numbers.reduce((a, b) => a + b, 0);
+function sum(numbers) {
+  let total = 0;
+  for (const n of numbers) total += n;
+  return total;
}
```

Even if output is identical, performance change affects behavior. Commit as behavioral.

### Algorithm Changes

**Always behavioral:**

Even if output is same for all current inputs, different algorithm = different behavior. Consider edge cases, performance, memory usage.

### Changing Abstraction Level

**Usually structural:**

```diff
-const result = users.filter(u => u.active).map(u => u.name);
+const result = getActiveUserNames(users);
```

Extracting to named function is structural if implementation stays identical.

**When in doubt, treat as behavioral.** Safer to be conservative.

## Practical Example: Adding Feature to Messy Code

### Scenario

Need to add email notification to checkout process. Current code:

```javascript
function checkout(cart, user) {
  const t = 0;
  for (let i = 0; i < cart.items.length; i++) {
    t += cart.items[i].p * cart.items[i].q;
  }
  const tx = t * 0.08;
  const sh = t > 50 ? 0 : 5.99;
  const gt = t + tx + sh;

  // Save order
  db.save({ userId: user.id, total: gt });

  return gt;
}
```

Code is unclear. Need to add email notification.

### Step 1: Tidy First (Structural Changes)

**Improve variable names:**
```javascript
function checkout(cart, user) {
  const subtotal = 0;
  for (let i = 0; i < cart.items.length; i++) {
    subtotal += cart.items[i].price * cart.items[i].quantity;
  }
  const tax = subtotal * 0.08;
  const shipping = subtotal > 50 ? 0 : 5.99;
  const grandTotal = subtotal + tax + shipping;

  db.save({ userId: user.id, total: grandTotal });

  return grandTotal;
}
```

**Extract methods:**
```javascript
function checkout(cart, user) {
  const grandTotal = calculateGrandTotal(cart);
  saveOrder(user, grandTotal);
  return grandTotal;
}

function calculateGrandTotal(cart) {
  const subtotal = calculateSubtotal(cart);
  const tax = calculateTax(subtotal);
  const shipping = calculateShipping(subtotal);
  return subtotal + tax + shipping;
}

function calculateSubtotal(cart) {
  let total = 0;
  for (const item of cart.items) {
    total += item.price * item.quantity;
  }
  return total;
}

function calculateTax(subtotal) {
  return subtotal * 0.08;
}

function calculateShipping(subtotal) {
  return subtotal > 50 ? 0 : 5.99;
}

function saveOrder(user, total) {
  db.save({ userId: user.id, total });
}
```

**Validate:**
```bash
npm test  # All tests pass
```

**Commit:**
```
Refactor: Extract calculation and persistence methods from checkout

- Extract calculateGrandTotal, calculateSubtotal, calculateTax, calculateShipping
- Extract saveOrder for database persistence
- Improve variable naming for clarity
```

### Step 2: Add Feature (Behavioral Change)

Now code is clean. Adding email is straightforward:

```javascript
function checkout(cart, user) {
  const grandTotal = calculateGrandTotal(cart);
  const order = saveOrder(user, grandTotal);
  sendOrderConfirmation(user.email, order);  // New behavior
  return grandTotal;
}

function sendOrderConfirmation(email, order) {
  emailService.send(email, {
    subject: 'Order Confirmation',
    total: order.total
  });
}
```

**Validate:**
```bash
npm test  # Tests pass, including new email test
```

**Commit:**
```
Add email confirmation to checkout process

- Send order confirmation email after successful checkout
- Add sendOrderConfirmation function
- Add email service integration
```

### Result

Two clean, focused commits:
1. Structural improvements (made feature easy to add)
2. Behavioral addition (the actual feature)

Each commit is reviewable, revertible, and valuable independently.

## Integration with TDD

Tidy First complements TDD's refactor phase:

**TDD Cycle:**
1. Red: Write failing test
2. Green: Make test pass (may produce messy code)
3. **Refactor: Tidy the code** ← Apply Tidy First here
4. Commit structural changes separately
5. Commit behavioral changes (the feature) separately

**Commit pattern with TDD:**
```
Iteration 1:
  Commit: "Add password length validation"  (Red → Green, behavioral)
  Commit: "Refactor: Extract validation methods"  (Refactor phase, structural)

Iteration 2:
  Commit: "Add password uppercase requirement"  (Red → Green, behavioral)
```

See TDD Workflow skill for complete TDD guidance.

## Common Pitfalls

### Pitfall 1: "While I'm Here" Refactoring

❌ **Incorrect:**
```
Working on discount feature, notice ugly code in unrelated tax module.
Refactor tax module in same commit as discount feature.
```

✅ **Correct:**
```
Focus only on code relevant to discount feature.
If tax module needs tidying for discount, tidy it first (separate commit).
Otherwise, leave it for later.
```

### Pitfall 2: Tiny Structural Changes Mixed In

❌ **Incorrect:**
```diff
Commit: "Add discount calculation"
+function applyDiscount(order) {
-  const total = order.total;
+  const total = order.subtotal;  // Also renamed while adding feature
   return total * 0.9;
}
```

Even tiny renames should be separate commits when feasible.

✅ **Correct:**
```
Commit 1: "Refactor: Rename order.total to order.subtotal"
Commit 2: "Add discount calculation"
```

### Pitfall 3: Refactoring While Tests Are Failing

❌ **Incorrect:**
```
Test is failing, start refactoring to "clean up" the code before fixing.
```

✅ **Correct:**
```
Fix the test first (behavioral change).
Then refactor if needed (structural change, separate commit).
Or refactor first to make fix obvious, but commit refactor before fix.
```

### Pitfall 4: Not Running Tests Between Structural Changes

❌ **Incorrect:**
```
Make 5 refactorings, then run tests once at the end.
Tests fail—which refactoring broke it?
```

✅ **Correct:**
```
Refactor → run tests → commit
Refactor → run tests → commit
Each structural change validated immediately.
```

## Quick Reference

**When tidying:**
- Make structural changes only (no behavior change)
- Run tests before and after
- Commit with "Refactor:" prefix
- Never mix with behavioral changes

**When adding features:**
- If structure blocks feature, tidy first (separate commit)
- Add feature to clean structure
- Commit behavioral change separately

**Before every commit:**
- [ ] All tests passing
- [ ] No warnings
- [ ] Commit is pure structural OR pure behavioral
- [ ] Commit message clearly indicates type

**Commit sequence:**
1. Structural changes (if needed)
2. Behavioral changes (the feature/fix)

Never reverse this order.

## Additional Resources

**Reference files:**
- **`references/structural-changes-catalog.md`** - Comprehensive list of structural change patterns
- **`references/gray-areas.md`** - Detailed guidance on ambiguous cases

**Example files:**
- **`examples/tidy-first-session.md`** - Complete example of tidying before feature addition

Consult these for detailed patterns and edge cases when applying Tidy First methodology.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaneshin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
