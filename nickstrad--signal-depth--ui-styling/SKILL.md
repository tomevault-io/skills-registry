---
name: ui-styling
description: Guides UI styling and design using Tailwind CSS 4, shadcn/ui components, and the global theme system. Use when styling components, designing UIs, or making visual changes. Emphasizes CSS variable-based theming for consistent, maintainable styles. Use when this capability is needed.
metadata:
  author: nickstrad
---

You are a UI styling expert specializing in modern, theme-driven design systems. Your role is to ensure all UI styling leverages the global theme system through CSS variables, creating consistent, maintainable, and automatically theme-aware interfaces.

## Core Principle: Theme-First Design

**CRITICAL**: Every color, spacing, and visual decision should flow from the global theme system defined in `src/app/globals.css`. Changes to the theme file should automatically cascade to ALL components without requiring individual component updates.

### The Golden Rule
❌ **NEVER** use hardcoded colors like `text-gray-500`, `bg-blue-600`, or `border-slate-300`
✅ **ALWAYS** use semantic color tokens like `text-foreground`, `bg-background`, `border-input`

This ensures:
- Automatic dark mode support
- Consistent theming across the entire app
- Single source of truth for design tokens
- Easy theme customization without touching components

## Tech Stack Overview

### Styling Technologies
- **Tailwind CSS 4**: Utility-first CSS framework with CSS variable support
- **shadcn/ui**: Accessible component library built on Radix UI
- **CVA (Class Variance Authority)**: Type-safe variant management for components
- **tailwind-merge + clsx**: Intelligent className composition via `cn()` utility

### Key Files
```
src/
├── app/
│   └── globals.css           # 🎨 Theme definition (CSS variables)
├── components/ui/            # shadcn/ui components (use, don't modify)
└── lib/utils/
    └── css-helpers.ts        # cn() utility for className composition
```

## Theme System Architecture

### Global Theme Definition (globals.css)

```css
@import "tailwindcss";

/* Light theme (default) */
:root {
  --background: #ffffff;
  --foreground: #171717;
}

/* Map CSS variables to Tailwind theme */
@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --font-sans: var(--font-geist-sans);
  --font-mono: var(--font-geist-mono);
}

/* Dark theme (automatic) */
@media (prefers-color-scheme: dark) {
  :root {
    --background: #0a0a0a;
    --foreground: #ededed;
  }
}

body {
  background: var(--background);
  color: var(--foreground);
  font-family: Arial, Helvetica, sans-serif;
}
```

### How It Works

1. **CSS Variables** defined in `:root` (`--background`, `--foreground`, etc.)
2. **@theme inline** block maps variables to Tailwind tokens (`--color-background`)
3. **Tailwind classes** reference these tokens (`bg-background`, `text-foreground`)
4. **Components** use Tailwind classes, automatically inherit theme
5. **Theme changes** in globals.css cascade to all components instantly

## Semantic Color Tokens

Your theme uses semantic color tokens that describe purpose, not appearance:

### Available Color Tokens

```typescript
// Layout & Surfaces
'bg-background'           // Main background color
'bg-card'                 // Card/surface background
'bg-popover'             // Popover/dropdown background
'bg-input'               // Input field background (dark mode variant)

// Text Colors
'text-foreground'        // Primary text color
'text-muted-foreground'  // Secondary/muted text
'text-card-foreground'   // Text on card surfaces
'text-primary-foreground' // Text on primary colored backgrounds

// Interactive Elements
'bg-primary'             // Primary action color
'bg-secondary'           // Secondary action color
'bg-accent'              // Accent/hover state
'bg-destructive'         // Destructive actions (delete, error)

// Borders & Rings
'border-input'           // Input border color
'border-ring'            // Focus ring border
'ring-ring'              // Focus ring color
'ring-destructive'       // Error state ring

// Special States
'hover:bg-accent'        // Hover background
'hover:text-accent-foreground' // Hover text
'focus-visible:ring-ring' // Focus ring
'disabled:opacity-50'    // Disabled state
```

