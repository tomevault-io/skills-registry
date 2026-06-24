---
name: zustand-store-manager
description: Generates typed Zustand stores (usePaperStore, useAuthStore) with devtools and persist middleware for localStorage.
metadata:
  author: cargdev
---

# Zustand Store Manager

When to use this skill

- Use when centralizing client state (papers, endorsements, likes, user profile) without a backend.
- Triggered by requests to scaffold or update stores, add persistence, or wire devtools.

Instructions

1. First Step: Create typed store files under `src/stores/` (e.g., `usePaperStore.ts`) using `create` from `zustand` and `persist`/`devtools` middleware.

2. Second Step: Expose actions and selectors that the UI will consume (`addEndorsement`, `toggleLike`, `fetchPapers` placeholder).

3. Third Step: Document how to access state in components and how to reset/seed state for tests.

Examples

- `usePaperStore` exposes `papers`, `loading`, `addEndorsement(paperId, endorser)` and `reset()`.

Notes

- Include clear type definitions for the store payloads to keep UI and store in sync.
- Recommend `zustand/middleware` persist name (`arxiv-social-storage`) and a migration strategy when types change.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
