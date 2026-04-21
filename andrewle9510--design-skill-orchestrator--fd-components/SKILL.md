---
name: fd-components
description: Expert skill for UI component design, variants, composition patterns, and shadcn/ui component customization. Use when building specific components, creating variants, or composing complex UI elements. Use when this capability is needed.
metadata:
  author: andrewle9510
---

# Components Expert

Provide expert guidance on UI component design, variants, composition patterns, and shadcn/ui customization for building consistent, reusable interface elements.

## Role Definition

You are a **Components Expert** — responsible for individual UI building blocks. You design and implement buttons, cards, inputs, modals, and other components that form the product's visual vocabulary.

## User Context

- **User Profile**: Domain expert (film curation), not a design specialist
- **Product**: Short-form film curation platform for content creators
- **Tech Stack**: Next.js 16+, React 19, Tailwind CSS v4, shadcn/ui (base-lyra style)
- **Component Library**: shadcn/ui as foundation, customized for brand

---

## Server vs Client Components

With Next.js App Router and React 19, understand the boundary:

| Server Components (default) | Client Components (`"use client"`) |
|-----------------------------|-----------------------------------|
| Layouts, page shells | Interactive elements (forms, buttons with handlers) |
| Static content, typography | shadcn/ui components with Context (Dialog, Dropdown) |
| Data fetching (async) | Animations (motion, transitions with state) |
| SEO-critical content | Real-time data (Convex `useQuery`) |

```tsx
// Server Component (default) - no directive needed
async function FilmPage({ params }: { params: { id: string } }) {
  const film = await fetchFilm(params.id);
  return (
    <main>
      <h1 className="text-3xl font-bold">{film.title}</h1>
      <LikeButton filmId={film.id} /> {/* Client component */}
    </main>
  );
}

// Client Component - requires directive
"use client";

function LikeButton({ filmId }: { filmId: string }) {
  const like = useMutation(api.films.toggleLike);
  return <Button onClick={() => like({ filmId })}>Like</Button>;
}
```

---

## shadcn/ui Foundation

### Installation Pattern

```bash
# Add a component
bunx shadcn@latest add button
bunx shadcn@latest add card
bunx shadcn@latest add dialog
```

Components are installed to `apps/web/src/components/ui/` and can be customized.

### Component Structure

```
components/
├── ui/                    # shadcn/ui base components
│   ├── button.tsx
│   ├── card.tsx
│   ├── input.tsx
│   └── ...
├── films/                 # Domain-specific components
│   ├── film-card.tsx
│   ├── film-grid.tsx
│   └── film-player.tsx
└── layout/               # Layout components
    ├── header.tsx
    ├── footer.tsx
    └── sidebar.tsx
```

---

## Core Component Patterns

### 1. Button Variants

```tsx
// components/ui/button.tsx (shadcn/ui pattern)
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva(
  "inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
        outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
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

// Usage
<Button variant="default" size="lg">Get Started</Button>
<Button variant="outline">Cancel</Button>
<Button variant="ghost" size="icon"><Heart /></Button>
```

### 2. Card Component

```tsx
// Base card structure
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>
    {/* Main content */}
  </CardContent>
  <CardFooter>
    {/* Actions */}
  </CardFooter>
</Card>

// Film card (domain-specific)
function FilmCard({ film }) {
  return (
    <Card className="group overflow-hidden">
      {/* Thumbnail with hover overlay */}
      <div className="relative aspect-video overflow-hidden">
        <img
          src={film.thumbnail}
          alt={film.title}
          className="object-cover w-full h-full transition-transform duration-300 group-hover:scale-105"
        />
        <div className="absolute inset-0 bg-black/60 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center">
          <Button size="sm" variant="secondary">
            <Play className="h-4 w-4 mr-2" />
            Watch
          </Button>
        </div>
      </div>
      
      <CardContent className="p-4">
        <h3 className="font-semibold truncate">{film.title}</h3>
        <p className="text-sm text-muted-foreground truncate">
          {film.director} • {film.year}
        </p>
      </CardContent>
    </Card>
  );
}
```

### 3. Input Components

```tsx
// Text input with label
<div className="space-y-2">
  <Label htmlFor="email">Email</Label>
  <Input
    id="email"
    type="email"
    placeholder="you@example.com"
  />
</div>

// Input with icon
<div className="relative">
  <Search className="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
  <Input placeholder="Search..." className="pl-9" />
</div>

// Input with error
<div className="space-y-2">
  <Label htmlFor="email">Email</Label>
  <Input
    id="email"
    className="border-destructive focus-visible:ring-destructive"
    aria-invalid="true"
  />
  <p className="text-sm text-destructive">Please enter a valid email</p>
</div>
```

### 4. Modal/Dialog

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";

<Dialog>
  <DialogTrigger asChild>
    <Button>Add Film</Button>
  </DialogTrigger>
  <DialogContent className="sm:max-w-[425px]">
    <DialogHeader>
      <DialogTitle>Add a Film</DialogTitle>
      <DialogDescription>
        Add a new film to your collection.
      </DialogDescription>
    </DialogHeader>
    
    <form className="space-y-4">
      <div className="space-y-2">
        <Label htmlFor="title">Title</Label>
        <Input id="title" placeholder="Film title" />
      </div>
      <div className="space-y-2">
        <Label htmlFor="url">Video URL</Label>
        <Input id="url" placeholder="https://..." />
      </div>
    </form>
    
    <DialogFooter>
      <Button type="submit">Add Film</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### 5. Dropdown Menu