### Extending the Theme

When you need new colors, add them to `globals.css`:

```css
:root {
  --background: #ffffff;
  --foreground: #171717;

  /* Add new semantic tokens */
  --success: #10b981;
  --warning: #f59e0b;
  --info: #3b82f6;
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);

  /* Map to Tailwind */
  --color-success: var(--success);
  --color-warning: var(--warning);
  --color-info: var(--info);
}

/* Dark mode variants */
@media (prefers-color-scheme: dark) {
  :root {
    --background: #0a0a0a;
    --foreground: #ededed;
    --success: #34d399;  /* Lighter variant for dark mode */
    --warning: #fbbf24;
    --info: #60a5fa;
  }
}
```

Now use throughout your app:
```tsx
<div className="bg-success text-white">Success!</div>
<div className="bg-warning text-foreground">Warning!</div>
```

## Component Styling Patterns

### Pattern 1: Using shadcn/ui Components

shadcn/ui components are pre-styled with semantic tokens. Use them as-is:

```tsx
import { Button } from "@/components/ui/button";
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";

export function MyComponent() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Themed Card</CardTitle>
      </CardHeader>
      <CardContent>
        <Input placeholder="Themed input" />
        <Button>Themed button</Button>
      </CardContent>
    </Card>
  );
}
```

✅ **These components automatically inherit your theme** - no additional styling needed!

### Pattern 2: Extending shadcn Components

Use the `cn()` utility to add additional classes:

```tsx
import { Button } from "@/components/ui/button";
import { cn } from "@/lib/utils/css-helpers";

// ✅ Good: Extend with layout classes
<Button className="w-full">Full width button</Button>

// ✅ Good: Add spacing
<Button className="mt-4">Button with margin</Button>

// ❌ Bad: Override colors
<Button className="bg-blue-500 text-white">Don't do this</Button>

// ✅ Good: Use variant prop instead
<Button variant="outline">Outlined button</Button>
```

### Pattern 3: Building Custom Components

Follow the shadcn pattern when building custom components:

```tsx
import { cn } from "@/lib/utils/css-helpers";

interface CustomCardProps extends React.HTMLAttributes<HTMLDivElement> {
  variant?: "default" | "highlighted";
}

export function CustomCard({
  variant = "default",
  className,
  children,
  ...props
}: CustomCardProps) {
  return (
    <div
      className={cn(
        // Base styles using semantic tokens
        "rounded-lg border bg-card text-card-foreground shadow-sm",
        "p-6 transition-colors",

        // Variants using semantic tokens
        variant === "highlighted" && "border-primary bg-accent/50",

        // Allow overrides
        className
      )}
      {...props}
    >
      {children}
    </div>
  );
}
```

### Pattern 4: Complex Components with CVA

For components with many variants, use class-variance-authority:

```tsx
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils/css-helpers";

const badgeVariants = cva(
  // Base styles (always applied)
  "inline-flex items-center rounded-full px-2.5 py-0.5 text-xs font-semibold transition-colors",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        secondary: "bg-secondary text-secondary-foreground",
        destructive: "bg-destructive text-white",
        outline: "border border-input bg-background",
        success: "bg-success text-white",
      },
      size: {
        sm: "px-2 py-0.5 text-xs",
        md: "px-2.5 py-0.5 text-xs",
        lg: "px-3 py-1 text-sm",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "md",
    },
  }
);

interface BadgeProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof badgeVariants> {}

export function Badge({ className, variant, size, ...props }: BadgeProps) {
  return (
    <div
      className={cn(badgeVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

## Common UI Patterns

### Layout Containers

```tsx
// ✅ Good: Semantic colors, responsive padding
<div className="container mx-auto px-4 py-8">
  <div className="max-w-4xl mx-auto">
    {/* Content */}
  </div>
</div>

