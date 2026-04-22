---
name: mobile-first-responsive-design
description: Approche Mobile-First avec Tailwind CSS, breakpoints, et progressive enhancement. MANDATORY pour toutes les pages. À utiliser lors de responsive design, layouts, ou quand l'utilisateur mentionne "mobile", "responsive", "tablet", "desktop". Use when this capability is needed.
metadata:
  author: romualdp
---

# Mobile-First Responsive Design

## 🎯 Mission

Implémenter une **approche Mobile-First** avec Tailwind CSS pour une expérience optimale sur tous les écrans.

## 📱 Philosophie Mobile-First

**Mobile-First** = Concevoir d'abord pour mobile, puis améliorer progressivement pour écrans plus grands.

### Pourquoi Mobile-First ?

1. ✅ **Performance**: Charge le minimum nécessaire pour mobile
2. ✅ **Priorité au contenu**: Force à identifier l'essentiel
3. ✅ **Progressive Enhancement**: Ajoute des fonctionnalités pour écrans plus grands
4. ✅ **Touch-First**: Conçu pour le tactile, fonctionne au clavier/souris

### Workflow Mobile-First

```
1. Design mobile (320-768px)
   ↓
2. Test sur mobile (iPhone, Android)
   ↓
3. Ajouter breakpoints tablet (md:)
   ↓
4. Ajouter breakpoints desktop (lg:, xl:)
   ↓
5. Test sur tous écrans
```

## 📐 Breakpoints Tailwind

```typescript
// tailwind.config.ts

export default {
  theme: {
    screens: {
      // Mobile-first approach
      'sm': '640px',   // @media (min-width: 640px)
      'md': '768px',   // Tablet
      'lg': '1024px',  // Desktop
      'xl': '1280px',  // Large desktop
      '2xl': '1536px', // Extra large
    },
  },
};
```

**Utilisation** :
```tsx
{/* Mobile par défaut (320-639px) */}
<div className="text-sm p-4 flex-col">

{/* Tablet (640px+) */}
<div className="sm:text-base sm:p-6">

{/* Desktop (1024px+) */}
<div className="lg:text-lg lg:p-8 lg:flex-row">
```

## 🎨 Patterns Mobile-First

### Layout: Stack → Grid

```tsx
// Mobile: Stack vertical
// Desktop: Grid 2 colonnes
<div className="flex flex-col gap-4 lg:grid lg:grid-cols-2 lg:gap-8">
  <div>Column 1</div>
  <div>Column 2</div>
</div>
```

### Navigation: Burger → Horizontal

```tsx
// components/Navigation.tsx
'use client';

import { useState } from 'react';
import { Menu, X } from 'lucide-react';

export function Navigation() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <nav className="relative">
      {/* Mobile: Burger button */}
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="lg:hidden p-2"
        aria-label="Toggle menu"
      >
        {isOpen ? <X /> : <Menu />}
      </button>

      {/* Mobile: Full-screen menu */}
      {isOpen && (
        <div className="fixed inset-0 z-50 bg-background lg:hidden">
          <div className="flex flex-col gap-4 p-6">
            <a href="/dashboard" className="text-lg">Dashboard</a>
            <a href="/teams" className="text-lg">Teams</a>
            <a href="/matches" className="text-lg">Matches</a>
          </div>
        </div>
      )}

      {/* Desktop: Horizontal menu (always visible) */}
      <div className="hidden lg:flex lg:gap-6">
        <a href="/dashboard">Dashboard</a>
        <a href="/teams">Teams</a>
        <a href="/matches">Matches</a>
      </div>
    </nav>
  );
}
```

### Typography: Responsive Scales

```tsx
<h1 className="text-2xl font-bold md:text-3xl lg:text-4xl">
  Responsive Heading
</h1>

<p className="text-sm md:text-base lg:text-lg">
  Responsive paragraph text.
</p>
```

### Spacing: Progressive Enhancement

```tsx
<div className="p-4 md:p-6 lg:p-8">
  {/* Padding: 16px mobile → 24px tablet → 32px desktop */}
</div>

<div className="gap-2 md:gap-4 lg:gap-6">
  {/* Gap: 8px → 16px → 24px */}
</div>
```

### Images: Responsive & Optimized

```tsx
import Image from 'next/image';

<div className="relative aspect-video w-full">
  <Image
    src="/club-photo.jpg"
    alt="Club photo"
    fill
    className="object-cover"
    sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  />
</div>
```

### Cards: Stack → Grid

```tsx
// Mobile: 1 card full width
// Tablet: 2 cards per row
// Desktop: 3 cards per row
<div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
  <ClubCard />
  <ClubCard />
  <ClubCard />
</div>
```

### Sidebar: Hidden → Visible

