---
name: view-transitions-generator
description: Implémente l'API View Transitions pour des transitions fluides entre pages et états. MANDATORY pour toutes navigations. À utiliser lors de navigation, page transitions, ou quand l'utilisateur mentionne "transition", "animation", "navigation", "smooth". Use when this capability is needed.
metadata:
  author: romualdp
---

# View Transitions Generator

## 🎯 Mission

Implémenter des **transitions fluides** entre pages et états avec l'**API View Transitions** pour une expérience utilisateur moderne.

## 🎨 View Transitions API

### Helper Function

```typescript
// lib/view-transitions.ts

export function startViewTransition(callback: () => void): void {
  if (typeof document !== 'undefined' && 'startViewTransition' in document) {
    (document as any).startViewTransition(callback);
  } else {
    // Fallback pour navigateurs non supportés
    callback();
  }
}
```

### Usage avec Navigation

```typescript
'use client';

import { useRouter } from 'next/navigation';
import { startViewTransition } from '@/lib/view-transitions';

export function ClubCard({ club }: Props) {
  const router = useRouter();

  const handleClick = () => {
    startViewTransition(() => {
      router.push(`/clubs/${club.id}`);
    });
  };

  return (
    <div onClick={handleClick} style={{ viewTransitionName: `club-${club.id}` }}>
      {/* ... */}
    </div>
  );
}
```

### CSS Animations

```css
/* app/globals.css */

::view-transition-old(root),
::view-transition-new(root) {
  animation-duration: 0.3s;
  animation-timing-function: ease-in-out;
}

/* Specific elements */
[style*="view-transition-name"] {
  contain: layout;
}

/* Custom animations */
@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slide-from-right {
  from { transform: translateX(100%); }
  to { transform: translateX(0); }
}

::view-transition-new(club-card) {
  animation: fade-in 0.3s ease-in-out;
}
```

### Patterns

**Page Navigation**:
```typescript
const navigate = (path: string) => {
  startViewTransition(() => router.push(path));
};
```

**State Changes**:
```typescript
const toggleView = () => {
  startViewTransition(() => setView('grid'));
};
```

## ✅ Checklist

- [ ] Helper `startViewTransition()` créé
- [ ] CSS animations définies
- [ ] `view-transition-name` sur éléments clés
- [ ] Fallback pour navigateurs non supportés
- [ ] Utilisé pour TOUTES les navigations

---

**Rappel**: MANDATORY pour toutes navigations = UX moderne et fluide.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romualdp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
