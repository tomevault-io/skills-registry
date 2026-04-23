---
name: frontend-page-layout
description: Use when creating page layouts, handling height calculations, or structuring page components with flex/grid
metadata:
  author: ntq78
---

# Frontend: Page Layout Patterns

This skill defines layout structure patterns for page components to ensure consistent height calculations and prevent overflow issues.

## The Layout Hierarchy

### Global Layout (Protected Routes)

The application uses a two-tier layout system:

1. **Root Layout** (`__root.tsx`) - Minimal, just renders `<Outlet />`
2. **Protected Layout** (`_protected.tsx`) - Handles authentication and provides sized container

**Key Pattern:**

```tsx
// _protected.tsx:56
<div style={{ height: "calc(100vh - 48px)" }}>
    <Outlet />
</div>
```

This creates a container that fills the viewport **minus the 48px global Nav**.

### Page Component (Your Responsibility)

Page components receive a pre-sized container from the protected layout.

**CRITICAL RULE: Pages MUST use `height: 100%`, NEVER recalculate viewport.**

```tsx
// ✅ CORRECT - Page component
export const Page_Example = () => {
    return (
        <div style={{ height: "100%", display: "flex", flexDirection: "column" }}>
            {/* Page content */}
        </div>
    );
};

// ❌ WRONG - Recalculating viewport
export const Page_Example = () => {
    return (
        <div style={{ height: "calc(100vh - 44px)", display: "flex", flexDirection: "column" }}>
            {/* This WILL cause overflow! */}
        </div>
    );
};
```

## Height Calculation Rules

### DO: Use Relative Heights

```tsx
// ✅ Page container - inherit from parent
<div style={{ height: "100%" }}>

// ✅ Main content area - grow to fill remaining space
<div style={{ flex: 1, overflow: "hidden" }}>

// ✅ Fixed-height secondary nav
<div style={{ height: 44 }}>
```

### DON'T: Recalculate Viewport

```tsx
// ❌ NEVER do this in page components
<div style={{ height: "calc(100vh - 44px)" }}>
<div style={{ height: "calc(100vh - 48px)" }}>
<div style={{ height: "100vh" }}>

// ❌ NEVER hardcode pixel heights matching viewport calculations
<div style={{ height: "1032px" }}>  // Viewport-derived value
```

## The Global Nav Pattern

**Single Source of Truth:**

- **Nav height:** 48px (defined in `Nav.tsx:80`)
- **Protected layout container:** `calc(100vh - 48px)` (defined in `_protected.tsx:56`)
- **Page components:** `height: 100%` (inherit from container)

**Why This Matters:**

If the Nav height changes to 50px:

- ✅ Update `_protected.tsx` only → All pages automatically adjust
- ❌ If pages used `calc(100vh - 48px)` → Must update every page manually

## Page Internal Layout Pattern

Common pattern for pages with secondary navigation:

```tsx
export const Page_Example = () => {
    return (
        <div style={{ height: "100%", display: "flex", flexDirection: "column" }}>
            {/* Fixed-height secondary nav */}
            <SecondaryNav height={44} />

            {/* Main content - fills remaining space */}
            <div style={{ flex: 1, overflow: "hidden" }}>
                <PanelGroup direction="horizontal">
                    {/* Panels handle their own sizing */}
                </PanelGroup>
            </div>
        </div>
    );
};
```

**Key Elements:**

1. **Page container:** `height: 100%` + `flex column`
2. **Secondary nav:** Fixed height (e.g., 44px)
3. **Main content:** `flex: 1` + `overflow: hidden`
4. **Panel groups:** Self-sizing within their container

## Working with Borders

Borders add to element dimensions. Use `box-sizing: border-box` (usually already global).

```tsx
// ✅ Border included in height calculation
<div style={{
    height: 44,
    borderBottom: `1px solid ${token.colorBorder}`,
    boxSizing: "border-box"  // Usually global, explicit here for clarity
}}>

// ❌ Border adds 1px to height
<div style={{
    height: 44,
    borderBottom: `1px solid ${token.colorBorder}`,
    boxSizing: "content-box"  // Avoid this
}}>
```

## Examples

### Example 1: Page_Set (Correct Pattern)

**File:** `spark/frontend/my-vite-app/src/pages/Page_Set/Page_Set.tsx`