```tsx
// Mobile: Sidebar hidden, toggle avec button
// Desktop: Sidebar always visible
<div className="flex">
  {/* Sidebar */}
  <aside className="hidden w-64 border-r lg:block">
    <nav className="p-6">
      <a href="/settings">Settings</a>
      <a href="/profile">Profile</a>
    </nav>
  </aside>

  {/* Main content */}
  <main className="flex-1 p-4 md:p-6 lg:p-8">
    {/* Content */}
  </main>
</div>
```

## 🖱️ Touch-Friendly Design

### Hit Areas: Min 44x44px

```tsx
// ❌ MAUVAIS - Trop petit pour tactile
<button className="p-1 text-xs">
  Click
</button>

// ✅ BON - Min 44x44px
<button className="min-h-[44px] min-w-[44px] p-3">
  Click
</button>
```

### Spacing: Éviter zones trop proches

```tsx
// ❌ MAUVAIS - Boutons trop proches
<div className="flex gap-1">
  <button>Edit</button>
  <button>Delete</button>
</div>

// ✅ BON - Spacing confortable
<div className="flex gap-4">
  <button>Edit</button>
  <button>Delete</button>
</div>
```

### Hover: Utiliser @media (hover: hover)

```tsx
// CSS avec hover conditionnel
<button className="bg-primary text-white hover:bg-primary/90 lg:hover:bg-primary/80">
  Click
</button>
```

```css
/* globals.css */

/* Hover uniquement sur devices avec souris */
@media (hover: hover) {
  .hover\:bg-primary\/90:hover {
    background-color: hsl(var(--primary) / 0.9);
  }
}
```

## 📋 Patterns Complets

### Dashboard Layout

```tsx
// app/(dashboard)/coach/page.tsx

export default function CoachDashboard() {
  return (
    <div className="min-h-screen">
      {/* Header */}
      <header className="border-b p-4 md:p-6">
        <h1 className="text-xl font-bold md:text-2xl lg:text-3xl">
          Dashboard Coach
        </h1>
      </header>

      {/* Main content */}
      <main className="p-4 md:p-6 lg:p-8">
        {/* Stats grid: 1 col mobile → 2 cols tablet → 4 cols desktop */}
        <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
          <StatCard title="Membres" value={42} />
          <StatCard title="Équipes" value={3} />
          <StatCard title="Matchs" value={12} />
          <StatCard title="Victoires" value={8} />
        </div>

        {/* Content sections: Stack mobile → Side-by-side desktop */}
        <div className="mt-8 flex flex-col gap-6 lg:flex-row lg:gap-8">
          {/* Recent activity */}
          <section className="flex-1">
            <h2 className="mb-4 text-lg font-semibold md:text-xl">
              Activité récente
            </h2>
            <ActivityList />
          </section>

          {/* Quick actions */}
          <aside className="lg:w-80">
            <h2 className="mb-4 text-lg font-semibold md:text-xl">
              Actions rapides
            </h2>
            <QuickActions />
          </aside>
        </div>
      </main>
    </div>
  );
}
```

### Form Layout

```tsx
// features/club-management/components/ClubCreationForm.tsx
'use client';

export function ClubCreationForm() {
  return (
    <form className="space-y-6">
      {/* Form sections: Stack mobile → 2 cols desktop */}
      <div className="grid grid-cols-1 gap-6 lg:grid-cols-2">
        {/* Club info */}
        <div className="space-y-4">
          <h3 className="text-base font-semibold md:text-lg">
            Informations du club
          </h3>

          <div>
            <label className="text-sm font-medium">Nom du club</label>
            <input
              type="text"
              className="mt-1 w-full rounded-md border p-3"
            />
          </div>

          <div>
            <label className="text-sm font-medium">Description</label>
            <textarea
              rows={4}
              className="mt-1 w-full rounded-md border p-3"
            />
          </div>
        </div>

        {/* Contact info */}
        <div className="space-y-4">
          <h3 className="text-base font-semibold md:text-lg">
            Contact
          </h3>

          <div>
            <label className="text-sm font-medium">Email</label>
            <input
              type="email"
              className="mt-1 w-full rounded-md border p-3"
            />
          </div>

          <div>
            <label className="text-sm font-medium">Téléphone</label>
            <input
              type="tel"
              className="mt-1 w-full rounded-md border p-3"
            />
          </div>
        </div>
      </div>

      {/* Actions: Stack mobile → Row desktop */}
      <div className="flex flex-col gap-3 sm:flex-row sm:justify-end">
        <button
          type="button"
          className="min-h-[44px] rounded-md border px-6 py-2"
        >
          Annuler
        </button>
        <button
          type="submit"
          className="min-h-[44px] rounded-md bg-primary px-6 py-2 text-white"
        >
          Créer le club
        </button>
      </div>
    </form>
  );
}
```

### Modal: Full-screen mobile → Centered desktop