```tsx
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";

<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="ghost" size="icon">
      <MoreHorizontal className="h-4 w-4" />
    </Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent align="end">
    <DropdownMenuLabel>Actions</DropdownMenuLabel>
    <DropdownMenuSeparator />
    <DropdownMenuItem>
      <Edit className="mr-2 h-4 w-4" />
      Edit
    </DropdownMenuItem>
    <DropdownMenuItem>
      <Share className="mr-2 h-4 w-4" />
      Share
    </DropdownMenuItem>
    <DropdownMenuSeparator />
    <DropdownMenuItem className="text-destructive">
      <Trash className="mr-2 h-4 w-4" />
      Delete
    </DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

---

## Component Composition Patterns

### Compound Components

```tsx
// Avatar with fallback
<Avatar>
  <AvatarImage src={user.avatar} alt={user.name} />
  <AvatarFallback>{user.name.charAt(0)}</AvatarFallback>
</Avatar>

// User card composition
<div className="flex items-center gap-3">
  <Avatar>
    <AvatarImage src={user.avatar} />
    <AvatarFallback>{user.initials}</AvatarFallback>
  </Avatar>
  <div>
    <p className="font-medium">{user.name}</p>
    <p className="text-sm text-muted-foreground">@{user.username}</p>
  </div>
</div>
```

### Slot Pattern (asChild)

```tsx
// Button as link
<Button asChild>
  <Link href="/films">Browse Films</Link>
</Button>

// Dialog trigger as custom element
<DialogTrigger asChild>
  <div className="cursor-pointer">Click me</div>
</DialogTrigger>
```

### Polymorphic Components

```tsx
// Component that renders different elements
interface BoxProps<T extends React.ElementType = "div"> {
  as?: T;
  children: React.ReactNode;
}

function Box<T extends React.ElementType = "div">({
  as,
  children,
  ...props
}: BoxProps<T>) {
  const Component = as || "div";
  return <Component {...props}>{children}</Component>;
}

// Usage
<Box as="section" className="py-8">{content}</Box>
<Box as="article">{content}</Box>
```

---

## Component States

### Loading State

```tsx
<Button disabled>
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Saving...
</Button>

// Skeleton for card
<Card>
  <div className="animate-pulse">
    <div className="aspect-video bg-muted" />
    <CardContent className="space-y-2">
      <div className="h-4 bg-muted rounded w-3/4" />
      <div className="h-3 bg-muted rounded w-1/2" />
    </CardContent>
  </div>
</Card>
```

### Disabled State

```tsx
<Button disabled>Unavailable</Button>
<Input disabled placeholder="Disabled input" />

// Visually muted but still visible
<div className="opacity-50 pointer-events-none">
  <Card>{content}</Card>
</div>
```

### Active/Selected State

```tsx
// Toggle button
<Button
  variant={isActive ? "default" : "outline"}
  onClick={() => setIsActive(!isActive)}
>
  {isActive ? <Check className="mr-2 h-4 w-4" /> : null}
  Selected
</Button>

// Active nav item
<Link
  href={href}
  className={cn(
    "px-3 py-2 rounded-md text-sm",
    isActive 
      ? "bg-primary text-primary-foreground" 
      : "hover:bg-muted"
  )}
>
  {label}
</Link>
```

---

## Customizing shadcn/ui

### Extending Variants

```tsx
// Add custom variant to button
const buttonVariants = cva(
  "...",
  {
    variants: {
      variant: {
        // ... existing variants
        premium: "bg-gradient-to-r from-amber-500 to-orange-500 text-white hover:from-amber-600 hover:to-orange-600",
      },
    },
  }
);
```

### Custom Component from Primitives

```tsx
// Custom toggle button built on Radix
import * as Toggle from "@radix-ui/react-toggle";

function LikeButton({ liked, onToggle }) {
  return (
    <Toggle.Root
      pressed={liked}
      onPressedChange={onToggle}
      className={cn(
        "p-2 rounded-full transition-colors",
        liked ? "text-red-500" : "text-muted-foreground hover:text-red-400"
      )}
    >
      <Heart className={cn("h-5 w-5", liked && "fill-current")} />
    </Toggle.Root>
  );
}
```

### Theme-Aware Components

```tsx
// Component that adapts to theme
function ThemedCard({ children }) {
  return (
    <Card className="bg-card text-card-foreground border-border">
      {children}
    </Card>
  );
}

// Use semantic colors, not hard-coded
<div className="bg-background text-foreground">  // ✅
<div className="bg-white text-black">            // ❌
```

---

## The cn() Utility

Always use `cn()` for conditional classes:

```tsx
import { cn } from "@/lib/utils";

// Conditional styling
<button
  className={cn(
    "px-4 py-2 rounded-md",
    isActive && "bg-primary text-primary-foreground",
    isDisabled && "opacity-50 pointer-events-none",
    className // Allow override from props
  )}
>
```

---

## Research Commands

```
web_search: "shadcn ui custom component tutorial"
read_web_page: https://ui.shadcn.com/docs/components/button
read_web_page: https://ui.shadcn.com/docs/theming
web_search: "radix ui primitives examples"
web_search: "react compound component pattern"
```

---

## Handoff to Other Experts

| To Expert | Component Requirements |
|-----------|----------------------|
| `fd-color-systems` | Component color tokens |
| `fd-typography` | Text styles within components |
| `fd-spacing-layout` | Component padding, margins |
| `fd-animations` | Hover/focus transitions |
| `fd-accessibility` | ARIA labels, keyboard support |
| `fd-tailwind-shadcn` | CVA setup, Tailwind config |

---

## Key Principles

1. **Composition Over Inheritance** — Build complex from simple
2. **Variants Not Props Soup** — Use CVA for organized variants
3. **Semantic Colors** — Use tokens like `primary`, not `blue-500`
4. **Accessible by Default** — Built-in keyboard/screen reader support
5. **Consistent API** — Same patterns across all components

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewle9510) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
