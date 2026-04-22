---
name: shadcn
description: Professional shadcn/ui component library guide for building enterprise-grade UI designs. Use when working with shadcn/ui components, customizing themes, creating professional layouts, or implementing design systems with shadcn/ui. Covers component usage, theming, accessibility, and professional design patterns. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# shadcn/ui Professional Design Skill

**Skill Location**: `{project_path}/skills/shadcn/`

Comprehensive guide for building professional, production-ready UI using shadcn/ui component library, optimized for minimal token usage while maintaining enterprise-grade quality.

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**

- Using shadcn/ui components in your project
- Customizing shadcn/ui theme and styling
- Building professional layouts with shadcn/ui
- Creating custom components based on shadcn/ui patterns
- Implementing design systems with shadcn/ui
- Ensuring accessibility with shadcn/ui

**Trigger phrases:**

- "use shadcn/ui components"
- "build professional UI with shadcn/ui"
- "customize shadcn/ui theme"
- "create design system with shadcn/ui"
- "shadcn/ui best practices"

---

## Core Concepts

### 1. What is shadcn/ui?

shadcn/ui is a **component library approach**, not a component kit:

- Copy-paste components to your project
- Full control over code and styling
- Built on Radix UI + Tailwind CSS
- Accessible by default
- Highly customizable

### 2. Architecture

```
shadcn/ui
├── Components (Radix UI primitives)
├── Styling (Tailwind CSS)
├── Theming (CSS variables)
└── Icons (Lucide React)
```

### 3. Token-Saving Approach

**❌ INEFFICIENT:**

```
"shadcn/ui is a component library that provides..."
```

**✅ EFFICIENT:**

```
// Import from @/components/ui
// Follow professional patterns
```

---

## Quick Start

### 1. Installation (Already Done in Project)

Project already has shadcn/ui configured. Use existing components.

### 2. Adding New Components

```bash
# Add specific component
bunx shadcn@latest add button
bunx shadcn@latest add card
bunx shadcn@latest add dialog

# Add multiple components
bunx shadcn@latest add button input card

# Use specific style (New York is default)
bunx shadcn@latest add button --defaults
```

### 3. Project Structure

```
src/
├── components/
│   └── ui/              # shadcn/ui components (DO NOT modify)
│       ├── button.tsx
│       ├── card.tsx
│       ├── dialog.tsx
│       └── ...
```

---

## Professional Usage Patterns

### 1. Button Combinations

```typescript
import { Button } from '@/components/ui/button'

// Primary actions
<Button>Submit</Button>

// Secondary actions
<Button variant="secondary">Cancel</Button>

// Destructive actions
<Button variant="destructive">Delete</Button>

// Outline style
<Button variant="outline">Learn More</Button>

// Ghost style (subtle)
<Button variant="ghost">Settings</Button>

// Link style
<Button variant="link">View Details</Button>

// Size variants
<Button size="sm">Small</Button>
<Button size="default">Default</Button>
<Button size="lg">Large</Button>
<Button size="icon"><Icon /></Button>

// Icon + text
<Button>
  <Save className="mr-2 h-4 w-4" />
  Save Changes
</Button>

// Loading state
<Button disabled>
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Saving...
</Button>

// With tooltip
<Tooltip>
  <TooltipTrigger asChild>
    <Button variant="outline" size="icon">
      <Settings />
    </Button>
  </TooltipTrigger>
  <TooltipContent>Settings</TooltipContent>
</Tooltip>
```

### 2. Card Layouts

```typescript
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from '@/components/ui/card'

// Simple card
<Card>
  <CardHeader>
    <CardTitle>Card Title</CardTitle>
    <CardDescription>Card description</CardDescription>
  </CardHeader>
  <CardContent>
    <p>Card content goes here.</p>
  </CardContent>
</Card>

// Card with footer actions
<Card>
  <CardHeader>
    <CardTitle>User Profile</CardTitle>
    <CardDescription>Manage your account settings</CardDescription>
  </CardHeader>
  <CardContent>
    {/* Content */}
  </CardContent>
  <CardFooter className="flex justify-between">
    <Button variant="ghost">Cancel</Button>
    <Button>Save Changes</Button>
  </CardFooter>
</Card>

// Statistics card
<Card>
  <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
    <CardTitle className="text-sm font-medium">Total Revenue</CardTitle>
    <DollarSign className="h-4 w-4 text-muted-foreground" />
  </CardHeader>
  <CardContent>
    <div className="text-2xl font-bold">$45,231.89</div>
    <p className="text-xs text-muted-foreground">+20.1% from last month</p>
  </CardContent>
</Card>

// Grid of cards
<div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4">
  {stats.map((stat) => (
    <Card key={stat.title}>
      <CardHeader className="flex flex-row items-center justify-between space-y-0 pb-2">
        <CardTitle className="text-sm font-medium">{stat.title}</CardTitle>
        <stat.icon className="h-4 w-4 text-muted-foreground" />
      </CardHeader>
      <CardContent>
        <div className="text-2xl font-bold">{stat.value}</div>
      </CardContent>
    </Card>
  ))}
</div>
```

