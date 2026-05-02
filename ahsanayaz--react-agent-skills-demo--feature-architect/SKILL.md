---
name: feature-architect
description: Scaffolds a new React feature using Clean Architecture patterns for a TypeScript/Vite project. Use when this capability is needed.
metadata:
  author: ahsanayaz
---

# React Feature Architect (Vite + TS)

Use this skill when I ask to "create a new feature" or "setup module [name]".

## Architectural Rules

1. **File Extensions**: Use `.tsx` for components and `.ts` for hooks, types, and services.
2. **Barrel Exports**: Every folder MUST have an `index.ts` to export its contents.
3. **Component Pattern**: Use functional components with `interface` for Props.
4. **Logic Separation**: UI goes in `/components`, state/logic goes in `/hooks`.

## Workflow

1. **Directory Setup**: Run `scripts/scaffold.js <name>` to build the folder structure.
2. **Type Definition**: Create `src/features/<name>/types/index.ts` with a base interface for the feature.
3. **Hook Generation**: Create a base `src/features/<name>/hooks/use<Name>.ts`.
4. **Component Generation**: Create a primary component in `src/features/<name>/components/<Name>.tsx`.
5. **Entry Point**: Create `src/features/<name>/index.ts` that exports the main component and hook.

## Template Example (Main Component)

```tsx
import React from 'react';
import { use[Name] } from '../hooks/use[Name]';

export const [Name]: React.FC = () => {
  const { data } = use[Name]();
  return <div className="p-4">[Name] Feature</div>;
};
```

After creating the feature, ensure that there's a path configured in the right tsconfig file with a mapping that enables this new feature or components inside to be used as follows:

```ts
import { UserProfile } from "@/features/user";
```

Make sure to use react best practices using the `vercel-react-best-practices` agent skill

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahsanayaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
