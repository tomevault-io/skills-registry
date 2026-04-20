---
name: antigravity-qwik-tailwind
description: Error prevention and best practices for QwikJS + Tailwind 4 in the Antigravity project. Use when working with Qwik components, Tailwind CSS, modal dialogs, or when encountering "p0 is not a function" errors or @apply/@layer issues. Use when this capability is needed.
metadata:
  author: micio86dev
---

# Antigravity QwikJS + Tailwind 4 Error Prevention

This skill prevents common recurring errors in the Antigravity project caused by QwikJS optimization and Tailwind 4 CSS layer usage.

## Usage as State Validator (GSD Phase S)

In the **Goal-State-Design (GSD)** protocol, this skill acts as a mandatory Validator during **Phase S (State)**.

**Before writing any code**, you must run these checks to understand the current state:

1.  **Check for Existing Semantic Classes**:
    Don't create new classes if they already exist.
    ```bash
    grep -r "@apply" src/ # Find existing utility compositions
    ```

2.  **Check for Forbidden Patterns**:
    Scan for invalid usages that need refactoring before adding new features.
    ```bash
    # Search for inline Tailwind that violates Hybrid Rule (e.g., px-4 in HTML)
    grep -r "class=\".*px-\|py-\|bg-\|text-\"" src/components
    
    # Search for @apply with variables (Forbidden)
    grep -r "@apply.*var(--" src/
    ```

3.  **Check for Signal Issues**:
    Identify potential "p0 is not a function" risks in related components.
    ```bash
    # Search for functions passed as props without QRL
    grep -r "on[A-Z].*={.*}" src/components
    ```

---

## Critical Error Patterns to Avoid

### 1. Tailwind @apply with @layer Classes

**PROBLEM:** Using classes defined in `@layer` directives inside `@apply` causes compilation errors.

**RULE:** NEVER use `@apply` with classes defined inside `@layer` blocks.

**WRONG:**
```css
/* globals.css */
@layer components {
  .custom-button {
    @apply px-4 py-2 bg-blue-500;
  }
}

/* component.css */
.my-element {
  @apply custom-button; /* ❌ ERROR: custom-button is in a @layer */
}
```

**CORRECT APPROACH 3 - Plain CSS (Preferred for Hybrid Rule):**
```css
.my-element {
  padding-left: 1rem;
  padding-right: 1rem;
  background-color: var(--brand-neon); /* Custom var using Plain CSS */
}
```

### 2. QwikJS Modal/Dialog Pattern - "p0 is not a function"

**PROBLEM:** Qwik's optimizer requires specific patterns for signal reactivity. Common with delete buttons opening confirmation modals.

**SYMPTOM:** Runtime error "p0 is not a function" when clicking buttons that trigger modals.

**ROOT CAUSE:** Improper signal handling or missing `$()` wrapper in event handlers.

## CSS Architecture Rules

1.  **Never mix @layer and @apply**
2.  **Prefer semantic class naming** (e.g. `.card-container`)
3.  **Hybrid CSS Rule**: Use `@apply` for standard utilities, **Plain CSS** for custom vars.

## Pre-flight Checklist

Before committing code with modals or dialogs:

- [ ] All event handlers use `$()` or have `$` suffix
- [ ] Modal props expecting functions use `QRL<>` type
- [ ] No `@apply` used with `@layer` defined classes
- [ ] **Hybrid CSS Rule respected** (No `@apply var(...)`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/micio86dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