### 3. Form Components

```typescript
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Textarea } from '@/components/ui/textarea'
import { Checkbox } from '@/components/ui/checkbox'
import { Switch } from '@/components/ui/switch'
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select'

// Basic input
<div className="space-y-2">
  <Label htmlFor="email">Email</Label>
  <Input id="email" type="email" placeholder="m@example.com" />
</div>

// Textarea
<div className="space-y-2">
  <Label htmlFor="message">Message</Label>
  <Textarea id="message" placeholder="Type your message..." />
</div>

// Checkbox
<div className="flex items-center space-x-2">
  <Checkbox id="terms" />
  <Label htmlFor="terms" className="font-normal">Accept terms and conditions</Label>
</div>

// Switch
<div className="flex items-center space-x-2">
  <Switch id="notifications" />
  <Label htmlFor="notifications">Enable notifications</Label>
</div>

// Select
<div className="space-y-2">
  <Label>Category</Label>
  <Select>
    <SelectTrigger>
      <SelectValue placeholder="Select a category" />
    </SelectTrigger>
    <SelectContent>
      <SelectItem value="personal">Personal</SelectItem>
      <SelectItem value="work">Work</SelectItem>
      <SelectItem value="other">Other</SelectItem>
    </SelectContent>
  </Select>
</div>

// Complete form
<form className="space-y-4">
  <div className="space-y-2">
    <Label htmlFor="name">Name</Label>
    <Input id="name" placeholder="John Doe" />
  </div>

  <div className="space-y-2">
    <Label htmlFor="email">Email</Label>
    <Input id="email" type="email" placeholder="john@example.com" />
  </div>

  <div className="space-y-2">
    <Label htmlFor="message">Message</Label>
    <Textarea id="message" placeholder="Your message..." />
  </div>

  <div className="flex items-center space-x-2">
    <Checkbox id="newsletter" />
    <Label htmlFor="newsletter" className="font-normal">
      Subscribe to newsletter
    </Label>
  </div>

  <Button type="submit" className="w-full">Submit</Button>
</form>
```

### 4. Data Display Components

```typescript
import { Badge } from '@/components/ui/badge'
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar'
import { Separator } from '@/components/ui/separator'
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table'
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs'

// Badge variants
<Badge>Default</Badge>
<Badge variant="secondary">Secondary</Badge>
<Badge variant="destructive">Destructive</Badge>
<Badge variant="outline">Outline</Badge>

// Status badges
<Badge variant="default">Active</Badge>
<Badge variant="secondary">Pending</Badge>
<Badge variant="destructive">Deleted</Badge>
<Badge variant="outline" className="text-green-500 bg-green-50">Completed</Badge>

// Avatar
<Avatar>
  <AvatarImage src="/avatar.jpg" alt="User" />
  <AvatarFallback>JD</AvatarFallback>
</Avatar>

// Avatar group
<div className="flex -space-x-2">
  {users.map((user) => (
    <Avatar key={user.id}>
      <AvatarImage src={user.avatar} alt={user.name} />
      <AvatarFallback>{user.initials}</AvatarFallback>
    </Avatar>
  ))}
</div>

// Separator
<Separator /> {/* Horizontal */}
<Separator orientation="vertical" /> {/* Vertical */}

// Table
<Table>
  <TableHeader>
    <TableRow>
      <TableHead>Name</TableHead>
      <TableHead>Status</TableHead>
      <TableHead>Email</TableHead>
      <TableHead className="text-right">Actions</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    {users.map((user) => (
      <TableRow key={user.id}>
        <TableCell>{user.name}</TableCell>
        <TableCell>
          <Badge variant={user.status === 'active' ? 'default' : 'secondary'}>
            {user.status}
          </Badge>
        </TableCell>
        <TableCell>{user.email}</TableCell>
        <TableCell className="text-right">
          <Button variant="ghost" size="sm">Edit</Button>
        </TableCell>
      </TableRow>
    ))}
  </TableBody>
</Table>

// Tabs
<Tabs defaultValue="account" className="w-full">
  <TabsList>
    <TabsTrigger value="account">Account</TabsTrigger>
    <TabsTrigger value="password">Password</TabsTrigger>
    <TabsTrigger value="settings">Settings</TabsTrigger>
  </TabsList>
  <TabsContent value="account">
    {/* Account settings */}
  </TabsContent>
  <TabsContent value="password">
    {/* Password settings */}
  </TabsContent>
  <TabsContent value="settings">
    {/* General settings */}
  </TabsContent>
</Tabs>
```

