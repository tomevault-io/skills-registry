---
name: production-harden
description: Transform vibe-coded MVPs into production-grade, reusable templates. Systematically audit and fix the Five Mortal Sins of production code. Use when this capability is needed.
metadata:
  author: hermeticormus
---

# Production Hardening Skill

**Purpose:** Systematically transform a working MVP into a production-grade, reusable template by addressing the Five Mortal Sins of vibe-coded projects.

## When to Use

- After completing an MVP that "works but feels fragile"
- Before turning a project into a reusable template
- When preparing for real users or production deployment
- After a Mars audit reveals issues

## The Five Mortal Sins (Audit Checklist)

### 1. Data Model Drift
- [ ] TypeScript types match actual data structure
- [ ] Optional vs required fields are correct
- [ ] Enums/unions include all actual values
- [ ] No `as never` or `as any` type bypasses

### 2. Happy Path Delusion
- [ ] Error boundaries exist (React)
- [ ] 404/not-found pages exist
- [ ] Image loading has fallbacks
- [ ] Form validation exists
- [ ] API calls have error handling

### 3. Observability Void
- [ ] Errors are logged properly
- [ ] Build-time validation catches data issues
- [ ] Console errors are meaningful

### 4. Environment Confusion
- [ ] .env.example documents all variables
- [ ] No hardcoded secrets
- [ ] Environment-specific configs separated

### 5. Type Safety Gaps
- [ ] TypeScript strict mode enabled
- [ ] No implicit any
- [ ] Generic components properly typed

## Execution Process

### Phase 1: Audit (Read-Only)
```
1. Run /mars or manually check each sin
2. Document all findings by severity:
   - CRITICAL: Security/data loss risks
   - SEVERE: Will cause outages
   - MODERATE: Degraded experience
   - MINOR: Technical debt
3. Create prioritized todo list
```

### Phase 2: Core Fixes

#### Error Boundaries (Next.js App Router)
Create these files in `src/app/`:

**error.tsx** - Catches route errors
```tsx
"use client";
import { useEffect } from "react";

interface ErrorProps {
  error: Error & { digest?: string };
  reset: () => void;
}

export default function Error({ error, reset }: ErrorProps) {
  useEffect(() => {
    console.error("Application error:", error);
  }, [error]);

  return (
    <div className="min-h-[50vh] flex flex-col items-center justify-center p-8">
      <h1 className="text-2xl font-bold mb-4">Something went wrong</h1>
      <button onClick={reset} className="px-4 py-2 bg-blue-500 text-white rounded">
        Try again
      </button>
    </div>
  );
}
```

**global-error.tsx** - Catches root layout errors
```tsx
"use client";

export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <html>
      <body>
        <h1>Critical Error</h1>
        <button onClick={reset}>Try again</button>
      </body>
    </html>
  );
}
```

**not-found.tsx** - Custom 404
```tsx
import Link from "next/link";

export default function NotFound() {
  return (
    <div className="min-h-[50vh] flex flex-col items-center justify-center">
      <h1 className="text-4xl font-bold mb-4">404</h1>
      <p className="mb-4">Page not found</p>
      <Link href="/" className="text-blue-500 hover:underline">
        Go home
      </Link>
    </div>
  );
}
```

#### Image Fallback Component
```tsx
"use client";
import Image from "next/image";
import { useState } from "react";

const FALLBACK_IMAGE = "/images/placeholder.png";

interface GameImageProps {
  src: string;
  alt: string;
  fill?: boolean;
  width?: number;
  height?: number;
  sizes?: string;
  className?: string;
}

export function GameImage({ src, alt, fill, width, height, sizes, className = "object-contain" }: GameImageProps) {
  const [imgSrc, setImgSrc] = useState(src);
  const [hasError, setHasError] = useState(false);

  const handleError = () => {
    if (!hasError) {
      setHasError(true);
      setImgSrc(FALLBACK_IMAGE);
    }
  };

  if (fill) {
    return <Image src={imgSrc} alt={alt} fill sizes={sizes} className={className} onError={handleError} />;
  }
  return <Image src={imgSrc} alt={alt} width={width} height={height} className={className} onError={handleError} />;
}
```

