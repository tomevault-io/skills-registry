---
name: modern-javascript-core
description: Core JavaScript features (ES2025+), modules, and syntax patterns. Use when this capability is needed.
metadata:
  author: sraloff
---

# Modern JavaScript Core

## When to use this skill
- Writing vanilla JavaScript logic.
- Understanding modern syntax in PR reviews.
- Avoiding legacy patterns (var, callbacks).

## 1. Essential Syntax
- **Variables**: `const` by default, `let` if reassignment is needed. Never `var`.
- **Functions**: Arrow functions `() => {}` for callbacks and lexical `this`.
- **Destructuring**: Use generously (`const { id, name } = user`).
- **Template Literals**: Use backticks for string interpolation.

## 2. Async Patterns
- **Async/Await**: Prefer over `.then()` chains.
- **Top-level Await**: Supported in modern modules.
- **Promise.allSettled**: Better for unrelated concurrent tasks than `Promise.all` (which fails fast).

## 3. Modules
- **ES Modules**: `import`/`export` syntax is standard.
- **Named Exports**: Prefer named exports (`export const foo`) over `export default` for better refactoring support.

## 4. Modern Array Methods
- **Usage**: `map`, `filter`, `reduce`, `find`, `some`, `every`.
- **Newer**: `floated` (toSorted), `toSpliced` (immutable alternatives).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
