---
name: react-state-management
description: Gestion d'état React avec Zustand, hooks personnalisés, et patterns de cleanup. MANDATORY pour state management. À utiliser lors de state global, custom hooks, ou quand l'utilisateur mentionne "state", "zustand", "useState", "useEffect", "cleanup". Use when this capability is needed.
metadata:
  author: romualdp
---

# React State Management

## 🎯 Mission

Implémenter une **gestion d'état robuste** avec Zustand, hooks personnalisés, et **patterns de cleanup** pour éviter les memory leaks.

## ⚠️ IMPORTANT: Server-First Data Fetching

**CRITICAL** : Depuis Next.js 16, **useEffect n'est PLUS pour le fetch initial des données**.

### ❌ ANTI-PATTERN: useEffect pour fetch initial

```typescript
// ❌ MAUVAIS - useEffect pour fetch initial (OLD PATTERN)
"use client";

export function TeamsList() {
  const [teams, setTeams] = useState([]);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    async function loadTeams() {
      const data = await fetchTeams();
      setTeams(data);
      setIsLoading(false);
    }
    void loadTeams();
  }, []); // ❌ Fetch initial = ANTI-PATTERN

  return <div>{teams.map(...)}</div>;
}
```

### ✅ PATTERN CORRECT: Server Component

```typescript
// ✅ BON - Server Component (NEW PATTERN)
// Pas de "use client"

export async function TeamsList() {
  const teams = await getTeams(); // ✅ Fetch server-side

  return <div>{teams.map(...)}</div>;
}
```

### 📋 Quand utiliser useEffect

**useEffect est UNIQUEMENT pour** :
1. **Polling** : Rafraîchir les données périodiquement
2. **Refetch après mutation** : Recharger après une action utilisateur
3. **Event listeners** : window.addEventListener, etc.
4. **Subscriptions** : WebSocket, EventEmitter
5. **Cleanup** : Timers, listeners, subscriptions

**useEffect n'est PAS pour** :
- ❌ Fetch initial des données (utiliser Server Components)
- ❌ Cache de données serveur (utiliser Server Components + Suspense)
- ❌ Loading states initiaux (utiliser Suspense)

### 📚 Zustand: État CLIENT uniquement

**Zustand est pour** :
- ✅ État UI (modals open/closed, selected items)
- ✅ État de formulaire (draft, validation)
- ✅ Préférences utilisateur (theme, language)
- ✅ État éphémère (notifications, toasts)

**Zustand n'est PAS pour** :
- ❌ Cache de données serveur (teams, users, etc.)
- ❌ Données fetchées du backend (utiliser Server Components)

## 🏗️ Architecture State

```
src/
├── features/
│   └── club-management/
│       ├── stores/
│       │   ├── club.store.ts         # Zustand store
│       │   └── subscription.store.ts
│       └── hooks/
│           ├── useClub.ts            # Custom hooks
│           ├── useSubscription.ts
│           └── useMembers.ts
└── store/
    ├── auth.store.ts                 # Global stores
    └── ui.store.ts
```

## 📦 Zustand Store Pattern

### Template Store

```typescript
// features/club-management/stores/club.store.ts

import { create } from 'zustand';
import { devtools } from 'zustand/middleware';

interface ClubState {
  // State
  currentClub: Club | null;
  clubs: Club[];
  isLoading: boolean;
  error: string | null;

  // Actions
  setCurrentClub: (club: Club | null) => void;
  setClubs: (clubs: Club[]) => void;
  addClub: (club: Club) => void;
  updateClub: (id: string, updates: Partial<Club>) => void;
  removeClub: (id: string) => void;
  setLoading: (loading: boolean) => void;
  setError: (error: string | null) => void;
  reset: () => void;
}

const initialState = {
  currentClub: null,
  clubs: [],
  isLoading: false,
  error: null,
};

export const useClubStore = create<ClubState>()(
  devtools(
    (set) => ({
      ...initialState,

      setCurrentClub: (club) => set({ currentClub: club }),

      setClubs: (clubs) => set({ clubs }),

      addClub: (club) =>
        set((state) => ({
          clubs: [...state.clubs, club],
        })),

      updateClub: (id, updates) =>
        set((state) => ({
          clubs: state.clubs.map((club) =>
            club.id === id ? { ...club, ...updates } : club
          ),
          currentClub:
            state.currentClub?.id === id
              ? { ...state.currentClub, ...updates }
              : state.currentClub,
        })),

      removeClub: (id) =>
        set((state) => ({
          clubs: state.clubs.filter((club) => club.id !== id),
          currentClub: state.currentClub?.id === id ? null : state.currentClub,
        })),

      setLoading: (loading) => set({ isLoading: loading }),

      setError: (error) => set({ error }),

      reset: () => set(initialState),
    }),
    { name: 'ClubStore' }
  )
);
```

