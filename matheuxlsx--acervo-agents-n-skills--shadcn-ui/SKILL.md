---
name: shadcn-ui
description: Complete skill for developing, suggesting, and implementing shadcn/ui components. Provides comprehensive knowledge about all available components, CLI commands, composition patterns, theming with CSS variables, dark mode implementation, and best practices for using shadcn/ui in React/Next.js projects with TypeScript and Tailwind CSS. Use when this capability is needed.
metadata:
  author: matheuxlsx
---

# shadcn/ui Development Skill

This skill provides comprehensive knowledge for developing, suggesting, and implementing shadcn/ui components in React/Next.js projects. shadcn/ui is a collection of beautifully designed, accessible UI components that work as a code distribution platform, allowing you to copy and customize components directly in your project.

## Overview

### What is shadcn/ui?

shadcn/ui is different from traditional component libraries:
- **Not a package to install** - You copy components into your project
- **Fully customizable** - Modify code to fit your needs
- **Accessible by default** - Built on Radix UI primitives
- **Modern stack** - TypeScript, Tailwind CSS, React
- **CLI-powered** - Easy installation and updates

### Key Characteristics

- **Code ownership** - Components live in your project
- **TypeScript support** - Full type inference and IntelliSense
- **Tailwind integration** - Utility-first styling with design tokens
- **Dark mode ready** - CSS variables for theming
- **Accessibility** - WCAG compliant via Radix UI
- **Framework support** - Next.js, Vite, Remix, and more

## Installation and Configuration

### Automatic Initialization

Start a new project with:

```bash
npx shadcn@latest init
```

The CLI will prompt for configuration options:

```bash
√ Would you like to use TypeScript? ... Yes
√ Would you like to use Tailwind CSS? ... Yes
√ Would you like to use CSS variables? ... Yes
√ Would you like to use cn (clsx + tailwind-merge)? ... Yes
√ What framework are you using? » Next.js
√ What style would you like to use? » Default
√ What color would you like to use as base color? » Slate
√ Where would you like to import components from? ... @/components/ui
```

### Manual Configuration

Create `components.json` in your project root:

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "new-york",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/app/globals.css",
    "baseColor": "neutral",
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

### Directory Structure

After initialization, your project will have:

```
components.json          # shadcn/ui configuration
src/
├── app/
│   └── globals.css     # CSS variables and Tailwind imports
├── components/
│   ├── ui/            # shadcn/ui components
│   └── ...            # Your custom components
└── lib/
    └── utils.ts       # cn() utility function
```

## Theming with CSS Variables

### CSS Variables Configuration

shadcn/ui uses CSS variables for theming, enabling easy light/dark mode switching:

