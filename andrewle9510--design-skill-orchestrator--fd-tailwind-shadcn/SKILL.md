---
name: fd-tailwind-shadcn
description: Expert skill for Tailwind CSS v4, shadcn/ui implementation, CSS utilities, component customization, and technical design implementation. Use for CSS utilities, component customization, cn() usage, and design-to-code translation. Use when this capability is needed.
metadata:
  author: andrewle9510
---

# Tailwind & shadcn/ui Implementation Expert

Provide expert guidance on Tailwind CSS v4, shadcn/ui configuration, CSS utilities, and translating design decisions into production code.

## Role Definition

You are a **Technical Implementation Expert** — the bridge between design decisions and working code. You translate color systems, typography, spacing, and component designs into Tailwind CSS and shadcn/ui implementations.

## User Context

- **User Profile**: Domain expert (film curation), not a design specialist
- **Product**: Short-form film curation platform for content creators
- **Tech Stack**: Next.js 16+, React 19, Tailwind CSS v4, shadcn/ui (base-lyra style)
- **Package Manager**: Bun

---

## Tailwind CSS v4 Setup

### Project Configuration

```css
/* apps/web/src/app/globals.css */
@import "tailwindcss";

/* Custom theme configuration */
@theme {
  /* Colors */
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  
  /* Custom colors */
  --color-brand-50: oklch(0.97 0.02 250);
  --color-brand-500: oklch(0.55 0.15 250);
  --color-brand-900: oklch(0.25 0.10 250);
  
  /* Typography */
  --font-sans: "Inter", system-ui, sans-serif;
  --font-display: "Playfair Display", Georgia, serif;
  --font-mono: "Geist Mono", monospace;
  
  /* Custom spacing */
  --spacing-18: 4.5rem;
  --spacing-22: 5.5rem;
  
  /* Animations */
  --animate-fade-in: fade-in 0.3s ease-out;
  --animate-slide-up: slide-up 0.3s ease-out;
}

/* CSS Variables for theming */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 240 10% 3.9%;
    --card: 0 0% 100%;
    --card-foreground: 240 10% 3.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 240 10% 3.9%;
    --primary: 240 5.9% 10%;
    --primary-foreground: 0 0% 98%;
    --secondary: 240 4.8% 95.9%;
    --secondary-foreground: 240 5.9% 10%;
    --muted: 240 4.8% 95.9%;
    --muted-foreground: 240 3.8% 46.1%;
    --accent: 240 4.8% 95.9%;
    --accent-foreground: 240 5.9% 10%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 0 0% 98%;
    --border: 240 5.9% 90%;
    --input: 240 5.9% 90%;
    --ring: 240 5.9% 10%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 240 10% 3.9%;
    --foreground: 0 0% 98%;
    --card: 240 10% 3.9%;
    --card-foreground: 0 0% 98%;
    --popover: 240 10% 3.9%;
    --popover-foreground: 0 0% 98%;
    --primary: 0 0% 98%;
    --primary-foreground: 240 5.9% 10%;
    --secondary: 240 3.7% 15.9%;
    --secondary-foreground: 0 0% 98%;
    --muted: 240 3.7% 15.9%;
    --muted-foreground: 240 5% 64.9%;
    --accent: 240 3.7% 15.9%;
    --accent-foreground: 0 0% 98%;
    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 0 0% 98%;
    --border: 240 3.7% 15.9%;
    --input: 240 3.7% 15.9%;
    --ring: 240 4.9% 83.9%;
  }
}

/* Keyframe animations */
@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slide-up {
  from { 
    opacity: 0;
    transform: translateY(10px);
  }
  to { 
    opacity: 1;
    transform: translateY(0);
  }
}
```

### Tailwind CSS v4 vs v3 Key Differences

| v3 | v4 |
|----|-----|
| `tailwind.config.js` | `@theme` in CSS |
| `theme.extend.colors` | `--color-*` in @theme |
| `theme.extend.fontFamily` | `--font-*` in @theme |
| `@apply` (discouraged) | Still works, prefer utilities |
| `darkMode: 'class'` | Automatic with `.dark` class |
| Container queries (plugin) | Native `@container` support |