### Store avec Async Actions

```typescript
// features/club-management/stores/subscription.store.ts

import { create } from 'zustand';
import { subscriptionsApi } from '../api/subscriptions.api';

interface SubscriptionState {
  subscription: Subscription | null;
  plans: SubscriptionPlan[];
  isLoading: boolean;
  error: string | null;

  // Async actions
  fetchSubscription: (clubId: string) => Promise<void>;
  fetchPlans: () => Promise<void>;
  upgradePlan: (planId: string) => Promise<void>;
}

export const useSubscriptionStore = create<SubscriptionState>((set, get) => ({
  subscription: null,
  plans: [],
  isLoading: false,
  error: null,

  fetchSubscription: async (clubId) => {
    set({ isLoading: true, error: null });
    try {
      const subscription = await subscriptionsApi.get(clubId);
      set({ subscription, isLoading: false });
    } catch (error) {
      set({ error: (error as Error).message, isLoading: false });
    }
  },

  fetchPlans: async () => {
    set({ isLoading: true, error: null });
    try {
      const plans = await subscriptionsApi.getPlans();
      set({ plans, isLoading: false });
    } catch (error) {
      set({ error: (error as Error).message, isLoading: false });
    }
  },

  upgradePlan: async (planId) => {
    const { subscription } = get();
    if (!subscription) return;

    set({ isLoading: true, error: null });
    try {
      const updated = await subscriptionsApi.upgrade(subscription.clubId, planId);
      set({ subscription: updated, isLoading: false });
    } catch (error) {
      set({ error: (error as Error).message, isLoading: false });
    }
  },
}));
```

## 🪝 Custom Hooks Patterns

### Hook Simple (Read-only)

```typescript
// features/club-management/hooks/useClub.ts

import { useEffect } from 'react';
import { useClubStore } from '../stores/club.store';
import { clubsApi } from '../api/clubs.api';

export function useClub(clubId: string) {
  const { currentClub, isLoading, error, setCurrentClub, setLoading, setError } =
    useClubStore();

  useEffect(() => {
    let cancelled = false;

    async function loadClub() {
      setLoading(true);
      setError(null);

      try {
        const club = await clubsApi.getById(clubId);
        if (!cancelled) {
          setCurrentClub(club);
        }
      } catch (err) {
        if (!cancelled) {
          setError((err as Error).message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }

    void loadClub();

    // ✅ CLEANUP: Annule les updates si le composant unmount
    return () => {
      cancelled = true;
    };
  }, [clubId, setCurrentClub, setLoading, setError]);

  return { club: currentClub, isLoading, error };
}
```

### Hook avec Polling

```typescript
// features/club-management/hooks/useSubscriptionStatus.ts

import { useEffect } from 'react';
import { useSubscriptionStore } from '../stores/subscription.store';

export function useSubscriptionStatus(clubId: string, pollInterval = 5000) {
  const { subscription, fetchSubscription } = useSubscriptionStore();

  useEffect(() => {
    let intervalId: NodeJS.Timeout;
    let cancelled = false;

    async function poll() {
      if (!cancelled) {
        await fetchSubscription(clubId);
      }
    }

    // Initial fetch
    void poll();

    // Start polling
    intervalId = setInterval(poll, pollInterval);

    // ✅ CLEANUP: Clear interval + cancel pending updates
    return () => {
      cancelled = true;
      clearInterval(intervalId);
    };
  }, [clubId, pollInterval, fetchSubscription]);

  return { subscription };
}
```

### Hook avec AbortController (API Fetch)

