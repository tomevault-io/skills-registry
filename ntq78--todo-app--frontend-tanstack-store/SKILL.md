---
name: frontend-tanstack-store
description: Use when creating or modifying TanStack Store for global state management
metadata:
  author: ntq78
---

# Frontend: TanStack Store

Global state management using `@tanstack/react-store` with granular selectors.

## When to Use

- **TanStack Store** → Global app state (theme, user prefs, cross-page state)
- **Provider Context** → Page/component-level state (see `frontend-provider-context`)
- **TanStack Query** → Server state
- **useState** → Single component state

## Core Pattern

```typescript
import { Store, useStore, shallow } from "@tanstack/react-store";

// 1. State class with defaults
class State_App {
    theme: "light" | "dark" = "light";
    sidebarCollapsed: boolean = false;
    notifications: string[] = [];
}

// 2. Store instance
export const Store_App = new Store(new State_App());

// 3. Selectors - one per key (granular subscriptions)
export const useStore_App_Theme = () => useStore(Store_App, (s) => s.theme);
export const useStore_App_SidebarCollapsed = () => useStore(Store_App, (s) => s.sidebarCollapsed);
export const useStore_App_Notifications = () =>
    useStore(Store_App, (s) => s.notifications, { equal: shallow });

// 4. Actions
export const Store_App_Actions = {
    overwrite: (partial: Partial<State_App>) => Store_App.setState((s) => ({ ...s, ...partial })),

    notifications: {
        add: (msg: string) =>
            Store_App.setState((s) => ({ ...s, notifications: [...s.notifications, msg] })),
        remove: (msg: string) =>
            Store_App.setState((s) => ({
                ...s,
                notifications: s.notifications.filter((n) => n !== msg),
            })),
        clear: () => Store_App.setState((s) => ({ ...s, notifications: [] })),
    },
};
```

## Naming Conventions

| Entity        | Pattern                 | Example                     |
| ------------- | ----------------------- | --------------------------- |
| State class   | `State_[Name]`          | `State_App`, `State_Editor` |
| Store         | `Store_[Name]`          | `Store_App`                 |
| Selector hook | `useStore_[Name]_[Key]` | `useStore_App_Theme`        |
| Actions       | `Store_[Name]_Actions`  | `Store_App_Actions`         |
| File          | `Store_[Name].ts`       | `src/stores/Store_App.ts`   |

## Usage

```typescript
// Read (granular - only re-renders when this value changes)
const theme = useStore_App_Theme();
const notifications = useStore_App_Notifications();

// Write - simple values via overwrite
Store_App_Actions.overwrite({ theme: "dark" });
Store_App_Actions.overwrite({ theme: "dark", sidebarCollapsed: true });

// Write - complex operations via nested handlers
Store_App_Actions.notifications.add("New message");
Store_App_Actions.notifications.clear();
```

## Selector Rules

| State Type     | Selector Pattern                                    |
| -------------- | --------------------------------------------------- |
| Primitives     | `useStore(store, (s) => s.key)`                     |
| Arrays/Objects | `useStore(store, (s) => s.key, { equal: shallow })` |

**MUST use `shallow`** for arrays/objects to prevent re-renders when reference changes but contents are equal.

## Action Rules

| Scenario              | Use                                                        |
| --------------------- | ---------------------------------------------------------- |
| Simple value updates  | `overwrite({ key: value })`                                |
| Multiple keys at once | `overwrite({ key1: v1, key2: v2 })`                        |
| Array mutations       | Nested handler: `key.add()`, `key.remove()`, `key.clear()` |
| Boolean toggle        | Nested handler: `key.toggle()`                             |
| Computed updates      | Nested handler with logic                                  |

**Nested handler naming:**

| Type     | Handlers                        |
| -------- | ------------------------------- |
| Arrays   | `add`, `remove`, `clear`, `set` |
| Booleans | `toggle`, `set`                 |
| Objects  | `update`, `set`, `clear`        |

## Anti-Patterns

| Wrong                                          | Correct                                             |
| ---------------------------------------------- | --------------------------------------------------- |
| `useStore(store, (s) => s)` (subscribe to all) | `useStore(store, (s) => s.specificKey)`             |
| Array selector without `shallow`               | `useStore(store, (s) => s.arr, { equal: shallow })` |
| Direct `Store.setState()` in components        | Use `Store_Actions.overwrite()`                     |
| Nested state objects                           | Keep state flat                                     |
| Individual setter per key                      | Use `overwrite()` for simple keys                   |

## Related Skills

- **frontend-provider-context** - Page/component-level state (not global)
- **frontend-naming-conventions** - General naming patterns

<!-- Last compacted: 2026-01-20 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
