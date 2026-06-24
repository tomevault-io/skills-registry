---
name: frontend-component
description: Generate React functional components with TypeScript for React 19 and Vite 6. Use this when asked to create UI components, buttons, cards, forms, modals, or any React component. Use when this capability is needed.
metadata:
  author: sliard
---

# React Component Generation

Generate React components following project conventions for React 19, TypeScript 5.x, and Vite 6.

## Component Structure

```tsx
import { type FC } from 'react';

interface ComponentNameProps {
  // Props definition
}

export const ComponentName: FC<ComponentNameProps> = ({ /* destructured props */ }) => {
  return (
    <div className="component-name">
      {/* Content */}
    </div>
  );
};
```

## Naming Conventions

- File name: PascalCase matching component name (`ProductCard.tsx`)
- Component: PascalCase (`ProductCard`)
- Props interface: `{ComponentName}Props`
- Hooks: camelCase with `use` prefix (`useProducts`)

## Rules

1. **Functional components only** - No class components
2. **Named exports** - No default exports
3. **TypeScript strict** - All props must be typed
4. **Props interface** - Always define a Props interface

## Props Patterns

```tsx
interface ButtonProps {
  children: React.ReactNode;           // Required content
  variant?: 'primary' | 'secondary';   // Optional with union type
  disabled?: boolean;                  // Optional boolean
  onClick?: () => void;                // Optional callback
  onSubmit?: (data: FormData) => void; // Callback with parameter
}
```

## Component Examples

### Simple Component

```tsx
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  onClick?: () => void;
}

export const Button: FC<ButtonProps> = ({
  children,
  variant = 'primary',
  size = 'md',
  disabled = false,
  onClick,
}) => {
  return (
    <button
      className={`btn btn--${variant} btn--${size}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
};
```

### Component with State

```tsx
interface CounterProps {
  initialValue?: number;
  onChange?: (value: number) => void;
}

