---
name: styling-guidelines
description: Tailwind CSS v4 styling guidelines - use @theme blocks for configuration, hsl(var(--color-x)) for colors, never hardcode values, use responsive utilities Use when this capability is needed.
metadata:
  author: jewelshovan
---

# Tailwind CSS v4 Styling Guidelines

This skill ensures consistent styling practices using Tailwind CSS v4 with CSS variables and modern design patterns.

## Tailwind CSS v4 Setup

### CSS Configuration

**Use `@import` instead of `@tailwind` directives:**

```css
/* src/index.css */
@import "tailwindcss";

@theme {
  /* Your theme configuration here */
}
```

### Vite Configuration

**Install and use the Vite plugin:**

```bash
npm install -D @tailwindcss/vite
```

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

### Key Differences from v3

- No `tailwind.config.js` needed - use `@theme` blocks in CSS
- Automatic content detection (no content array configuration)
- Use `@import "tailwindcss"` instead of `@tailwind` directives
- Built-in Vite plugin: `@tailwindcss/vite`

## Theme System

### Defining Theme Variables

All theme variables are defined in the `@theme` block in `src/index.css`:

```css
@theme {
  /* Border radius */
  --radius: 0.5rem;

  /* Colors - using HSL format for v4 */
  --color-background: 0 0% 100%;
  --color-foreground: 0 0% 3.9%;
  --color-primary: 0 0% 9%;
  --color-primary-foreground: 0 0% 98%;
  --color-secondary: 0 0% 96.1%;
  --color-secondary-foreground: 0 0% 9%;
  --color-destructive: 0 84.2% 60.2%;
  --color-border: 0 0% 89.8%;
  --color-ring: 0 0% 3.9%;
}
```

### HSL Color Format

Colors MUST be defined in HSL format without the `hsl()` wrapper:

```css
/* CORRECT */
--color-primary: 221 83% 53%;
--color-accent: 142 76% 36%;

/* INCORRECT */
--color-primary: hsl(221, 83%, 53%);
--color-primary: #3B82F6;
```

### Using Theme Colors

Use the `hsl(var(--color-name))` pattern with Tailwind utilities:

```tsx
/* CORRECT - Using CSS variables */
<div className="bg-[hsl(var(--color-primary))]" />
<div className="text-[hsl(var(--color-foreground))]" />
<div className="border-[hsl(var(--color-border))]" />

/* CORRECT - With opacity */
<div className="bg-[hsl(var(--color-primary)/0.5)]" />
<div className="bg-[hsl(var(--color-accent)/0.2)]" />

/* INCORRECT - Hardcoded colors */
<div className="bg-blue-500" />
<div className="text-[#3B82F6]" />
<div className="border-gray-200" />
```

### Border Radius Tokens

Use semantic radius tokens defined in the theme:

```css
@theme {
  --radius: 0.5rem;
  /* Additional radius tokens can reference the base */
  --radius-lg: calc(var(--radius) * 1.5);
  --radius-sm: calc(var(--radius) * 0.5);
}
```

```tsx
/* CORRECT - Using Tailwind utilities that reference --radius */
<div className="rounded-lg" />  /* Uses --radius-lg */
<div className="rounded-md" />  /* Uses --radius */
<div className="rounded-sm" />  /* Uses --radius-sm */

/* INCORRECT - Hardcoded values */
<div className="rounded-[8px]" />
<div className="rounded-[12px]" />
```

## Never Hardcode Values

### Color Hardcoding (DO NOT DO)

```tsx
/* ❌ WRONG - Hardcoded Tailwind colors */
<button className="bg-blue-500 hover:bg-blue-600">Click me</button>
<div className="text-gray-900 border-gray-200">Content</div>

/* ❌ WRONG - Hardcoded hex values */
<div className="bg-[#3B82F6] text-[#1F2937]">Content</div>

/* ✅ CORRECT - Use theme variables */
<button className="bg-[hsl(var(--color-primary))] hover:bg-[hsl(var(--color-primary)/0.9)]">
  Click me
</button>
<div className="text-[hsl(var(--color-foreground))] border-[hsl(var(--color-border))]">
  Content
</div>
```

### Spacing Hardcoding (DO NOT DO)

