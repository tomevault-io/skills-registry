---
name: verdex-playwright-complete
description: Complete Playwright test authoring workflow with Verdex MCP tools. Three phases - explore apps interactively to map user journeys, build stable role-first selectors via progressive DOM exploration, write idiomatic Playwright tests. Includes workflow discovery, selector patterns, and testing best practices. Use when this capability is needed.
metadata:
  author: verdexhq
---

# Verdex Playwright Complete Workflow

Complete test authoring in three phases: **Explore → Select → Test**

## When to Use This Skill

**Use this skill when you need to:**
- Explore unfamiliar web applications and map user workflows
- Write stable, maintainable Playwright selectors that avoid positional `.nth()` selectors
- Create production-ready Playwright tests following best practices
- Understand complex UIs through progressive DOM exploration

## The Complete Workflow

### Phase 1: Explore & Map Workflows 🗺️
**Goal**: Understand the application and user journeys before writing any code

**When to read the detailed guide**: When starting work on a new application or feature
- Read **[guides/workflow-discovery.md](guides/workflow-discovery.md)** for interactive exploration techniques
- Use Verdex MCP browser tools (`browser_navigate`, `browser_click`, `browser_snapshot`) with refs
- Document user journeys, state transitions, and assertion points
- Identify all interactive elements and their behaviors

### Phase 2: Build Stable Selectors 🎯
**Goal**: Convert exploration discoveries into production-ready Playwright selectors

**When to read the detailed guide**: After workflow exploration, when you have refs and need selectors
- Read **[guides/selector-writing.md](guides/selector-writing.md)** for comprehensive selector patterns
- Use the **Container → Content → Role** pattern
- Apply progressive DOM exploration (`resolve_container`, `inspect_pattern`, `extract_anchors`)
- Check uniqueness first—many elements need only simple selectors

### Phase 3: Write Idiomatic Tests ✍️
**Goal**: Transform selectors and workflows into clean, maintainable Playwright tests

**When to read the detailed guide**: When you have selectors and workflows ready to code
- Read **[guides/playwright-patterns.md](guides/playwright-patterns.md)** for test structure and patterns
- Follow Arrange-Act-Assert pattern
- Use Playwright's auto-waiting (avoid manual waits)
- Create independent, focused tests

---

## Quick Start (Most Common Path)

### Step 1: Explore the Application

```typescript
await browser_initialize()
await browser_navigate("https://your-app.com")
// Snapshot shows interactive elements with refs (e1, e2, e3...)

// Click and interact using refs to explore
await browser_click("e5")
await browser_snapshot()  // See what changed
```

**Document** what you learn: URLs, actions, expected outcomes

### Step 2: Build Selectors

**Check uniqueness FIRST**:
```typescript
// Is element unique? If YES → use simple selector
page.getByRole("button", { name: "Proceed to Checkout" })

// If NO → use Container → Content → Role pattern
page
  .getByTestId("product-card")
  .filter({ hasText: "iPhone 15 Pro" })
  .getByRole("button", { name: "Add to Cart" })
```

**Progressive exploration** when needed:
```typescript
resolve_container("e25")     // Find stable containers
inspect_pattern("e25", 2)    // Understand siblings
extract_anchors("e25", 1)    // Mine deep content (optional)
```

### Step 3: Write Tests

```typescript
test('should add product to cart', async ({ page }) => {
  // Arrange
  await page.goto('/products')
  
  // Act
  await page
    .getByTestId('product-card')
    .filter({ hasText: 'iPhone 15 Pro' })
    .getByRole('button', { name: 'Add to Cart' })
    .click()
  
  // Assert
  await expect(page.getByText('Item added to cart')).toBeVisible()
  await expect(page.getByRole('link', { name: /Cart \(1\)/ })).toBeVisible()
})
```

---

## Core Principles (Keep These in Mind)

### The Winning Pattern: Container → Content → Role

```typescript
page
  .getByTestId("stable-container")           // 1. Scope to container
  .filter({ hasText: "unique-content" })     // 2. Filter by content
  .getByRole("button", { name: "Action" })   // 3. Target by role
```

### Critical Anti-Patterns to Avoid

```typescript
// ❌ NEVER: Positional selectors
page.getByRole("button").nth(5)
page.getByRole("button").first()  // .first() = .nth(0), still brittle

// ❌ NEVER: Filter in wrong place
page.getByRole("button").filter({ hasText: "text-in-sibling" })

// ❌ NEVER: Parent traversal
page.getByText("text").locator("..")

// ✅ ALWAYS: Check uniqueness first
const count = await page.getByRole("button", { name: "Submit" }).count()
// If count === 1, use simple selector. If > 1, add container scoping.
```

---

## Decision Trees

### Which Guide Should I Read?

```
Are you starting work on a new app/feature?
├─ YES → Read guides/workflow-discovery.md
│        Learn: Interactive exploration, mapping user journeys
│        Tools: browser_navigate, browser_click, browser_snapshot
│        Output: Workflow documentation with refs
│
└─ NO → Do you have refs and need to build selectors?
    ├─ YES → Read guides/selector-writing.md
    │        Learn: Container → Content → Role pattern
    │        Tools: resolve_container, inspect_pattern, extract_anchors
    │        Output: Stable Playwright selectors
    │
    └─ NO → Ready to write test code?
        └─ YES → Read guides/playwright-patterns.md
                 Learn: Test structure, assertions, best practices
                 Output: Production-ready Playwright tests
```

### Which Selector Approach Should I Use?

