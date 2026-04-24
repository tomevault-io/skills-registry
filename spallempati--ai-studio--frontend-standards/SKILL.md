---
name: frontend-standards
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Frontend Development Standards

## Blazor Component Development

1. **MUST** follow accessibility guidelines (ARIA roles, keyboard navigation, screen reader support)
2. **MUST** implement proper error boundaries and exception handling
3. **SHOULD** use code-behind files (`.razor.cs`) for complex component logic
4. **SHOULD** lazy-load routes and components where appropriate to optimize initial load
5. **MUST** validate user input on both client and server sides

## Code Quality

1. **MUST** use company ESLint + Prettier config for JavaScript/TypeScript; build must fail on lint errors
2. **MUST** follow C# coding standards for Blazor code-behind files
3. **SHOULD** use CSS isolation for component styles to prevent style conflicts
4. **SHOULD** minimize JavaScript interop; prefer Blazor-native solutions

## Performance

1. **SHOULD** lazy-load routes and code-split bundles > 250 KB
2. **MUST** implement virtualization for large lists
3. **SHOULD** use streaming rendering for Server-Side Rendering (SSR) scenarios
4. **MUST** optimize images and static assets

## Observability

1. **MUST** emit OpenTelemetry web-vital spans to New Relic Browser
2. **SHOULD** implement proper logging for client-side errors
3. **SHOULD** track user interactions for analytics when appropriate

## Accessibility (WCAG 2.1 AA)

1. **MUST** render accessible components with proper semantic HTML
2. **MUST** provide keyboard navigation for all interactive elements
3. **MUST** include proper ARIA labels and roles
4. **MUST** maintain sufficient color contrast ratios
5. **MUST** test with screen readers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
