---
name: component-templates
description: UI component library templates (buttons, forms, cards, modals, etc.). Use when generating frontend UI components. Use when this capability is needed.
metadata:
  author: vanman2024
---

# Component Templates Skill

## Instructions

1. **Identify Component Type**:
   - Button, Input, Form, Card, Modal, Dropdown, etc.
   - Determine complexity (simple, complex, composite)
   - Check for existing similar components

2. **Select Template**:
   - Match component type to template library
   - Choose variant (primary, secondary, outlined, etc.)
   - Adapt to detected framework (React, Vue, Svelte)

3. **Generate Component**:
   - Load base template
   - Add props/properties for customization
   - Include accessibility features (ARIA labels, roles)
   - Add responsive design patterns
   - Include styling (CSS modules, Tailwind, styled-components)

4. **Add Variations**:
   - Size variants (sm, md, lg, xl)
   - Color variants (primary, secondary, success, danger)
   - State variants (disabled, loading, error)

## Component Library

### Basic Components
- Button (primary, secondary, outlined, ghost, link)
- Input (text, email, password, number, search)
- Textarea
- Checkbox, Radio, Switch
- Select, Dropdown
- Label, Badge, Tag

### Form Components
- Form wrapper
- FormField (label + input + error)
- FormGroup (multiple fields)
- Validation displays

### Layout Components
- Container, Grid, Flex
- Card, Panel
- Header, Footer, Sidebar
- Navigation, Breadcrumbs

### Feedback Components
- Alert, Toast, Notification
- Modal, Dialog
- Tooltip, Popover
- Loading spinner, Skeleton

### Data Display
- Table, List
- Pagination
- Tabs, Accordion
- Progress bar

## Examples

**Example 1: Button Component (React + TypeScript)**
```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'outline'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
  loading?: boolean
  onClick?: () => void
  children: React.ReactNode
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  ...props
}) => { /* implementation */ }
```

**Example 2: Input Component (Vue)**
```vue
<template>
  <div class="input-wrapper">
    <label :for="id">{{ label }}</label>
    <input
      :id="id"
      :type="type"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  </div>
</template>
```

## Best Practices

- **Accessibility first** - Include ARIA labels, keyboard navigation
- **Responsive design** - Mobile-first, breakpoint support
- **Type safety** - TypeScript props, prop validation
- **Composability** - Build complex from simple components
- **Theming support** - CSS variables, theme context
- **Documentation** - Storybook stories, usage examples

---

**Purpose**: Reusable UI component templates
**Used by**: frontend-generator agent, /component command

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vanman2024) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
