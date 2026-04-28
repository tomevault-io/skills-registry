---
name: phase-5-design-system
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 5: Design System

> Build a consistent component library

## Components

### 1. Design Tokens

```css
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-secondary: #64748b;
  --color-success: #22c55e;
  --color-error: #ef4444;

  /* Typography */
  --font-sans: 'Inter', sans-serif;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;

  /* Spacing */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-4: 1rem;
  --space-8: 2rem;

  /* Border Radius */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 1rem;
}
```

### 2. Base Components

```typescript
// components/ui/button.tsx
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'outline';
  size: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
}

export function Button({ variant, size, children, onClick }: ButtonProps) {
  return (
    <button
      className={cn('btn', `btn-${variant}`, `btn-${size}`)}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

### 3. Component Categories

- **Primitives**: Button, Input, Select, Checkbox
- **Layout**: Container, Grid, Stack, Flex
- **Navigation**: Navbar, Sidebar, Tabs, Breadcrumb
- **Feedback**: Alert, Toast, Modal, Spinner
- **Data Display**: Table, Card, List, Badge

## Tools

- shadcn/ui (recommended)
- Tailwind CSS
- Radix UI primitives

## Output

Save components to: `components/ui/`

## Next Phase

After completion: `/phase-6-ui-integration`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
