---
name: zustand-store
description: Manage app state with Zustand, including persistence and actions for quiz modes, progress, etc. Use when this capability is needed.
metadata:
  author: santazaaa
---

## What I do
- Define Zustand stores with create and persist middleware.
- Add state slices (e.g., quizMode, reviewedCount) and actions (e.g., setQuizMode, incrementReviewed).
- Ensure persistence for localStorage, following project patterns in store/flashcardStore.ts.

## When to use me
Use for adding or modifying app state. Provide state fields, actions, and persistence needs.

## Examples
For quiz mode state:

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface QuizState {
  quizMode: 'normal' | 'reverse';
  setQuizMode: (mode: 'normal' | 'reverse') => void;
}

export const useQuizStore = create<QuizState>()(
  persist(
    (set) => ({
      quizMode: 'normal',
      setQuizMode: (mode) => set({ quizMode: mode }),
    }),
    { name: 'quiz-storage' }
  )
);
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/santazaaa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