```css
@import "tailwindcss";
@import "tw-animate-css";

@custom-variant dark (&:is(.dark *));

:root {
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --popover: oklch(1 0 0);
  --popover-foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --secondary: oklch(0.97 0 0);
  --secondary-foreground: oklch(0.205 0 0);
  --muted: oklch(0.97 0 0);
  --muted-foreground: oklch(0.556 0 0);
  --accent: oklch(0.97 0 0);
  --accent-foreground: oklch(0.205 0 0);
  --destructive: oklch(0.577 0.245 27.325);
  --destructive-foreground: oklch(0.577 0.245 27.325);
  --border: oklch(0.922 0 0);
  --input: oklch(0.922 0 0);
  --ring: oklch(0.708 0 0);
  --chart-1: oklch(0.646 0.222 41.116);
  --chart-2: oklch(0.6 0.118 184.704);
  --chart-3: oklch(0.398 0.07 227.392);
  --chart-4: oklch(0.828 0.189 84.429);
  --chart-5: oklch(0.769 0.188 70.08);
  --radius: 0.625rem;
  --sidebar: oklch(0.985 0 0);
  --sidebar-foreground: oklch(0.145 0 0);
  --sidebar-primary: oklch(0.205 0 0);
  --sidebar-primary-foreground: oklch(0.985 0 0);
  --sidebar-accent: oklch(0.97 0 0);
  --sidebar-accent-foreground: oklch(0.205 0 0);
  --sidebar-border: oklch(0.922 0 0);
  --sidebar-ring: oklch(0.708 0 0);
}

.dark {
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  --card: oklch(0.145 0 0);
  --card-foreground: oklch(0.985 0 0);
  --popover: oklch(0.145 0 0);
  --popover-foreground: oklch(0.985 0 0);
  --primary: oklch(0.985 0 0);
  --primary-foreground: oklch(0.205 0 0);
  --secondary: oklch(0.269 0 0);
  --secondary-foreground: oklch(0.985 0 0);
  --muted: oklch(0.269 0 0);
  --muted-foreground: oklch(0.708 0 0);
  --accent: oklch(0.269 0 0);
  --accent-foreground: oklch(0.985 0 0);
  --destructive: oklch(0.396 0.141 25.723);
  --destructive-foreground: oklch(0.637 0.237 25.331);
  --border: oklch(0.269 0 0);
  --input: oklch(0.269 0 0);
  --ring: oklch(0.439 0 0);
  --chart-1: oklch(0.488 0.243 264.376);
  --chart-2: oklch(0.696 0.17 162.48);
  --chart-3: oklch(0.769 0.188 70.08);
  --chart-4: oklch(0.627 0.265 303.9);
  --chart-5: oklch(0.645 0.246 16.439);
  --sidebar: oklch(0.205 0 0);
  --sidebar-foreground: oklch(0.985 0 0);
  --sidebar-primary: oklch(0.488 0.243 264.376);
  --sidebar-primary-foreground: oklch(0.985 0 0);
  --sidebar-accent: oklch(0.269 0 0);
  --sidebar-accent-foreground: oklch(0.985 0 0);
  --sidebar-border: oklch(0.269 0 0);
  --sidebar-ring: oklch(0.439 0 0);
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
  --color-chart-1: var(--chart-1);
  --color-chart-2: var(--chart-2);
  --color-chart-3: var(--chart-3);
  --color-chart-4: var(--chart-4);
  --color-chart-5: var(--chart-5);
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
  --color-sidebar: var(--sidebar);
  --color-sidebar-foreground: var(--sidebar-foreground);
  --color-sidebar-primary: var(--sidebar-primary);
  --color-sidebar-primary-foreground: var(--sidebar-primary-foreground);
  --color-sidebar-accent: var(--sidebar-accent);
  --color-sidebar-accent-foreground: var(--sidebar-accent-foreground);
  --color-sidebar-border: var(--sidebar-border);
  --color-sidebar-ring: var(--sidebar-ring);
}

@layer base {
  * {
    @apply border-border outline-ring/50;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### Applying Theme Classes

Use Tailwind classes referencing CSS variables:

```tsx
<div className="bg-background text-foreground" />
<div className="bg-primary text-primary-foreground" />
<div className="bg-destructive text-destructive-foreground" />
```

## Dark Mode Implementation

### Theme Provider

Create a theme context provider:

```typescript
import { createContext, useContext, useEffect, useState } from "react"

type Theme = "dark" | "light" | "system"

type ThemeProviderProps = {
  children: React.ReactNode
  defaultTheme?: Theme
  storageKey?: string
}

type ThemeProviderState = {
  theme: Theme
  setTheme: (theme: Theme) => void
}

const initialState: ThemeProviderState = {
  theme: "system",
  setTheme: () => null,
}

const ThemeProviderContext = createContext<ThemeProviderState>(initialState)

export function ThemeProvider({
  children,
  defaultTheme = "system",
  storageKey = "vite-ui-theme",
  ...props
}: ThemeProviderProps) {
  const [theme, setTheme] = useState<Theme>(
    () => (localStorage.getItem(storageKey) as Theme) || defaultTheme
  )

  useEffect(() => {
    const root = window.document.documentElement

    root.classList.remove("light", "dark")

    if (theme === "system") {
      const systemTheme = window.matchMedia("(prefers-color-scheme: dark)")
        .matches
        ? "dark"
        : "light"

      root.classList.add(systemTheme)
      return
    }

    root.classList.add(theme)
  }, [theme])

  const value = {
    theme,
    setTheme: (theme: Theme) => {
      localStorage.setItem(storageKey, theme)
      setTheme(theme)
    },
  }

  return (
    <ThemeProviderContext.Provider {...props} value={value}>
      {children}
    </ThemeProviderContext.Provider>
  )
}