### Container Queries (v4 Native)

Container queries let components respond to their container size, not viewport:

```css
/* No plugin needed in Tailwind v4 */
.card-wrapper {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .card-layout {
    grid-template-columns: 1fr 1fr;
  }
}
```

```tsx
// Usage in components
<div className="@container">
  <div className="flex flex-col @md:flex-row gap-4">
    <img className="w-full @md:w-1/3" />
    <div className="flex-1">{content}</div>
  </div>
</div>
```

This is especially useful for reusable components like cards that may appear in different container widths.

---

## shadcn/ui Configuration

### Installation

```bash
# Initialize shadcn/ui
bunx shadcn@latest init

# Add components
bunx shadcn@latest add button card dialog input
```

### components.json

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "new-york",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "",
    "css": "src/app/globals.css",
    "baseColor": "zinc",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "lib": "@/lib",
    "hooks": "@/hooks"
  },
  "iconLibrary": "lucide"
}
```

### The cn() Utility

```tsx
// lib/utils.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Usage
import { cn } from "@/lib/utils";

<div className={cn(
  "base-classes here",
  condition && "conditional-class",
  props.className
)} />
```

---

## Common Tailwind Patterns

### Responsive Design

```tsx
// Mobile-first approach
<div className="
  p-4          // All screens
  md:p-6       // 768px+
  lg:p-8       // 1024px+
  xl:p-10      // 1280px+
">

// Responsive grid
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4 md:gap-6">

// Hide/show by breakpoint
<div className="hidden md:block">{desktopOnly}</div>
<div className="md:hidden">{mobileOnly}</div>
```

### Flexbox Layouts

```tsx
// Center content
<div className="flex items-center justify-center min-h-screen">

// Space between
<div className="flex items-center justify-between">

// Stack with gap
<div className="flex flex-col gap-4">

// Responsive direction
<div className="flex flex-col md:flex-row gap-4">
```

### Grid Layouts

```tsx
// Fixed columns
<div className="grid grid-cols-3 gap-6">

// Auto-fit responsive
<div className="grid grid-cols-[repeat(auto-fit,minmax(280px,1fr))] gap-6">

// Sidebar layout
<div className="grid grid-cols-1 lg:grid-cols-[280px_1fr] gap-8">

// Span columns
<div className="col-span-2">
```

### Typography

```tsx
// Headings
<h1 className="text-4xl font-bold tracking-tight">
<h2 className="text-2xl font-semibold tracking-tight">
<h3 className="text-xl font-medium">

// Body text
<p className="text-base leading-relaxed text-muted-foreground">

// Truncation
<p className="truncate">Long text...</p>
<p className="line-clamp-2">Multi-line truncate...</p>
```

### Spacing Patterns

```tsx
// Component spacing
<div className="p-4 md:p-6">        // Padding
<div className="space-y-4">          // Vertical stack
<div className="gap-4">              // Flex/grid gap
<div className="my-8">               // Vertical margin

// Section spacing
<section className="py-12 md:py-16 lg:py-20">
```

### Interactive States

```tsx
// Hover, focus, active
<button className="
  bg-primary
  hover:bg-primary/90
  focus-visible:outline-none
  focus-visible:ring-2
  focus-visible:ring-ring
  focus-visible:ring-offset-2
  active:scale-95
  disabled:opacity-50
  disabled:pointer-events-none
  transition-colors
">

// Group hover
<div className="group">
  <img className="transition-transform group-hover:scale-105" />
  <div className="opacity-0 group-hover:opacity-100 transition-opacity">
    Overlay content
  </div>
</div>
```

### Dark Mode

```tsx
// Automatic with CSS variables (preferred)
<div className="bg-background text-foreground">

// Explicit dark variant (when needed)
<div className="bg-white dark:bg-gray-900">
<div className="text-gray-900 dark:text-gray-100">
```

---

## Class Variance Authority (CVA)

### Creating Variants

```tsx
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva(
  // Base classes
  "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
        outline: "border border-input bg-background hover:bg-accent",
        secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/80",
        ghost: "hover:bg-accent hover:text-accent-foreground",
        link: "text-primary underline-offset-4 hover:underline",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
);

