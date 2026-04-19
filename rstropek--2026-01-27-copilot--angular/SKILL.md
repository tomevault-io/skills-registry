---
name: angular-coding-guidelines
description: Angular and TypeScript coding guidelines for building maintainable, performant, and accessible web applications. Use this skill when generating TypeScript or Angular code for web applications. Use when this capability is needed.
metadata:
  author: rstropek
---

You are an expert in TypeScript, Angular, and scalable web application development. You write maintainable, performant, and accessible code following Angular and TypeScript best practices.

## TypeScript Best Practices

- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

## Angular Best Practices

- Always use standalone components over NgModules
- Must NOT set `standalone: true` inside Angular decorators. It's the default.
- Use signals for state management
- Implement lazy loading for feature routes
- Do NOT use the `@HostBinding` and `@HostListener` decorators. Put host bindings inside the `host` object of the `@Component` or `@Directive` decorator instead
- Use `NgOptimizedImage` for all static images.
  - `NgOptimizedImage` does not work for inline base64 images.

## Components

- Keep components small and focused on a single responsibility
- Use `input()` and `output()` functions instead of decorators
- Use `computed()` for derived state
- Set `changeDetection: ChangeDetectionStrategy.OnPush` in `@Component` decorator
- Prefer inline templates for small components
- Prefer Reactive forms instead of Template-driven ones
- Do NOT use `ngClass`, use `class` bindings instead
- Do NOT use `ngStyle`, use `style` bindings instead

## State Management

- Use signals for local component state
- Use `computed()` for derived state
- Keep state transformations pure and predictable
- Do NOT use `mutate` on signals, use `update` or `set` instead

## Templates

- Keep templates simple and avoid complex logic
- Use native control flow (`@if`, `@for`, `@switch`) instead of `*ngIf`, `*ngFor`, `*ngSwitch`
- Use the async pipe to handle observables

## Services

- Design services around a single responsibility
- Use the `providedIn: 'root'` option for singleton services
- Use the `inject()` function instead of constructor injection

Here’s a suggested **Styles** section for your `AGENTS.md` file, aligned with your existing best practices and requirements:

## Styles

- Avoid third-party CSS frameworks. Write custom styles for full control and consistency.
- Assume support for modern browsers (Chrome-based). Use modern CSS features without fallbacks for older browsers.
- Use CSS Nesting to improve maintainability and readability.
- Prefer CSS Grid and Flexbox for layout, avoid old techniques like floats or table-based layouts.
- Layout Constraints:
  - Content must be 1280px wide and horizontally centered on the page.
  - Assume desktop-only usage (full HD resolution). No responsive design is required.
- Use `global.css` for styles that must be consistent across the entire application (e.g., typography, reset rules, consistent styling of reoccurring elements). This includes, but is not limited to:
  - CSS variables for e.g. colors, spacing, and other design tokens
  - Buttons
  - Form elements
  - Styles for grids (e.g. headers, zebra striping, etc.)
- Use component-scoped styles for styles specific to a single component.
- Avoid `!important` in styles. Use specific selectors or refactor styles to avoid specificity issues.
- Use meaningful, BEM-like class names for clarity and to avoid style conflicts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rstropek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
