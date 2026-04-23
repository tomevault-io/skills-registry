---
name: frontend-provider-context
description: Use when creating or modifying Provider Context components for local state management
metadata:
  author: ntq78
---

# Frontend: Provider Context Pattern

Standard pattern for React Context providers using `class State + useReducer + setState(partial)` for page/component-level state management.

## When to Use

- ✅ Page-level state shared across subcomponents
- ✅ Component-level state in complex component trees
- ✅ Parent-child/sibling communication without prop drilling
- ❌ Global app state → use TanStack Store
- ❌ Server state → use TanStack Query
- ❌ Single component state → use useState/useReducer directly

## Base Pattern

```typescript
import React, { createContext, useReducer } from "react";

class State {
    key1: string = "";
    key2: boolean = false;
    key3: number = 0;
}

const ContextDefault: { state: State; setState: React.Dispatch<Partial<State>> } = {
    state: new State(),
    setState: () => {},
};

const Reducer = (state: State, partial: Partial<State>): State => ({ ...state, ...partial });
const Context = createContext(ContextDefault);

export const Provider_[Name] = ({ children }: { children: React.ReactNode }) => {
    const [state, setState] = useReducer(Reducer, new State());
    return <Context.Provider value={{ state, setState }}>{children}</Context.Provider>;
};

export const useProvider_[Name] = () => React.useContext(Context);
```

### Pattern Components

1. **State class** - Property initializers with defaults
2. **ContextDefault** - `Partial<State>` for setState, no-op prevents crashes outside provider
3. **Reducer** - One-line spread merge: `({ ...state, ...partial })`
4. **Provider** - `useReducer` + `new State()` initialization
5. **Hook** - `useContext(Context)` for consuming

## Naming Conventions

| Type     | Pattern               | Example                                                |
| -------- | --------------------- | ------------------------------------------------------ |
| Provider | `Provider_[Name]`     | `Provider_Page_Set`, `Provider_Spark_FileBrowserModal` |
| Hook     | `useProvider_[Name]`  | `useProvider_Page_Set`                                 |
| Variable | `p[Name]` (camelCase) | `pPageSet`, `pModal`                                   |

## Usage

```typescript
// 1. Wrap component tree
<Provider_Page_Set>
    <PageContent />
</Provider_Page_Set>

// 2. Use in child components
const pPageSet = useProvider_Page_Set();
pPageSet.setState({ selectedSceneFileId: "123" });
pPageSet.state.selectedSceneFileId;

// Multiple properties
pPageSet.setState({ selectedSceneFileId: "123", selectedSceneId: "456" });

// Toggle
pPageSet.setState({ isLoading: !pPageSet.state.isLoading });
```

## initialState Extension

For initializing from props (e.g., route params):

```typescript
export const Provider_Page_Set = ({
    children,
    initialState,
}: {
    children: React.ReactNode;
    initialState?: Partial<State>;
}) => {
    const [state, setState] = useReducer(
        Reducer,
        initialState ? { ...new State(), ...initialState } : new State()
    );
    return <Context.Provider value={{ state, setState }}>{children}</Context.Provider>;
};

// Usage
<Provider_Page_Set initialState={{ selectedSceneId: sceneId }}>
```

## Anti-Patterns

| Wrong                           | Correct                                       |
| ------------------------------- | --------------------------------------------- |
| useState + individual setters   | useReducer + setState(partial)                |
| Deep nested state objects       | Flat state: `modalIsOpen`, `panelIsCollapsed` |
| Default exports                 | Named exports only                            |
| Custom setter functions per key | Single `setState({ key: value })`             |

**Why useReducer + partial is better:**

- Consistent API for all updates
- No useCallback overhead
- Easy to maintain (just add to State class)
- Type-safe partial updates

## Scope Guidelines

### Page-Level Provider

```typescript
// Place at page root
export const Page_Set = () => (
    <Provider_Page_Set initialState={{ selectedSceneId }}>
        <PageSetContent />
    </Provider_Page_Set>
);
```

Use for: State shared across subcomponents, persists across sub-component mounts.

### Component-Level Provider

```typescript
// Place at component root
export const PageSet_Canvas = () => (
    <Provider_PageSet_Canvas>
        <Canvas><CanvasContent /></Canvas>
    </Provider_PageSet_Canvas>
);
```

Use for: State only needed in subtree, resets on unmount.

### Level Decision

- **Move UP:** Siblings need access, parent needs to react, persist across unmounts
- **Move DOWN:** Single subtree uses it, implementation detail, should reset on unmount

## Codebase Examples

| Provider                          | Location                             | Purpose                                 |
| --------------------------------- | ------------------------------------ | --------------------------------------- |
| `Provider_Page_Set`               | `Page_Set/`                          | Selected scene/file IDs, placement mode |
| `Provider_Page_Scene`             | `Page_Scene/`                        | Scene editor state                      |
| `Provider_Spark_FileBrowserModal` | `components/Spark_FileBrowserModal/` | Modal file selection                    |

## Checklist

- [ ] `State` class with default values
- [ ] `ContextDefault` with `Partial<State>` type
- [ ] One-line `Reducer` spread merge
- [ ] Named exports: `Provider_[Name]`, `useProvider_[Name]`
- [ ] `initialState` prop if needed
- [ ] Variable naming: `p[Name]`

## Related Skills

- **frontend-naming-conventions** - Provider and hook naming patterns

<!-- Last compacted: 2025-12-18 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
