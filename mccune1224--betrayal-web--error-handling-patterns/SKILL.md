---
name: error-handling-patterns
description: Patterns for consistent error reporting and logging in Go and SvelteKit, including user-facing notifications and backend structured logs. Use when this capability is needed.
metadata:
  author: mccune1224
---

## What I do
- Illustrate structured error propagation in Go (context, HTTP error returns, log best practices)
- Provide SvelteKit/UI-side notification patterns for user-visible errors/toasts
- Checklist for full-stack traceability in multiplayer/websocket flows

## When to use me
Use when designing new features, reviewing code (esp. multiplayer/session flows), or debugging production issues. Prevents silent/hidden failure modes across stack.

## Example Usage
Useful in `backend/internal/handlers/`, `frontend/src/routes/`, and in SvelteKit UI logic that needs robust user feedback.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mccune1224) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
