---
name: atomic-component-generator
description: Génère des composants React suivant l'Atomic Design avec décomposition maximale et pattern Smart/Dumb. MANDATORY pour tous composants. À utiliser lors de la création de composants, pages, ou quand l'utilisateur mentionne "component", "atomic", "smart", "dumb", "decompose", "React", "UI". Use when this capability is needed.
metadata:
  author: romualdp
---

# Atomic Component Generator

## 🎯 Mission

Créer des composants React **maximalement décomposés** suivant l'**Atomic Design** et le pattern **Smart/Dumb** pour une **réutilisabilité** et **maintenabilité** maximales.

## ⚛️ Philosophie Atomic Design

### Le Principe

**Atomic Design** décompose l'UI en **5 niveaux** :

1. **Atoms** (Atomes) : Plus petites unités (Button, Input, Label)
2. **Molecules** (Molécules) : Groupes d'atomes (FormField = Label + Input + Error)
3. **Organisms** (Organismes) : Groupes de molécules (Header = Logo + Nav + UserMenu)
4. **Templates** : Layouts avec placeholders
5. **Pages** : Templates avec données réelles

**Dans ce projet**, nous utilisons principalement **3 niveaux** :
- **Atoms** : Composants de base (`components/ui/` - shadcn/ui)
- **Smart Components** : Logique + état (`features/*/components/*Form`, `*List`)
- **Dumb Components** : Présentation pure (`features/*/components/*Step`, `*Card`)

### Règle d'Or : Décomposition Maximale

> **CRITICAL** : Components MUST be maximally decomposed.

**Pourquoi** :
- ✅ **Réutilisabilité** : Petits composants = réutilisables partout
- ✅ **Testabilité** : Petits composants = faciles à tester
- ✅ **Maintenabilité** : Un composant = Une responsabilité
- ✅ **Lisibilité** : Code clair et compréhensible
- ✅ **Collaboration** : Équipe peut travailler en parallèle

**Mauvais signe** :
- ❌ Fichier > 150 lignes
- ❌ Composant fait plusieurs choses
- ❌ JSX imbriqué sur 10+ niveaux
- ❌ Logique + présentation mélangées

## 🧩 Pattern Server/Client Components (Next.js 16)

### 🎯 Server Components par défaut

**IMPORTANT** : Depuis Next.js 16, **TOUS les composants sont Server Components par défaut**.

**Server Components** :
- ✅ Fetch data server-side (async/await)
- ✅ Accès direct à la base de données / APIs backend
- ✅ Zero JavaScript client-side pour le data fetching
- ✅ Meilleur SEO (contenu pré-rendu)
- ✅ Pas besoin de "use client"

**Quand utiliser** :
- Pages, layouts, templates
- Widgets qui affichent des données fetchées
- Composants sans interactivité

**Exemple** :
```typescript
// Server Component (par défaut, pas de "use client")
export async function TeamsWidget() {
  const teams = await getTeams(); // Fetch server-side

  return <div>{teams.map(t => <TeamCard key={t.id} team={t} />)}</div>;
}
```

### 🖱️ Client Components (uniquement si nécessaire)

**Client Components** (avec `"use client"`) :
- ✅ Interactivité (onClick, onChange, etc.)
- ✅ Hooks React (useState, useEffect, useContext)
- ✅ Stores Zustand (state client)
- ✅ Browser APIs (localStorage, etc.)

**Quand utiliser** :
- Formulaires interactifs
- Composants avec state UI (modals, dropdowns)
- Event handlers
- useOptimistic, useTransition

**Exemple** :
```typescript
"use client"; // Requis pour Client Component

export function TeamForm() {
  const [name, setName] = useState("");

  return <input value={name} onChange={(e) => setName(e.target.value)} />;
}
```

### 📦 Pattern de Composition Server → Client

**Règle d'or** : Server Component parent → Client Components enfants

```typescript
// ✅ BON - Server Component avec fetch
export async function TeamsList() {
  const teams = await getTeams(); // Server-side

  return (
    <div>
      {teams.map(team => (
        <TeamCard key={team.id} team={team} /> // Client si interactivité
      ))}
    </div>
  );
}

// Client Component si besoin d'interactivité
"use client";
export function TeamCard({ team }: Props) {
  const [isExpanded, setIsExpanded] = useState(false);

  return (
    <div onClick={() => setIsExpanded(!isExpanded)}>
      {/* ... */}
    </div>
  );
}
```

## ⚠️ CRITICAL: Component Folder Architecture

### ❌ NEVER Create Components in `app/`

