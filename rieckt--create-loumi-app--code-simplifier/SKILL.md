---
name: code-simplifier
description: Simplifies and refines code for clarity, consistency, and maintainability while preserving all functionality Use when this capability is needed.
metadata:
  author: rieckt
---

# Code Simplifier

> Simplifies and refines code for clarity, consistency, and maintainability while preserving all functionality. Focuses on recently modified code unless instructed otherwise.

---

## Core Rules

| Rule | Description |
|------|-------------|
| **Preserve Functionality** | Never change what the code does — only how it does it |
| **Clarity Over Brevity** | Explicit, readable code beats overly compact solutions |
| **Minimal Scope** | Only refine recently modified code unless told otherwise |
| **No Over-Simplification** | Don't remove helpful abstractions or combine too many concerns |

---

## Project Standards

| Standard | Convention |
|----------|------------|
| **Modules** | ES modules with proper import sorting |
| **Functions** | Prefer `function` keyword over arrow functions for top-level |
| **Return Types** | Explicit return type annotations for top-level functions |
| **React Components** | Explicit Props types, proper component patterns |
| **Error Handling** | Avoid try/catch when possible, prefer Result patterns |
| **Naming** | Clear, consistent naming conventions |

---

## Simplification Targets

| Target | Action |
|--------|--------|
| **Unnecessary complexity** | Reduce nesting, flatten logic |
| **Redundant code** | Eliminate dead code and duplicate abstractions |
| **Unclear naming** | Improve variable and function names |
| **Scattered logic** | Consolidate related code |
| **Obvious comments** | Remove comments that describe what code already says |
| **Nested ternaries** | Replace with `switch` or `if/else` chains |
| **Dense one-liners** | Expand for readability when clarity suffers |

---

## Anti-Patterns (DON'T)

| Don't | Why |
|-------|-----|
| Reduce clarity for fewer lines | Readability > line count |
| Create clever one-liners | Others must understand it |
| Remove helpful abstractions | Good abstractions aid maintenance |
| Combine too many concerns | Single responsibility matters |
| Auto-fix without context | Understand intent first |
| Touch untouched code | Stay scoped to recent changes |

---

## Refinement Process

1. **Identify** recently modified code sections
2. **Analyze** for clarity, consistency, and simplification opportunities
3. **Apply** project-specific best practices and coding standards
4. **Verify** all functionality remains unchanged
5. **Confirm** refined code is simpler and more maintainable

---

## Self-Check Before Completing

| Check | Question |
|-------|----------|
| **Functionality intact?** | Does the code still do exactly the same thing? |
| **More readable?** | Is the refined code easier to understand? |
| **Standards followed?** | Does it match project conventions? |
| **Scope respected?** | Did I only touch recently modified code? |
| **No regressions?** | Are imports, types, and dependencies still correct? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rieckt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