// ✅ Good: Card grid with gap
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map(item => <Card key={item.id}>...</Card>)}
</div>
```

### Text Hierarchy

```tsx
// ✅ Good: Using semantic text colors
<div>
  <h1 className="text-3xl font-bold text-foreground">Main Title</h1>
  <p className="text-muted-foreground mt-2">Subtitle or description</p>
  <p className="text-sm text-muted-foreground">Small helper text</p>
</div>

// ❌ Bad: Hardcoded colors
<p className="text-gray-500">Don't use gray-500</p>
<p className="text-slate-600">Don't use slate-600</p>
```

### Interactive States

```tsx
// ✅ Good: Theme-aware hover and focus states
<button
  className={cn(
    "rounded-md px-4 py-2",
    "bg-primary text-primary-foreground",
    "hover:bg-primary/90",
    "focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2",
    "disabled:opacity-50 disabled:pointer-events-none",
    "transition-colors"
  )}
>
  Click me
</button>

// ✅ Good: Hover effects on cards
<div
  className={cn(
    "border rounded-lg p-4 bg-card",
    "hover:bg-accent hover:border-accent-foreground/20",
    "transition-colors cursor-pointer"
  )}
>
  Hoverable card
</div>
```

### Forms

```tsx
// ✅ Good: Using shadcn form components
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";

<div className="space-y-2">
  <Label htmlFor="email">Email</Label>
  <Input
    id="email"
    type="email"
    placeholder="you@example.com"
    className="w-full"
  />
  <p className="text-sm text-muted-foreground">We'll never share your email.</p>
</div>
```

### Loading States

```tsx
// ✅ Good: Skeleton using theme colors
<div className="animate-pulse space-y-4">
  <div className="h-4 bg-muted rounded w-3/4" />
  <div className="h-4 bg-muted rounded w-1/2" />
  <div className="h-4 bg-muted rounded w-5/6" />
</div>

// ✅ Good: Spinner using theme colors
<div className="animate-spin rounded-full h-8 w-8 border-2 border-primary border-t-transparent" />
```

### Empty States

```tsx
// ✅ Good: Semantic colors for empty state
<div className="flex flex-col items-center justify-center py-12 text-center">
  <div className="rounded-full bg-muted p-4 mb-4">
    <IconComponent className="size-8 text-muted-foreground" />
  </div>
  <h3 className="text-lg font-semibold text-foreground">No items found</h3>
  <p className="text-sm text-muted-foreground mt-2 max-w-sm">
    Get started by creating your first item.
  </p>
  <Button className="mt-4">Create Item</Button>
</div>
```

## The cn() Utility

The `cn()` function combines `clsx` and `tailwind-merge` for intelligent className composition:

```typescript
// src/lib/utils/css-helpers.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

### How to Use cn()

```tsx
import { cn } from "@/lib/utils/css-helpers";

// Merge multiple classes
cn("text-base", "font-bold", "text-foreground")
// → "text-base font-bold text-foreground"

// Conditional classes
cn("base-class", condition && "conditional-class")
// → "base-class conditional-class" (if condition is true)

// Object syntax
cn("base", { "text-destructive": hasError, "text-foreground": !hasError })

// Override conflicting classes (tailwind-merge magic)
cn("text-sm", "text-lg") // → "text-lg" (last one wins)
cn("p-4", "px-6")         // → "p-4 px-6" (doesn't conflict)

// Real example
<div
  className={cn(
    "rounded-lg border p-4",
    "bg-card text-card-foreground",
    isActive && "border-primary bg-primary/10",
    disabled && "opacity-50 pointer-events-none",
    className // Allow prop-based overrides
  )}
>
```

## Dark Mode Handling

Your app uses automatic dark mode via `prefers-color-scheme`:

### Current Setup
```css
/* globals.css */
@media (prefers-color-scheme: dark) {
  :root {
    --background: #0a0a0a;
    --foreground: #ededed;
  }
}
```

### Dark Mode Variants in Components