// Component
interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size, className }))}
      {...props}
    />
  );
}
```

### Compound Variants

```tsx
const buttonVariants = cva("...", {
  variants: {
    variant: { ... },
    size: { ... },
  },
  compoundVariants: [
    {
      variant: "outline",
      size: "sm",
      className: "border-2", // Extra border for small outline
    },
  ],
});
```

---

## Custom Utilities

### Extending Tailwind

```css
/* In globals.css */
@theme {
  /* Custom colors */
  --color-film-overlay: oklch(0 0 0 / 0.6);
  
  /* Custom shadows */
  --shadow-card: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  
  /* Custom animations */
  --animate-shimmer: shimmer 2s infinite;
}

@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}

/* Custom utility class */
@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
  
  .aspect-film {
    aspect-ratio: 16 / 9;
  }
}
```

---

## Performance Tips

### React 19 Compiler

React Compiler is enabled in this project. This means:
- `useMemo` and `useCallback` are largely unnecessary for render optimization
- Focus on clean, readable code instead of manual memoization
- The compiler automatically optimizes re-renders

```tsx
// With React Compiler, this is fine without useMemo
function FilmGrid({ films }) {
  const sortedFilms = films.sort((a, b) => b.createdAt - a.createdAt);
  return <Grid items={sortedFilms} />;
}
```

### Minimize Bundle Size

```tsx
// Import only what you need from Lucide
import { Heart, Play, Share } from "lucide-react";

// Don't import all icons
import * as Icons from "lucide-react"; // ❌
```

### Optimize Images

```tsx
import Image from "next/image";

<Image
  src={film.thumbnail}
  alt={film.title}
  width={400}
  height={225}
  className="object-cover"
  priority={isAboveFold}
/>
```

### Avoid @apply (When Possible)

```css
/* Prefer utilities in JSX over @apply */

/* ❌ Avoid */
.btn {
  @apply px-4 py-2 bg-primary text-primary-foreground rounded-md;
}

/* ✅ Prefer - in component */
<button className="px-4 py-2 bg-primary text-primary-foreground rounded-md">
```

---

## Common Patterns Reference

### Aspect Ratios

```tsx
<div className="aspect-video">  // 16:9
<div className="aspect-square"> // 1:1
<div className="aspect-[4/3]">  // Custom
```

### Transitions

```tsx
<div className="transition-all duration-200 ease-out">
<div className="transition-colors duration-150">
<div className="transition-transform duration-300">
```

### Z-Index Scale

```tsx
z-0      // 0
z-10     // 10 - Dropdowns
z-20     // 20 - Sticky elements
z-30     // 30 - Fixed elements
z-40     // 40 - Modals
z-50     // 50 - Toasts/popovers
```

### Container

```tsx
<div className="container mx-auto px-4 md:px-6 lg:px-8">
  {/* Max-width + centered + responsive padding */}
</div>
```

---

## Research Commands

```
read_web_page: https://tailwindcss.com/docs/installation
read_web_page: https://tailwindcss.com/docs/theme
read_web_page: https://ui.shadcn.com/docs/installation
read_web_page: https://ui.shadcn.com/docs/theming
web_search: "tailwind css v4 new features"
web_search: "shadcn ui custom theme"
```

---

## Handoff from Other Experts

| From Expert | What to Implement |
|-------------|-------------------|
| `fd-color-systems` | CSS variables, color tokens |
| `fd-typography` | Font families, sizes, scale |
| `fd-spacing-layout` | Spacing scale, container config |
| `fd-components` | CVA variants, component styles |
| `fd-animations` | Keyframes, transitions |

---

## Key Principles

1. **CSS Variables for Theming** — Use `--color-*` not hard-coded values
2. **Mobile-First** — Start with mobile, enhance with breakpoints
3. **Utility-First** — Compose with utilities, extract when repeated
4. **CVA for Variants** — Organized variant management
5. **Semantic Colors** — `bg-primary` not `bg-blue-500`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewle9510) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
