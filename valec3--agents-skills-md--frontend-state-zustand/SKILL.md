---
name: frontend-state-zustand
description: Standardized guidelines and patterns for Frontend State Zustand. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend State Zustand

## When to use this skill
- Managing global client state
- Simpler alternative to Redux
- Sharing state across components

## Workflow
- [ ] Create store with `create`
- [ ] Define state and actions
- [ ] Use selectors to prevent re-renders
- [ ] Split into slices for large stores
- [ ] Persist with middleware if needed

## Instructions

### Basic Store
```typescript
import { create } from 'zustand';

interface Store {
  count: number;
  increment: () => void;
  decrement: () => void;
}

export const useStore = create<Store>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 }))
}));

// Usage
function Counter() {
  const count = useStore(state => state.count);
  const increment = useStore(state => state.increment);
  
  return <button onClick={increment}>{count}</button>;
}
```

### Slices Pattern
```typescript
const createUserSlice = (set) => ({
  user: null,
  setUser: (user) => set({ user })
});

const createCartSlice = (set) => ({
  items: [],
  addItem: (item) => set((state) => ({ 
    items: [...state.items, item] 
  }))
});

export const useStore = create((set) => ({
  ...createUserSlice(set),
  ...createCartSlice(set)
}));
```

## Resources
- Use selectors to optimize
- Split large stores into slices
- Persist with zustand/middleware/persist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