**RÈGLE ABSOLUE** : Le dossier `app/` est **uniquement pour le routing Next.js**.

```
❌ MAUVAIS (NEVER DO THIS):
app/
  └── players/
      ├── page.tsx
      └── components/          ← ❌ JAMAIS DE COMPOSANTS ICI
          ├── PlayerCard.tsx   ← ❌ MAUVAIS
          ├── PlayersList.tsx  ← ❌ MAUVAIS
          └── ...

✅ BON:
app/
  └── players/
      └── page.tsx             ← Routing only (max 50 lignes)

features/
  └── players/                 ← ✅ TOUS les composants vont ici
      ├── components/
      │   ├── PlayerCard.tsx   ← ✅ BON
      │   ├── PlayersList.tsx  ← ✅ BON
      │   └── ...
      ├── api/
      │   └── players.server.ts
      └── types/
```

### Pourquoi Cette Règle Est Critique

1. **Séparation des responsabilités**
   - `app/` = Routing Next.js (infrastructure)
   - `features/` = Business logic (métier)

2. **Réutilisabilité**
   - Composants dans `features/` peuvent être utilisés dans plusieurs routes
   - Composants dans `app/` sont isolés à une seule route

3. **Maintenabilité**
   - Structure claire et prévisible
   - Évite la duplication de code

4. **Best Practices Next.js 16**
   - Pages dans `app/` doivent être minces (orchestration)
   - Logique métier dans `features/`

### Examples Réels

```typescript
// ❌ MAUVAIS
// app/players/components/PlayerCard.tsx
export function PlayerCard({ player }) { ... }

// ❌ MAUVAIS
// app/teams/[id]/components/TeamDetails.tsx
export function TeamDetails({ team }) { ... }

// ✅ BON
// features/players/components/PlayerCard.tsx
export function PlayerCard({ player }) { ... }

// ✅ BON
// features/teams/components/TeamDetails.tsx
export function TeamDetails({ team }) { ... }
```

### Page Structure

```typescript
// app/players/page.tsx (Max 50 lignes - Orchestration uniquement)
import { PlayersListServer } from "@/features/players/components/PlayersListServer";
import { PlayersStatsGrid } from "@/features/players/components/PlayersStatsGrid";

export default async function PlayersPage() {
  return (
    <div>
      <Suspense fallback={<StatsGridSkeleton />}>
        <PlayersStatsGrid />
      </Suspense>

      <Suspense fallback={<ListSkeleton />}>
        <PlayersListServer />
      </Suspense>
    </div>
  );
}
```

## 🧩 Pattern Smart/Dumb Components (pour Client Components)

### Smart Components (Container Components)

**Responsabilités** :
- ✅ Gèrent l'état client (useState, Zustand stores)
- ✅ Gèrent la logique UI
- ✅ Appellent les Server Actions (mutations)
- ✅ Orchestrent les Dumb Components
- ✅ Gèrent les side effects (useEffect pour polling, refetch, etc.)

**Caractéristiques** :
- **"use client"** directive
- Nom se termine par **Form**, **Manager**, **Container**
- Peu ou pas de JSX (délègue aux Dumb Components)
- Beaucoup de logique JavaScript
- Props minimales (reçoit peu, délègue beaucoup)

**Emplacement** : `features/*/components/` (ex: `ClubCreationForm`, `TeamManager`)

### Dumb Components (Presentational Components)

**Responsabilités** :
- ✅ Affichent l'UI (JSX uniquement)
- ✅ Reçoivent des props
- ✅ Émettent des events (callbacks)
- ✅ Aucun état (ou état UI minimal: hover, focus)
- ✅ Aucune logique métier

**Caractéristiques** :
- Nom descriptif : **Step**, **Card**, **Item**, **Display**, **Section**
- Beaucoup de JSX
- Peu ou pas de logique JavaScript
- Props strictement typées (interfaces)
- Pure functions (même props = même output)

**Emplacement** : `features/*/components/` ou `components/shared/` si réutilisable

### Exemple de Séparation