export const Counter: FC<CounterProps> = ({ initialValue = 0, onChange }) => {
  const [count, setCount] = useState(initialValue);

  const increment = () => {
    const newValue = count + 1;
    setCount(newValue);
    onChange?.(newValue);
  };

  return (
    <div className="counter">
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
};
```

### Component with Loading/Error States

```tsx
interface ProductListProps {
  categoryId?: string;
}

export const ProductList: FC<ProductListProps> = ({ categoryId }) => {
  const { products, loading, error, refetch } = useProducts({ categoryId });

  if (loading) return <Spinner />;
  
  if (error) return <ErrorMessage message={error.message} onRetry={refetch} />;
  
  if (products.length === 0) return <EmptyState message="No products found" />;

  return (
    <div className="product-list">
      {products.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
};
```

### Form Component

```tsx
interface ProductFormProps {
  initialData?: Partial<ProductRequest>;
  onSubmit: (data: ProductRequest) => Promise<void>;
  isLoading?: boolean;
}

export const ProductForm: FC<ProductFormProps> = ({
  initialData,
  onSubmit,
  isLoading = false,
}) => {
  const [formData, setFormData] = useState<ProductRequest>({
    name: initialData?.name ?? '',
    price: initialData?.price ?? 0,
  });

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    await onSubmit(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={formData.name}
        onChange={(e) => setFormData(prev => ({ ...prev, name: e.target.value }))}
        disabled={isLoading}
      />
      <button type="submit" disabled={isLoading}>
        {isLoading ? 'Saving...' : 'Save'}
      </button>
    </form>
  );
};
```

## Accessibility

- Use semantic HTML (`<button>`, `<nav>`, `<article>`)
- Add `aria-label` for icon buttons
- Support keyboard navigation
- Handle focus management for modals

## Performance

- Use `useCallback` for callbacks passed as props
- Use `React.memo` for frequently re-rendered pure components
- Use `React.lazy` for large components

---

## Form Generation with React Hook Form + Zod

### Required Dependencies

```json
{
  "dependencies": {
    "react-hook-form": "^7.50.0",
    "@hookform/resolvers": "^3.3.0",
    "zod": "^3.22.0"
  }
}
```

### Schema Definition

```typescript
// schemas/productSchema.ts
import { z } from 'zod';

export const productSchema = z.object({
  name: z.string().min(1, 'Le nom est obligatoire').max(255),
  price: z.number({ required_error: 'Le prix est obligatoire' }).positive('Doit être positif'),
  description: z.string().max(2000).optional(),
  categoryId: z.string().uuid().optional(),
});

export type ProductFormData = z.infer<typeof productSchema>;
```

### Form Component

```typescript
// components/ProductForm.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { productSchema, type ProductFormData } from '../schemas/productSchema';

interface ProductFormProps {
  defaultValues?: Partial<ProductFormData>;
  onSubmit: (data: ProductFormData) => Promise<void>;
  onCancel?: () => void;
  submitLabel?: string;
}

export const ProductForm: React.FC<ProductFormProps> = ({
  defaultValues, onSubmit, onCancel, submitLabel = 'Créer',
}) => {
  const {
    register, handleSubmit, formState: { errors, isSubmitting }, reset,
  } = useForm<ProductFormData>({
    resolver: zodResolver(productSchema),
    defaultValues: { name: '', price: undefined, description: '', ...defaultValues },
  });

  const handleFormSubmit = async (data: ProductFormData) => {
    await onSubmit(data);
    reset();
  };

  return (
    <form onSubmit={handleSubmit(handleFormSubmit)} noValidate>
      <div className="form-group">
        <label htmlFor="name">Nom *</label>
        <input id="name" type="text" {...register('name')} aria-invalid={!!errors.name} />
        {errors.name && <span className="error" role="alert">{errors.name.message}</span>}
      </div>
      <div className="form-group">
        <label htmlFor="price">Prix (€) *</label>
        <input id="price" type="number" step="0.01" {...register('price', { valueAsNumber: true })} />
        {errors.price && <span className="error" role="alert">{errors.price.message}</span>}
      </div>
      <div className="form-actions">
        {onCancel && <button type="button" onClick={onCancel} disabled={isSubmitting}>Annuler</button>}
        <button type="submit" disabled={isSubmitting}>{isSubmitting ? 'Chargement...' : submitLabel}</button>
      </div>
    </form>
  );
};
```

### Common Schema Patterns

```typescript
// String validations
const email = z.string().email('Email invalide');
const password = z.string().min(8).regex(/[A-Z]/, 'Doit contenir une majuscule').regex(/[0-9]/, 'Doit contenir un chiffre');

// Number validations
const quantity = z.number().int().min(0);

// Array validations
const tags = z.array(z.string()).min(1, 'Au moins un tag requis');

// Enum validations
const status = z.enum(['DRAFT', 'PUBLISHED', 'ARCHIVED']);
```

### Dynamic Form (Array Fields)

```typescript
import { useForm, useFieldArray } from 'react-hook-form';

const orderSchema = z.object({
  items: z.array(
    z.object({
      productId: z.string().uuid(),
      quantity: z.number().int().min(1),
    })
  ).min(1),
});

export const OrderForm: React.FC = () => {
  const { register, control, handleSubmit } = useForm({
    resolver: zodResolver(orderSchema),
    defaultValues: { items: [{ productId: '', quantity: 1 }] },
  });
  const { fields, append, remove } = useFieldArray({ control, name: 'items' });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {fields.map((field, index) => (
        <div key={field.id}>
          <select {...register(`items.${index}.productId`)}>{/* Options */}</select>
          <input type="number" {...register(`items.${index}.quantity`, { valueAsNumber: true })} />
          {fields.length > 1 && <button type="button" onClick={() => remove(index)}>Supprimer</button>}
        </div>
      ))}
      <button type="button" onClick={() => append({ productId: '', quantity: 1 })}>Ajouter</button>
      <button type="submit">Commander</button>
    </form>
  );
};
```

### Reusable Input Component

```typescript
import { UseFormRegisterReturn } from 'react-hook-form';

interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
  registration: UseFormRegisterReturn;
}

export const Input: React.FC<InputProps> = ({ label, error, registration, type = 'text', ...props }) => (
  <div className="form-group">
    <label htmlFor={registration.name}>{label}</label>
    <input id={registration.name} type={type} {...registration} {...props} aria-invalid={!!error} />
    {error && <span className="error" role="alert">{error}</span>}
  </div>
);
```

### Form Hook Pattern

```typescript
export const useProductForm = ({ mode, productId, defaultValues, onSuccess }) => {
  const form = useForm<ProductFormData>({
    resolver: zodResolver(productSchema),
    defaultValues,
  });
  const createMutation = useCreateProduct({ onSuccess });
  const updateMutation = useUpdateProduct({ onSuccess });

  const onSubmit = async (data: ProductFormData) => {
    mode === 'create' ? await createMutation.createProduct(data) : await updateMutation.updateProduct(productId!, data);
  };

  return { ...form, onSubmit: form.handleSubmit(onSubmit), isLoading: createMutation.loading || updateMutation.loading };
};
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sliard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
