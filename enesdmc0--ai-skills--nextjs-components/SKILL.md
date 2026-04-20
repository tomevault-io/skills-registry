---
name: nextjs-components
description: This skill should be used when the user asks to \"create a component\", \"build a UI element\", \"add a button\", \"create a form\", \"build a modal\", \"add a card component\", \"create a layout\", or is working with React components in a Next.js project. Use when this capability is needed.
metadata:
  author: enesdmc0
---

# Next.js Component Development Standards

## Overview

This skill provides component development standards for Next.js 15 App Router projects using TypeScript, Tailwind CSS, and shadcn/ui. All components follow a consistent pattern for maintainability and performance.

## Component Creation Rules

### File Location Decision

- Shared UI components: `src/components/shared/ComponentName.tsx`
- Page-specific components: `src/app/(route)/components/ComponentName.tsx`
- shadcn/ui customization: `src/components/ui/` (modify existing)
- Multi-file component: `src/components/ComponentName/index.tsx`

### Component Structure Pattern

Every component MUST follow this exact pattern:

```typescript
import { cn } from "@/lib/utils"

type CardProps = {
  title: string
  description?: string
  children: React.ReactNode
  className?: string
  variant?: "default" | "outline" | "ghost"
}

export const Card = ({
  title,
  description,
  children,
  className,
  variant = "default",
}: CardProps) => {
  return (
    <div
      className={cn(
        "rounded-lg border p-6",
        variant === "default" && "bg-card text-card-foreground shadow-sm",
        variant === "outline" && "border-2 bg-transparent",
        variant === "ghost" && "border-none bg-transparent",
        className
      )}
    >
      <h3 className="text-lg font-semibold">{title}</h3>
      {description && (
        <p className="mt-1 text-sm text-muted-foreground">{description}</p>
      )}
      <div className="mt-4">{children}</div>
    </div>
  )
}
```

### Server vs Client Component Decision

Use Server Component (default - no directive needed):
- Data display and fetching
- Static content rendering
- SEO-critical pages
- Layout components
- Components that only pass data down

Add "use client" only when ANY of these apply:
- Event handlers: onClick, onChange, onSubmit
- React hooks: useState, useEffect, useRef
- Browser APIs: localStorage, window, navigator
- Third-party client libraries: framer-motion animations, chart libraries

### Props Design Rules

1. Always define props as a separate type (not inline)
2. Export the props type for reusability
3. Use optional props with defaults for variants
4. Always include `className?: string` for style extension
5. Use `React.ReactNode` for children, not `JSX.Element`
6. Prefer union types over boolean flags: `variant: "sm" | "md" | "lg"` not `isLarge: boolean`

### Styling Rules

- Use Tailwind CSS exclusively, never inline styles
- Use `cn()` helper from `@/lib/utils` for conditional classes
- Mobile-first responsive: `base` → `sm:` → `md:` → `lg:` → `xl:`
- Use CSS variables for theme colors (defined in globals.css)
- Prefer shadcn/ui components and customize them
- Use Framer Motion for animations (when "use client" is already needed)

### Composition Pattern

Prefer composition over prop drilling:

```typescript
// GOOD - Composition
<Card>
  <Card.Header>
    <Card.Title>Title</Card.Title>
  </Card.Header>
  <Card.Content>Content</Card.Content>
</Card>

// AVOID - Too many props
<Card
  title="Title"
  subtitle="Sub"
  headerIcon={icon}
  footerAction={action}
  // ...20 more props
/>
```

### Anti-Patterns (NEVER DO)

- Never use `any` type for props
- Never use `index` as `key` in lists
- Never use `useEffect` for data fetching (use Server Component or React Query)
- Never use inline styles
- Never use `var` keyword
- Never use default exports for components
- Never put "use client" on Server Components
- Never use barrel exports (index.ts re-exports) - breaks tree-shaking

## Additional Resources

- **references/patterns.md** - Advanced component patterns catalog
- **references/form-patterns.md** - Form handling with react-hook-form + zod
- **examples/** - Complete working component examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enesdmc0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
