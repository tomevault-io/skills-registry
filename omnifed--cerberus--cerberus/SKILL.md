---
name: cerberus-form-component-styling
description: Style custom React form components with Cerberus Design System's panda-preset. Use when creating form inputs, implementing design tokens, customizing Field primitives, or ensuring consistent styling with Cerberus semantic colors, typography, and spacing tokens. Use when this capability is needed.
metadata:
  author: omnifed
---

# Cerberus Form Component Styling

Style custom React form components using Cerberus Design System's panda-preset configuration and design tokens.

## When to Apply

- Building custom form inputs with Cerberus styling
- Implementing consistent design tokens across form components
- Customizing FieldParts primitives with semantic colors
- Migrating existing forms to use Cerberus design system

## Critical Rules

**Token Reference Syntax**: Use `{path.to.token}` for composite CSS values

```tsx
// WRONG - Direct values break design system consistency
<Box border="1px solid red" p="16px 24px" />

// RIGHT - Token references maintain system consistency
<Box
  border="1px solid"
  borderColor="danger.border.initial"
  p="md"
/>
```

**Semantic Color Structure**: Follow Cerberus semantic color patterns

```tsx
// WRONG - Using arbitrary color names
<FieldParts.Input css={{ borderColor: 'red', color: 'darkred' }} />

// RIGHT - Using semantic color tokens via inline style props
<FieldParts.Input
  borderColor="danger.border.initial"
  color="danger.text.initial"
/>

// ALTERNATIVE - Using the `css` prop for custom selectors and conditions
<FieldParts.Input
  css={{
    '& :is([my-custom-attr])': {
      borderColor: 'danger.border.initial',
      color: 'danger.text.initial' ,
    }
  }}
/>
```

**Design Token Values**: All tokens must be nested within 'value' key

```typescript
// WRONG - Direct token assignment
theme: {
  tokens: {
    colors: {
      primary: '#0FEE0F'
    }
  }
}

// RIGHT - Tokens wrapped in value object
theme: {
  tokens: {
    colors: {
      primary: {
        value: '#0FEE0F'
      }
    }
  }
}
```

## Key Patterns

### Panda Config Setup

```typescript
import { createCerberusConfig, createCerberusPreset } from '@cerberus/panda-preset'

export default createCerberusConfig({
  presets: [createCerberusPreset()],
  include: ['./app/**/*.{ts,tsx}'],
  exclude: [],

  theme: {
    tokens: {
      colors: {
        brand: { value: '#0FEE0F' },
      },
      spacing: {
        formGutter: { value: '16px' },
      },
    },
  },
})
```

### Custom Form Component with FieldParts

```tsx
import { FieldParts, Show } from '@cerberus/react'
import { Box } from 'styled-system/jsx'

export function CustomFormField({ label, error, ...props }) {
  return (
    <Box w="full">
      <FieldParts.Root invalid={Boolean(error)}>
        <FieldParts.Label
          textStyle="body-md"
          color="page.text.100"
          fontWeight="semibold"
          _userInvalid={{
            color: 'danger.text.initial',
          }}
        >
          {label}
        </FieldParts.Label>

        <FieldParts.Input
          css={{
            borderColor: 'page.border.100',
            _invalid: {
              borderColor: 'danger.border.initial',
            },
            _focus: {
              borderColor: 'action.border.focus',
              boxShadow: '0 0 0 2px {colors.action.bg.100/20}',
            },
          }}
          {...props}
        />

        <Show when={error}>
          {() => (
            <FieldParts.ErrorText color="danger.text.initial" textStyle="body-sm">
              {error}
            </FieldParts.ErrorText>
          )}
        </Show>
      </FieldParts.Root>
    </Box>
  )
}
```

### Semantic Color Application

```tsx
// Success state
<FieldParts.Input css={{
  borderColor: 'success.border.initial',
  bgColor: 'success.bg.initial/10'
}} />

// Warning state
<FieldParts.Input css={{
  borderColor: 'warning.border.initial',
  color: 'warning.text.initial'
}} />

// Danger state with opacity
<Text bgColor="danger.bg.initial/40" color="danger.text.initial">
  Error message
</Text>
```

### Typography and Spacing Tokens

```tsx
<FieldParts.Label
  css={{
    textStyle: 'heading-xs', // Predefined text styles
    marginBottom: 'md',     // Predefined spacing values
    color: 'page.text.100'
  }}>
  Field Label
</FieldParts.Label>

<Box
  p="{spacing.4} {spacing.6}" // Token reference in composite values
  gap="md"
>
  Form content
</Box>
```

### CSS Variables for Dynamic Tokens

```tsx
<Box
  css={{
    '--focus-color': '{colors.action.border.focus}',
    '--error-bg': '{colors.danger.bg.initial/20}',
  }}
>
  <FieldParts.Input
    css={{
      _focus: { borderColor: 'var(--focus-color)' },
    }}
  />
  <Box bg="var(--error-bg)" />
</Box>
```

## Common Mistakes

- **Hardcoded colors**: Use semantic tokens (`danger.text.initial`) not hex values
- **Missing token syntax**: Use `{colors.primary}` not `colors.primary` in composite CSS
- **Skipping FieldParts**: Use `FieldParts.Root` for proper accessibility and structure
- **Direct spacing values**: Use spacing tokens (`{spacing.4}`) not pixel values ("16px")
- **Inconsistent text styles**: Apply `textStyle` prop for typography consistency

## Recommendations

- **Use style props**: Utilize style props on all Cerberus components for faster development.
- **Reserve token syntax for variables**: Avoid the use of token syntax unless referencing a token to store in a CSS Variable.

---
> Source: [omnifed/cerberus](https://github.com/omnifed/cerberus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-02 -->