```typescript
// features/club-management/hooks/useMembers.ts

import { useState, useEffect } from 'react';
import { membersApi } from '../api/members.api';

export function useMembers(clubId: string) {
  const [members, setMembers] = useState<Member[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const abortController = new AbortController();

    async function loadMembers() {
      setIsLoading(true);
      setError(null);

      try {
        const result = await membersApi.list(clubId, {
          signal: abortController.signal,
        });
        setMembers(result);
      } catch (err) {
        if ((err as Error).name !== 'AbortError') {
          setError((err as Error).message);
        }
      } finally {
        setIsLoading(false);
      }
    }

    void loadMembers();

    // ✅ CLEANUP: Abort pending request
    return () => {
      abortController.abort();
    };
  }, [clubId]);

  return { members, isLoading, error };
}
```

### Hook avec Event Listeners

```typescript
// hooks/useWindowSize.ts

import { useState, useEffect } from 'react';

export function useWindowSize() {
  const [size, setSize] = useState({
    width: typeof window !== 'undefined' ? window.innerWidth : 0,
    height: typeof window !== 'undefined' ? window.innerHeight : 0,
  });

  useEffect(() => {
    function handleResize() {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener('resize', handleResize);

    // ✅ CLEANUP: Remove event listener
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return size;
}
```

### Hook avec Subscription (WebSocket, EventEmitter)

```typescript
// hooks/useRealtimeNotifications.ts

import { useState, useEffect } from 'react';
import { notificationService } from '@/lib/notifications';

export function useRealtimeNotifications(userId: string) {
  const [notifications, setNotifications] = useState<Notification[]>([]);

  useEffect(() => {
    // Subscribe to notifications
    const subscription = notificationService.subscribe(userId, (notification) => {
      setNotifications((prev) => [notification, ...prev]);
    });

    // ✅ CLEANUP: Unsubscribe
    return () => {
      subscription.unsubscribe();
    };
  }, [userId]);

  return { notifications };
}
```

## 🚨 Patterns de Cleanup CRITIQUES

### 1. Async Data Fetching

```typescript
// ❌ MAUVAIS - Pas de cleanup
useEffect(() => {
  async function loadData() {
    const result = await fetchData(userId);
    setData(result); // Peut set state après unmount = ERROR
  }
  void loadData();
}, [userId]);

// ✅ BON - Avec cancelled flag
useEffect(() => {
  let cancelled = false;

  async function loadData() {
    const result = await fetchData(userId);
    if (!cancelled) {
      setData(result);
    }
  }

  void loadData();

  return () => {
    cancelled = true;
  };
}, [userId]);
```

### 2. Timers & Intervals

```typescript
// ❌ MAUVAIS - Interval jamais cleared
useEffect(() => {
  const id = setInterval(() => {
    fetchStats();
  }, 5000);
}, []);

// ✅ BON - Clear interval
useEffect(() => {
  const id = setInterval(() => {
    fetchStats();
  }, 5000);

  return () => {
    clearInterval(id);
  };
}, []);
```

### 3. Event Listeners

```typescript
// ❌ MAUVAIS - Listener jamais removed
useEffect(() => {
  window.addEventListener('scroll', handleScroll);
}, []);

// ✅ BON - Remove listener
useEffect(() => {
  window.addEventListener('scroll', handleScroll);

  return () => {
    window.removeEventListener('scroll', handleScroll);
  };
}, []);
```

### 4. AbortController (Fetch)

```typescript
// ❌ MAUVAIS - Request continue après unmount
useEffect(() => {
  async function load() {
    const data = await fetch('/api/data');
    setData(data);
  }
  void load();
}, []);

// ✅ BON - Abort request
useEffect(() => {
  const controller = new AbortController();

  async function load() {
    try {
      const data = await fetch('/api/data', { signal: controller.signal });
      setData(data);
    } catch (err) {
      if (err.name !== 'AbortError') {
        setError(err);
      }
    }
  }

  void load();

  return () => {
    controller.abort();
  };
}, []);
```

### 5. Subscriptions (WebSocket, EventEmitter)

```typescript
// ❌ MAUVAIS - Subscription jamais unsubscribed
useEffect(() => {
  const sub = service.subscribe('event', handler);
}, []);

// ✅ BON - Unsubscribe
useEffect(() => {
  const sub = service.subscribe('event', handler);

  return () => {
    sub.unsubscribe();
  };
}, []);
```

## 🎨 Patterns Avancés

### Hook avec Debounce