```tsx
// components/ui/modal.tsx
'use client';

import { X } from 'lucide-react';

export function Modal({ children, onClose }: Props) {
  return (
    <div className="fixed inset-0 z-50 flex items-end justify-center bg-black/50 sm:items-center">
      {/* Modal content */}
      <div className="relative w-full rounded-t-2xl bg-white sm:max-w-lg sm:rounded-2xl">
        {/* Close button */}
        <button
          onClick={onClose}
          className="absolute right-4 top-4 min-h-[44px] min-w-[44px] p-2"
          aria-label="Close"
        >
          <X />
        </button>

        {/* Content with responsive padding */}
        <div className="p-6 sm:p-8">
          {children}
        </div>
      </div>
    </div>
  );
}
```

### Table: Responsive avec horizontal scroll

```tsx
// components/MembersTable.tsx

export function MembersTable({ members }: Props) {
  return (
    <div className="overflow-x-auto">
      <table className="w-full min-w-[600px]">
        <thead>
          <tr className="border-b">
            <th className="p-3 text-left text-sm font-medium">Nom</th>
            <th className="p-3 text-left text-sm font-medium">Email</th>
            <th className="p-3 text-left text-sm font-medium">Rôle</th>
            <th className="p-3 text-right text-sm font-medium">Actions</th>
          </tr>
        </thead>
        <tbody>
          {members.map((member) => (
            <tr key={member.id} className="border-b">
              <td className="p-3 text-sm">{member.name}</td>
              <td className="p-3 text-sm">{member.email}</td>
              <td className="p-3 text-sm">{member.role}</td>
              <td className="p-3 text-right">
                <button className="min-h-[44px] min-w-[44px] p-2">
                  Edit
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

## 🔍 Testing Responsive

### Breakpoints à tester

```
📱 Mobile:  320px (iPhone SE), 375px (iPhone 12), 414px (iPhone 14 Pro Max)
📱 Tablet:  768px (iPad), 820px (iPad Air), 1024px (iPad Pro)
💻 Desktop: 1280px, 1440px, 1920px
```

### Chrome DevTools

1. **F12** → Toggle device toolbar
2. Tester chaque breakpoint
3. Vérifier touch targets (Show rulers)
4. Tester orientation portrait/landscape

### Browser Testing

```bash
# Mobile
- Safari iOS (iPhone)
- Chrome Android

# Desktop
- Chrome, Firefox, Safari, Edge
```

## ✅ Checklist Mobile-First

- [ ] Design mobile AVANT desktop
- [ ] Classes Tailwind sans préfixe = mobile (base)
- [ ] Breakpoints progressifs (`sm:`, `md:`, `lg:`)
- [ ] Typography responsive (text-sm → text-base → text-lg)
- [ ] Spacing responsive (p-4 → p-6 → p-8)
- [ ] Images avec `sizes` attribute
- [ ] Touch targets min 44x44px
- [ ] Navigation: Burger mobile → Horizontal desktop
- [ ] Modals: Full-screen mobile → Centered desktop
- [ ] Tables: Horizontal scroll ou cards sur mobile
- [ ] Forms: Stack mobile → Grid desktop
- [ ] Testé sur vrais devices (iPhone, Android)

## 🚨 Erreurs Courantes

### 1. Desktop-First (MAUVAIS)

```tsx
// ❌ MAUVAIS - Desktop par défaut, puis mobile
<div className="grid grid-cols-3 gap-8 md:grid-cols-1 md:gap-4">
```

```tsx
// ✅ BON - Mobile par défaut, puis desktop
<div className="grid grid-cols-1 gap-4 lg:grid-cols-3 lg:gap-8">
```

### 2. Touch Targets Trop Petits

```tsx
// ❌ MAUVAIS - 24x24px (trop petit)
<button className="p-1">
  <Icon size={16} />
</button>

// ✅ BON - Min 44x44px
<button className="min-h-[44px] min-w-[44px] p-3">
  <Icon size={20} />
</button>
```

### 3. Texte Trop Petit sur Mobile

```tsx
// ❌ MAUVAIS
<p className="text-xs">Long paragraph...</p>

// ✅ BON - Lisible sur mobile
<p className="text-sm md:text-base">Long paragraph...</p>
```

### 4. Hover sur Mobile (Inutile)

```tsx
// ❌ MAUVAIS - Hover sur mobile = pas d'effet
<button className="hover:bg-blue-500">Click</button>

// ✅ BON - Active pour tactile, Hover pour souris
<button className="active:bg-blue-500 lg:hover:bg-blue-500">
  Click
</button>
```

### 5. Layout Cassé sur Petits Écrans

```tsx
// ❌ MAUVAIS - Fixed width
<div className="w-[600px]">Content</div>

// ✅ BON - Responsive width
<div className="w-full max-w-2xl">Content</div>
```

## 📚 Skills Complémentaires

- **atomic-component** : Composants responsive
- **suspense-streaming** : Skeleton responsive
- **view-transitions** : Transitions smooth sur mobile

---

**Rappel** : **TOUJOURS** design mobile AVANT desktop = Meilleure UX et performance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romualdp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
