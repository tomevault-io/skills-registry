---
name: nuxt-patterns
description: Nuxt development patterns - validation, error handling, code organization, state management Use when this capability is needed.
metadata:
  author: maxbeworks
---

## Validation

- Always implement server-side validation on top of client-side validation
- Client-side is for UX, server-side is for security - never trust the client
- Reuse validation schemas between client and server when possible (Zod, Valibot, etc.)

## Code Reusability

- Check for existing patterns before implementing something new
- Look for standardized logic across the app to reduce overhead and maintenance
- If similar functionality exists, follow the established pattern
- Don't reinvent the wheel - ask yourself "has this been solved already?"

## State Management

- **Store (Pinia)**: For data that needs to be shared across multiple parts of the app
- **Composables**: For complex reusable logic that may or may not need state
- Keep stores focused - don't dump everything into one massive store
- Composables for logic, stores for state

## Component Organization

- Components should never be too large - split when they become hard to maintain
- Use `component/index.vue` with sub-components for complex features
- Keep component responsibilities clear and focused
- Colocate related components

## Error Handling

- Properly separate server-side errors from client-side errors
- Every error should be handled - no silent failures
- Both users and developers need to know when something breaks

### Developer Errors
- Console logs/warnings for debugging (when necessary)
- Proper error boundaries to catch and log issues
- Include context: what failed, why, and where

### User Errors
- Toast notifications for global/async errors
- Form feedback for validation errors
- UI feedback (loading states, error states) for user actions
- Translate technical errors into user-friendly messages - no stack traces or "Error 500" bullshit

### Error Strategy
- Ask yourself: "Where did this error happen? Who needs to know?"
- API errors → user feedback + console log
- Validation errors → form-specific feedback
- Unexpected errors → user toast + detailed console error
- Network errors → user notification with retry option

## Async and Loading States

- Always show loading states for async operations
- Disable buttons during submission to prevent double-clicks
- Give users feedback that something is happening

## Server vs Client

- Server routes for data fetching, mutations, sensitive operations
- Client-side for UI interactions, optimistic updates
- Use proper hydration patterns - don't fight Nuxt's SSR

## Anti-Patterns

- Giant components that do everything
- Silent error catching without user feedback
- Client-side only validation
- Duplicate logic that should be shared
- Overly complex state management when a composable would work
- Not showing loading states during async operations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxbeworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