```
Take browser_snapshot() first, then ask:

Is my target element unique on the page?
├─ YES → Use simple selector ✅
│        page.getByRole("button", { name: "Unique Text" })
│        Done! No exploration needed.
│
└─ NO → Element appears multiple times
    │
    Call resolve_container(ref) to find stable containers
    │
    Found data-testid or semantic element?
    ├─ YES → Use as container scope
    │   │
    │   Call inspect_pattern(ref, level) to see siblings
    │   │
    │   Found unique text to filter by?
    │   ├─ YES → Build selector ✅
    │   │        page.getByTestId("container")
    │   │          .filter({ hasText: "unique" })
    │   │          .getByRole("button")
    │   │
    │   └─ NO → Call extract_anchors(ref, level) for deeper analysis
    │            Then build selector with discovered anchors
    │
    └─ NO → Found only generic divs
            Use locator("div").filter() pattern (last resort)
```

---

## Token Efficiency Guide

| Scenario | Tools Needed | Avg Cost | When to Use |
|----------|-------------|----------|-------------|
| **Unique element** | Snapshot only | ~800 | Element appears once on page |
| **Test ID scoped** | Snapshot + resolve + inspect | ~1,200 | Well-structured apps with test IDs |
| **Semantic scoped** | Snapshot + resolve + inspect | ~1,500 | Apps with good semantic HTML |
| **Structure-based** | Snapshot + resolve + inspect + extract | ~2,500 | Legacy code, poorly structured DOM |

**Optimization tip**: Always check uniqueness first. Skip `extract_anchors` if `inspect_pattern` shows clear unique text.

---

## Essential Quick Reference

### Verdex MCP Tool Commands

```typescript
// === EXPLORATION PHASE ===
await browser_initialize()                    // Start fresh browser
await browser_navigate("https://app.com")     // Go to URL, auto-snapshots
await browser_snapshot()                      // See current page state with refs

await browser_click("e5")                     // Click element by ref
await browser_type("e1", "text@example.com")  // Type into input by ref
await wait_for_browser(2000)                  // Wait 2 seconds for dynamic content

// === SELECTOR BUILDING PHASE ===
resolve_container("e25")                      // Find stable containers (test IDs, landmarks)
inspect_pattern("e25", 2)                     // Analyze sibling structure at level
extract_anchors("e25", 1)                     // Mine deep content (use if inspect insufficient)
```

### Common Selector Patterns

```typescript
// Pattern 1: Unique element (simplest)
page.getByRole("button", { name: "Proceed to Checkout" })

// Pattern 2: Test ID container (most common)
page
  .getByTestId("product-card")
  .filter({ hasText: "iPhone 15 Pro" })
  .getByRole("button", { name: "Add to Cart" })

// Pattern 3: Semantic container
page
  .getByRole("article", { name: /John Doe/ })
  .getByRole("button", { name: "Helpful" })

// Pattern 4: Generic container (last resort)
page
  .locator("div")
  .filter({ hasText: "Order #ORD-2024-1234" })
  .getByRole("button", { name: "Track" })
```

### Test Structure Template

```typescript
test.describe('Feature Name', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/starting-point')
  })

  test('should do specific thing', async ({ page }) => {
    // Arrange: Set up state
    await expect(page.getByRole('heading', { name: 'Title' })).toBeVisible()
    
    // Act: Perform action
    await page.getByRole('button', { name: 'Submit' }).click()
    
    // Assert: Verify outcome
    await expect(page).toHaveURL(/success/)
    await expect(page.getByText('Success message')).toBeVisible()
  })
})
```

### Selector Quality Checklist

Before finalizing any selector:
- [ ] Returns exactly 1 element (verify with `.count()`)
- [ ] Uses test IDs or semantic elements (not classes)
- [ ] Ends with `getByRole()` or semantic locator
- [ ] Chain is ≤ 3 levels deep
- [ ] No `.nth()`, `.first()`, `.last()`, or `locator('..')`
- [ ] No filter in wrong place
- [ ] Survives DOM reordering

---

## Progressive Disclosure Resources

**Start here** and read guides as needed:

1. **[guides/workflow-discovery.md](guides/workflow-discovery.md)** - Interactive exploration and journey mapping
   - When to read: Starting work on new app or unfamiliar feature
   - What you'll learn: Using browser tools with refs, documenting workflows
   - Output: Workflow maps and assertion points

2. **[guides/selector-writing.md](guides/selector-writing.md)** - Building stable selectors
   - When to read: After exploration, have refs, need production selectors
   - What you'll learn: Container → Content → Role pattern, progressive DOM exploration
   - Output: Stable, maintainable Playwright selectors

3. **[guides/playwright-patterns.md](guides/playwright-patterns.md)** - Writing idiomatic tests
   - When to read: Have selectors and workflows, ready to code tests
   - What you'll learn: Test structure, assertions, authentication, patterns
   - Output: Production-ready Playwright test code

---

## Key Principle

**Container → Content → Role beats positional selectors every time.**

Complete workflow:
1. **Explore**: Map user journeys with browser tools and refs
2. **Check uniqueness**: Many elements need only simple selectors
3. **Find containers**: Use `resolve_container` for stable scoping
4. **Understand patterns**: Use `inspect_pattern` for sibling analysis
5. **Extract anchors**: Use `extract_anchors` only when needed
6. **Build selectors**: Apply Container → Content → Role pattern
7. **Write tests**: Follow Playwright best practices

**Don't dump the entire DOM. Ask targeted questions with progressive disclosure.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/verdexhq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
