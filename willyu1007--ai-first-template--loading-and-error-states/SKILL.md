---
name: loading-and-error-states
description: Loading and error UX patterns that avoid layout shift and improve clarity. Keywords: loading, error, suspense, skeleton, error boundary, CLS. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Loading and Error States

This skill provides patterns for consistent loading and error UX.

---

## 1. Avoid layout shift (CLS)

- Reserve space for content during loading (skeletons, placeholders).
- Avoid patterns that drastically change layout between loading and loaded states.

---

## 2. Choose a consistent strategy

Two common approaches:
- **Suspense-based**: use Suspense boundaries + error boundaries.
- **Explicit state-based**: `isLoading` / `isError` flags with consistent layout.

Pick one approach per application (or per major area) and apply consistently.

---

## 3. Error boundaries (recommended)

For unexpected errors, prefer an error boundary that:
- renders a safe fallback UI
- logs/captures the error to monitoring
- offers a retry action if appropriate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
