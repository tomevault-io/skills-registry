---
name: react-tailwind-patterns
description: React 18 + Tailwind CSS + shadcn/ui development patterns for Token Dashboard. Modern React hooks patterns, Tailwind utility-first styling, shadcn/ui component integration, proper component structure, state management with hooks, and performance optimization. Use when creating components, styling with Tailwind, integrating shadcn/ui components, or working with React code. Use when this capability is needed.
metadata:
  author: mbelenmontoya
---

# React + Tailwind + shadcn/ui Development Patterns

## Purpose

Comprehensive guide for React 18 development in Token Dashboard, emphasizing Tailwind CSS utility-first styling, shadcn/ui component integration, modern hooks patterns, and the ongoing migration from Chakra UI.

## When to Use This Skill

- Creating new React components
- Styling components with Tailwind CSS
- Integrating shadcn/ui components
- Using React hooks (useState, useEffect, useCallback, useMemo)
- Component organization and file structure
- Migrating from Chakra UI to Tailwind + shadcn/ui
- Performance optimization
- TypeScript with React

---

## Quick Start

### New Component Checklist

Creating a React component? Follow this checklist:

- [ ] Use functional components with hooks (no class components)
- [ ] Use `.jsx` or `.tsx` extension (TypeScript preferred for complex logic)
- [ ] Import React: `import { useState, useEffect } from 'react'`
- [ ] Use Tailwind utility classes for styling
- [ ] Import shadcn/ui components from `@/components/ui/`
- [ ] Define prop types with PropTypes or TypeScript interfaces
- [ ] Use hooks for state management and side effects
- [ ] Implement proper loading and error states
- [ ] Add `data-testid` attributes for testing
- [ ] Follow ProjectHub color scheme for consistency

### Component Structure Template

```jsx
import { useState, useEffect } from 'react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';

/**
 * ComponentName - Brief description
 * @param {Object} props - Component props
 */
export function ComponentName({ prop1, prop2, onAction }) {
  // State
  const [state, setState] = useState(initialValue);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  // Effects
  useEffect(() => {
    // Side effects here
    return () => {
      // Cleanup
    };
  }, [dependencies]);

  // Event handlers
  const handleAction = async () => {
    try {
      setLoading(true);
      setError(null);
      // Action logic
      onAction?.(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  // Render
  return (
    <div className="container mx-auto p-6">
      <h2 className="text-2xl font-semibold text-gray-900 dark:text-gray-50 mb-4">
        Component Title
      </h2>

      {error && (
        <div className="bg-red-50 dark:bg-red-900/20 border border-red-200 dark:border-red-800 rounded-md p-4 mb-4">
          <p className="text-red-800 dark:text-red-200">{error}</p>
        </div>
      )}

      <div className="space-y-4">
        <Input
          placeholder="Enter value"
          value={state}
          onChange={(e) => setState(e.target.value)}
          className="w-full"
        />

        <Button
          onClick={handleAction}
          disabled={loading}
          className="bg-teal-600 hover:bg-teal-700 text-white"
        >
          {loading ? 'Processing...' : 'Submit'}
        </Button>
      </div>
    </div>
  );
}
```

---

## Tailwind CSS Patterns

### Token Dashboard Color Scheme

**Follow ProjectHub color scheme for consistency:**

```jsx
// Primary Actions
className="bg-teal-600 hover:bg-teal-700 text-white"

// Backgrounds
className="bg-white dark:bg-gray-800"

// Borders
className="border border-gray-200 dark:border-gray-700"

// Text Primary
className="text-gray-900 dark:text-gray-50"

// Text Secondary
className="text-gray-500 dark:text-gray-400"

// Success State
className="bg-green-50 dark:bg-green-900/20 text-green-800 dark:text-green-200"

// Error State
className="bg-red-50 dark:bg-red-900/20 text-red-800 dark:text-red-200"

// Warning State
className="bg-yellow-50 dark:bg-yellow-900/20 text-yellow-800 dark:text-yellow-200"
```

### Common Layout Patterns

```jsx
// Container with padding
<div className="container mx-auto p-6">

// Card layout
<div className="bg-white dark:bg-gray-800 rounded-lg border border-gray-200 dark:border-gray-700 p-6">

// Flex row with gap
<div className="flex items-center gap-4">

// Grid layout
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">

// Responsive spacing
<div className="space-y-4">

// Full width with max-width
<div className="w-full max-w-4xl mx-auto">
```

### Button Patterns

```jsx
// Primary button
<button className="px-4 py-2 bg-teal-600 hover:bg-teal-700 text-white rounded-md transition-colors">
  Primary Action
</button>

// Secondary button
<button className="px-4 py-2 border border-gray-300 dark:border-gray-600 hover:bg-gray-50 dark:hover:bg-gray-700 rounded-md transition-colors">
  Secondary Action
</button>

// Danger button
<button className="px-4 py-2 bg-red-600 hover:bg-red-700 text-white rounded-md transition-colors">
  Delete
</button>

// Disabled button
<button disabled className="px-4 py-2 bg-gray-300 dark:bg-gray-700 text-gray-500 dark:text-gray-400 cursor-not-allowed rounded-md">
  Disabled
</button>
```

### Form Input Patterns

```jsx
// Standard input
<input
  type="text"
  className="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-white dark:bg-gray-800 text-gray-900 dark:text-gray-50 focus:outline-none focus:ring-2 focus:ring-teal-500"
  placeholder="Enter text"
/>

// Select dropdown
<select className="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-white dark:bg-gray-800">
  <option>Option 1</option>
  <option>Option 2</option>
</select>

// Textarea
<textarea
  className="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-white dark:bg-gray-800 resize-none"
  rows="4"
  placeholder="Enter description"
/>
```

---

## shadcn/ui Integration