```typescript
// ❌ MAUVAIS - Tout dans un seul composant (150+ lignes)
export function ClubCreation() {
  const [step, setStep] = useState(1);
  const [clubData, setClubData] = useState({});
  const [isPending, startTransition] = useTransition();

  const handleSubmit = async () => {
    startTransition(async () => {
      await createClubAction(clubData);
    });
  };

  return (
    <div>
      {step === 1 && (
        <div>
          <h2>Informations du club</h2>
          <input onChange={(e) => setClubData({...clubData, name: e.target.value})} />
          {/* 50 lignes de formulaire */}
        </div>
      )}
      {step === 2 && (
        <div>
          <h2>Choisir un plan</h2>
          {/* 50 lignes de sélection de plan */}
        </div>
      )}
      {/* ... */}
    </div>
  );
}

// ✅ BON - Décomposé en Smart + Dumb

// Smart Component (orchestration)
export function ClubCreationForm() {
  const [step, setStep] = useState(1);
  const { clubData, updateClubData } = useClubCreationStore();
  const [isPending, startTransition] = useTransition();

  const handleSubmit = async () => {
    startTransition(async () => {
      await createClubAction(clubData);
    });
  };

  return (
    <FormWizard currentStep={step} onStepChange={setStep}>
      {step === 1 && (
        <ClubInfoStep
          data={clubData}
          onChange={updateClubData}
          onNext={() => setStep(2)}
        />
      )}
      {step === 2 && (
        <PlanSelectionStep
          selectedPlan={clubData.plan}
          onSelectPlan={(plan) => updateClubData({ plan })}
          onNext={handleSubmit}
          isPending={isPending}
        />
      )}
    </FormWizard>
  );
}

// Dumb Components (présentation)
interface ClubInfoStepProps {
  data: Partial<ClubData>;
  onChange: (data: Partial<ClubData>) => void;
  onNext: () => void;
}

export function ClubInfoStep({ data, onChange, onNext }: ClubInfoStepProps) {
  return (
    <div className="space-y-4">
      <h2 className="text-2xl font-bold">Informations du club</h2>
      <FormField
        label="Nom du club"
        value={data.name}
        onChange={(name) => onChange({ name })}
      />
      <FormField
        label="Description"
        value={data.description}
        onChange={(description) => onChange({ description })}
      />
      <Button onClick={onNext}>Suivant</Button>
    </div>
  );
}
```

## 📏 Règles de Décomposition

### 1. Limite de Lignes

- **Page** : MAX 50 lignes (composition uniquement)
- **Smart Component** : MAX 100 lignes (logique + orchestration)
- **Dumb Component** : MAX 80 lignes (présentation)
- **Atomic Component** : MAX 50 lignes (UI basique)

**Si dépassé** → Décomposer immédiatement

### 2. Single Responsibility Principle

Un composant = **UNE responsabilité**

```typescript
// ❌ MAUVAIS - Responsabilités multiples
export function UserProfile() {
  // 1. Fetch data
  // 2. Display profile
  // 3. Edit form
  // 4. Settings panel
  // ... 200 lignes
}

// ✅ BON - Responsabilités séparées
export function UserProfilePage() {
  return (
    <>
      <UserProfileHeader />
      <UserProfileDetails />
      <UserProfileSettings />
    </>
  );
}

export function UserProfileHeader() { /* ... */ }
export function UserProfileDetails() { /* ... */ }
export function UserProfileSettings() { /* ... */ }
```

### 3. Extraction de la Logique

**Règle** : Si logique > 10 lignes → Extraire dans un **custom hook** ou **util**

```typescript
// ❌ MAUVAIS - Logique dans le composant
export function ClubCreationForm() {
  const [step, setStep] = useState(1);
  const [clubData, setClubData] = useState({});

  const validateStep1 = () => {
    if (!clubData.name || clubData.name.length < 3) return false;
    if (!clubData.description) return false;
    return true;
  };

  const validateStep2 = () => {
    // 20 lignes de validation
  };

  // ... logique complexe
}

// ✅ BON - Logique extraite
export function ClubCreationForm() {
  const {
    step,
    clubData,
    goToNextStep,
    goToPreviousStep,
    updateClubData,
    validateCurrentStep,
  } = useClubCreationFlow();

  return (
    <FormWizard currentStep={step}>
      {/* Présentation pure */}
    </FormWizard>
  );
}

// Custom hook (logique extraite)
function useClubCreationFlow() {
  const [step, setStep] = useState(1);
  const [clubData, setClubData] = useState({});

  const validateCurrentStep = () => {
    return clubCreationValidators[step](clubData);
  };

  // ... logique

  return {
    step,
    clubData,
    goToNextStep,
    goToPreviousStep,
    updateClubData,
    validateCurrentStep,
  };
}
```

### 4. Composition > Monolithic

**Privilégier la composition** de petits composants