### 5. Feedback Components

```typescript
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert'
import { Toast, ToastDescription, ToastProvider, ToastTitle, ToastViewport } from '@/components/ui/toast'
import { useToast } from '@/components/ui/use-toast'

// Alert variants
<Alert>
  <AlertCircle className="h-4 w-4" />
  <AlertTitle>Default Alert</AlertTitle>
  <AlertDescription>This is a default alert message.</AlertDescription>
</Alert>

<Alert variant="destructive">
  <AlertCircle className="h-4 w-4" />
  <AlertTitle>Error</AlertTitle>
  <AlertDescription>An error occurred. Please try again.</AlertDescription>
</Alert>

// Toast
function Toaster() {
  const { toast } = useToast()

  return (
    <div>
      <Button onClick={() => toast({
        title: "Success",
        description: "Your changes have been saved."
      })}>
        Show Toast
      </Button>

      <Button onClick={() => toast({
        variant: "destructive",
        title: "Error",
        description: "Something went wrong."
      })}>
        Show Error Toast
      </Button>
    </div>
  )
}
```

---

## Professional Layout Patterns

### 1. Dashboard Layout

```typescript
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

export default function DashboardLayout() {
  return (
    <div className="min-h-screen">
      {/* Header */}
      <header className="border-b">
        <div className="container mx-auto py-4 flex items-center justify-between">
          <h1 className="text-2xl font-bold">Dashboard</h1>
          <nav className="flex items-center gap-4">
            <Button variant="ghost">Overview</Button>
            <Button variant="ghost">Analytics</Button>
            <Button variant="ghost">Settings</Button>
          </nav>
        </div>
      </header>

      {/* Main content */}
      <main className="container mx-auto py-6">
        {/* Stats grid */}
        <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4 mb-6">
          <Card>
            <CardHeader className="pb-2">
              <CardTitle className="text-sm font-medium">Total Users</CardTitle>
            </CardHeader>
            <CardContent>
              <div className="text-2xl font-bold">2,345</div>
            </CardContent>
          </Card>
          {/* More stats... */}
        </div>

        {/* Main card */}
        <Card>
          <CardHeader>
            <CardTitle>Recent Activity</CardTitle>
          </CardHeader>
          <CardContent>{/* Content */}</CardContent>
        </Card>
      </main>
    </div>
  );
}
```

### 2. Sidebar Layout

```typescript
import { Button } from "@/components/ui/button";
import { Card } from "@/components/ui/card";
import { Sheet, SheetContent, SheetTrigger } from "@/components/ui/sheet";

export default function SidebarLayout() {
  return (
    <div className="min-h-screen flex">
      {/* Sidebar */}
      <aside className="w-64 border-r hidden md:block">
        <div className="p-6">
          <h1 className="text-xl font-bold mb-6">App Name</h1>
          <nav className="space-y-2">
            <Button variant="ghost" className="w-full justify-start">
              Dashboard
            </Button>
            <Button variant="ghost" className="w-full justify-start">
              Users
            </Button>
            <Button variant="ghost" className="w-full justify-start">
              Settings
            </Button>
          </nav>
        </div>
      </aside>

      {/* Mobile sidebar */}
      <Sheet>
        <SheetTrigger asChild>
          <Button variant="ghost" size="icon" className="md:hidden">
            <Menu />
          </Button>
        </SheetTrigger>
        <SheetContent side="left">
          <nav className="space-y-2 mt-6">
            <Button variant="ghost" className="w-full justify-start">
              Dashboard
            </Button>
            {/* More items */}
          </nav>
        </SheetContent>
      </Sheet>

      {/* Main content */}
      <main className="flex-1 p-6">
        <Card>
          <CardContent className="pt-6">{/* Content */}</CardContent>
        </Card>
      </main>
    </div>
  );
}
```

