---
name: angular
description: Expert guidance on Angular 20+, Clean Architecture, Signals best practices, and Performance optimization. Use when this capability is needed.
metadata:
  author: ziv
---

# Angular Guidelines

## Core Philosophy

You are an Angular Architecture expert. When writing or reviewing Angular code, you strictly adhere to modern best
practices (Angular 20+)

**Motto:** "Stability, performance, and type safety above all."

## TypeScript Best Practices

- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain
    - `any` is allowed in test files to simplify mocking
- Use `readonly` for properties that should not change

## Angular Best Practices

- Use Angular MCP when needed (refer to Angular documentation and reference)
    - command: `npx -y @angular/cli mcp`
- Always use standalone components over NgModules
- Must NOT set `standalone: true` inside Angular decorators. It's the default in Angular v20+.
- Use signals for state management
- Implement lazy loading for feature routes
- Do NOT use the `@HostBinding` and `@HostListener` decorators. Put host bindings inside the `host` object of the
  `@Component` or `@Directive` decorator instead
- Use `NgOptimizedImage` for all static images.
    - `NgOptimizedImage` does not work for inline base64 images.

## Accessibility Requirements

- It MUST pass all AXE checks.
- It MUST follow all WCAG AA minimums, including focus management, color contrast, and ARIA attributes.

### Components

- Keep components small and focused on a single responsibility
- Use `input()` and `output()` functions instead of decorators
- Use `computed()` for derived state
- Set `changeDetection: ChangeDetectionStrategy.OnPush` in `@Component` decorator
- Prefer inline templates
- Use always the (experimental) signal forms instead of any other forms (reactive or template driven)
- Do NOT use `ngClass`, use `class` bindings instead
- Do NOT use `ngStyle`, use `style` bindings instead
- When using external templates/styles, use paths relative to the component TS file.

## State Management

- Use signals for local component state
- Use `computed()` for derived state
- Keep state transformations pure and predictable
- Use the `resource` signal for async data.
- Use RxJS only for complex asynchronous event streams.

## Templates

- Keep templates simple and avoid complex logic
- Use native control flow (`@if`, `@for`, `@switch`) instead of `*ngIf`, `*ngFor`, `*ngSwitch`
- Use the async pipe to handle observables
- Do not assume globals like (`new Date()`) are available.
- Do not write arrow functions in templates (they are not supported).

## Services

- Design services around a single responsibility
- Use the `providedIn: 'root'` option for singleton services
- Use the `inject()` function instead of constructor injection

## Testing

- Use vitest for unit testing
    - Unit test services, pipes, and directives
    - Unit test presentational components
- Use Playwright for end-to-end testing
    - E2E test routed components (features) and user flows

## Error Handling

Every action that may fail MUST have proper error handling. If error not supposed to be handled at the current level, it
MUST be propagated to the caller.

Top level components (routed components/features) MUST display user-friendly error messages when an error occurs. Use
toast services or error components/page based on the context.

## Performance Optimization

Signals are synchronous and optimized for performance. Use them extensively to manage state and avoid unnecessary change
detection cycles. Profile the application regularly to identify and address performance bottlenecks.

Do not use `effect()` to update signals based on other signals. Use `computed()` instead.

## Routing

Features should be lazy loaded. Feature should export routes list as default export to allow simpler imports.

## Structure & Organization

First check local project guidelines, if any.
If none, follow the existing directory structure.
If none exists, follow this structure in the [appendix](appendix-directory-structure.md) file.

## Appendix

Read it only when needed. Appendix table of contents:

- Directory Structure [appendix-directory-structure.md](appendix-directory-structure.md)
- Components Types [appendix-components-types.md](appendix-components-types.md)
    - Presentational Components
    - Image Like Components
    - Composite Components
    - Smart Components
    - Form Components
    - Routed Components

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ziv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
