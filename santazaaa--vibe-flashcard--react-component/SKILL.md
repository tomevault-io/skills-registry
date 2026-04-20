---
name: react-component
description: Create reusable React components using DaisyUI, Next.js conventions, and Zustand hooks. Use when this capability is needed.
metadata:
  author: santazaaa
---

## What I do
- Generate TSX components with 'use client' directive for Next.js App Router.
- Use DaisyUI classes for styling (e.g., btn, card, modal).
- Integrate Zustand state via useStore hook from store/flashcardStore.
- Follow project patterns: TypeScript interfaces for props, export default, concise JSX.

## When to use me
Use for new UI elements like buttons, modals, or quiz components. Provide component name, props, and purpose.

## Examples
For a quiz button integrating store state:

```tsx
'use client';

import { useStore } from '@/store/flashcardStore';

interface QuizButtonProps {
  label: string;
  onClick: () => void;
}

export default function QuizButton({ label, onClick }: QuizButtonProps) {
  const { quizMode } = useStore();

  return (
    <button className="btn btn-primary" onClick={onClick}>
      {label} ({quizMode})
    </button>
  );
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/santazaaa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
