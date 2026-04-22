---
name: apex-sovereign-state
description: Manages cart and user state using Zustand with hydration, persistence, and Redis synchronization.
metadata:
  author: adelfree2023-dev
---

# 🐻 State Management Logic (v2.0)

**Focus**: State Management Logic (17).

---

## 🏗️ State Logic Protocols
- **Clean Architecture for Zustand**: All state stores must be partitioned by domain (Cart, Auth, UI). Stores must be strictly typed and favor immutable updates.
- **Hydration & Persistence**: Implementation of Two-Phase Hydration to prevent SSR-Client mismatches. Mandatory persistence to LocalStorage with version-controlled migrations.
- **Redis Synchronization**: Cross-device synchronization logic triggered on state change with debounced background sync to avoid platform saturation.

## 🚀 Root Solutions (State)
- **Derived Selectors**: Use memoized selectors to prevent unnecessary re-renders in complex React trees.
- **Optimistic UI**: Mandatory implementation of optimistic updates for high-frequency actions (e.g., adding items to cart), with rollback logic on API failure.
- **State-to-API Sync**: Clean decoupling between UI state updates and API persistence calls using middleware or store actions.

## ⚖️ Integrity Rule
Never put raw server data in global client state. Use React Query for server-side cache and Zustand only for purely client-managed state.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adelfree2023-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