```tsx
// ✅ Automatic: Most colors adapt automatically
<div className="bg-background text-foreground">
  Automatically adapts to dark mode!
</div>

// ✅ Dark-specific overrides when needed
<div className="bg-input/30 dark:bg-input/50">
  Different opacity in dark mode
</div>

// ✅ Dark mode hover states
<button className="hover:bg-accent dark:hover:bg-accent/50">
  Different hover in dark mode
</button>
```

### Adding Manual Dark Mode Toggle (Future Enhancement)

To add a manual dark mode toggle using `next-themes`:

```tsx
// app/layout.tsx
import { ThemeProvider } from "next-themes";

export default function RootLayout({ children }) {
  return (
    <html lang="en" suppressHydrationWarning>
      <body>
        <ThemeProvider attribute="class" defaultTheme="system" enableSystem>
          {children}
        </ThemeProvider>
      </body>
    </html>
  );
}

// components/theme-toggle.tsx
"use client";

import { useTheme } from "next-themes";
import { Button } from "@/components/ui/button";

export function ThemeToggle() {
  const { theme, setTheme } = useTheme();

  return (
    <Button
      variant="ghost"
      onClick={() => setTheme(theme === "dark" ? "light" : "dark")}
    >
      Toggle theme
    </Button>
  );
}
```

## shadcn/ui Component Usage

### Available Components

Your app includes these shadcn components (in `src/components/ui/`):

- **Forms**: Button, Input, Textarea, Select, Checkbox, Radio, Switch, Label, Form
- **Layout**: Card, Separator, Tabs, Accordion, Collapsible
- **Navigation**: Breadcrumb, NavigationMenu, Menubar, Dropdown, Command
- **Feedback**: Alert, AlertDialog, Dialog, Drawer, Toast (Sonner), Progress, Spinner
- **Data**: Table, Badge, Avatar, Calendar, Carousel, Chart
- **Overlay**: Popover, Tooltip, HoverCard, ContextMenu, Sheet, Sidebar

### Component Variant Patterns

Most shadcn components support variants:

```tsx
// Button variants
<Button variant="default">Default</Button>
<Button variant="destructive">Destructive</Button>
<Button variant="outline">Outline</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>

// Button sizes
<Button size="default">Default</Button>
<Button size="sm">Small</Button>
<Button size="lg">Large</Button>
<Button size="icon">Icon only</Button>

// Combining variants
<Button variant="outline" size="lg">Large Outline</Button>
```

### When to Create Custom Components

**Use shadcn components** when:
- The component matches your need (90% of cases)
- You need accessibility built-in
- You want automatic theme integration

**Create custom components** when:
- You need a truly unique UI pattern
- The pattern will be reused across the app
- You need specific business logic integration

Even then, **compose from shadcn primitives**:

```tsx
// ✅ Good: Custom component built on shadcn primitives
import { Card, CardHeader, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

export function ProductCard({ product }) {
  return (
    <Card className="hover:shadow-lg transition-shadow">
      <CardHeader>
        <img
          src={product.image}
          alt={product.name}
          className="rounded-t-lg"
        />
      </CardHeader>
      <CardContent>
        <h3 className="font-semibold text-foreground">{product.name}</h3>
        <p className="text-sm text-muted-foreground">{product.description}</p>
        <Button className="w-full mt-4">Add to Cart</Button>
      </CardContent>
    </Card>
  );
}
```

## Common Mistakes & Solutions

### ❌ Mistake 1: Hardcoding Colors

```tsx
// ❌ Bad: Hardcoded gray colors
<p className="text-gray-600">Secondary text</p>
<div className="bg-gray-100 border-gray-300">Container</div>

// ✅ Good: Semantic tokens
<p className="text-muted-foreground">Secondary text</p>
<div className="bg-card border-input">Container</div>
```

### ❌ Mistake 2: Inconsistent Spacing

```tsx
// ❌ Bad: Arbitrary spacing values
<div className="p-[13px] mt-[27px]">Content</div>

// ✅ Good: Tailwind spacing scale
<div className="p-4 mt-6">Content</div>
```

### ❌ Mistake 3: Not Using the cn() Utility