export const useTheme = () => {
  const context = useContext(ThemeProviderContext)

  if (context === undefined)
    throw new Error("useTheme must be used within a ThemeProvider")

  return context
}
```

### Theme Toggle Component

Create a dropdown theme switcher:

```typescript
import { Moon, Sun } from "lucide-react"

import { Button } from "@/components/ui/button"
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"
import { useTheme } from "@/components/theme-provider"

export function ModeToggle() {
  const { setTheme } = useTheme()

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="outline" size="icon">
          <Sun className="h-[1.2rem] w-[1.2rem] scale-100 rotate-0 transition-all dark:scale-0 dark:-rotate-90" />
          <Moon className="absolute h-[1.2rem] w-[1.2rem] scale-0 rotate-90 transition-all dark:scale-100 dark:rotate-0" />
          <span className="sr-only">Toggle theme</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuItem onClick={() => setTheme("light")}>
          Light
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("dark")}>
          Dark
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("system")}>
          System
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  )
}
```

## CLI Commands Reference

### Add Components

```bash
# Add a single component
npx shadcn@latest add button

# Add multiple components
npx shadcn@latest add button input card dialog

# Add all components
npx shadcn@latest add --all

# Skip confirmation prompts
npx shadcn@latest add button --yes

# Overwrite existing component
npx shadcn@latest add button --overwrite

# Add from registry
npx shadcn@latest add @v0/dashboard

# Add to specific path
npx shadcn@latest add button --path src/components/ui
```

### Component Management

```bash
# List available components
npx shadcn@latest list

# Search for components
npx shadcn@latest search button

# Check for updates
npx shadcn@latest diff

# See specific component differences
npx shadcn@latest diff button

# Add with specific style
npx shadcn@latest add button --style new-york
```

## Essential Components

### Button

```tsx
import { Button } from "@/components/ui/button"

// Variants
<Button>Default</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="destructive">Destructive</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>

// Sizes
<Button size="default">Default</Button>
<Button size="sm">Small</Button>
<Button size="lg">Large</Button>
<Button size="icon">
  <Icon className="h-4 w-4" />
</Button>

// With icon
<Button>
  <Icon className="mr-2 h-4 w-4" />
  Click me
</Button>
```

### Input with Field

```tsx
import { Field, FieldLabel, FieldDescription, FieldError } from "@/components/ui/field"
import { Input } from "@/components/ui/input"

<Field>
  <FieldLabel htmlFor="email">Email</FieldLabel>
  <Input 
    id="email" 
    type="email" 
    placeholder="john@example.com" 
  />
  <FieldDescription>
    We'll never share your email.
  </FieldDescription>
  <FieldError>Please enter a valid email.</FieldError>
</Field>
```

### Select

```tsx
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select"

<Select>
  <SelectTrigger>
    <SelectValue placeholder="Select a fruit" />
  </SelectTrigger>
  <SelectContent>
    <SelectGroup>
      <SelectItem value="apple">Apple</SelectItem>
      <SelectItem value="banana">Banana</SelectItem>
      <SelectItem value="orange">Orange</SelectItem>
    </SelectGroup>
  </SelectContent>
</Select>
```

### Form with TanStack Form and Zod

```tsx
"use client"

import { useForm } from "@tanstack/react-form"
import { toast } from "sonner"
import * as z from "zod"

import { Button } from "@/components/ui/button"
import {
  Field,
  FieldContent,
  FieldDescription,
  FieldError,
  FieldGroup,
  FieldLabel,
} from "@/components/ui/field"
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select"

const formSchema = z.object({
  language: z
    .string()
    .min(1, "Please select your language."),
})