### Available Components

Token Dashboard uses these shadcn/ui components:

- **Dialog** - Modals and dialogs
- **Button** - Interactive buttons with variants
- **Input** - Form inputs with validation
- **Table** - Data tables
- **Badge** - Status indicators
- **Checkbox** - Checkboxes and toggles
- **Switch** - Toggle switches
- **Sheet** - Side panels and drawers
- **Progress** - Progress indicators

### Import Pattern

```jsx
// Import from @/components/ui/
import { Button } from '@/components/ui/button';
import { Dialog, DialogContent, DialogHeader, DialogTitle } from '@/components/ui/dialog';
import { Input } from '@/components/ui/input';
```

### Dialog Pattern (Modal)

```jsx
import { Dialog, DialogContent, DialogHeader, DialogTitle } from '@/components/ui/dialog';
import { Button } from '@/components/ui/button';

function MyModal({ isOpen, onClose, onSave }) {
  const [formData, setFormData] = useState({ name: '', value: '' });

  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="sm:max-w-md">
        <DialogHeader>
          <DialogTitle>Create New Token</DialogTitle>
        </DialogHeader>

        <div className="space-y-4 py-4">
          <Input
            placeholder="Token name"
            value={formData.name}
            onChange={(e) => setFormData({ ...formData, name: e.target.value })}
          />
          <Input
            placeholder="Token value"
            value={formData.value}
            onChange={(e) => setFormData({ ...formData, value: e.target.value })}
          />
        </div>

        <div className="flex justify-end gap-2">
          <Button variant="outline" onClick={onClose}>
            Cancel
          </Button>
          <Button onClick={() => onSave(formData)}>
            Save
          </Button>
        </div>
      </DialogContent>
    </Dialog>
  );
}
```

---

## React Hooks Patterns

### useState - State Management

```jsx
// Simple state
const [count, setCount] = useState(0);

// Object state
const [form, setForm] = useState({
  name: '',
  email: '',
  category: 'color'
});

// Update object state
setForm({ ...form, name: 'new-name' });

// Functional update (when new state depends on old)
setCount(prev => prev + 1);
```

### useEffect - Side Effects

```jsx
// Run once on mount
useEffect(() => {
  fetchData();
}, []);

// Run when dependency changes
useEffect(() => {
  if (token) {
    loadUserData(token);
  }
}, [token]);

// Cleanup on unmount
useEffect(() => {
  const interval = setInterval(() => {
    // Do something
  }, 1000);

  return () => clearInterval(interval);
}, []);
```

### useCallback - Memoize Functions

```jsx
// Prevent function recreation on every render
const handleSave = useCallback(async (data) => {
  await saveToken(data);
  onSuccess();
}, [onSuccess]);

// Pass to child components
<ChildComponent onSave={handleSave} />
```

### useMemo - Memoize Computed Values

```jsx
// Expensive computation
const filteredTokens = useMemo(() => {
  return tokens.filter(token =>
    token.category === selectedCategory &&
    token.name.includes(searchQuery)
  );
}, [tokens, selectedCategory, searchQuery]);

// Don't overuse - only for expensive operations
```

---

## Migration from Chakra UI

### Common Replacements

```jsx
// Chakra UI → Tailwind + shadcn/ui

// Button
<Button colorPalette="teal"> → <Button className="bg-teal-600 hover:bg-teal-700">

// Input
<Input placeholder="..." /> → <Input placeholder="..." className="..." />

// Box/Container
<Box p={4}> → <div className="p-4">

// Flex
<Flex gap={4}> → <div className="flex gap-4">

// Grid
<Grid columns={3}> → <div className="grid grid-cols-3 gap-4">

// Text
<Text color="gray.500"> → <p className="text-gray-500 dark:text-gray-400">

// Heading
<Heading size="lg"> → <h2 className="text-2xl font-semibold">
```

### Avoid Mixed Usage

```jsx
// ❌ BAD - Mixing Chakra and Tailwind
<Button colorPalette="teal" className="px-4">

// ✅ GOOD - Pure Tailwind
<button className="px-4 py-2 bg-teal-600 hover:bg-teal-700 text-white rounded-md">
```

---

## Performance Optimization

### Avoid Unnecessary Re-renders

```jsx
// ❌ BAD - Creates new object every render
<ChildComponent config={{ option: value }} />

// ✅ GOOD - Stable reference
const config = useMemo(() => ({ option: value }), [value]);
<ChildComponent config={config} />

// ❌ BAD - Creates new function every render
<button onClick={() => handleClick(id)}>

// ✅ GOOD - Memoized handler
const handleClick = useCallback(() => {
  handleAction(id);
}, [id, handleAction]);
<button onClick={handleClick}>
```

### Lazy Loading

```jsx
// Lazy load heavy components
import { lazy, Suspense } from 'react';

const TokenManager = lazy(() => import('./components/TokenManager'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <TokenManager />
    </Suspense>
  );
}
```

---

## Testing Patterns

### Component Testing with Vitest

```jsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import { MyComponent } from './MyComponent';

describe('MyComponent', () => {
  it('renders correctly', () => {
    render(<MyComponent />);
    expect(screen.getByText('Expected Text')).toBeInTheDocument();
  });

  it('handles user interaction', async () => {
    const handleClick = vi.fn();
    render(<MyComponent onAction={handleClick} />);

    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalled();
  });
});
```

---

## Resource Files

- [component-patterns.md](resources/component-patterns.md) - Advanced component patterns
- [styling-guide.md](resources/styling-guide.md) - Complete Tailwind styling reference
- [hooks-best-practices.md](resources/hooks-best-practices.md) - React hooks deep dive
- [migration-guide.md](resources/migration-guide.md) - Chakra UI to Tailwind migration guide

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbelenmontoya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
