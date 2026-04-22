---
name: server-components-pattern
description: Pattern Server-First avec Next.js 16 pour data fetching optimal. MANDATORY pour toutes les pages et widgets. À utiliser lors de la création de composants, pages, ou quand l'utilisateur mentionne "fetch", "data loading", "SSR", "server component". Use when this capability is needed.
metadata:
  author: romualdp
---

# Server Components Pattern (Next.js 16)

## 🎯 Mission

Implémenter le pattern **Server-First** avec Next.js 16 pour un data fetching optimal, meilleur SEO, et moins de JavaScript client.

## 🌟 Philosophie Server-First

**Depuis Next.js 16, tous les composants sont Server Components par défaut.**

### Pourquoi Server-First ?

- ✅ **SEO optimal** : Contenu pré-rendu côté serveur
- ✅ **Performance** : Moins de JavaScript client
- ✅ **Data fetching** : Accès direct à la base de données/APIs
- ✅ **Security** : Clés API, secrets restent côté serveur
- ✅ **UX** : Streaming progressif avec Suspense

## 📋 Decision Tree: Server vs Client Component

```
┌─────────────────────────────────────┐
│  Besoin d'interactivité ?          │
│  (onClick, onChange, useState, etc.)│
└────────────┬────────────────────────┘
             │
      ┌──────┴──────┐
      │             │
     NON           OUI
      │             │
      ▼             ▼
┌───────────┐  ┌────────────────┐
│  SERVER   │  │  CLIENT        │
│ COMPONENT │  │ COMPONENT      │
│           │  │ "use client"   │
└───────────┘  └────────────────┘
```

### Server Components (par défaut)

**Quand utiliser** :
- Pages, layouts, templates
- Widgets qui affichent des données
- Composants sans interactivité
- Tout ce qui n'a PAS besoin de JavaScript client

**Exemple** :
```typescript
// app/(dashboard)/coach/page.tsx
// Server Component (par défaut, pas de "use client")

import { getUser } from "@/lib/auth";
import { getTeams } from "@/features/teams/api/teams.server";

export default async function CoachDashboardPage() {
  const user = await getUser(); // ✅ Fetch server-side
  const teams = await getTeams(); // ✅ Fetch server-side

  return (
    <div>
      <h1>Bienvenue, {user.firstName}</h1>
      <TeamsList teams={teams} />
    </div>
  );
}
```

### Client Components (uniquement si nécessaire)

**Quand utiliser** :
- Interactivité (onClick, onChange, onSubmit)
- Hooks React (useState, useEffect, useContext)
- Stores Zustand
- Browser APIs (localStorage, window, etc.)
- useOptimistic, useTransition

**Exemple** :
```typescript
// features/teams/components/TeamForm.tsx
"use client"; // ✅ Requis pour Client Component

import { useState, useTransition } from "react";
import { createTeamAction } from "../actions/create-team.action";

export function TeamForm() {
  const [name, setName] = useState("");
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      await createTeamAction({ name });
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <button disabled={isPending}>
        {isPending ? "Création..." : "Créer"}
      </button>
    </form>
  );
}
```

## 🏗️ Architecture Server-First

### Structure des fichiers

```
features/
└── teams/
    ├── api/
    │   └── teams.server.ts          # ✅ API server-side
    ├── actions/
    │   └── create-team.action.ts    # ✅ Server Actions (mutations)
    ├── components/
    │   ├── TeamsList.tsx            # ✅ Server Component (fetch)
    │   ├── TeamCard.tsx             # ✅ Server Component (présentation)
    │   └── TeamForm.tsx             # ✅ Client Component (interactivité)
    └── hooks/
        └── useTeamForm.ts           # ✅ Custom hook (client-side logic)

app/
└── (dashboard)/
    └── teams/
        └── page.tsx                 # ✅ Server Component (async)
```

### Template: API Server (*.server.ts)

```typescript
// features/teams/api/teams.server.ts

import { serverFetch } from "@/lib/server-fetch";
import type { Team } from "@/types";

/**
 * Server-side API for Teams
 *
 * Functions to fetch team data from Server Components
 * Uses serverFetch with httpOnly cookies for auth
 */

export async function getTeams(): Promise<Team[]> {
  const teams = await serverFetch<Team[]>("/teams", {
    cache: "no-store", // ou "force-cache" pour caching
  });

  return teams || [];
}

export async function getTeam(teamId: string): Promise<Team | null> {
  const team = await serverFetch<Team>(`/teams/${teamId}`, {
    cache: "no-store",
  });

  return team;
}
```

### Template: Server Component Page