export function FormWithValidation() {
  const form = useForm({
    defaultValues: {
      language: "",
    },
    validators: {
      onSubmit: formSchema,
    },
    onSubmit: async ({ value }) => {
      toast.success("Form submitted!", {
        description: JSON.stringify(value, null, 2),
      })
    },
  })

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault()
        form.handleSubmit()
      }}
    >
      <FieldGroup>
        <form.Field
          name="language"
          children={(field) => {
            const isInvalid =
              field.state.meta.isTouched && !field.state.meta.isValid
            return (
              <Field orientation="responsive" data-invalid={isInvalid}>
                <FieldContent>
                  <FieldLabel>Language</FieldLabel>
                  {isInvalid && (
                    <FieldError errors={field.state.meta.errors} />
                  )}
                </FieldContent>
                <Select
                  name={field.name}
                  value={field.state.value}
                  onValueChange={field.handleChange}
                >
                  <SelectTrigger aria-invalid={isInvalid}>
                    <SelectValue placeholder="Select" />
                  </SelectTrigger>
                  <SelectContent>
                    <SelectItem value="en">English</SelectItem>
                    <SelectItem value="es">Spanish</SelectItem>
                    <SelectItem value="fr">French</SelectItem>
                  </SelectContent>
                </Select>
              </Field>
            )
          }}
        />
      </FieldGroup>
      <Button type="submit">Submit</Button>
    </form>
  )
}
```

### Dialog

```tsx
import {
  Dialog,
  DialogClose,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"
import { Button } from "@/components/ui/button"

<Dialog>
  <DialogTrigger asChild>
    <Button variant="outline">Open Dialog</Button>
  </DialogTrigger>
  <DialogContent className="sm:max-w-sm">
    <DialogHeader>
      <DialogTitle>Edit profile</DialogTitle>
      <DialogDescription>
        Make changes to your profile here.
      </DialogDescription>
    </DialogHeader>
    <div className="grid gap-4 py-4">
      {/* Form fields here */}
    </div>
    <DialogFooter>
      <DialogClose asChild>
        <Button variant="outline">Cancel</Button>
      </DialogClose>
      <Button type="submit">Save changes</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### Card

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card"
import { Button } from "@/components/ui/button"

<Card className="w-full max-w-sm">
  <CardHeader>
    <CardTitle>Card Title</CardTitle>
    <CardDescription>
      Card description goes here.
    </CardDescription>
  </CardHeader>
  <CardContent>
    {/* Content here */}
  </CardContent>
  <CardFooter className="flex justify-between">
    <Button variant="outline">Cancel</Button>
    <Button>Continue</Button>
  </CardFooter>
</Card>
```

### Dropdown Menu

```tsx
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"

<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="outline">Open Menu</Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent>
    <DropdownMenuLabel>My Account</DropdownMenuLabel>
    <DropdownMenuSeparator />
    <DropdownMenuItem>Profile</DropdownMenuItem>
    <DropdownMenuItem>Billing</DropdownMenuItem>
    <DropdownMenuItem>Team</DropdownMenuItem>
    <DropdownMenuSeparator />
    <DropdownMenuItem>Log out</DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

### Tabs

```tsx
import {
  Tabs,
  TabsContent,
  TabsList,
  TabsTrigger,
} from "@/components/ui/tabs"

<Tabs defaultValue="account">
  <TabsList>
    <TabsTrigger value="account">Account</TabsTrigger>
    <TabsTrigger value="password">Password</TabsTrigger>
    <TabsTrigger value="settings">Settings</TabsTrigger>
  </TabsList>
  <TabsContent value="account">
    Account settings content
  </TabsContent>
  <TabsContent value="password">
    Password settings content
  </TabsContent>
  <TabsContent value="settings">
    Settings content
  </TabsContent>
</Tabs>
```

### Table

```tsx
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table"

<Table>
  <TableHeader>
    <TableRow>
      <TableHead>Name</TableHead>
      <TableHead>Email</TableHead>
      <TableHead>Role</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    <TableRow>
      <TableCell>John Doe</TableCell>
      <TableCell>john@example.com</TableCell>
      <TableCell>Admin</TableCell>
    </TableRow>
  </TableBody>
</Table>
```

### Checkbox

```tsx
import { Checkbox } from "@/components/ui/checkbox"
import { Field, FieldLabel } from "@/components/ui/field"

<Field orientation="horizontal">
  <Checkbox id="terms" />
  <FieldLabel htmlFor="terms" className="font-normal">
    I agree to the terms and conditions
  </FieldLabel>
</Field>
```

### Toast Notifications

```tsx
import { useToast } from "@/components/ui/use-toast"

const { toast } = useToast()

// Simple toast
toast("Event has been created")

// With title
toast({
  title: "Success",
  description: "Your changes have been saved.",
})

// With action
toast({
  title: "Scheduled: Catch up",
  description: "Friday, February 10, 2025 at 5:57 PM",
  action: {
    label: "Undo",
    onClick: () => console.log("Undo"),
  },
})
```

## Utility Functions

### cn() - Class Name Merging

```typescript
// lib/utils.ts
import { type ClassValue, clsx } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

Usage:

```tsx
import { cn } from "@/lib/utils"

<div className={cn(
  "base-class",
  isActive && "active-class",
  className
)} />
```

## Accessibility Patterns

### Field Label Association

Always associate labels with inputs:

```tsx
<Field>
  <FieldLabel htmlFor="email">Email</FieldLabel>
  <Input id="email" />
</Field>
```

### Error States

Use aria-invalid and data-invalid:

```tsx
<Field data-invalid>
  <FieldLabel htmlFor="email">Email</FieldLabel>
  <Input 
    id="email" 
    aria-invalid 
    aria-describedby="email-error"
  />
  <FieldError id="email-error">
    Please enter a valid email address.
  </FieldError>
</Field>
```

### Keyboard Navigation

Components support keyboard navigation by default:
- Tab - Move focus
- Enter/Space - Activate
- Arrow keys - Navigate within components
- Escape - Close dialogs/menus

## Component Categories Quick Reference

| Category | Components |
|----------|-------------|
| **Forms** | Button, Input, Textarea, Select, Checkbox, RadioGroup, Switch, Slider, Calendar, Field |
| **Navigation** | Tabs, NavigationMenu, Breadcrumb, Pagination |
| **Layout** | Card, Separator, ScrollArea, Sheet, Collapsible, Resizable, AspectRatio |
| **Feedback** | Dialog, Alert, Toast, Badge, Progress, Skeleton, LoadingSpinner |
| **Menus** | DropdownMenu, ContextMenu, Popover, Tooltip, HoverCard, Command, Menubar |
| **Data Display** | Table, DataTable, Avatar, Calendar, DatePicker, Chart |

## Development Workflow

### 1. Initialize Project
```bash
npx shadcn@latest init
```

### 2. Add Components as Needed
```bash
npx shadcn@latest add button input card dialog
```

### 3. Use Components
```tsx
import { Button } from "@/components/ui/button"

<Button>Click me</Button>
```

### 4. Customize Components
- Edit component files directly in `components/ui/`
- Modify styles, add props, extend functionality

### 5. Update Components
```bash
# Check for updates
npx shadcn@latest diff

# Update specific component
npx shadcn@latest add button --overwrite
```

## When to Use This Skill

Use this skill when:

- User requests shadcn/ui components
- Building forms with accessible inputs
- Implementing dialogs, sheets, or modals
- Creating navigation menus or tabs
- Adding dark mode support
- Needing table or data display components
- Implementing toast notifications
- Working with TypeScript and Tailwind CSS
- User needs help with CLI commands

## Comparison: shadcn/ui vs Kibo UI

| Aspect | shadcn/ui | Kibo UI |
|--------|-----------|---------|
| **Focus** | Primitive, accessible components | Complex, feature-rich components |
| **Complexity** | Lower - basic building blocks | Higher - pre-built functionality |
| **Best For** | Forms, buttons, dialogs, tables | Kanban, Gantt, advanced data views |
| **Customization** | Full control - edit code | Some customization, some locked logic |
| **Learning Curve** | Lower - combine primitives | Medium - understand component API |

**Recommendation:**
- Use **shadcn/ui** for: forms, buttons, dialogs, navigation, basic data display
- Use **Kibo UI** for: complex dashboards, Kanban boards, Gantt charts, advanced data visualizations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheuxlsx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