```typescript
// ❌ MAUVAIS - Composant monolithique
export function MemberCard({ member }: Props) {
  return (
    <div className="card">
      <div className="avatar">
        <img src={member.avatar} />
      </div>
      <div className="info">
        <h3>{member.name}</h3>
        <p>{member.email}</p>
        <span className="role">{member.role}</span>
      </div>
      <div className="actions">
        <button onClick={onEdit}>Edit</button>
        <button onClick={onDelete}>Delete</button>
      </div>
    </div>
  );
}

// ✅ BON - Composé de petits composants
export function MemberCard({ member, onEdit, onDelete }: Props) {
  return (
    <Card>
      <MemberAvatar src={member.avatar} alt={member.name} />
      <MemberInfo name={member.name} email={member.email} role={member.role} />
      <MemberActions onEdit={onEdit} onDelete={onDelete} />
    </Card>
  );
}

// Composants réutilisables
function MemberAvatar({ src, alt }: AvatarProps) { /* ... */ }
function MemberInfo({ name, email, role }: InfoProps) { /* ... */ }
function MemberActions({ onEdit, onDelete }: ActionsProps) { /* ... */ }
```

## 🏗️ Structure des Composants

### Organisation des Dossiers

```
features/
└── club-management/
    └── components/
        ├── ClubCreationForm.tsx          # Smart (orchestration)
        ├── ClubInfoStep.tsx              # Dumb (présentation step 1)
        ├── PlanSelectionStep.tsx         # Dumb (présentation step 2)
        ├── MembersList.tsx               # Smart (fetch + state)
        ├── MemberCard.tsx                # Dumb (présentation membre)
        ├── MemberAvatar.tsx              # Dumb (avatar)
        ├── MemberActions.tsx             # Dumb (boutons actions)
        └── shared/
            ├── FormWizard.tsx            # Réutilisable (wizard)
            └── StepIndicator.tsx         # Réutilisable (steps)

components/
└── ui/                                   # Atomic components (shadcn/ui)
    ├── button.tsx
    ├── input.tsx
    ├── card.tsx
    └── ...
```

### Template Server Component (preferred)

```typescript
// features/club-management/components/MembersList.tsx
// Server Component (pas de "use client")

import { getMembers } from '../api/members.server';
import { MemberCard } from './MemberCard';
import { MembersListSkeleton } from './MembersListSkeleton';

interface MembersListProps {
  clubId: string;
}

export async function MembersList({ clubId }: MembersListProps) {
  // ✅ Fetch server-side
  const members = await getMembers(clubId);

  // Empty state
  if (members.length === 0) {
    return <EmptyMembersList />;
  }

  // Render (délègue aux Client Components si besoin d'interactivité)
  return (
    <div className="space-y-4">
      <MembersListHeader count={members.length} />
      <div className="grid gap-4">
        {members.map(member => (
          <MemberCard
            key={member.id}
            member={member}
            clubId={clubId}
          />
        ))}
      </div>
    </div>
  );
}
```

### Template Client Component (si interactivité nécessaire)

```typescript
// features/club-management/components/MemberCard.tsx
"use client";

import { useTransition } from 'react';
import { removeMemberAction } from '../actions/remove-member.action';

interface MemberCardProps {
  member: Member;
  clubId: string;
}

export function MemberCard({ member, clubId }: MemberCardProps) {
  const [isPending, startTransition] = useTransition();

  // Event handler (mutation)
  const handleRemove = () => {
    startTransition(async () => {
      await removeMemberAction(clubId, member.id);
    });
  };

  return (
    <Card>
      <MemberInfo member={member} />
      <Button onClick={handleRemove} disabled={isPending}>
        {isPending ? "Suppression..." : "Retirer"}
      </Button>
    </Card>
  );
}
```

### Template Dumb Component

```typescript
// features/club-management/components/MemberCard.tsx

import { Card } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { MemberAvatar } from './MemberAvatar';

interface MemberCardProps {
  member: {
    id: string;
    name: string;
    email: string;
    role: string;
    avatar?: string;
  };
  onRemove: () => void;
}

export function MemberCard({ member, onRemove }: MemberCardProps) {
  return (
    <Card className="p-4 flex items-center gap-4">
      <MemberAvatar src={member.avatar} name={member.name} />

      <div className="flex-1">
        <h3 className="font-semibold">{member.name}</h3>
        <p className="text-sm text-muted-foreground">{member.email}</p>
        <span className="text-xs text-primary">{member.role}</span>
      </div>

      <Button
        variant="destructive"
        size="sm"
        onClick={onRemove}
      >
        Retirer
      </Button>
    </Card>
  );
}

// Sous-composant (si réutilisable ailleurs)
interface MemberAvatarProps {
  src?: string;
  name: string;
}

function MemberAvatar({ src, name }: MemberAvatarProps) {
  const initials = name
    .split(' ')
    .map(n => n[0])
    .join('')
    .toUpperCase();

  if (src) {
    return (
      <img
        src={src}
        alt={name}
        className="w-12 h-12 rounded-full object-cover"
      />
    );
  }

  return (
    <div className="w-12 h-12 rounded-full bg-primary text-primary-foreground flex items-center justify-center font-semibold">
      {initials}
    </div>
  );
}
```

