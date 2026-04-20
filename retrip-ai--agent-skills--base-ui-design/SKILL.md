---
name: base-ui-design
description: Base UI component patterns and design system guidelines. Use when creating or styling UI components, managing spacing, typography, border radius, icons, or following project design standards. Use when this capability is needed.
metadata:
  author: retrip-ai
---

# Base UI Design System

Complete guide to the project's design system using Base UI components and strict design guidelines.

## Overview

This skill covers:
- **Base UI Components** - Unstyled, accessible React primitives
- **Design Principles** - Typography, spacing, border radius standards
- **Form Patterns** - Edit forms, UnsavedChangesBar integration
- **Component Usage** - Custom UI components in `@/components/ui`

## When to Apply

Reference these guidelines when:
- Creating new UI components
- Styling existing components
- Working with forms (especially edit forms)
- Managing spacing, typography, or icons
- Ensuring accessibility compliance
- Following design consistency

## Quick Reference

### Design Principles (CRITICAL)

| Principle | Rule | Valid Values |
|-----------|------|--------------|
| **Typography** | Use default sizes ONLY | No `text-sm`, `text-xs`, etc. |
| **Spacing** | Powers of 2 | `gap-2`, `gap-4`, `gap-8`, `gap-16` |
| **Border Radius** | Always `rounded-md` | No `rounded-lg`, `rounded-full` |
| **Spacing Utils** | Use flex gap | `flex flex-col gap-*` NOT `space-y-*` |
| **Icons** | Icon suffix | `MonitorIcon` NOT `Monitor` |

### Component Hierarchy

```
@base-ui/react          # Unstyled primitives
    ↓
@/components/ui         # Custom styled components
    ↓
Page components         # App-specific usage
```

### Common Components

**Dialog:**
```typescript
import * as Dialog from '@base-ui/react/Dialog';

<Dialog.Root>
  <Dialog.Trigger className="px-4 py-2 bg-blue-500 rounded-md">
    Open
  </Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Backdrop className="fixed inset-0 bg-black/50" />
    <Dialog.Popup className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white rounded-md p-6">
      <Dialog.Title className="font-semibold mb-4">Title</Dialog.Title>
      <Dialog.Description className="mb-4">Description</Dialog.Description>
      <Dialog.Close className="px-4 py-2 bg-gray-200 rounded-md">Close</Dialog.Close>
    </Dialog.Popup>
  </Dialog.Portal>
</Dialog.Root>
```

**Popover:**
```typescript
import * as Popover from '@base-ui/react/Popover';

<Popover.Root>
  <Popover.Trigger>Open</Popover.Trigger>
  <Popover.Portal>
    <Popover.Popup className="bg-white rounded-md p-4">
      Content
    </Popover.Popup>
  </Popover.Portal>
</Popover.Root>
```

## References

Complete documentation with examples:

- `references/components.md` - Base UI components, custom UI, form patterns, accessibility

To find specific patterns:
```bash
grep -l "Dialog" references/*.md
grep -l "UnsavedChangesBar" references/*.md
grep -l "spacing" references/*.md
```

## Core Principles

### 1. Typography - Default Sizes Only

**❌ WRONG - Custom text sizes:**
```typescript
<p className="text-sm">Small text</p>
<span className="text-xs">Tiny text</span>
<h1 className="text-2xl">Heading</h1>
```

**✅ CORRECT - Default sizes:**
```typescript
<p>Regular text</p>
<span>Regular text</span>
<h1>Heading</h1>
```

Let component hierarchy and semantic HTML define visual hierarchy naturally.

### 2. Spacing - Powers of 2 Only

**❌ WRONG - Space utilities or odd gaps:**
```typescript
<div className="space-y-6">...</div>
<div className="flex gap-3">...</div>
<div className="flex gap-6">...</div>
```

**✅ CORRECT - Flex gap with powers of 2:**
```typescript
<div className="flex flex-col gap-8">...</div>
<div className="flex gap-4">...</div>
<div className="flex gap-16">...</div>
```

