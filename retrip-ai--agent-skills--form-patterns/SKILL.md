---
name: form-patterns
description: React Hook Form patterns with Zod validation. Use when implementing forms, validation schemas, form state management, edit forms with unsaved changes, or array fields. Use when this capability is needed.
metadata:
  author: retrip-ai
---

# Form Patterns

Complete guide to form handling using React Hook Form with Zod validation and Field components.

## Overview

This skill covers:
- **React Hook Form** - Form state management and validation
- **Zod Integration** - Type-safe schema validation
- **Field Components** - Styled, accessible form fields
- **UnsavedChangesBar** - Edit form pattern with change detection
- **tRPC Integration** - Form submission with mutations

## When to Apply

Reference these guidelines when:
- Creating or editing forms
- Implementing form validation
- Working with form state management
- Building edit forms with unsaved changes detection
- Handling array fields or dynamic forms
- Integrating forms with tRPC mutations

## Quick Reference

### Form Setup

```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';

const schema = z.object({
  email: z.string().email('Enter a valid email'),
  name: z.string().min(2, 'Name required'),
});

const form = useForm({
  resolver: zodResolver(schema),
  defaultValues: { email: '', name: '' },
  mode: 'onBlur', // For edit forms
});
```

### Field Pattern

```typescript
import { Controller } from 'react-hook-form';
import { Field, FieldLabel, FieldError } from '@/components/ui/field';
import { Input } from '@/components/ui/input';

<Controller
  name="email"
  control={form.control}
  render={({ field, fieldState }) => (
    <Field data-invalid={fieldState.invalid || undefined}>
      <FieldLabel htmlFor={field.name}>Email</FieldLabel>
      <Input
        {...field}
        id={field.name}
        type="email"
        aria-invalid={fieldState.invalid || undefined}
      />
      {fieldState.error && (
        <FieldError errors={[{ message: fieldState.error.message || '' }]} />
      )}
    </Field>
  )}
/>
```

### Edit Form Pattern (UnsavedChangesBar)

```typescript
import { useFormState } from 'react-hook-form';
import { UnsavedChangesBar } from '@/components/unsaved-changes-bar';

const { isDirty, isSubmitting } = useFormState({ control: form.control });
const isSaving = isSubmitting || mutation.isPending;

const onSubmit = form.handleSubmit(async (value) => {
  await mutation.mutateAsync(value);
  form.reset(value); // Clear isDirty
});

const handleDiscard = () => form.reset();

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
```

## References

Complete documentation with examples:

- `references/forms.md` - Comprehensive form patterns, field types, validation, integration

To find specific patterns:
```bash
grep -l "UnsavedChangesBar" references/*.md
grep -l "useFieldArray" references/*.md
grep -l "validation" references/*.md
```

## Core Patterns

### 1. Basic Form

**Three steps:**
1. Create Zod schema
2. Setup useForm with zodResolver
3. Build fields with Controller

```typescript
import { useForm, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';
import { Field, FieldLabel, FieldError } from '@/components/ui/field';
import { Input } from '@/components/ui/input';

const formSchema = z.object({
  email: z.string().email('Enter a valid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

function MyForm() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      email: '',
      password: '',
    },
  });

  function onSubmit(data: z.infer<typeof formSchema>) {
    console.log(data);
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      <Controller
        name="email"
        control={form.control}
        render={({ field, fieldState }) => (
          <Field data-invalid={fieldState.invalid || undefined}>
            <FieldLabel htmlFor={field.name}>Email</FieldLabel>
            <Input
              {...field}
              id={field.name}
              type="email"
              aria-invalid={fieldState.invalid || undefined}
            />
            {fieldState.error && (
              <FieldError errors={[{ message: fieldState.error.message || '' }]} />
            )}
          </Field>
        )}
      />

      <button type="submit">Submit</button>
    </form>
  );
}
```

### 2. Edit Forms with UnsavedChangesBar

**When to use:** Modifying existing data (profiles, settings, configurations)

