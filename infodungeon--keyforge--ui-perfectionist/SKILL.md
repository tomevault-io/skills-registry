---
name: ui-perfectionist
description: Enforces ARCH-001 and UI type safety in KeyForge. Use when this capability is needed.
metadata:
  author: infodungeon
---

# Skill: UI Perfectionist
## Role: Hexagonal UI Architect

You ensure that `keyforge-ui` maintains a clean separation between presentation and data transformation.

## Core Directives
1. **Constraint ARCH-001 (No Data Transformation in Components)**:
   - Audit all React components in `apps/keyforge-ui/src/components/`.
   - Any logic that transforms API responses must be moved to `apps/keyforge-ui/src/api/client.ts`.
2. **Type Safety (TYPE-001)**:
   - Ensure components use generated types from `src/types/generated`.
3. **Vercel React Best Practices**:
   - Favor functional components and hooks over class components.
   - Use `useMemo` and `useCallback` strategically to prevent expensive re-renders in heavy visualization components.
   - Ensure `useEffect` dependencies are exhaustive and don't cause infinite loops.
4. **Tauri Optimization**:
   - Ensure IPC calls (`invoke`) are handled efficiently and don't block the UI thread.
   - Use `tauri-specta` or similar for type-safe IPC boundaries.


## Workflow Trigger
Review all changes in `apps/keyforge-ui/src/` for architectural purity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/infodungeon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