**Valid gap values:** `gap-2`, `gap-4`, `gap-8`, `gap-16` (in px: 8, 16, 32, 64)

### 3. Border Radius - Always `rounded-md`

**❌ WRONG - Other rounded values:**
```typescript
<div className="rounded-lg">...</div>
<div className="rounded-full">...</div>
<button className="rounded-xl">...</button>
```

**✅ CORRECT - Only `rounded-md`:**
```typescript
<div className="rounded-md">...</div>
<div className="rounded-md">...</div>
<button className="rounded-md">...</button>
```

### 4. Icons - Always Use Icon Suffix

**❌ WRONG - Import without Icon suffix:**
```typescript
import { Monitor, Moon, Sun } from 'lucide-react';

<Monitor className="w-4 h-4" />
```

**✅ CORRECT - Icon suffix:**
```typescript
import { MonitorIcon, MoonIcon, SunIcon } from 'lucide-react';
import { BadgeCheckIcon, Building2Icon, UsersIcon } from 'lucide-react';

<MonitorIcon className="w-4 h-4" />
```

This ensures consistent naming and clarity.

## Form Patterns

### Edit Forms (UnsavedChangesBar)

**When to use:** Modifying existing data (profiles, settings, configurations)

**Key features:**
- Bar appears when form becomes dirty
- Bar disappears when changes saved or discarded
- Validation on `onBlur` for fields, `onSubmit` for form
- Only calls tRPC mutation on submit

```typescript
import { useForm, useFormState, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { UnsavedChangesBar } from '@/components/unsaved-changes-bar';
import { Field, FieldLabel, FieldError } from '@/components/ui/field';

function EditForm({ initialData }: Props) {
  const form = useForm({
    defaultValues: initialData,
    resolver: zodResolver(schema),
    mode: 'onBlur', // Validate on blur
  });

  const { isDirty, isSubmitting } = useFormState({ control: form.control });
  const mutation = useMutation(trpc.organizations.update.mutationOptions());
  const isSaving = isSubmitting || mutation.isPending;

  const onSubmit = form.handleSubmit(async (value) => {
    await mutation.mutateAsync(value);
    form.reset(value); // Reset with new values to clear isDirty
  });

  const handleDiscard = () => form.reset(); // Return to initial state

  return (
    <form onSubmit={onSubmit}>
      <Controller
        name="name"
        control={form.control}
        render={({ field, fieldState }) => (
          <Field data-invalid={fieldState.invalid || undefined}>
            <FieldLabel>Name</FieldLabel>
            <Input {...field} aria-invalid={fieldState.invalid || undefined} />
            {fieldState.error && (
              <FieldError errors={[{ message: fieldState.error.message || '' }]} />
            )}
          </Field>
        )}
      />

      <UnsavedChangesBar
        show={isDirty}
        isSaving={isSaving}
        onDiscard={handleDiscard}
        labels={{
          unsavedChanges: 'Unsaved changes',
          discard: 'Discard',
          save: 'Save',
        }}
      />
    </form>
  );
}
```

**Critical requirements:**
- Use `useFormState({ control: form.control })` for reactive `isDirty`
- Use `Controller` for controlled inputs
- Use `Field`, `FieldLabel`, `FieldError` components
- Set `mode: 'onBlur'` for field validation
- Call `form.reset(value)` after successful save

### Create Forms (No UnsavedChangesBar)

**When to use:** Creating new entities (API keys, invitations, new organizations)

**Key features:**
- Submit immediately, no save bar
- Validation on `onBlur`
- Simpler pattern

```typescript
function CreateForm() {
  const form = useForm({
    defaultValues: { name: '' },
    resolver: zodResolver(schema),
    mode: 'onBlur',
  });

  const mutation = useMutation(trpc.apiKeys.create.mutationOptions());

  const onSubmit = form.handleSubmit(async (value) => {
    await mutation.mutateAsync(value);
    form.reset();
  });

  return (
    <form onSubmit={onSubmit}>
      <Controller
        name="name"
        control={form.control}
        render={({ field, fieldState }) => (
          <Field data-invalid={fieldState.invalid || undefined}>
            <FieldLabel>Name</FieldLabel>
            <Input {...field} />
            {fieldState.error && <FieldError errors={[{ message: fieldState.error.message || '' }]} />}
          </Field>
        )}
      />

      <button disabled={mutation.isPending}>
        {mutation.isPending ? 'Creating...' : 'Create'}
      </button>
    </form>
  );
}
```

