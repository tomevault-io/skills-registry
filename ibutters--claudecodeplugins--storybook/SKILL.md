---
name: storybook
description: Storybook 8 for React component documentation and testing. Use for creating stories, documenting components with Controls/Actions, visual testing, and MDX documentation. Triggers on requests for Storybook stories, component documentation, visual testing, or interactive component demos. Use when this capability is needed.
metadata:
  author: ibutters
---

# Storybook Development

## Story Structure (CSF 3.0)

```tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Atoms/Button',
  component: Button,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'ghost'],
      description: 'Visual style variant',
    },
    size: {
      control: 'radio',
      options: ['sm', 'md', 'lg'],
    },
    disabled: { control: 'boolean' },
    onClick: { action: 'clicked' },
  },
  args: {
    children: 'Button',
    variant: 'primary',
    size: 'md',
  },
};

export default meta;
type Story = StoryObj<typeof meta>;

export const Primary: Story = {
  args: {
    variant: 'primary',
  },
};

export const Secondary: Story = {
  args: {
    variant: 'secondary',
  },
};

export const AllVariants: Story = {
  render: (args) => (
    <div style={{ display: 'flex', gap: '1rem' }}>
      <Button {...args} variant="primary">Primary</Button>
      <Button {...args} variant="secondary">Secondary</Button>
      <Button {...args} variant="ghost">Ghost</Button>
    </div>
  ),
};
```

## File Naming

```
ComponentName/
├── ComponentName.tsx
├── ComponentName.stories.tsx   # Stories file
├── ComponentName.mdx           # Optional: Extended docs
```

## Controls Configuration

| Control Type | Use For |
|--------------|---------|
| `select` | Enum with many options |
| `radio` | Enum with 2-4 options |
| `boolean` | True/false toggles |
| `text` | String inputs |
| `number` | Numeric values |
| `color` | Color pickers |
| `object` | Complex objects |
| `date` | Date values |

## Common Patterns

### Interactive Story with State

```tsx
export const WithState: Story = {
  render: function Render(args) {
    const [count, setCount] = useState(0);
    return (
      <Button {...args} onClick={() => setCount(c => c + 1)}>
        Clicked {count} times
      </Button>
    );
  },
};
```

### Story with Decorators

```tsx
const meta: Meta<typeof Card> = {
  component: Card,
  decorators: [
    (Story) => (
      <div style={{ padding: '2rem', background: '#f5f5f5' }}>
        <Story />
      </div>
    ),
  ],
};
```

### Responsive Preview

```tsx
export const Responsive: Story = {
  parameters: {
    viewport: {
      viewports: {
        mobile: { name: 'Mobile', styles: { width: '375px', height: '667px' } },
        tablet: { name: 'Tablet', styles: { width: '768px', height: '1024px' } },
      },
      defaultViewport: 'mobile',
    },
  },
};
```

## Organization by Atomic Design

Stories are grouped by `title` in the Storybook sidebar following Atomic Design:

```tsx
// Atoms
title: 'Atoms/Button'
title: 'Atoms/Input'
title: 'Atoms/Icon'

// Molecules  
title: 'Molecules/SearchField'
title: 'Molecules/FormField'

// Organisms
title: 'Organisms/Header'
title: 'Organisms/ProductCard'

// Templates
title: 'Templates/DashboardLayout'

// Pages
title: 'Pages/HomePage'
```

Story files are located directly with the components:

```
src/components/
├── atoms/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.module.css
│   │   ├── Button.stories.tsx    # title: 'Atoms/Button'
│   │   └── index.ts
│   └── Input/
│       └── Input.stories.tsx     # title: 'Atoms/Input'
├── molecules/
│   └── SearchField/
│       └── SearchField.stories.tsx  # title: 'Molecules/SearchField'
└── organisms/
    └── Header/
        └── Header.stories.tsx    # title: 'Organisms/Header'
```

## Best Practices

1. One story file per component
2. Use `tags: ['autodocs']` for auto-generated docs
3. Provide meaningful default args
4. Show all variants in an overview story
5. Use actions for event callbacks
6. Document edge cases (loading, error, empty states)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ibutters) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