#### Zod Validation for JSON Data
```tsx
// src/lib/schemas.ts
import { z } from "zod";

// Define schemas matching your TypeScript types
export const ItemSchema = z.object({
  name: z.string(),
  rarity: z.enum(["Common", "Rare", "Epic", "Legendary"]),
  description: z.string().nullable(),
  // ... other fields
});

export const ItemsFileSchema = z.record(z.string(), ItemSchema);

// Validation helper
export function validateData<T>(schema: z.ZodType<T>, data: unknown, name: string): T {
  const result = schema.safeParse(data);
  if (!result.success) {
    console.error(`Validation failed for ${name}:`, result.error.format());
    throw new Error(`Invalid ${name} data`);
  }
  return result.data;
}

// In data.ts - validate at module load (build time for SSG)
import { validateData, ItemsFileSchema } from "./schemas";
import itemsData from "../../data/items.json";

const validatedItems = validateData(ItemsFileSchema, itemsData, "items");
```

#### Generic Filter Components
```tsx
// Avoid `as never` by using generics
interface FilterBarProps<T extends string> {
  options: { value: T; label: string }[];
  selected: T[];
  onChange: (selected: T[]) => void;
}

export function FilterBar<T extends string>({ options, selected, onChange }: FilterBarProps<T>) {
  // Component implementation
}

// Usage with proper typing
const [selectedTags, setSelectedTags] = useState<ItemTag[]>([]);
<FilterBar<ItemTag> options={tagOptions} selected={selectedTags} onChange={setSelectedTags} />
```

#### SEO Metadata Pattern
```tsx
// For client components, split into:
// 1. src/components/pages/items-content.tsx (client component with useState)
// 2. src/app/items/page.tsx (server component with metadata)

// page.tsx
import type { Metadata } from "next";
import { ItemsContent } from "@/components/pages/items-content";

export const metadata: Metadata = {
  title: "Items | My App",
  description: "Browse all items with filtering and search.",
  openGraph: {
    title: "Items | My App",
    description: "Browse all items with filtering and search.",
  },
};

export default function ItemsPage() {
  return <ItemsContent />;
}
```

#### Accessibility Additions
```tsx
// Use semantic HTML
<article aria-label={`${item.name} - ${item.rarity}`}>
  {/* content */}
</article>

// Add aria attributes to interactive elements
<button
  aria-pressed={isSelected}
  aria-label={`Filter by ${option.label}`}
>
  {option.label}
</button>

// Group related controls
<div role="group" aria-label="Filter by rarity">
  {/* filter buttons */}
</div>
```

### Phase 3: Environment & Config

#### .env.example
```bash
# Document all environment variables
# Copy to .env.local and configure

# Required
# DATABASE_URL=postgresql://...

# Optional
# NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX

# Note: This project uses static data, no external APIs required
```

#### Verify tsconfig.json
```json
{
  "compilerOptions": {
    "strict": true,
    // ... other options
  }
}
```

### Phase 4: Verify & Deploy

```bash
# Type check
npx tsc --noEmit

# Build (validates Zod schemas at build time)
npm run build

# Deploy
vercel --prod
```

## Key Patterns Learned

### Pattern: Zod Catches Data Drift
When Zod validation fails during build, it reveals real issues:
- Missing enum values (add to both types.ts and schemas.ts)
- Nullable vs optional fields (use `.nullable()` not `.optional()`)
- Missing required fields

### Pattern: Client/Server Component Split
For pages needing both metadata and interactivity:
1. Keep page.tsx as server component (exports metadata)
2. Move interactive logic to separate client component
3. Import and render client component from page.tsx

### Pattern: Centralized Constants
```tsx
// Single source of truth for ordering
export const RARITY_ORDER = ["Common", "Rare", "Epic", "Legendary"] as const;

// Typed color maps
export const rarityColors: Record<Rarity, string> = {
  Common: "text-gray-500",
  Rare: "text-blue-500",
  // ...
};
```

## Typical Timeline

| Phase | Tasks | Time |
|-------|-------|------|
| Audit | Review all files, document issues | 15-30 min |
| Error Handling | Boundaries, fallbacks, validation | 30-60 min |
| Type Safety | Fix assertions, add Zod | 30-60 min |
| Polish | SEO, accessibility, .env | 20-30 min |
| Deploy | Build, test, deploy | 10-15 min |

**Total: 2-3 hours for a typical MVP**

## Success Criteria

- [ ] Build passes with no warnings
- [ ] Zod validates all JSON data at build time
- [ ] Error boundaries catch and display errors gracefully
- [ ] Missing images show fallback
- [ ] All pages have unique metadata
- [ ] No `as never` or `as any` in codebase
- [ ] TypeScript strict mode enabled
- [ ] .env.example documents all variables

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hermeticormus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