```tsx
// ❌ Bad: String concatenation
<div className={`base-class ${isActive ? 'active-class' : ''}`}>

// ✅ Good: Using cn()
<div className={cn("base-class", isActive && "active-class")}>
```

### ❌ Mistake 4: Overriding shadcn Component Styles

```tsx
// ❌ Bad: Fighting shadcn styles
<Button className="bg-blue-500 text-white hover:bg-blue-600">
  Overridden button
</Button>

// ✅ Good: Use variants or extend the theme
<Button variant="default">Themed button</Button>

// Or add new variant to globals.css:
:root {
  --custom-brand: #3b82f6;
}
<Button className="bg-custom-brand">Custom brand button</Button>
```

### ❌ Mistake 5: Creating Unnecessary Wrapper Components

```tsx
// ❌ Bad: Unnecessary wrapper
export function MyButton({ children }) {
  return <Button>{children}</Button>; // Does nothing!
}

// ✅ Good: Just use Button directly
import { Button } from "@/components/ui/button";
<Button>Click me</Button>

// ✅ Good: Wrapper adds value
export function SubmitButton({ isLoading, children }) {
  return (
    <Button type="submit" disabled={isLoading}>
      {isLoading ? "Loading..." : children}
    </Button>
  );
}
```

## Responsive Design Patterns

Use Tailwind's responsive prefixes with semantic tokens:

```tsx
// ✅ Good: Responsive layout
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {items.map(item => <Card key={item.id}>...</Card>)}
</div>

// ✅ Good: Responsive typography
<h1 className="text-2xl md:text-3xl lg:text-4xl font-bold text-foreground">
  Responsive heading
</h1>

// ✅ Good: Responsive padding
<div className="px-4 md:px-6 lg:px-8 py-6 md:py-8">
  Responsive container
</div>

// ✅ Good: Hide/show on different screens
<div className="hidden md:block">
  Visible on medium screens and up
</div>
```

## Accessibility Considerations

shadcn components have accessibility built-in, but keep these in mind:

```tsx
// ✅ Good: Proper focus states (already in shadcn)
<button className="focus-visible:ring-2 focus-visible:ring-ring">
  Accessible button
</button>

// ✅ Good: ARIA attributes
<button aria-label="Close dialog" aria-pressed={isPressed}>
  <CloseIcon />
</button>

// ✅ Good: Proper semantic HTML
<nav>
  <ul>
    <li><a href="/">Home</a></li>
  </ul>
</nav>

// ✅ Good: Color contrast with semantic tokens
// (semantic tokens ensure proper contrast in both themes)
<div className="bg-background text-foreground">
  High contrast text
</div>
```

## Testing Your Theme Changes

When modifying `globals.css`:

1. **Test in both themes**
   - Check light mode (default)
   - Check dark mode (system preference or toggle)

2. **Test color changes cascade**
   - Modify a CSS variable in globals.css
   - Verify all components using that token update automatically

3. **Verify components still work**
   - All shadcn components still look correct
   - No broken layouts or invisible text
   - Focus states are visible

4. **Check contrast**
   - Text is readable in both themes
   - Hover states are noticeable
   - Disabled states are clear

## Style Organization Checklist

When implementing UI:

- [ ] Use semantic color tokens from the theme (no hardcoded colors)
- [ ] Use shadcn/ui components when possible
- [ ] Use `cn()` utility for className composition
- [ ] Add responsive variants (sm:, md:, lg:)
- [ ] Include interactive states (hover:, focus-visible:, disabled:)
- [ ] Test in both light and dark modes
- [ ] Verify accessibility (focus states, ARIA attributes)
- [ ] Use Tailwind spacing scale (not arbitrary values)
- [ ] Keep components composable and reusable
- [ ] Document any new theme tokens in globals.css

---

**Remember**: Your goal is to create a cohesive, maintainable design system where visual changes flow from the theme definition, not individual components. Every UI element should feel part of a unified whole, adapting seamlessly to theme changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickstrad) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
