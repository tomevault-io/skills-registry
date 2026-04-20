---
name: scaffold-component
description: Generate a React component or page following CrossQuest project conventions Use when this capability is needed.
metadata:
  author: sandrobeatz
---

# /scaffold-component <Name> [--page <route>]

Generate a new React component file following CrossQuest project patterns.

## Arguments

- `<Name>` (required) — Component name in PascalCase (e.g., `LeaderBoard`, `HintModal`)
- `--page <route>` (optional) — Also create a route page at `/app/<route>/page.tsx`

## Instructions

### Component File: `/components/<Name>.tsx`

Generate the component using this template:

```tsx
'use client';

import React from 'react';
import { motion } from 'framer-motion';

const MotionDiv = motion.div as any;

interface <Name>Props {
  // TODO: Define props
}

const <Name>: React.FC<<Name>Props> = ({}) => {
  return (
    <MotionDiv
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      className="min-h-screen bg-gradient-to-b from-slate-50 to-white p-4"
    >
      <div className="max-w-lg mx-auto">
        <h1 className="text-2xl font-black text-slate-800 mb-6">
          {/* TODO: Title */}
        </h1>
      </div>
    </MotionDiv>
  );
};

export default <Name>;
```

### Page File (if `--page` specified): `/app/<route>/page.tsx`

```tsx
'use client';

import React from 'react';
import <Name> from '@/components/<Name>';

export default function <Name>Page() {
  return <<Name> />;
}
```

### After Scaffolding

1. Create the component file
2. If `--page` specified, create the route directory and page file
3. Report what was created
4. Suggest next steps:
   - "Define props in the interface"
   - "Add state with useAppContext() if profile data is needed"
   - "Import icons from lucide-react as needed"

## Template Conventions (from project patterns)

- Always starts with `'use client';`
- Always includes the `MotionDiv = motion.div as any` cast
- Uses `@/` import alias
- Default export
- Props defined as named interface
- Entry animation with Framer Motion
- Tailwind-only styling with project color palette
- Mobile-first layout (`max-w-lg mx-auto`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sandrobeatz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