```typescript
// app/(dashboard)/teams/page.tsx

import { Suspense } from "react";
import { getTeams } from "@/features/teams/api/teams.server";
import { TeamsList } from "@/features/teams/components/TeamsList";
import { TeamsListSkeleton } from "@/features/teams/components/TeamsListSkeleton";

/**
 * Teams Page - Server Component
 *
 * Pattern: Server Component with Suspense streaming
 */
export default async function TeamsPage() {
  return (
    <div className="container py-8">
      <h1 className="text-3xl font-bold mb-6">Mes Équipes</h1>

      <Suspense fallback={<TeamsListSkeleton />}>
        <TeamsList />
      </Suspense>
    </div>
  );
}
```

### Template: Server Component Widget

```typescript
// features/teams/components/TeamsList.tsx

import { getTeams } from "../api/teams.server";
import { TeamCard } from "./TeamCard";

/**
 * TeamsList - Server Component
 *
 * Fetch teams server-side and display
 * No client-side JavaScript for data fetching
 */
export async function TeamsList() {
  const teams = await getTeams(); // ✅ Fetch server-side

  if (teams.length === 0) {
    return <EmptyTeamsList />;
  }

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      {teams.map(team => (
        <TeamCard key={team.id} team={team} />
      ))}
    </div>
  );
}
```

### Template: Client Component (si interactivité)

```typescript
// features/teams/components/TeamCard.tsx
"use client";

import { useTransition } from "react";
import { deleteTeamAction } from "../actions/delete-team.action";

interface TeamCardProps {
  team: Team;
}

export function TeamCard({ team }: TeamCardProps) {
  const [isPending, startTransition] = useTransition();

  const handleDelete = () => {
    startTransition(async () => {
      await deleteTeamAction(team.id);
    });
  };

  return (
    <Card>
      <h3>{team.name}</h3>
      <Button onClick={handleDelete} disabled={isPending}>
        {isPending ? "Suppression..." : "Supprimer"}
      </Button>
    </Card>
  );
}
```

## 🔄 Pattern de Composition

### Server Component parent → Client Components enfants

**Règle d'or** : Fetch server-side, pass props aux Client Components

```typescript
// ✅ BON - Server Component parent
export async function TeamsDashboard() {
  const teams = await getTeams(); // Server-side fetch

  return (
    <div>
      <TeamsHeader /> {/* Server Component */}
      <TeamsList teams={teams} /> {/* Client Component si interactivité */}
    </div>
  );
}

// Client Component enfant
"use client";
export function TeamsList({ teams }: { teams: Team[] }) {
  const [selected, setSelected] = useState<string | null>(null);

  return (
    <div>
      {teams.map(team => (
        <div
          key={team.id}
          onClick={() => setSelected(team.id)}
          className={selected === team.id ? "selected" : ""}
        >
          {team.name}
        </div>
      ))}
    </div>
  );
}
```

### ❌ ANTI-PATTERN: Client Component avec useEffect pour fetch

```typescript
// ❌ MAUVAIS - Client Component avec useEffect
"use client";

export function TeamsList() {
  const [teams, setTeams] = useState([]);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    async function loadTeams() {
      const data = await fetchTeams(); // ❌ Fetch client-side
      setTeams(data);
      setIsLoading(false);
    }
    void loadTeams();
  }, []); // ❌ useEffect pour fetch initial = ANTI-PATTERN

  if (isLoading) return <Skeleton />;

  return <div>{teams.map(...)}</div>;
}

// ✅ BON - Server Component
export async function TeamsList() {
  const teams = await getTeams(); // ✅ Fetch server-side

  return <div>{teams.map(...)}</div>;
}
```

## 🌊 Suspense & Streaming

### Pattern avec Suspense

```typescript
// app/(dashboard)/teams/page.tsx

export default async function TeamsPage() {
  return (
    <div>
      {/* Section 1 - Streams independently */}
      <Suspense fallback={<TeamStatsSkeleton />}>
        <TeamStats />
      </Suspense>

      {/* Section 2 - Streams independently */}
      <Suspense fallback={<TeamsListSkeleton />}>
        <TeamsList />
      </Suspense>
    </div>
  );
}

// Chaque composant fetch ses données
async function TeamStats() {
  const stats = await getTeamStats(); // Fetch 1
  return <div>{stats.total} équipes</div>;
}

async function TeamsList() {
  const teams = await getTeams(); // Fetch 2 (en parallèle)
  return <div>{teams.map(...)}</div>;
}
```

