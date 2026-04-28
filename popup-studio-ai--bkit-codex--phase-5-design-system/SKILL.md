---
name: phase-5-design-system
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 5: Design System

> Build a reusable component library with consistent design tokens and theming.

## Purpose

Phase 5 transforms Phase 3 mockups into a production-ready component system. A well-defined design system ensures visual consistency, accelerates development, and reduces design debt. Every component is built once, documented, and reused across the entire application.

## Actions

| Action | Description | Example |
|--------|-------------|---------|
| `start` | Begin Phase 5 | `$phase-5-design-system start` |
| `tokens` | Define design tokens | `$phase-5-design-system tokens` |
| `components` | Build component library | `$phase-5-design-system components` |
| `theme` | Set up theming (light/dark) | `$phase-5-design-system theme` |
| `docs` | Generate component docs | `$phase-5-design-system docs` |

## Deliverables

1. **Design Tokens File** - Colors, spacing, typography, shadows, border radii
2. **Atomic Components** - Button, Input, Badge, Avatar, Icon, Spinner
3. **Molecular Components** - FormField, Card, Alert, Tooltip, Dropdown
4. **Organism Components** - Navigation, Sidebar, DataTable, Modal, Form
5. **Layout Components** - Container, Grid, Stack, PageLayout
6. **Theme Configuration** - Light/dark mode with CSS variables
7. **Component Documentation** - Usage guides and Storybook stories

```
src/
├── components/
│   ├── ui/                    # Atomic components
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Badge.tsx
│   │   ├── Avatar.tsx
│   │   ├── Icon.tsx
│   │   ├── Spinner.tsx
│   │   └── index.ts
│   ├── composed/              # Molecular components
│   │   ├── FormField.tsx
│   │   ├── Card.tsx
│   │   ├── Alert.tsx
│   │   ├── Tooltip.tsx
│   │   ├── Dropdown.tsx
│   │   └── index.ts
│   ├── patterns/              # Organism components
│   │   ├── Navigation.tsx
│   │   ├── Sidebar.tsx
│   │   ├── DataTable.tsx
│   │   ├── Modal.tsx
│   │   └── index.ts
│   └── layout/                # Layout components
│       ├── Container.tsx
│       ├── Grid.tsx
│       ├── Stack.tsx
│       ├── PageLayout.tsx
│       └── index.ts
├── styles/
│   ├── tokens.css             # CSS custom properties
│   └── globals.css            # Global styles
└── lib/
    └── utils.ts               # cn() and helper utilities

docs/02-design/
└── design-system.md           # Full design system documentation
```

## Process

### Step 1: Define Design Tokens

Design tokens are the smallest units of your visual language. They ensure consistency across all components.

#### Colors

```typescript
// tailwind.config.ts
const colors = {
  primary: {
    50: '#EFF6FF', 100: '#DBEAFE', 200: '#BFDBFE', 300: '#93C5FD',
    400: '#60A5FA', 500: '#3B82F6', 600: '#2563EB', 700: '#1D4ED8',
    800: '#1E40AF', 900: '#1E3A8A',
  },
  gray: {
    50: '#F9FAFB', 100: '#F3F4F6', 200: '#E5E7EB', 300: '#D1D5DB',
    400: '#9CA3AF', 500: '#6B7280', 600: '#4B5563', 700: '#374151',
    800: '#1F2937', 900: '#111827',
  },
  semantic: { success: '#10B981', warning: '#F59E0B', error: '#EF4444', info: '#3B82F6' },
};
```

#### Typography and Spacing

```css
:root {
  --font-sans: 'Inter', system-ui, -apple-system, sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', monospace;
  --text-xs: 0.75rem;   --text-sm: 0.875rem;  --text-base: 1rem;
  --text-lg: 1.125rem;  --text-xl: 1.25rem;   --text-2xl: 1.5rem;
  --text-3xl: 1.875rem; --text-4xl: 2.25rem;
  --font-normal: 400; --font-medium: 500; --font-semibold: 600; --font-bold: 700;
  --space-1: 0.25rem; --space-2: 0.5rem;  --space-3: 0.75rem; --space-4: 1rem;
  --space-6: 1.5rem;  --space-8: 2rem;    --space-10: 2.5rem; --space-12: 3rem;
  --radius-sm: 0.25rem; --radius-md: 0.375rem; --radius-lg: 0.5rem; --radius-full: 9999px;
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0,0,0,0.1);
}
```

### Step 2: Component Architecture (Atomic Design)

Follow the atomic design methodology to build components from small to large:

```
Atoms       -> Button, Input, Badge, Avatar, Icon, Spinner
Molecules   -> FormField (Label+Input+Error), Card (Header+Body+Footer)
Organisms   -> Navigation, Sidebar, DataTable, Modal
Templates   -> PageLayout, AuthLayout, DashboardLayout
Pages       -> Composed from templates + organisms + data
```

#### Atom Example: Button with CVA

```tsx
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none',
  {
    variants: {
      variant: {
        default: 'bg-primary-500 text-white hover:bg-primary-600 focus:ring-primary-500',
        secondary: 'bg-gray-100 text-gray-900 hover:bg-gray-200 focus:ring-gray-500',
        outline: 'border border-gray-300 bg-white hover:bg-gray-50 focus:ring-primary-500',
        ghost: 'hover:bg-gray-100 focus:ring-gray-500',
        destructive: 'bg-red-500 text-white hover:bg-red-600 focus:ring-red-500',
      },
      size: { sm: 'h-8 px-3 text-sm gap-1.5', md: 'h-10 px-4 text-sm gap-2', lg: 'h-12 px-6 text-base gap-2.5' },
    },
    defaultVariants: { variant: 'default', size: 'md' },
  }
);

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>, VariantProps<typeof buttonVariants> {
  isLoading?: boolean;
}

export function Button({ variant, size, isLoading, className, children, ...props }: ButtonProps) {
  return (
    <button className={cn(buttonVariants({ variant, size }), className)} disabled={isLoading} {...props}>
      {isLoading && <Spinner className="h-4 w-4 animate-spin" />}
      {children}
    </button>
  );
}
```

### Step 3: Theming (Light/Dark Mode)

```css
:root {
  --background: #ffffff; --foreground: #111827;
  --card: #ffffff; --card-foreground: #111827;
  --border: #e5e7eb; --input: #e5e7eb;
  --muted: #f3f4f6; --muted-foreground: #6b7280;
}
.dark {
  --background: #0f172a; --foreground: #f8fafc;
  --card: #1e293b; --card-foreground: #f8fafc;
  --border: #334155; --input: #334155;
  --muted: #1e293b; --muted-foreground: #94a3b8;
}
```

### Step 4: Storybook Documentation

For Dynamic and Enterprise levels, document each component with Storybook:

```tsx
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'UI/Button', component: Button,
  argTypes: {
    variant: { control: 'select', options: ['default', 'secondary', 'outline', 'ghost', 'destructive'] },
    size: { control: 'select', options: ['sm', 'md', 'lg'] },
  },
};
export default meta;
type Story = StoryObj<typeof Button>;
export const Default: Story = { args: { children: 'Click me' } };
export const Loading: Story = { args: { children: 'Saving...', isLoading: true } };
```

### Step 5: Utility Helpers

```typescript
// lib/utils.ts
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';
export function cn(...inputs: ClassValue[]) { return twMerge(clsx(inputs)); }
```

## Level-wise Application

| Level | Design System Scope |
|-------|-------------------|
| Starter | Use Tailwind utility classes directly; no formal component library needed |
| Dynamic | Core component library (Button, Input, Card, Modal, Alert, Table); design tokens via CSS variables |
| Enterprise | Full atomic design system with Storybook, accessibility testing, visual regression tests, and design token pipeline |

## Design System Patterns

See `references/design-system-guide.md` for detailed patterns:
- Component hierarchy (Atomic Design)
- Tailwind CSS variant system with `cva`
- cn() utility for class merging
- Accessibility checklist
- Dark mode setup

## PDCA Application

- **Plan**: Inventory needed components from mockups; define token scales
- **Design**: Create component API specs (props, variants, states)
- **Do**: Implement atoms first, then molecules, then organisms
- **Check**: Visual review, accessibility audit (axe-core), cross-browser testing
- **Act**: Refine based on review and proceed to Phase 6

## Common Mistakes

| Mistake | Solution |
|---------|----------|
| Inconsistent spacing | Use token scale exclusively; never use arbitrary values |
| Missing focus states | Add focus:ring to all interactive elements |
| No loading states | Include isLoading prop on buttons and async components |
| Hardcoded colors | Always reference token variables, not hex values |
| No dark mode support | Use CSS variables from the start, not hardcoded colors |
| Over-engineering for Starter | Starter level does not need a formal design system |

## Output Location

```
docs/02-design/
└── design-system.md           # Full design system documentation
src/components/                # Component library
src/styles/tokens.css          # Design tokens
```

## Next Phase

When the design system is complete, proceed to **$phase-6-ui-integration** to connect components with APIs and state management.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
