---
name: add-shadcn-component
description: Add a new shadcn/ui component to the project following the New York style variant Use when this capability is needed.
metadata:
  author: kwkraus
---

When adding a new shadcn/ui component to this Next.js 15 dashboard:

1. **Use the shadcn CLI** to add components:
   ```bash
   npx shadcn@latest add [component-name]
   ```
   This ensures proper configuration with the "New York" style variant.

2. **Component will be created** in `src/components/ui/[component-name].tsx` automatically.

3. **Import the component** using the path alias:
   ```tsx
   import { ComponentName } from "@/components/ui/component-name";
   ```

4. **Apply theme-aware styling** when customizing:
   - Use CSS custom properties: `hsl(var(--background))`, `hsl(var(--foreground))`
   - Follow the existing color system in `globals.css`
   - Ensure components work in both light and dark modes

5. **Use the cn() utility** for conditional classes:
   ```tsx
   import { cn } from "@/lib/utils";
   
   <div className={cn(
     "base-classes",
     isActive && "active-classes"
   )}>
   ```

6. **Available shadcn components** in this project:
   - avatar
   - button
   - card
   - dropdown-menu
   - separator
   - tooltip
   
   Check `src/components/ui/` for the full list.

7. **Component conventions**:
   - All UI components use TypeScript
   - Props are defined with clear interfaces
   - Components are exported as named exports
   - Follow Radix UI patterns for accessibility


## Examples

### Add a dialog component

```tsx
# Step 1: Add the component
npx shadcn@latest add dialog

# Step 2: Use it in your code
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog"

<Dialog>
  <DialogTrigger>Open</DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Title</DialogTitle>
      <DialogDescription>Description</DialogDescription>
    </DialogHeader>
  </DialogContent>
</Dialog>

```

### Add a badge component with custom styling

```tsx
# Add the component
npx shadcn@latest add badge

# Use with cn() for conditional styling
import { Badge } from "@/components/ui/badge"
import { cn } from "@/lib/utils"

<Badge className={cn(
  isActive && "bg-green-500",
  !isActive && "bg-gray-500"
)}>
  Status
</Badge>

```


## Tips

- The project uses "New York" style variant - components will match this automatically
- All shadcn components are built on Radix UI primitives for accessibility
- Components are pre-styled but can be customized with Tailwind classes
- The cn() utility handles class name conflicts properly


## Related Files

- `components.json`
- `src/components/ui/`
- `src/lib/utils.ts`


## Related Skills

- `add-dashboard-card`
- `add-theme-colors`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kwkraus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