### 3. Form Page Layout

```typescript
import { Button } from "@/components/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";

export default function FormPage() {
  return (
    <div className="container mx-auto py-10">
      <div className="mx-auto max-w-2xl">
        <Card>
          <CardHeader>
            <CardTitle>Create Account</CardTitle>
            <CardDescription>
              Enter your information to create a new account
            </CardDescription>
          </CardHeader>
          <CardContent>
            <form className="space-y-4">
              <div className="space-y-2">
                <Label htmlFor="name">Full Name</Label>
                <Input id="name" placeholder="John Doe" />
              </div>

              <div className="space-y-2">
                <Label htmlFor="email">Email</Label>
                <Input id="email" type="email" placeholder="john@example.com" />
              </div>

              <div className="space-y-2">
                <Label htmlFor="password">Password</Label>
                <Input id="password" type="password" />
              </div>
            </form>
          </CardContent>
          <CardFooter className="flex justify-between">
            <Button variant="ghost">Cancel</Button>
            <Button>Create Account</Button>
          </CardFooter>
        </Card>
      </div>
    </div>
  );
}
```

---

## Theming & Customization

### 1. Theme Structure

```css
/* src/app/globals.css */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;

    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;

    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;

    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;

    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;

    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;

    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;

    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;

    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;

    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;

    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;

    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;

    /* ... more dark mode variables */
  }
}
```

### 2. Custom Theme Colors

```css
/* Blue theme for enterprise apps */
:root {
  --primary: 221 83% 53%;
  --primary-foreground: 210 40% 98%;
}

/* Green theme for success-focused apps */
:root {
  --primary: 142 76% 36%;
  --primary-foreground: 355.7 100% 97.3%;
}

/* Purple theme for creative apps */
:root {
  --primary: 262.1 83.3% 57.8%;
  --primary-foreground: 210 40% 98%;
}
```

### 3. Component-Level Customization

```typescript
// Override specific component styles using className
<Button className="bg-blue-600 hover:bg-blue-700 text-white">
  Custom Blue Button
</Button>

<Card className="border-2 border-blue-200 shadow-lg">
  <CardHeader>
    <CardTitle>Custom Card</CardTitle>
  </CardHeader>
</Card>
```

---

## Accessibility Best Practices

### 1. Proper Labeling

```typescript
// Always associate labels with inputs
<div className="space-y-2">
  <Label htmlFor="email">Email</Label>
  <Input id="email" type="email" aria-describedby="email-hint" />
  <p id="email-hint" className="text-sm text-muted-foreground">
    We'll never share your email
  </p>
</div>

// Use aria-label for icon-only buttons
<Button aria-label="Close">
  <X className="h-4 w-4" />
</Button>
```

### 2. Keyboard Navigation

```typescript
// Ensure all interactive elements are keyboard accessible
<Button>Accessible Button</Button>

// Use Tabs for keyboard-navigable content
<Tabs defaultValue="tab1">
  <TabsList>
    <TabsTrigger value="tab1">Tab 1</TabsTrigger>
    <TabsTrigger value="tab2">Tab 2</TabsTrigger>
  </TabsList>
  {/* Content */}
</Tabs>
```

### 3. Focus Indicators

```typescript
// shadcn/ui components have built-in focus states
// Ensure they're visible by using proper contrast

// Custom focus styles can be added
<Button className="focus:ring-2 focus:ring-offset-2">Custom Focus</Button>
```

---

## Common Patterns

### 1. Loading States

```typescript
import { Skeleton } from '@/components/ui/skeleton'

// Skeleton loader
<Card>
  <CardHeader>
    <Skeleton className="h-5 w-1/2" />
    <Skeleton className="h-4 w-1/4 mt-2" />
  </CardHeader>
  <CardContent>
    <Skeleton className="h-32 w-full" />
  </CardContent>
</Card>

// Button loading state
<Button disabled>
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Loading...
</Button>
```

### 2. Empty States