**Critical pattern:**
```typescript
import { useForm, useFormState, Controller } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { UnsavedChangesBar } from '@/components/unsaved-changes-bar';
import { useMutation } from '@tanstack/react-query';
import { trpc } from '@/trpc';

function EditForm({ initialData }: Props) {
  // 1. Setup form with initial data
  const form = useForm({
    defaultValues: initialData,
    resolver: zodResolver(schema),
    mode: 'onBlur', // Validate on blur for better UX
  });

  // 2. Track dirty state reactively
  const { isDirty, isSubmitting } = useFormState({ control: form.control });

  // 3. Setup mutation
  const mutation = useMutation(
    trpc.organizations.update.mutationOptions()
  );

  const isSaving = isSubmitting || mutation.isPending;

  // 4. Submit handler
  const onSubmit = form.handleSubmit(async (value) => {
    await mutation.mutateAsync(value);
    form.reset(value); // CRITICAL: Reset with new values to clear isDirty
  });

  // 5. Discard handler
  const handleDiscard = () => form.reset(); // Return to initial state

  return (
    <form onSubmit={onSubmit}>
      {/* Fields */}

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
- ✅ Use `useFormState({ control: form.control })` for reactive `isDirty`
- ✅ Use `Controller` for controlled inputs
- ✅ Set `mode: 'onBlur'` for field validation
- ✅ Call `form.reset(value)` after successful save (not `form.reset()`)
- ✅ Use `Field`, `FieldLabel`, `FieldError` components

### 3. Create Forms (No UnsavedChangesBar)

**When to use:** Creating new entities (API keys, invitations, organizations)

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
    form.reset(); // Clear form after create
  });

  return (
    <form onSubmit={onSubmit}>
      {/* Fields */}

      <button disabled={mutation.isPending}>
        {mutation.isPending ? 'Creating...' : 'Create'}
      </button>
    </form>
  );
}
```

### 4. Array Fields

**Dynamic fields with add/remove:**

```typescript
import { useFieldArray } from 'react-hook-form';

const schema = z.object({
  emails: z
    .array(
      z.object({
        address: z.string().email('Enter a valid email'),
      })
    )
    .min(1, 'Add at least one email')
    .max(5, 'Maximum 5 emails'),
});

function EmailListForm() {
  const form = useForm({
    resolver: zodResolver(schema),
    defaultValues: {
      emails: [{ address: '' }],
    },
  });

  const { fields, append, remove } = useFieldArray({
    control: form.control,
    name: 'emails',
  });

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      {fields.map((field, index) => (
        <div key={field.id}> {/* Use field.id as key */}
          <Controller
            name={`emails.${index}.address`}
            control={form.control}
            render={({ field: controllerField, fieldState }) => (
              <Field data-invalid={fieldState.invalid}>
                <Input
                  {...controllerField}
                  type="email"
                  aria-invalid={fieldState.invalid}
                />
                {fieldState.invalid && <FieldError errors={[fieldState.error]} />}
              </Field>
            )}
          />

          <button type="button" onClick={() => remove(index)}>
            Remove
          </button>
        </div>
      ))}

      <button type="button" onClick={() => append({ address: '' })}>
        Add Email
      </button>

      <button type="submit">Save</button>
    </form>
  );
}
```

## Field Types

### Input

```typescript
<Controller
  name="name"
  control={form.control}
  render={({ field, fieldState }) => (
    <Field data-invalid={fieldState.invalid}>
      <FieldLabel htmlFor={field.name}>Name</FieldLabel>
      <Input {...field} id={field.name} />
      {fieldState.invalid && <FieldError errors={[fieldState.error]} />}
    </Field>
  )}
/>
```

### Textarea

```typescript
<Controller
  name="description"
  control={form.control}
  render={({ field, fieldState }) => (
    <Field data-invalid={fieldState.invalid}>
      <FieldLabel htmlFor={field.name}>Description</FieldLabel>
      <Textarea {...field} id={field.name} className="min-h-[120px]" />
      {fieldState.invalid && <FieldError errors={[fieldState.error]} />}
    </Field>
  )}
/>
```

### Select

```typescript
<Controller
  name="language"
  control={form.control}
  render={({ field, fieldState }) => (
    <Field data-invalid={fieldState.invalid}>
      <FieldLabel htmlFor={field.name}>Language</FieldLabel>
      <Select
        name={field.name}
        value={field.value}
        onValueChange={field.onChange}
      >
        <SelectTrigger id={field.name}>
          <SelectValue placeholder="Select" />
        </SelectTrigger>
        <SelectContent>
          <SelectItem value="en">English</SelectItem>
          <SelectItem value="es">Spanish</SelectItem>
        </SelectContent>
      </Select>
      {fieldState.invalid && <FieldError errors={[fieldState.error]} />}
    </Field>
  )}
/>
```

### Checkbox

```typescript
<Controller
  name="terms"
  control={form.control}
  render={({ field, fieldState }) => (
    <Field orientation="horizontal" data-invalid={fieldState.invalid}>
      <Checkbox
        id={field.name}
        checked={field.value}
        onCheckedChange={field.onChange}
      />
      <FieldLabel htmlFor={field.name}>Accept terms</FieldLabel>
      {fieldState.invalid && <FieldError errors={[fieldState.error]} />}
    </Field>
  )}
/>
```

### Switch

```typescript
<Controller
  name="twoFactor"
  control={form.control}
  render={({ field, fieldState }) => (
    <Field orientation="horizontal" data-invalid={fieldState.invalid}>
      <FieldContent>
        <FieldLabel htmlFor={field.name}>Two-factor auth</FieldLabel>
        <FieldDescription>Enable multi-factor authentication</FieldDescription>
      </FieldContent>
      <Switch
        id={field.name}
        checked={field.value}
        onCheckedChange={field.onChange}
      />
    </Field>
  )}
/>
```