**Avantages** :
- ✅ Parallel fetching (TeamStats et TeamsList en parallèle)
- ✅ Progressive rendering (TeamStats peut s'afficher avant TeamsList)
- ✅ Meilleure perceived performance

## 🔐 Auth Server-Side

### Fonction getUser()

```typescript
// lib/auth.ts

import { serverFetch } from "./server-fetch";
import type { User } from "@/types";

export async function getUser(): Promise<User | null> {
  const user = await serverFetch<User>("/auth/profile", {
    requireAuth: true,
    cache: "no-store",
  });

  return user;
}

export async function requireAuth(): Promise<User> {
  const user = await getUser();

  if (!user) {
    throw new Error("Unauthorized");
  }

  return user;
}
```

### Usage dans une page

```typescript
// app/(dashboard)/profile/page.tsx

import { requireAuth } from "@/lib/auth";

export default async function ProfilePage() {
  const user = await requireAuth(); // ✅ Fetch user server-side

  return (
    <div>
      <h1>Profil de {user.firstName}</h1>
      <ProfileForm user={user} />
    </div>
  );
}
```

## 📊 Caching Strategy

### cache: "no-store" (default)

```typescript
export async function getTeams(): Promise<Team[]> {
  const teams = await serverFetch<Team[]>("/teams", {
    cache: "no-store", // ✅ Toujours fresh (dashboard, etc.)
  });

  return teams || [];
}
```

### cache: "force-cache"

```typescript
export async function getPublicStats(): Promise<Stats> {
  const stats = await serverFetch<Stats>("/stats/public", {
    cache: "force-cache", // ✅ Cache agressif (données statiques)
  });

  return stats;
}
```

### revalidate

```typescript
export async function getNews(): Promise<News[]> {
  const news = await serverFetch<News[]>("/news", {
    next: { revalidate: 60 }, // ✅ Revalidate toutes les 60s
  });

  return news || [];
}
```

## ✅ Checklist Server-First

### Avant de créer un composant

- [ ] Ai-je besoin d'interactivité ? (onClick, onChange, etc.)
- [ ] Ai-je besoin de hooks React ? (useState, useEffect, etc.)
- [ ] Ai-je besoin de Browser APIs ? (localStorage, window, etc.)

**Si OUI à l'une** → Client Component (`"use client"`)
**Si NON à toutes** → Server Component (par défaut)

### Pour les pages

- [ ] Page est un Server Component (async)
- [ ] Data fetching server-side (await getTeams())
- [ ] Suspense pour le streaming
- [ ] Skeletons pour les fallbacks
- [ ] Client Components seulement pour l'interactivité

### Pour les APIs

- [ ] Fichier `*.server.ts` pour APIs server-side
- [ ] Utilise `serverFetch` avec cookies
- [ ] Gestion d'erreur appropriée
- [ ] Caching strategy définie

## 🚨 Erreurs Courantes

### 1. useEffect pour fetch initial

```typescript
// ❌ MAUVAIS
"use client";
useEffect(() => {
  fetchData().then(setData);
}, []);

// ✅ BON
export async function Component() {
  const data = await getData();
  return <div>{data}</div>;
}
```

### 2. "use client" partout

```typescript
// ❌ MAUVAIS - Tout en Client Component
"use client";
export function Page() {
  return <TeamsList />;
}

"use client";
export function TeamsList() {
  const teams = useTeams(); // Custom hook qui fait fetch
  return <div>{teams.map(...)}</div>;
}

// ✅ BON - Server Component + Client si besoin
export async function Page() {
  const teams = await getTeams();
  return <TeamsList teams={teams} />;
}

// Client seulement si interactivité
"use client";
export function TeamsList({ teams }: Props) {
  const [selected, setSelected] = useState(null);
  return <div>{teams.map(...)}</div>;
}
```

### 3. Props non-sérialisables

```typescript
// ❌ MAUVAIS - Fonction passée de Server à Client
export async function ServerComp() {
  const handleClick = () => console.log("click");

  return <ClientComp onClick={handleClick} />; // ❌ ERROR
}

// ✅ BON - Server Action
export async function ServerComp() {
  return <ClientComp />;
}

// Client Component définit son propre handler
"use client";
export function ClientComp() {
  const handleClick = async () => {
    await someAction(); // Server Action
  };

  return <button onClick={handleClick}>Click</button>;
}
```

## 📚 Skills Complémentaires

- **suspense-streaming** : Suspense et Streaming patterns
- **server-actions** : Server Actions pour mutations
- **atomic-component** : Décomposition et composition
- **react-state-management** : State client (Zustand)

---

**Rappel CRITIQUE** : **Server Components par défaut, Client Components seulement si nécessaire**. Le fetch initial des données se fait TOUJOURS côté serveur, jamais avec useEffect.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romualdp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
