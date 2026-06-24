---
name: conventions
description: Apply project file/folder naming conventions. Use when creating components, routes, types, server functions, or test files. Use when this capability is needed.
metadata:
  author: seosd97
---

# Project Conventions (Simple FSD)

## Architecture: Simple FSD (3 Layers)

This project follows Feature-Sliced Design with 3 fixed layers + layout (outside layers).

### Layer Hierarchy (top imports bottom, never reverse)

```
pages     → features, shared
features  → shared (cross-feature imports forbidden)
shared    → external packages only
```

**Cross-slice imports within the same layer are forbidden.**

### Layer Descriptions

| Layer | Purpose | Example Slices |
|-------|---------|----------------|
| `pages/` | Route page composition | `home/`, `account/` |
| `features/` | Domain slices (entity UI + user scenarios) | `artist/`, `track/`, `auth/`, `stats/` |
| `shared/` | Business-agnostic reusable code | (segments, not slices) |

### Outside Layers

| Directory | Purpose |
|-----------|---------|
| `layout/` | App-wide layout components (app-shell, sidebar, top-nav) — used by root.tsx |
| `root.tsx`, `routes.ts`, `entry.*.tsx` | App entry points — can import any layer |

### Segments (inside each slice)

| Segment | Role | Examples |
|---------|------|---------|
| `ui/` | React components | `artist-card.tsx` |
| `api/` | Relay fragments, server logic | `artist.fragments.ts` |
| `model/` | Types, Zod schemas, state | `artist.types.ts` |
| `lib/` | Pure helper functions | `format-duration.ts` |
| `config/` | Constants, configuration | `api-endpoints.ts` |

Not all segments are required — create only what's needed.

## Comments Policy

- **Avoid comments** unless absolutely necessary
- Code should be self-documenting through clear naming
- Only add comments for:
  - Complex business logic that isn't obvious
  - Workarounds with links to issues

## File & Folder Naming

| Target | Convention | Example |
|--------|-----------|---------|
| Layer folders | plural, lowercase | `pages/`, `features/` |
| Slice folders | kebab-case | `top-nav/`, `filter-stats/` |
| Segment folders | lowercase | `ui/`, `api/`, `model/` |
| Component files | kebab-case.tsx | `artist-card.tsx` |
| Style files | slice-name.css.ts | `sidebar.css.ts` |
| Type files | slice-name.types.ts | `artist.types.ts` |
| Schema files | slice-name.schema.ts | `artist.schema.ts` |
| Server-only | *.server.ts | `spotify-api.server.ts` |
| Test files | original-name.test.tsx | `artist-card.test.tsx` |
| Public API | index.ts | Required at each slice root |

## Public API (index.ts)

Each slice MUST have an `index.ts` that defines its public API.

```typescript
// features/artist/index.ts
export { ArtistCard } from "./ui/artist-card";
export { ArtistList } from "./ui/artist-list";
```

**Always import from the slice root:**
```typescript
// OK
import { ArtistCard } from "@/features/artist";

// WRONG
import { ArtistCard } from "@/features/artist/ui/artist-card";
```

## Routes (React Router v7)

Routes are configured in `src/routes.ts`. Page files are RR7 route modules with `default export`:

```typescript
// src/routes.ts
import { index, type RouteConfig, route } from "@react-router/dev/routes";

export default [
  index("pages/home/ui/home-page.tsx"),
  route("account", "pages/account/ui/account-page.tsx"),
] satisfies RouteConfig;
```

## Import Order

1. External packages (react, react-router, etc.)
2. Layer imports (@/layout/*, @/features/*, @/shared/*, etc.)
3. Relative imports (./*, ../*)
4. Type imports

```tsx
import { useState } from "react";
import { useLoaderData } from "react-router";

import { Button } from "@/shared/ui/button";
import { ArtistCard } from "@/features/artist";

import * as styles from "../sidebar.css";

import type { SidebarProps } from "./sidebar.types";
```

## Component Template

```tsx
// features/artist/ui/artist-card.tsx
import type { ReactNode } from "react";

import * as styles from "../artist.css";

interface ArtistCardProps {
  children?: ReactNode;
}

export function ArtistCard({ children }: ArtistCardProps) {
  return <div className={styles.card}>{children}</div>;
}
```

## Style Template (co-located)

```typescript
// features/artist/artist.css.ts
import { style } from "@vanilla-extract/css";
import { vars } from "@/shared/styles/theme.css";

export const card = style({
  display: "flex",
  flexDirection: "column",
  backgroundColor: vars.color.bg.surface,
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seosd97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