```tsx
/* ❌ WRONG - Arbitrary pixel values */
<div className="p-[24px] m-[16px] gap-[12px]">Content</div>

/* ✅ CORRECT - Use Tailwind spacing scale */
<div className="p-6 m-4 gap-3">Content</div>
```

### Border Radius Hardcoding (DO NOT DO)

```tsx
/* ❌ WRONG - Arbitrary radius values */
<div className="rounded-[8px]">Content</div>
<div className="rounded-[12px]">Content</div>

/* ✅ CORRECT - Use semantic tokens */
<div className="rounded-lg">Content</div>
<div className="rounded-xl">Content</div>
```

## When to Use Tailwind Utilities vs CSS Variables

### Use Tailwind Utilities For:

- **Spacing**: `p-4`, `m-6`, `gap-8`, `space-y-4`
- **Responsive breakpoints**: `md:grid-cols-2`, `lg:flex-row`
- **Flex/Grid layouts**: `flex`, `grid`, `justify-between`, `items-center`
- **Typography**: `text-xl`, `font-semibold`, `leading-relaxed`
- **Standard utilities**: `rounded-lg`, `shadow-lg`, `transition-all`

### Use CSS Variables For:

- **Theme colors**: All colors should use CSS variables
- **Custom radii**: Brand-specific border radius values
- **Brand-specific values**: Any value that might change with themes

### Combine Both:

Use utility prefixes with variable values:

```tsx
/* Color utilities with variables */
<div className="bg-[hsl(var(--color-primary))]" />
<div className="text-[hsl(var(--color-foreground))]" />
<div className="border-[hsl(var(--color-border))]" />

/* With opacity modifiers */
<div className="bg-[hsl(var(--color-primary)/0.5)]" />
<div className="shadow-[0_0_20px_hsl(var(--color-primary)/0.3)]" />
```

## Responsive Design Patterns

### Mobile-First Approach

Always start with base (mobile) styles, then add responsive modifiers:

```tsx
/* CORRECT - Mobile first */
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
  {/* Grid items */}
</div>

<div className="flex flex-col md:flex-row gap-4 md:gap-6 lg:gap-8">
  {/* Flex items */}
</div>

/* INCORRECT - Desktop first (avoid) */
<div className="grid grid-cols-3 md:grid-cols-2 grid-cols-1">
  {/* This is backwards */}
</div>
```

### Breakpoint Reference

Tailwind CSS v4 uses the same default breakpoints as v3:

| Breakpoint | Min Width | Example                     |
| ---------- | --------- | --------------------------- |
| `sm:`      | 640px     | `sm:text-lg`                |
| `md:`      | 768px     | `md:grid-cols-2`            |
| `lg:`      | 1024px    | `lg:grid-cols-3`            |
| `xl:`      | 1280px    | `xl:max-w-7xl`              |
| `2xl:`     | 1536px    | `2xl:px-0`                  |

### Real Example from This Project

From `/Users/julien.hovan/Desktop/Internals/Vite-React-Typescript-Boiler/src/pages/Home.tsx`:

```tsx
{/* Responsive grid - 1 column mobile, 2 tablet, 3 desktop */}
<div className="grid gap-8 md:grid-cols-2 lg:grid-cols-3">
  {features.map((feature) => (
    <Card key={feature.title}>
      {/* Card content */}
    </Card>
  ))}
</div>

{/* Responsive text sizing */}
<h1 className="text-6xl sm:text-7xl md:text-8xl">
  {APP_NAME}
</h1>

{/* Responsive spacing */}
<div className="mx-auto max-w-4xl">
  <p className="text-xl sm:text-2xl">
    Subtitle text
  </p>
</div>
```

## Gradient Patterns

### Linear Gradients

```tsx
/* Basic gradient */
<div className="bg-gradient-to-r from-blue-500 to-purple-500">
  Content
</div>

/* With direction variants */
<div className="bg-gradient-to-br from-blue-500 to-purple-500">  {/* bottom-right */}
<div className="bg-gradient-to-tr from-blue-500 to-purple-500">  {/* top-right */}
<div className="bg-gradient-to-bl from-blue-500 to-purple-500">  {/* bottom-left */}
```

