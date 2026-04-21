---
name: create-component
description: Create a new reusable UI component with proper structure, documentation, and best practices Use when this capability is needed.
metadata:
  author: luanps2
---

# Create Component Skill

## Purpose

This skill guides you through creating a new, reusable UI component following modern best practices, accessibility standards, and design system principles.

## When to Use

- Creating a new UI component from scratch
- Building a reusable component library
- Implementing design system components
- Need consistent component structure

## Prerequisites

- Design system or style guide (if available)
- Component requirements (props, behavior, variants)
- Technology stack (React, Vue, Svelte, etc.)

## Step-by-Step Process

### 1. Define Component API

```typescript
// Example: Button component API
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'outline' | 'ghost';
  size: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  loading?: boolean;
  icon?: ReactNode;
  onClick?: () => void;
  children: ReactNode;
}
```

**Checklist:**
- [ ] Define all props with types
- [ ] Document default values
- [ ] Identify required vs. optional props
- [ ] Consider variants and states

### 2. Create Component Structure

```
components/
  Button/
    index.ts          # Export
    Button.tsx        # Component implementation
    Button.module.css # Styles
    Button.test.tsx   # Tests
    Button.stories.tsx # Storybook (if applicable)
    README.md         # Documentation
```

**Checklist:**
- [ ] Organize files logically
- [ ] Separate concerns (logic, styles, tests)
- [ ] Add barrel export (index.ts)

### 3. Implement Component Logic

```tsx
// Button.tsx
import React, { forwardRef } from 'react';
import styles from './Button.module.css';

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ variant = 'primary', size = 'md', disabled, loading, icon, onClick, children }, ref) => {
    return (
      <button
        ref={ref}
        className={`${styles.button} ${styles[variant]} ${styles[size]}`}
        disabled={disabled || loading}
        onClick={onClick}
        aria-busy={loading}
      >
        {loading && <span className={styles.spinner} aria-hidden="true" />}
        {icon && <span className={styles.icon}>{icon}</span>}
        <span>{children}</span>
      </button>
    );
  }
);

Button.displayName = 'Button';
```

**Checklist:**
- [ ] Use forwardRef for ref forwarding
- [ ] Apply className composition
- [ ] Handle loading and disabled states
- [ ] Add displayName for debugging
- [ ] Implement event handlers

### 4. Style the Component

```css
/* Button.module.css */
.button {
  /* Base styles */
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  border: none;
  border-radius: var(--radius-md);
  font-family: var(--font-sans);
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  
  /* Remove default button styles */
  background: none;
  padding: 0;
  margin: 0;
}

.button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Sizes */
.sm {
  padding: 0.5rem 1rem;
  font-size: 0.875rem;
}

.md {
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
}

.lg {
  padding: 1rem 2rem;
  font-size: 1.125rem;
}

/* Variants */
.primary {
  background: var(--color-primary);
  color: white;
}

.primary:hover:not(:disabled) {
  background: var(--color-primary-dark);
}

.secondary {
  background: var(--color-secondary);
  color: white;
}

.outline {
  background: transparent;
  border: 2px solid var(--color-primary);
  color: var(--color-primary);
}

.ghost {
  background: transparent;
  color: var(--color-text);
}

.ghost:hover:not(:disabled) {
  background: var(--color-bg-hover);
}

/* Loading spinner */
.spinner {
  width: 1rem;
  height: 1rem;
  border: 2px solid currentColor;
  border-top-color: transparent;
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

**Checklist:**
- [ ] Use CSS custom properties (design tokens)
- [ ] Implement all variants and sizes
- [ ] Add hover, focus, active states
- [ ] Handle disabled states
- [ ] Add smooth transitions
- [ ] Ensure mobile-friendly touch targets (min 44x44px)

### 5. Ensure Accessibility

```tsx
// Enhanced with accessibility
<button
  ref={ref}
  className={className}
  disabled={disabled || loading}
  onClick={onClick}
  aria-busy={loading}
  aria-label={ariaLabel}
  aria-describedby={ariaDescribedby}
  type={type || 'button'} // Prevent accidental form submission
>
  {children}
