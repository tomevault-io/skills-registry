---
name: client-state-data-orchestration
description: Standards for managing server state (TanStack Query) and local client state (Zustand), and how they interoperate. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Client State & Data Orchestration

This skill defines how Arlis manages data flow between the server and the user interface. It ensures a clean separation between **Server State** and **Local Client State**.

## The Golden Rule: Separation of Concerns

1.  **Server State** belongs in **TanStack Query**.
2.  **Local Client State** belongs in **Zustand**.
3.  **NEVER** sync raw server data into a Zustand store. If you need server data, fetch it via a TanStack hook.

## 📡 Server State (TanStack Query)

Use TanStack Query for anything that lives in the database or comes from an external API.

- **Storage**: `lib/hooks/queries.ts`
- **Responsibilities**: Caching, Background Refetching, Loading/Error states, Optimistic Updates.
- **Pattern**: Centralized hooks wrapping the `api` client.

```typescript
// Good: Server state is managed by Query
export function useApplications() {
  return useQuery({
    queryKey: ['applications'],
    queryFn: api.applications.list,
  });
}
```

## 🕹️ Local Client State (Zustand)

Use Zustand for UI-only state that doesn't need to be persisted to the database (or is persisted to LocalStorage).

- **Storage**: `lib/store/` (e.g., `lib/store/use-ui-store.ts`, `lib/store/use-chat-store.ts`)
- **Responsibilities**: Modals, Sidebar toggle, Active tab selection, ephemeral user inputs.

```typescript
// Good: Client state is managed by Zustand
interface UIStore {
  activeApplicationId: string | null;
  setActiveApplicationId: (id: string | null) => void;
}
export const useUIStore = create<UIStore>((set) => ({
  activeApplicationId: null,
  setActiveApplicationId: (id) => set({ activeApplicationId: id }),
}));
```

## 🤝 Orchestration (Working Together)

To coordinate these two, use the **"Reference Pattern"**:
- Zustand holds a **Reference** (e.g., an ID).
- TanStack Query uses that reference to fetch the **Resource**.

### Example: Selecting a Case
```typescript
function Dashboard() {
  // 1. Get the reference from Zustand
  const activeId = useUIStore(s => s.activeApplicationId);
  
  // 2. Pass the reference to TanStack Query
  const { data: application } = useApplication(activeId);
  
  return activeId ? <DetailView data={application} /> : <ListView />;
}
```

## When to use Zustand for "Complex" UI logic?
- When multiple components need to share a state that isn't in a database (e.g., a multi-step form progress).
- When handling optimistic behavior where TanStack's built-in optimistic updates aren't sufficient.

## Forbidden Patterns
- ❌ `const { data } = useQuery(...); useEffect(() => { setZustandData(data) }, [data])` (Sync-loop anti-pattern).
- ❌ Storing huge arrays of server data in Zustand.
- ❌ Using `useState` for state that needs to be accessed by deeply nested children (use Zustand instead).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