### Gradients with Custom Colors

Use CSS variables in gradients:

```tsx
/* CORRECT - Using theme variables */
<div className="bg-gradient-to-br from-[hsl(var(--color-primary))] to-[hsl(var(--color-secondary))]">
  Content
</div>

/* With via color */
<div className="bg-gradient-to-r from-[hsl(var(--color-primary))] via-[hsl(var(--color-accent))] to-[hsl(var(--color-secondary))]">
  Content
</div>
```

### Gradients with Opacity

```tsx
/* With opacity modifiers */
<div className="bg-gradient-to-br from-blue-400/20 via-purple-400/20 to-pink-400/20">
  Content
</div>

/* With blur for background effects */
<div className="h-[600px] w-[600px] rounded-full bg-gradient-to-br from-blue-400/20 via-purple-400/20 to-pink-400/20 blur-3xl">
  {/* Blurred gradient blob */}
</div>
```

### Real Examples from This Project

From `/Users/julien.hovan/Desktop/Internals/Vite-React-Typescript-Boiler/src/pages/Home.tsx`:

```tsx
{/* Gradient background blobs */}
<div className="absolute left-1/2 top-0 -translate-x-1/2 -translate-y-1/2">
  <div className="h-[600px] w-[600px] rounded-full bg-gradient-to-br from-blue-400/20 via-purple-400/20 to-pink-400/20 blur-3xl" />
</div>

{/* Text gradient */}
<h1 className="bg-gradient-to-r from-gray-900 via-blue-900 to-purple-900 bg-clip-text text-transparent">
  {APP_NAME}
</h1>

{/* Icon gradient background */}
<div className="rounded-full bg-gradient-to-br from-blue-500 to-purple-600 p-3 shadow-lg shadow-blue-500/50">
  <Sparkles className="h-8 w-8 text-white" />
</div>

{/* Card hover effect with dynamic gradient */}
<div className={`bg-gradient-to-br ${feature.gradient} opacity-0 group-hover:opacity-100`} />
```

## Spacing Conventions

### Tailwind Spacing Scale

Tailwind uses a consistent 4px base unit:

| Class  | Value  | Use Case               |
| ------ | ------ | ---------------------- |
| `p-1`  | 4px    | Tiny spacing           |
| `p-2`  | 8px    | Small spacing          |
| `p-3`  | 12px   | Compact spacing        |
| `p-4`  | 16px   | Standard spacing       |
| `p-6`  | 24px   | Medium spacing         |
| `p-8`  | 32px   | Large spacing          |
| `p-12` | 48px   | Extra large spacing    |
| `p-16` | 64px   | Section spacing        |
| `p-32` | 128px  | Hero section spacing   |

### Consistent Spacing Patterns

```tsx
/* Vertical stacks - use space-y */
<div className="space-y-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>

/* Grid gaps - use gap */
<div className="grid grid-cols-3 gap-6">
  <div>Card 1</div>
  <div>Card 2</div>
  <div>Card 3</div>
</div>

/* Flex gaps - use gap */
<div className="flex gap-4">
  <button>Button 1</button>
  <button>Button 2</button>
</div>
```

### Real Examples from This Project

From `/Users/julien.hovan/Desktop/Internals/Vite-React-Typescript-Boiler/src/pages/Home.tsx`:

```tsx
{/* Large section spacing */}
<div className="space-y-32">
  {/* Hero Section */}
  <div className="relative overflow-hidden">
    <div className="px-6 py-32 text-center">
      {/* Content */}
    </div>
  </div>

  {/* Features Grid with consistent gap */}
  <div className="grid gap-8 md:grid-cols-2 lg:grid-cols-3">
    {/* Cards */}
  </div>
</div>

{/* Card header spacing */}
<CardHeader className="space-y-4">
  {/* Icon and badge */}
  <div className="flex items-start justify-between">
    {/* Content */}
  </div>
  {/* Title and description */}
</CardHeader>
```

## Base Layer Styles

Apply theme variables to base elements using the `@layer base` directive:

```css
@layer base {
  * {
    border-color: hsl(var(--color-border));
  }
  body {
    background-color: hsl(var(--color-background));
    color: hsl(var(--color-foreground));
  }
}
```

