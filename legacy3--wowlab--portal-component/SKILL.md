---
name: portal-component
description: Generate Next.js components for the portal app with proper patterns. Use when creating new pages, components, or features in apps/portal. Use when this capability is needed.
metadata:
  author: legacy3
---

# Portal Component Generator

Generate Next.js 16 components following portal conventions.

## Page Structure

For a new feature at `/app/[locale]/feature/page.tsx`:

```
app/[locale]/feature/
├── page.tsx           # Minimal, renders component
├── loading.tsx        # Uses FeatureSkeleton
└── layout.tsx         # Optional layout wrapper

components/feature/
├── index.ts           # Barrel exports
├── feature-page.tsx   # Main component + skeleton
├── feature-content.tsx # Inner content
└── feature-context.tsx # Optional context/provider
```

## Page Template

```tsx
// app/[locale]/feature/page.tsx
import { FeaturePage } from "@/components/feature";

interface Props {
  params: Promise<{ id: string }>;
}

export default async function FeatureRoute({ params }: Props) {
  const { id } = await params;
  return <FeaturePage featureId={id} />;
}
```

## Loading Template

```tsx
// app/[locale]/feature/loading.tsx
import { FeatureSkeleton } from "@/components/feature";

export default function FeatureLoading() {
  return <FeatureSkeleton />;
}
```

## Component Template

```tsx
// components/feature/feature-page.tsx
"use client";

import { Skeleton } from "@/components/ui";

interface FeaturePageProps {
  featureId: string;
}

export function FeaturePage({ featureId }: FeaturePageProps) {
  return (
    <div className="flex flex-col gap-6">
      <FeatureContent featureId={featureId} />
    </div>
  );
}

export function FeatureSkeleton() {
  return (
    <div className="flex flex-col gap-6">
      <Skeleton className="h-32 rounded-xl" />
      <div className="flex flex-col md:flex-row gap-4">
        <Skeleton className="h-64 rounded-xl flex-1" />
        <Skeleton className="h-64 rounded-xl flex-1" />
      </div>
    </div>
  );
}
```

## Barrel Export Template

```ts
// components/feature/index.ts
/* eslint-disable */

// Components

export { FeaturePage, FeatureSkeleton } from "./feature-page";
export { FeatureContent } from "./feature-content";
```

## Zustand Store Template

For client-only state (selection, UI preferences, editor state):

```ts
// lib/state/feature/store.ts
"use client";

import { create } from "zustand";

interface FeatureStore {
  selectedId: string | null;
  setSelected: (id: string | null) => void;
}

export const useFeatureStore = create<FeatureStore>()((set) => ({
  selectedId: null,
  setSelected: (id) => set({ selectedId: id }),
}));
```

## React Query Template

For server data (fetching, mutations), add hooks to `lib/query/services/{domain}.ts`:

```ts
// lib/query/services/feature.ts
"use client";

import { useQuery } from "@tanstack/react-query";
import type { Tables } from "@/lib/supabase/database.types";
import { createClient } from "@/lib/supabase/client";

type FeatureRow = Tables<"features">;

export function useFeature(id: string | undefined) {
  return useQuery({
    enabled: !!id,
    queryFn: async () => {
      const supabase = createClient();
      const { data, error } = await supabase
        .from("features")
        .select("*")
        .eq("id", id)
        .single();
      if (error) throw error;
      return data;
    },
    queryKey: ["features", id],
  });
}
```

Then export from `lib/query/services/index.ts`.

## Formatting

Use `Intl` APIs or `date-fns` for formatting. No shared formatting library exists.

```tsx
// Numbers
const formatter = new Intl.NumberFormat("en", { notation: "compact" });
formatter.format(1234567); // "1.2M"

// Relative time
const rtf = new Intl.RelativeTimeFormat("en", { numeric: "auto" });
rtf.format(-2, "day"); // "2 days ago"

// Dates (import from date-fns)
import { formatDistanceToNow } from "date-fns";
formatDistanceToNow(date, { addSuffix: true }); // "2 hours ago"
```

## Instructions

1. Ask what the feature/component should do
2. Determine if it needs server state (React Query) or client state (Zustand)
3. Generate page, loading, and component files
4. Create barrel exports
5. Add query hooks in `lib/query/services/` or stores in `lib/state/` as needed
6. Use Intl APIs or date-fns for formatting numbers and dates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/legacy3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