## Validation Modes

| Mode | Behavior | Use Case |
|------|----------|----------|
| `onBlur` | Validates when field loses focus | **Edit forms** (recommended) |
| `onChange` | Validates on every keystroke | Real-time validation |
| `onSubmit` | Validates only on submit | Simple forms |
| `onTouched` | First blur, then every change | Balance between onBlur/onChange |
| `all` | Both blur and change | Strict validation |

**Recommendation:** Use `onBlur` for edit forms to avoid annoying users with errors while typing.

## tRPC Integration

```typescript
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { trpc } from '@/trpc';
import { toast } from '@/components/ui/toast';

function MyForm() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    ...trpc.memberProfiles.update.mutationOptions(),
    onSuccess: (data) => {
      toast.success('Saved successfully');

      // Invalidate related queries
      queryClient.invalidateQueries({
        queryKey: [['memberProfiles', 'getCurrent']],
      });

      // Clear isDirty
      form.reset(data);
    },
    onError: (error) => {
      toast.error(error.message);

      // Optionally set server errors to specific fields
      if (error.data?.code === 'CONFLICT') {
        form.setError('email', {
          message: 'Email already exists',
        });
      }
    },
  });

  const form = useForm({
    defaultValues: { ... },
    resolver: zodResolver(schema),
  });

  const onSubmit = form.handleSubmit((data) => {
    mutation.mutate(data);
  });

  return (
    <form onSubmit={onSubmit}>
      {/* Fields */}
      <button disabled={mutation.isPending}>
        {mutation.isPending ? 'Saving...' : 'Save'}
      </button>
    </form>
  );
}
```

## Error Handling

### Display Field Errors

```typescript
<Field data-invalid={fieldState.invalid}>
  <FieldLabel>Email</FieldLabel>
  <Input {...field} aria-invalid={fieldState.invalid} />
  {fieldState.invalid && (
    <FieldError errors={[{ message: fieldState.error.message || '' }]} />
  )}
</Field>
```

### Set Server Errors

```typescript
// In mutation onError
form.setError('email', {
  message: 'Email already exists',
});

// Form-level error
form.setError('root', {
  message: 'Something went wrong',
});
```

## Best Practices

### ✅ Do:

**Form Setup:**
- Use Zod schemas for validation
- Use `zodResolver` for integration
- Set `mode: 'onBlur'` for edit forms
- Provide default values

**Fields:**
- Use `Controller` for all controlled inputs
- Add `data-invalid` to `<Field>`
- Add `aria-invalid` to form controls
- Show error messages with `<FieldError>`
- Use semantic HTML (`htmlFor`, `id`, proper `type`)

**State Management:**
- Use `useFormState` for reactive states (`isDirty`, `isSubmitting`)
- Reset form after successful save: `form.reset(newValue)`
- Handle loading states during submission
- Invalidate queries after mutations

**UX:**
- Show loading states (`isPending`, `isSubmitting`)
- Disable submit button while saving
- Show success/error toasts
- Use `UnsavedChangesBar` for edit forms

### ❌ Don't:

**Form Setup:**
- ❌ Use uncontrolled inputs
- ❌ Skip validation schemas
- ❌ Use `any` types
- ❌ Mix controlled and uncontrolled inputs

**Edit Forms:**
- ❌ Use `form.reset()` without new values (won't clear `isDirty`)
- ❌ Forget `useFormState` for reactive `isDirty`
- ❌ Use `UnsavedChangesBar` for create forms

**Fields:**
- ❌ Skip error messages
- ❌ Forget accessibility attributes
- ❌ Use register() for complex components (use Controller instead)

**Integration:**
- ❌ Forget to invalidate queries after mutations
- ❌ Skip loading states
- ❌ Ignore error handling

## Common Patterns

### Form with Mutation

```typescript
const mutation = useMutation(trpc.organizations.update.mutationOptions());

const onSubmit = form.handleSubmit(async (data) => {
  await mutation.mutateAsync(data);
  form.reset(data);
});
```

### Conditional Validation

```typescript
const schema = z.object({
  type: z.enum(['individual', 'business']),
  businessName: z.string().optional(),
}).refine(
  (data) => data.type !== 'business' || data.businessName,
  {
    message: 'Business name required',
    path: ['businessName'],
  }
);
```

### Dependent Fields

```typescript
const type = form.watch('type');

{type === 'business' && (
  <Controller name="businessName" ... />
)}
```

## Related Skills

- `base-ui-design` - Field components and design guidelines
- `tanstack-comprehensive` - tRPC mutations and data invalidation

---

**Version:** 1.0.0
**Last updated:** 2026-01-14

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/retrip-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