This ensures:
- Default border colors use your theme
- Body background and text use theme variables
- Consistent styling across the entire app

## Quick Reference: Common Patterns

### ✅ DO

```tsx
/* Use CSS variables for colors */
<div className="bg-[hsl(var(--color-primary))]" />

/* Use Tailwind spacing scale */
<div className="p-6 m-4 gap-8" />

/* Use semantic border radius */
<div className="rounded-lg" />

/* Mobile-first responsive */
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3" />

/* Gradients with opacity */
<div className="bg-gradient-to-br from-blue-400/20 via-purple-400/20 to-pink-400/20" />

/* Combine utilities with variables */
<div className="text-[hsl(var(--color-foreground))] hover:text-[hsl(var(--color-primary))]" />
```

### ❌ DON'T

```tsx
/* Don't hardcode Tailwind colors */
<div className="bg-blue-500" />

/* Don't use arbitrary pixel values */
<div className="p-[24px] m-[16px]" />

/* Don't hardcode border radius */
<div className="rounded-[8px]" />

/* Don't use hex colors */
<div className="bg-[#3B82F6]" />

/* Don't define gradients without opacity when used as overlays */
<div className="bg-gradient-to-br from-blue-400 via-purple-400 to-pink-400" />
```

## Project-Specific Examples

### Navigation (from MainLayout.tsx)

```tsx
<header className="sticky top-0 z-50 border-b border-gray-200 bg-white/95 backdrop-blur supports-[backdrop-filter]:bg-white/60">
  <div className="mx-auto max-w-7xl px-8 py-4">
    <nav className="flex items-center justify-end">
      <div className="flex items-center gap-8">
        <Link className="relative px-4 py-2 text-base font-medium transition-colors hover:text-blue-600">
          Home
        </Link>
      </div>
    </nav>
  </div>
</header>
```

### Hero Section Pattern

```tsx
<div className="relative overflow-hidden">
  {/* Background gradient blobs */}
  <div className="absolute inset-0 -z-10">
    <div className="absolute left-1/2 top-0 -translate-x-1/2 -translate-y-1/2">
      <div className="h-[600px] w-[600px] rounded-full bg-gradient-to-br from-blue-400/20 via-purple-400/20 to-pink-400/20 blur-3xl" />
    </div>
  </div>

  <div className="relative px-6 py-32 text-center">
    {/* Content */}
  </div>
</div>
```

### Interactive Card Pattern

```tsx
<Card className="group relative overflow-hidden border-2 transition-all duration-300 hover:-translate-y-2 hover:border-transparent hover:shadow-2xl">
  {/* Gradient border effect on hover */}
  <div className={`absolute inset-0 -z-10 bg-gradient-to-br ${feature.gradient} opacity-0 transition-opacity duration-300 group-hover:opacity-100`} />
  <div className="absolute inset-[2px] -z-10 rounded-lg bg-white" />

  <CardHeader className="space-y-4">
    {/* Content */}
  </CardHeader>
</Card>
```

## Verification Checklist

Before committing styled components, verify:

- [ ] No hardcoded Tailwind color classes (e.g., `bg-blue-500`)
- [ ] No hardcoded hex/rgb colors (e.g., `bg-[#3B82F6]`)
- [ ] All theme colors use `hsl(var(--color-name))` pattern
- [ ] Spacing uses Tailwind scale (not arbitrary values like `p-[24px]`)
- [ ] Border radius uses semantic tokens (not `rounded-[8px]`)
- [ ] Responsive design is mobile-first
- [ ] Gradients use consistent opacity patterns for overlays
- [ ] All custom colors are defined in `@theme` block

## Additional Resources

- **Tailwind CSS v4 Docs**: https://tailwindcss.com/docs/v4-beta
- **Project Theme File**: `/Users/julien.hovan/Desktop/Internals/Vite-React-Typescript-Boiler/src/index.css`
- **Vite Config**: `/Users/julien.hovan/Desktop/Internals/Vite-React-Typescript-Boiler/vite.config.ts`
- **Example Components**: Check `src/pages/Home.tsx` and `src/layouts/MainLayout.tsx` for reference implementations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jewelshovan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