### Template Page (Ultra-thin)

```typescript
// app/(dashboard)/coach/clubs/[id]/members/page.tsx

import { Suspense } from 'react';
import { MembersList } from '@/features/club-management/components/MembersList';
import { MembersListSkeleton } from '@/features/club-management/components/MembersListSkeleton';

interface PageProps {
  params: { id: string };
}

export default function ClubMembersPage({ params }: PageProps) {
  return (
    <div className="container py-8">
      <h1 className="text-3xl font-bold mb-6">Membres du club</h1>

      <Suspense fallback={<MembersListSkeleton />}>
        <MembersList clubId={params.id} />
      </Suspense>
    </div>
  );
}
```

## ✅ Checklist Atomic Design

### Avant de Créer un Composant

- [ ] Le composant a-t-il UNE seule responsabilité ?
- [ ] Est-il Smart (logique) ou Dumb (présentation) ?
- [ ] Peut-il être décomposé davantage ?
- [ ] Est-il réutilisable ailleurs ?
- [ ] Les props sont-elles strictement typées ?
- [ ] Le nom est-il descriptif ?

### Pendant la Création

- [ ] Logique extraite dans hooks/utils si > 10 lignes
- [ ] JSX imbrication < 5 niveaux
- [ ] Composant < 150 lignes
- [ ] Aucun state dans Dumb Component (sauf UI: hover, focus)
- [ ] Aucune logique métier dans Dumb Component
- [ ] Composition privilégiée

### Après la Création

- [ ] Composant testé (si Smart)
- [ ] Props documentées (JSDoc si nécessaire)
- [ ] Accessible (ARIA, keyboard navigation)
- [ ] Responsive (mobile-first)
- [ ] Pas de warnings ESLint/TypeScript

## 🚨 Erreurs Courantes à Éviter

### 1. Composant Monolithique

```typescript
// ❌ MAUVAIS - 300 lignes, fait tout
export function ClubDashboard() {
  // Fetch data
  // Display stats
  // Display members list
  // Display latest activities
  // Display settings panel
  // ... 300 lignes
}

// ✅ BON - Décomposé
export function ClubDashboard() {
  return (
    <>
      <ClubStats />
      <MembersList />
      <ActivitiesFeed />
      <SettingsPanel />
    </>
  );
}
```

### 2. Mélange Smart/Dumb

```typescript
// ❌ MAUVAIS - Mélange logique + présentation
export function MemberCard({ member }: Props) {
  const [isPending, startTransition] = useTransition();

  const handleRemove = async () => {
    startTransition(async () => {
      await removeMemberAction(member.id);
    });
  };

  return <Card>...</Card>; // Présentation
}

// ✅ BON - Séparation claire
// Smart Component (logique)
export function MembersList() {
  const handleRemove = async (id: string) => {
    await removeMemberAction(id);
  };

  return members.map(m => (
    <MemberCard member={m} onRemove={() => handleRemove(m.id)} />
  ));
}

// Dumb Component (présentation)
export function MemberCard({ member, onRemove }: Props) {
  return <Card onClick={onRemove}>...</Card>;
}
```

### 3. Props Drilling Excessif

```typescript
// ❌ MAUVAIS - Props drilling sur 5 niveaux
<GrandParent data={data}>
  <Parent data={data}>
    <Child data={data}>
      <GrandChild data={data} />
    </Child>
  </Parent>
</GrandParent>

// ✅ BON - Context ou Zustand store
const useDataStore = create((set) => ({
  data: null,
  setData: (data) => set({ data }),
}));

// Composants consomment directement
export function GrandChild() {
  const data = useDataStore(state => state.data);
  return <div>{data}</div>;
}
```

## 📚 Skills Complémentaires

Pour aller plus loin :
- **mobile-first** : Design mobile-first principles
- **react-state-management** : State management patterns
- **zero-warnings** : Quality standards

---

**Rappel** : **Décomposition maximale** = Code maintenable, réutilisable et testable. Un composant = Une responsabilité.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romualdp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
