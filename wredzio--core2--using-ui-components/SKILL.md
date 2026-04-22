---
name: using-ui-components
description: Use components from components/ui as building blocks for complex components instead of creating custom implementations Use when this capability is needed.
metadata:
  author: wredzio
---

# UI Components Usage

Always use components from `@/components/ui` as building blocks for complex components. These are shadcn/ui based components with consistent styling, accessibility, and behavior.

## Usage Pattern

### Build Complex Components from UI Primitives

```typescript
// ✅ Good - Using UI components as building blocks
'use client';

import { Button } from '@/components/ui/button/button';
import {
  Dialog,
  DialogClose,
  DialogContent,
  DialogFooter,
  DialogHeader,
  DialogMain,
  DialogTitle,
} from '@/components/ui/dialog/dialog';
import { Separator } from '@/components/ui/separator/separator';
import { Tabs, TabsList, TabsTrigger } from '@/components/ui/tabs/tabs';
import { Typography } from '@/components/ui/typography/typography';

export const FiltersDialog = (props: FiltersDialogProps) => {
  const { customerNumbersAndMeteringPoints, meteringDetails } = props;
  const [open, setOpen] = React.useState(false);

  return (
    <Dialog open={open} onOpenChange={setOpen}>
      <DialogContent className='dialog-content-tabs-width gap-6'>
        <DialogClose />
        <DialogHeader>
          <DialogTitle>
            <LocalizedT tKey='MasterData.GroupsCreateFiltersDialogTitle' />
          </DialogTitle>
        </DialogHeader>
        <DialogMain className='-ml-6'>
          <Tabs defaultValue='tab1'>
            <TabsList>
              <TabsTrigger value='tab1'>Tab 1</TabsTrigger>
              <TabsTrigger value='tab2'>Tab 2</TabsTrigger>
            </TabsList>
          </Tabs>
        </DialogMain>
        <DialogFooter>
          <Button variant='link'>Clear</Button>
          <Button>Apply</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  );
};
```

### Use Variants and Composition

UI components support variants via `class-variance-authority`:

```typescript
// Button variants
<Button variant='default'>Primary Action</Button>
<Button variant='destructive'>Delete</Button>
<Button variant='outline'>Secondary Action</Button>
<Button variant='ghost'>Subtle Action</Button>
<Button variant='link'>Link Style</Button>

// Button sizes
<Button size='default'>Default</Button>
<Button size='sm'>Small</Button>
<Button size='lg'>Large</Button>
<Button size='icon'><IconComponent /></Button>

// Typography variants
<Typography type='heading' tag='h1' variant='xl'>Heading</Typography>
<Typography type='body' tag='p' variant='small' weight='regular'>Body</Typography>
```

### Combine with Tailwind Classes

Use `cn()` utility to merge custom classes:

```typescript
import { cn } from '@/lib/utils/cn';
import { Button } from '@/components/ui/button/button';

<Button className={cn('w-full', isActive && 'bg-accent')}>
  Custom Styled Button
</Button>
```

### Use with React Hook Form

UI components integrate with React Hook Form:

```typescript
import { FormField, FormItem, FormLabel, FormControl, FormMessage } from '@/components/ui/form/form';
import { Input } from '@/components/ui/input/input';

<FormField
  control={form.control}
  name='groupName'
  render={({ field }) => (
    <FormItem>
      <FormLabel>Group Name</FormLabel>
      <FormControl>
        <Input {...field} />
      </FormControl>
      <FormMessage />
    </FormItem>
  )}
/>
```

## Component Selection Guide

### When to Use Which Component

| Use Case             | Component                | Example                                 |
| -------------------- | ------------------------ | --------------------------------------- |
| Modal dialog         | Dialog                   | Filters, confirmations, forms           |
| Tabbed interface     | Tabs                     | Multi-section forms, navigation         |
| Collapsible sections | Accordion                | FAQ, settings groups                    |
| Data tables          | ClientDataTable or Table | Lists with sorting, filtering           |
| Form inputs          | Input, Select, Checkbox  | User input fields                       |
| Actions              | Button                   | Primary, secondary, destructive actions |
| Navigation           | Tabs, Wizard             | Multi-step flows                        |
| Feedback             | Alert, Tooltip           | Messages, help text                     |
| Loading states       | Loader, Skeleton         | Async data loading                      |
| Cards                | Card                     | Content containers                      |