```typescript
// hooks/useDebounce.ts

import { useState, useEffect } from 'react';

export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // ✅ CLEANUP: Clear timeout si value change
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function SearchComponent() {
  const [search, setSearch] = useState('');
  const debouncedSearch = useDebounce(search, 500);

  useEffect(() => {
    if (debouncedSearch) {
      // API call avec valeur debounced
      searchApi.search(debouncedSearch);
    }
  }, [debouncedSearch]);

  return <input value={search} onChange={(e) => setSearch(e.target.value)} />;
}
```

### Hook avec Ref pour Latest Value

```typescript
// hooks/useLatest.ts

import { useRef, useEffect } from 'react';

export function useLatest<T>(value: T) {
  const ref = useRef(value);

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref;
}

// Usage: Évite de recréer callback à chaque render
function Component({ onSuccess }: Props) {
  const latestOnSuccess = useLatest(onSuccess);

  useEffect(() => {
    const id = setInterval(() => {
      // Utilise toujours la dernière version de onSuccess
      latestOnSuccess.current();
    }, 1000);

    return () => clearInterval(id);
  }, []); // Pas besoin de dépendance onSuccess
}
```

### Store avec Persist (LocalStorage)

```typescript
// store/auth.store.ts

import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  token: string | null;
  setUser: (user: User | null) => void;
  setToken: (token: string | null) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,

      setUser: (user) => set({ user }),

      setToken: (token) => set({ token }),

      logout: () => set({ user: null, token: null }),
    }),
    {
      name: 'auth-storage', // LocalStorage key
      partialize: (state) => ({
        // Ne persiste que token (pas user)
        token: state.token,
      }),
    }
  )
);
```

### Selector Pattern (Performance)

```typescript
// ❌ MAUVAIS - Re-render à chaque changement du store
function Component() {
  const { clubs, currentClub, isLoading } = useClubStore();
  return <div>{currentClub?.name}</div>;
}

// ✅ BON - Selector (re-render uniquement si currentClub change)
function Component() {
  const currentClub = useClubStore((state) => state.currentClub);
  return <div>{currentClub?.name}</div>;
}

// ✅ BON - Multiple selectors
function Component() {
  const currentClub = useClubStore((state) => state.currentClub);
  const isLoading = useClubStore((state) => state.isLoading);

  return <div>{isLoading ? 'Loading...' : currentClub?.name}</div>;
}
```

## ✅ Checklist State Management

- [ ] Zustand stores dans `features/[feature]/stores/`
- [ ] Custom hooks dans `features/[feature]/hooks/`
- [ ] **TOUS les useEffect ont un cleanup function**
- [ ] Async operations avec `cancelled` flag
- [ ] Timers avec `clearTimeout/clearInterval`
- [ ] Event listeners avec `removeEventListener`
- [ ] Fetch requests avec `AbortController`
- [ ] Subscriptions avec `unsubscribe()`
- [ ] Selectors pour optimiser re-renders
- [ ] DevTools middleware activé en dev
- [ ] Persist middleware pour auth/settings uniquement

## 🚨 Erreurs Courantes

### 1. Oublier Cleanup

```typescript
// ❌ MAUVAIS
useEffect(() => {
  const id = setInterval(tick, 1000);
}, []); // Memory leak !

// ✅ BON
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);
```

### 2. setState après Unmount

```typescript
// ❌ MAUVAIS
useEffect(() => {
  fetchData().then(setData); // Si unmount avant then = ERROR
}, []);

// ✅ BON
useEffect(() => {
  let cancelled = false;
  fetchData().then((data) => {
    if (!cancelled) setData(data);
  });
  return () => {
    cancelled = true;
  };
}, []);
```

### 3. Re-renders Excessifs

```typescript
// ❌ MAUVAIS - Re-render à chaque changement
function Component() {
  const store = useClubStore(); // Tout le store
  return <div>{store.currentClub?.name}</div>;
}

// ✅ BON - Selector
function Component() {
  const name = useClubStore((state) => state.currentClub?.name);
  return <div>{name}</div>;
}
```

## 📚 Skills Complémentaires

- **atomic-component** : Composants utilisant les hooks
- **server-actions** : Server Actions avec state management
- **use-optimistic** : Optimistic updates + Zustand

---

**Rappel CRITIQUE** : **TOUS les useEffect doivent avoir un cleanup function** pour éviter memory leaks et setState après unmount.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romualdp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
