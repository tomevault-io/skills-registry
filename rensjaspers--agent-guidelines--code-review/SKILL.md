---
name: code-review
description: Code review checklist focused on bug prevention. Covers crashes, logic errors, security, memory leaks, and async issues. Use when reviewing pull requests, code changes, or when the user asks for a code review. Use when this capability is needed.
metadata:
  author: rensjaspers
---

# Code Review

**Goal:** Prevent bugs. Ignore style, formatting, naming — linter handles that.

---

## Report These

- Null/undefined crashes
- Logic errors (off-by-one, wrong conditions, missing edge cases)
- Race conditions, missing Promise handling
- Memory leaks (unsubscribed observables, event listeners)
- Infinite loops
- SQL/XSS injection, unsanitized input
- Authentication bypass, wrong permission checks
- Exposed secrets
- Breaking changes without migrations
- N+1 queries, blocking operations in main thread

---

## Ignore These

- Formatting, spacing
- Variable names (unless truly misleading)
- Refactoring ideas without concrete bug
- Style preferences
- Missing comments

---

## Review Checklist

1. Will it crash? (null/undefined, types)
2. Is logic correct? (edge cases)
3. Is data safe? (validation, permissions)
4. Resource leaks? (subscriptions, listeners)
5. Async correct? (Promise handling, race conditions)

If all clear → APPROVE.

---

## Tone

Be direct. State impact clearly: "Crash", "Bug", "Data loss", "Breaking".
When in doubt, don't comment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rensjaspers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