```typescript
import { EmptyState } from "@/components/features/empty-state";

// Using Card for empty state
<Card>
  <CardContent className="flex flex-col items-center justify-center py-16">
    <Inbox className="h-12 w-12 text-muted-foreground mb-4" />
    <h3 className="text-lg font-semibold mb-2">No data found</h3>
    <p className="text-sm text-muted-foreground text-center mb-4">
      Get started by creating your first item.
    </p>
    <Button>Create New</Button>
  </CardContent>
</Card>;
```

### 3. Confirmation Dialogs

```typescript
import {
  AlertDialog,
  AlertDialogAction,
  AlertDialogCancel,
  AlertDialogContent,
  AlertDialogDescription,
  AlertDialogFooter,
  AlertDialogHeader,
  AlertDialogTitle,
} from "@/components/ui/alert-dialog";

function DeleteConfirmDialog() {
  return (
    <AlertDialog>
      <AlertDialogContent>
        <AlertDialogHeader>
          <AlertDialogTitle>Are you sure?</AlertDialogTitle>
          <AlertDialogDescription>
            This action cannot be undone. This will permanently delete the item.
          </AlertDialogDescription>
        </AlertDialogHeader>
        <AlertDialogFooter>
          <AlertDialogCancel>Cancel</AlertDialogCancel>
          <AlertDialogAction className="bg-destructive text-destructive-foreground hover:bg-destructive/90">
            Delete
          </AlertDialogAction>
        </AlertDialogFooter>
      </AlertDialogContent>
    </AlertDialog>
  );
}
```

---

## Component Reference

### Available Components

**Form Components:**

- Button, Input, Textarea, Label, Select, Checkbox, Switch
- Slider, ToggleGroup, RadioGroup

**Layout Components:**

- Card, Separator, Sheet, Drawer
- Collapsible, Accordion, Tabs, ScrollArea

**Feedback Components:**

- Alert, Toast, Skeleton, Progress
- Badge, Avatar, Alert Dialog

**Data Display:**

- Table, Calendar, DatePicker
- Command (command palette)

**Navigation:**

- Breadcrumb, Pagination, Navigation Menu
- Menubar, Context Menu, Dropdown Menu

**Other:**

- Dialog, Popover, Tooltip
- Form (with react-hook-form integration)

---

## Common Pitfalls & Solutions

### ❌ Problem: Overriding shadcn/ui components

**✅ Solution:** Use className for customization, don't modify source

```typescript
// Bad: Modify @/components/ui/button.tsx

// Good: Use className for customization
<Button className="custom-styles">Button</Button>
```

### ❌ Problem: Not using semantic HTML

**✅ Solution:** Use proper semantic structure

```typescript
// Bad
<div className="font-bold">Title</div>

// Good
<h1 className="font-bold">Title</h1>
```

### ❌ Problem: Missing accessibility attributes

**✅ Solution:** Always include proper ARIA attributes

```typescript
<Button aria-label="Close">
  <X />
</Button>
```

---

## Token-Efficient Prompt Templates

### Build Professional UI

```
Build <UI_ELEMENT> with shadcn/ui:
- Use components from @/components/ui/
- Follow professional design patterns
- Include loading/empty/error states
- Accessibility compliant
- Responsive design
```

### Customize Theme

```
Customize shadcn/ui theme:
- Primary color: <COLOR>
- Dark mode support
- Professional color palette
- Maintain contrast ratios (WCAG AA)
```

### Create Layout

```
Create professional layout:
- Header navigation
- Content area with cards
- Responsive sidebar (mobile: sheet, desktop: aside)
- Sticky footer
```

---

## Quick Commands

```bash
# Add new components
bunx shadcn@latest add <component-name>

# Add multiple components
bunx shadcn@latest add button input card dialog

# Update all components
bunx shadcn@latest add --overwrite

# View available components
bunx shadcn@latest add --help
```

---

## Important Reminders

1. **Don't modify** @/components/ui/ files - use className for customization
2. **Semantic HTML** - use proper elements (header, nav, main, etc.)
3. **Accessibility first** - all components are accessible by default
4. **Responsive design** - use Tailwind responsive prefixes
5. **Theme consistency** - use CSS variables for theming
6. **Professional patterns** - follow enterprise design standards
7. **Loading states** - use Skeleton for async content
8. **Empty states** - provide clear guidance to users

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