## Base UI Components

### Available Components

| Component | Usage |
|-----------|-------|
| **Dialog** | Modals, alerts, confirmations |
| **Popover** | Tooltips, context menus |
| **Select** | Dropdowns, option selection |
| **Tabs** | Tab navigation |
| **Checkbox** | Boolean inputs |
| **Switch** | Toggle switches |
| **Slider** | Range inputs |

All components are:
- **Unstyled** - You control all styles
- **Accessible** - ARIA compliant, keyboard navigation
- **Composable** - Build complex UIs with simple primitives

### Custom UI Components

Located in `@/components/ui`:
```
src/components/ui/
├── button.tsx         # Button variants
├── dialog.tsx         # Pre-styled dialogs
├── field.tsx          # Form field components
├── input.tsx          # Input fields
├── select.tsx         # Select dropdowns
└── ...
```

**Prefer custom UI components** when available - they follow design guidelines automatically.

## Accessibility

Base UI provides:
- **Keyboard Navigation** - Tab, Arrow keys, Escape, Enter
- **ARIA Attributes** - Proper roles, labels, descriptions
- **Screen Readers** - State change announcements
- **Focus Management** - Traps focus in modals/dialogs

**Don't override ARIA attributes** unless absolutely necessary.

## Best Practices

### ✅ Do:

**Components:**
- Use custom UI components from `@/components/ui` first
- Fall back to Base UI for custom needs
- Combine Base UI primitives for complex UIs
- Leverage TypeScript for component props

**Design:**
- Follow spacing guidelines (powers of 2)
- Use `rounded-md` for all border radius
- Import icons with `Icon` suffix
- Use default text sizes
- Use flex gap instead of space utilities

**Forms:**
- Use `UnsavedChangesBar` for edit forms
- Use `Controller` for controlled inputs
- Validate on `onBlur`
- Reset form after successful save

### ❌ Don't:

**Typography:**
- ❌ `text-sm`, `text-xs`, `text-lg`, `text-xl`, etc.

**Spacing:**
- ❌ `space-y-*`, `space-x-*` utilities
- ❌ `gap-1`, `gap-3`, `gap-6`, `gap-12` (not powers of 2)

**Border Radius:**
- ❌ `rounded-lg`, `rounded-xl`, `rounded-full`, etc.

**Icons:**
- ❌ Import without `Icon` suffix

**Other:**
- ❌ Override ARIA attributes
- ❌ Create custom implementations of Base UI components
- ❌ Use UnsavedChangesBar for create forms

## Common Patterns

### Item Lists

```typescript
import { ItemGroup, Item, ItemContent, ItemTitle, ItemDescription } from '@/components/ui/item';

<ItemGroup className="gap-2">
  {items.map(item => (
    <Item key={item.id} className="px-0">
      <ItemContent>
        <ItemTitle>{item.title}</ItemTitle>
        <ItemDescription>{item.description}</ItemDescription>
      </ItemContent>
    </Item>
  ))}
</ItemGroup>
```

### Consistent Spacing

```typescript
// Page layout
<div className="flex flex-col gap-8">
  <header>...</header>
  <main>...</main>
  <footer>...</footer>
</div>

// Form fields
<div className="flex flex-col gap-4">
  <Field>...</Field>
  <Field>...</Field>
  <Field>...</Field>
</div>

// Horizontal layout
<div className="flex gap-2">
  <Button>Cancel</Button>
  <Button>Save</Button>
</div>
```

## Related Skills

- `form-patterns` - Detailed form handling with React Hook Form
- `tanstack-comprehensive` - Data fetching and mutations for forms

---

**Version:** 1.0.0
**Last updated:** 2026-01-14

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/retrip-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