</button>
```

**Accessibility Checklist:**
- [ ] Proper semantic HTML (`<button>` not `<div>`)
- [ ] Keyboard accessible (focusable, Enter/Space triggers)
- [ ] Clear focus indicator
- [ ] ARIA attributes (aria-label, aria-busy, aria-disabled)
- [ ] Color contrast meets WCAG AA (4.5:1 for text)
- [ ] Touch target size (min 44x44px)
- [ ] Screen reader announcements for state changes

### 6. Add Documentation

```markdown
# Button Component

A flexible, accessible button component with multiple variants and states.

## Usage

\`\`\`tsx
import { Button } from '@/components/Button';

<Button variant="primary" size="md" onClick={handleClick}>
  Click me
</Button>
\`\`\`

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'primary' \| 'secondary' \| 'outline' \| 'ghost' | 'primary' | Visual style variant |
| size | 'sm' \| 'md' \| 'lg' | 'md' | Button size |
| disabled | boolean | false | Disables the button |
| loading | boolean | false | Shows loading state |
| icon | ReactNode | - | Icon to display |
| onClick | () => void | - | Click handler |
| children | ReactNode | required | Button content |

## Examples

### Primary Button
\`\`\`tsx
<Button variant="primary">Primary</Button>
\`\`\`

### With Icon
\`\`\`tsx
<Button icon={<Icon name="plus" />}>Add Item</Button>
\`\`\`

### Loading State
\`\`\`tsx
<Button loading>Processing...</Button>
\`\`\`

## Accessibility

- Keyboard accessible (Tab, Enter, Space)
- ARIA attributes for state communication
- WCAG AA compliant color contrast
- Minimum 44x44px touch target
```

**Documentation Checklist:**
- [ ] Usage examples
- [ ] Props table with types and descriptions
- [ ] Visual examples for all variants
- [ ] Accessibility notes
- [ ] Common patterns and recipes

### 7. Write Tests

```tsx
// Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders children correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('disables button when disabled prop is true', () => {
    render(<Button disabled>Disabled</Button>);
    expect(screen.getByText('Disabled')).toBeDisabled();
  });

  it('shows loading state', () => {
    render(<Button loading>Loading</Button>);
    expect(screen.getByRole('button')).toHaveAttribute('aria-busy', 'true');
  });

  it('applies correct variant class', () => {
    const { container } = render(<Button variant="secondary">Secondary</Button>);
    expect(container.firstChild).toHaveClass('secondary');
  });
});
```

**Testing Checklist:**
- [ ] Test rendering with different props
- [ ] Test click handlers
- [ ] Test disabled state prevents clicks
- [ ] Test loading state
- [ ] Test accessibility attributes
- [ ] Test keyboard interactions

## Output Deliverables

When this skill completes, you should have:

✅ **Component file** with full implementation  
✅ **Styles** with design system tokens  
✅ **Tests** with good coverage  
✅ **Documentation** with usage examples  
✅ **Accessibility** WCAG AA compliant  
✅ **Type safety** with TypeScript (if applicable)  

## Best Practices

1. **Keep it simple**: Component should do one thing well
2. **Make it reusable**: Avoid hardcoding values, use props
3. **Compose over inheritance**: Build complex components from simple ones
4. **Accessibility first**: Always consider keyboard and screen reader users
5. **Document clearly**: Future you will thank present you
6. **Test thoroughly**: Tests are documentation that never lies

## Common Pitfalls to Avoid

❌ **Don't** include business logic in UI components  
❌ **Don't** make API calls directly from components  
❌ **Don't** use div when button/a is semantically correct  
❌ **Don't** forget to handle loading and error states  
❌ **Don't** hardcode colors/spacing (use design tokens)  
❌ **Don't** skip accessibility testing  

## Related Skills

- `review_ui_accessibility` - Review component for accessibility
- `create_api_endpoint` - If component needs backend data

## Framework-Specific Notes

### React
- Use `forwardRef` for ref forwarding
- Use hooks for state and effects
- Prefer functional components

### Vue
- Use `<script setup>` for composition API
- Emit events for parent communication
- Use slots for flexible content

### Svelte
- Use reactive declarations ($:)
- Bind directly to component props
- Use actions for DOM manipulation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