```tsx
export const Page_Set = () => {
    return (
        <Provider_Page_Set>
            {/* ✅ height: 100% - inherits from _protected container */}
            <div style={{ height: "100%", display: "flex", flexDirection: "column" }}>
                {/* Fixed-height secondary nav (44px) */}
                <PageSet_Nav setId={setId} />

                {/* Main content fills remaining space */}
                <div style={{ flex: 1, overflow: "hidden" }}>
                    <PanelGroup direction="horizontal">{/* Resizable panels */}</PanelGroup>
                </div>
            </div>
        </Provider_Page_Set>
    );
};
```

### Example 2: Anti-Pattern (What NOT to Do)

```tsx
export const Page_Bad = () => {
    return (
        // ❌ WRONG - Recalculating viewport height
        <div style={{ height: "calc(100vh - 44px)", display: "flex", flexDirection: "column" }}>
            <SecondaryNav />
            <MainContent />
        </div>
    );
};
```

**Why This Breaks:**

- `_protected.tsx` provides `calc(100vh - 48px)` container
- Page tries to be `calc(100vh - 44px)` = **4px overflow**
- Even if numbers matched today, Nav height changes break all pages

## Anti-Patterns to Avoid

### 1. Viewport Calculations in Pages

```tsx
// ❌ DON'T recalculate viewport
<div style={{ height: "calc(100vh - 44px)" }}>

// ✅ DO inherit from parent
<div style={{ height: "100%" }}>
```

### 2. Hardcoded Pixel Heights (Viewport-Derived)

```tsx
// ❌ DON'T hardcode viewport-based heights
<div style={{ height: "1032px" }}>  // Someone calculated viewport - 48px

// ✅ DO use relative heights
<div style={{ height: "100%" }}>
```

### 3. Missing overflow: hidden

```tsx
// ❌ DON'T forget overflow handling
<div style={{ flex: 1 }}>
    <PanelGroup>  {/* May overflow container */}

// ✅ DO constrain overflow
<div style={{ flex: 1, overflow: "hidden" }}>
    <PanelGroup>  {/* Contained within bounds */}
```

### 4. Mixing Absolute and Relative Heights

```tsx
// ❌ DON'T mix calculation strategies
<div style={{ height: "calc(100vh - 48px)" }}>
    <div style={{ height: "100%" }}>  {/* Which 100%? Confusing! */}

// ✅ DO use consistent relative heights
<div style={{ height: "100%" }}>
    <div style={{ flex: 1 }}>  {/* Clear hierarchy */}
```

## Debugging Overflow Issues

If you see unexpected vertical scroll:

1. **Check page container height:**
    - ✅ Should be `height: 100%`
    - ❌ If `calc(100vh - Xpx)` → Replace with `100%`

2. **Check parent layout:**
    - ✅ `_protected.tsx` should provide sized container
    - ❌ If missing → Page can't inherit correct height

3. **Check borders:**
    - ✅ `box-sizing: border-box` should be global
    - ❌ If borders add to height → Use border-box

4. **Check nested content:**
    - ✅ Use `flex: 1` + `overflow: hidden` for main content
    - ❌ If content unconstrained → May expand beyond container

## Related Patterns

### Flexbox Layout

```tsx
// Column layout with fixed header + flexible content
<div style={{ height: "100%", display: "flex", flexDirection: "column" }}>
    <Header height={44} />
    <Content flex={1} overflow="hidden" />
</div>

// Row layout with sidebar + main area
<div style={{ height: "100%", display: "flex", flexDirection: "row" }}>
    <Sidebar width={250} />
    <Main flex={1} overflow="auto" />
</div>
```

### Grid Layout (Alternative)

```tsx
<div
    style={{
        height: "100%",
        display: "grid",
        gridTemplateRows: "44px 1fr", // Fixed header + flexible content
    }}
>
    <Header />
    <Content />
</div>
```

## Checklist for New Pages

When creating a new page component:

- [ ] Page container uses `height: 100%` (not viewport calculations)
- [ ] Page container uses `display: flex` with `flexDirection: column`
- [ ] Secondary navs (if any) use fixed heights
- [ ] Main content area uses `flex: 1` + `overflow: hidden`
- [ ] No `calc(100vh - Xpx)` anywhere in page component
- [ ] Tested for vertical overflow at different viewport sizes

## Related Skills

- **frontend-routing** - Route structure and layout hierarchy
- **frontend-antd-components** - Using theme tokens for spacing/heights

---

<!-- Last created: 2025-12-02 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
