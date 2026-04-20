---
name: create-component
description: Create a new React component following project conventions. Use when creating components, UI elements, or reusable modules. Use when this capability is needed.
metadata:
  author: seosd97
---

# Create Component (Simple FSD)

## Step 1: Determine the Layer

| If the component... | Layer |
|---------------------|-------|
| Is business-agnostic (Button, Card, Input) | `shared/ui/` |
| Belongs to a business domain (ArtistCard, TrackRow, LoginForm) | `features/{feature}/ui/` |
| Composes features into a full page | `pages/{page}/ui/` |
| Is app-wide layout (shell, nav, sidebar) | `layout/{slice}/ui/` |

## Step 2: Create the Slice Structure

### shared/ui component

```
src/shared/ui/{component-name}/
├── index.tsx                    # Component + export
├── {component-name}.css.ts      # Styles (recipe for variants)
└── {component-name}.test.tsx    # Tests (optional)
```

### Feature component

```
src/features/{slice-name}/
├── ui/
│   ├── {component-name}.tsx
│   └── {component-name}.test.tsx  # Tests (optional)
├── {slice-name}.css.ts            # Styles for all UI in this slice
└── index.ts                       # Public API
```

## Step 3: Write the Component

### shared/ui (with Recipe variants)

```tsx
// shared/ui/button/index.tsx
import clsx from "clsx";
import type { ButtonHTMLAttributes } from "react";

import { buttonStyle, type ButtonVariants } from "./button.css";

interface ButtonProps
  extends ButtonHTMLAttributes<HTMLButtonElement>,
    ButtonVariants {
  children: React.ReactNode;
}

export function Button({
  variant,
  size,
  className,
  children,
  ...props
}: ButtonProps) {
  return (
    <button
      className={clsx(buttonStyle({ variant, size }), className)}
      {...props}
    >
      {children}
    </button>
  );
}
```

```typescript
// shared/ui/button/button.css.ts
import { recipe, type RecipeVariants } from "@vanilla-extract/recipes";
import { vars } from "@/shared/styles/theme.css";

export const buttonStyle = recipe({
  base: {
    display: "inline-flex",
    alignItems: "center",
    justifyContent: "center",
    borderRadius: vars.borderRadius.lg,
    fontWeight: vars.fontWeight.medium,
    transition: vars.transition.colors,
    cursor: "pointer",
  },
  variants: {
    variant: {
      primary: {
        backgroundColor: vars.color.action.primary.bg,
        color: vars.color.action.primary.text,
      },
      secondary: {
        backgroundColor: vars.color.action.secondary.bg,
        color: vars.color.action.secondary.text,
        border: `1px solid ${vars.color.action.secondary.border}`,
      },
      ghost: {
        backgroundColor: vars.color.action.ghost.bg,
        color: vars.color.action.ghost.text,
      },
    },
    size: {
      sm: { height: "32px", padding: `0 ${vars.space[3]}`, fontSize: vars.fontSize.sm },
      md: { height: "40px", padding: `0 ${vars.space[4]}`, fontSize: vars.fontSize.base },
      lg: { height: "48px", padding: `0 ${vars.space[6]}`, fontSize: vars.fontSize.lg },
    },
  },
  defaultVariants: {
    variant: "secondary",
    size: "md",
  },
});

export type ButtonVariants = RecipeVariants<typeof buttonStyle>;
```

### Feature component

```tsx
// features/artist/ui/artist-card.tsx
import { graphql, useFragment } from "react-relay";

import * as styles from "../artist.css";

import type { ArtistCard_artist$key } from "./__generated__/ArtistCard_artist.graphql";

const ArtistCardFragment = graphql`
  fragment ArtistCard_artist on Artist {
    id
    name
    imageUrl
  }
`;

export function ArtistCard({ artist }: { artist: ArtistCard_artist$key }) {
  const data = useFragment(ArtistCardFragment, artist);
  return <div className={styles.card}>{data.name}</div>;
}
```

```typescript
// features/artist/index.ts (Public API)
export { ArtistCard } from "./ui/artist-card";
```

## Naming Rules

| Type | Convention | Example |
|------|------------|---------|
| Slice folder | kebab-case | `top-nav/`, `artist-card/` |
| Component name | PascalCase | `TopNav`, `ArtistCard` |
| Component file | kebab-case.tsx | `top-nav.tsx`, `artist-card.tsx` |
| Style file | slice-name.css.ts | `top-nav.css.ts` |
| Props | PascalCase + Props | `TopNavProps` |

## Import Rules

- Always import from slice root `index.ts`
- Never import from internal segments directly
- Respect layer hierarchy (upper layers import lower layers only)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seosd97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