## Custom Component Patterns

### Feature-Specific Wrappers

Create thin wrappers around UI components for feature-specific logic:

```typescript
// ✅ Good - Wrapper with feature logic
const ClearFiltersButton = () => {
  const { resetState } = useFiltersDialogContext();

  return (
    <Button variant='link' onClick={resetState}>
      <LocalizedT tKey='General.DialogClearFiltersButton' />
    </Button>
  );
};
```

### Compound Components

Compose UI components for complex interactions:

```typescript
// ✅ Good - Composition of UI primitives
export const GroupSelector = (props: GroupSelectorProps) => {
  return (
    <div className={cn('h-full overflow-y-auto rounded-md border bg-card', scrollbar)}>
      <Tabs orientation='vertical' value={currentValue} onValueChange={handleChange}>
        <TabsList className='h-full w-full gap-0'>
          <TabsTrigger value='all'>
            <Typography type='body' tag='p' variant='small'>
              All Groups
            </Typography>
          </TabsTrigger>
          <Separator className='bg-muted' />
          {groups.map(group => (
            <TabsTrigger key={group.id} value={group.id}>
              <Typography type='body' tag='p' variant='small'>
                {group.name}
              </Typography>
            </TabsTrigger>
          ))}
        </TabsList>
      </Tabs>
    </div>
  );
};
```

## Accessibility Considerations

UI components have built-in accessibility features:

- **Keyboard navigation**: Focus management, arrow keys
- **ARIA attributes**: Proper roles, labels, descriptions
- **Screen reader support**: Semantic HTML, announcements
- **Focus indicators**: Visible focus states

Always preserve these when customizing:

```typescript
// ✅ Good - Preserves accessibility
<Button
  aria-label='Clear all filters'
  onClick={handleClear}
>
  Clear
</Button>

// ❌ Bad - Custom button without accessibility
<div className='custom-button' onClick={handleClear}>
  Clear
</div>
```

## Common Mistakes to Avoid

❌ **Don't** recreate existing UI components:

```typescript
// Bad - custom button implementation
const CustomButton = ({ children, onClick }) => (
  <div className='px-4 py-2 bg-blue-500 rounded cursor-pointer' onClick={onClick}>
    {children}
  </div>
);

// Good - use existing Button
import { Button } from '@/components/ui/button/button';
<Button onClick={onClick}>{children}</Button>
```

❌ **Don't** use native HTML elements when UI components exist:

```typescript
// Bad
<input type='text' className='...' />
<button className='...'>Click</button>
<table>...</table>

// Good
import { Input } from '@/components/ui/input/input';
import { Button } from '@/components/ui/button/button';
import { Table } from '@/components/ui/table/table';

<Input />
<Button>Click</Button>
<Table>...</Table>
```

❌ **Don't** ignore variants - use them:

```typescript
// Bad - custom styling
<Button className='bg-red-500 text-white'>Delete</Button>

// Good - use variant
<Button variant='destructive'>Delete</Button>
```

❌ **Don't** fight the component API:

```typescript
// Bad - trying to force different behavior
<Dialog>
  <div className='custom-dialog-hack'>...</div>
</Dialog>

// Good - use the component's composition API
<Dialog>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>...</DialogTitle>
    </DialogHeader>
    <DialogMain>...</DialogMain>
  </DialogContent>
</Dialog>
```

✅ **Do** follow the established patterns:

- Import UI components from `@/components/ui/*`
- Use component variants instead of custom styling
- Compose components for complex UIs
- Preserve accessibility features
- Create thin wrappers for feature-specific logic
- Use `cn()` for conditional styling
- Follow component composition patterns (Header, Content, Footer)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wredzio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
