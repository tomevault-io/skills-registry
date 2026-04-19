---
name: tamagui-core
description: Guide for building cross-platform React Native and Expo applications with Tamagui. Use this skill when implementing UI components, styling, theming, responsive design, or animations with Tamagui. Applies to tasks involving styled(), tokens, themes, media queries, variants, or TamaguiProvider setup. Use when this capability is needed.
metadata:
  author: mattppal
---

# Tamagui Core

Tamagui is a universal UI library for React Native and Expo that provides 100% parity between web and native with an optimizing compiler. This skill covers core styling, theming, and component patterns.

## Quick Start with Expo Go

### Installation

```bash
bunx create-expo-app@latest my-app -t expo-template-blank-typescript
cd my-app
bun add tamagui @tamagui/config
```

### Configuration

Create `tamagui.config.ts` at the project root:

```typescript
import { config } from '@tamagui/config/v4'
import { createTamagui } from 'tamagui'

export const tamaguiConfig = createTamagui(config)

export default tamaguiConfig
export type Conf = typeof tamaguiConfig

declare module 'tamagui' {
  interface TamaguiCustomConfig extends Conf {}
}
```

### Provider Setup

Wrap the app root with TamaguiProvider:

```typescript
import { TamaguiProvider } from 'tamagui'
import tamaguiConfig from './tamagui.config'

export default function App() {
  return (
    <TamaguiProvider config={tamaguiConfig} defaultTheme="light">
      {/* App content */}
    </TamaguiProvider>
  )
}
```

### Babel Configuration (Optional but Recommended)

Update `babel.config.js` for compiler optimization:

```javascript
module.exports = function (api) {
  api.cache(true)
  return {
    presets: ['babel-preset-expo'],
    plugins: [
      [
        '@tamagui/babel-plugin',
        {
          components: ['tamagui'],
          config: './tamagui.config.ts',
          logTimings: true,
          disableExtraction: process.env.NODE_ENV === 'development',
        },
      ],
    ],
  }
}
```

## styled() Function

The `styled()` function creates typed, optimized components:

```typescript
import { View, Text, styled } from 'tamagui'

export const Card = styled(View, {
  backgroundColor: '$background',
  borderRadius: '$4',
  padding: '$4',

  variants: {
    elevated: {
      true: {
        shadowColor: '$shadowColor',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.1,
        shadowRadius: 8,
        elevation: 3,
      },
    },
    size: {
      small: { padding: '$2' },
      medium: { padding: '$4' },
      large: { padding: '$6' },
    },
  } as const,

  defaultVariants: {
    size: 'medium',
  },
})
```

### Variant Patterns

**Boolean variants** use `true`/`false` keys:

```typescript
variants: {
  centered: {
    true: {
      alignItems: 'center',
      justifyContent: 'center',
    },
  },
}
```

**Token-spread variants** use `...` prefix to map token scales:

```typescript
variants: {
  size: {
    '...size': (size, { tokens }) => ({
      width: tokens.size[size] ?? size,
      height: tokens.size[size] ?? size,
    }),
  },
}
```

**Functional variants** receive the value and extras:

```typescript
variants: {
  width: (val) => ({ width: val }),
}
```

### Property Order

Style property order matters for overrides. Properties defined later take precedence:

```typescript
const Button = styled(View, {
  backgroundColor: '$blue',  // Can be overridden
  ...props,                  // Props override above
  borderRadius: '$4',        // Cannot be overridden by props
})
```

## Tokens

Tokens are design system variables defined via `createTokens()`:

```typescript
import { createTokens } from 'tamagui'

const tokens = createTokens({
  size: {
    0: 0,
    1: 4,
    2: 8,
    3: 12,
    4: 16,
    // ...
  },
  space: {
    0: 0,
    1: 4,
    2: 8,
    '-1': -4,  // Negative space tokens
    '-2': -8,
  },
  radius: {
    0: 0,
    1: 4,
    2: 8,
    full: 9999,
  },
  color: {
    white: '#fff',
    black: '#000',
    primary: '#007AFF',
  },
  zIndex: {
    0: 0,
    1: 100,
    2: 200,
  },
})
```

### Token Usage

Access tokens with `$` prefix in style values:

```typescript
<View padding="$4" backgroundColor="$primary" borderRadius="$2" />
```

Token categories auto-map to CSS properties:
- `size` → width, height, minWidth, maxWidth, minHeight, maxHeight
- `space` → padding, margin, gap
- `radius` → borderRadius
- `color` → color, backgroundColor, borderColor
- `zIndex` → zIndex

### Custom Token Categories

Define arbitrary token groups:

```typescript
const tokens = createTokens({
  icon: {
    small: 16,
    medium: 24,
    large: 32,
  },
})

// Usage: width="$icon.small"
```

## Themes

Themes provide contextual styling that changes based on the React tree:

```typescript
import { createTamagui } from 'tamagui'

const config = createTamagui({
  tokens,
  themes: {
    light: {
      background: '#fff',
      color: '#000',
      primary: '$color.primary',
    },
    dark: {
      background: '#000',
      color: '#fff',
      primary: '$color.primary',
    },
    // Sub-themes follow pattern: parentTheme_subTheme
    light_accent: {
      background: '$color.primary',
      color: '#fff',
    },
    dark_accent: {
      background: '$color.primary',
      color: '#000',
    },
  },
})
```

### Theme Component

Change themes anywhere in the component tree:

```typescript
import { Theme, YStack, Text } from 'tamagui'

function App() {
  return (
    <Theme name="dark">
      <YStack backgroundColor="$background">
        <Text color="$color">Dark theme text</Text>

        <Theme name="accent">
          {/* Resolves to "dark_accent" */}
          <Text color="$color">Accent theme text</Text>
        </Theme>

        <Theme inverse>
          {/* Switches to "light" */}
          <Text>Inverted theme text</Text>
        </Theme>
      </YStack>
    </Theme>
  )
}
```

### useTheme Hook

Access theme values programmatically:

```typescript
import { useTheme } from 'tamagui'

function Component() {
  const theme = useTheme()

  // Use .val for raw values
  const bgColor = theme.background.val

  // Use .get() for platform-optimized access (CSS var on web)
  const optimizedBg = theme.background.get()

  return <View style={{ backgroundColor: bgColor }} />
}
```

## Media Queries & Responsive Design

### Configuration

Define breakpoints in config:

```typescript
const config = createTamagui({
  media: {
    xs: { maxWidth: 660 },
    sm: { maxWidth: 860 },
    md: { maxWidth: 980 },
    lg: { maxWidth: 1120 },
    xl: { minWidth: 1121 },
    short: { maxHeight: 820 },
    tall: { minHeight: 821 },
    hoverNone: { hover: 'none' },
    pointerCoarse: { pointer: 'coarse' },
  },
})
```

### Inline Media Props

Apply responsive styles with `$` prefix:

```typescript
<YStack
  padding="$4"
  $sm={{ padding: '$2' }}
  $md={{ padding: '$3' }}
  flexDirection="column"
  $lg={{ flexDirection: 'row' }}
/>
```

### useMedia Hook

Access breakpoints programmatically:

```typescript
import { useMedia } from 'tamagui'

function Component() {
  const media = useMedia()

  return (
    <YStack
      backgroundColor={media.sm ? '$blue' : '$green'}
      {...(media.lg && { padding: '$6' })}
    >
      {media.sm ? <MobileView /> : <DesktopView />}
    </YStack>
  )
}
```

**Performance note**: useMedia uses proxies to track accessed keys and only re-renders when those specific breakpoints change.

## Animations

### Animation Drivers

Tamagui supports three swappable animation drivers:

1. **CSS Animations** (web-optimized): `@tamagui/animations-css`
2. **React Native Animated**: `@tamagui/animations-react-native`
3. **Reanimated**: `@tamagui/animations-moti`

### Configuration

```typescript
import { createAnimations } from '@tamagui/animations-css'

const animations = createAnimations({
  fast: 'ease-in 150ms',
  medium: 'ease-in 300ms',
  slow: 'ease-in 450ms',
  bouncy: 'cubic-bezier(0.175, 0.885, 0.32, 1.275) 300ms',
})

const config = createTamagui({
  animations,
  // ...
})
```

For spring animations with React Native Animated:

```typescript
import { createAnimations } from '@tamagui/animations-react-native'

const animations = createAnimations({
  fast: {
    type: 'spring',
    damping: 20,
    mass: 1.2,
    stiffness: 250,
  },
  slow: {
    type: 'spring',
    damping: 15,
    stiffness: 100,
  },
})
```

### Using Animations

```typescript
<YStack
  animation="fast"
  enterStyle={{ opacity: 0, scale: 0.9 }}
  opacity={1}
  scale={1}
  hoverStyle={{ scale: 1.05 }}
  pressStyle={{ scale: 0.95 }}
/>
```

### AnimatePresence for Exit Animations

```typescript
import { AnimatePresence } from 'tamagui'

function Modal({ isOpen, children }) {
  return (
    <AnimatePresence>
      {isOpen && (
        <YStack
          key="modal"
          animation="medium"
          enterStyle={{ opacity: 0, y: 20 }}
          exitStyle={{ opacity: 0, y: -20 }}
          opacity={1}
          y={0}
        >
          {children}
        </YStack>
      )}
    </AnimatePresence>
  )
}
```

**Important**: Once animation prop is added, keep it present. Use `null`, `false`, or `undefined` to disable rather than removing the prop conditionally.

## Pseudo-State Styles

Apply interactive state styles:

```typescript
<Button
  backgroundColor="$blue"
  hoverStyle={{ backgroundColor: '$blueHover' }}
  pressStyle={{ backgroundColor: '$bluePress', scale: 0.98 }}
  focusStyle={{ outlineWidth: 2, outlineColor: '$blueFocus' }}
  focusVisibleStyle={{ outlineWidth: 2 }}
  disabledStyle={{ opacity: 0.5 }}
/>
```

## Platform-Specific Styles

Target specific platforms:

```typescript
<YStack
  padding="$4"
  $platform-native={{ padding: '$2' }}
  $platform-web={{ cursor: 'pointer' }}
  $platform-ios={{ paddingTop: '$6' }}
  $platform-android={{ elevation: 4 }}
/>
```

## Groups (Parent-Child Styling)

Enable parent-based child styling:

```typescript
<YStack group="card" backgroundColor="$background">
  <Text
    color="$color"
    $group-card-hover={{ color: '$primary' }}
    $group-card-press={{ opacity: 0.8 }}
  >
    Hover the card to change my color
  </Text>
</YStack>
```

## Component Composition with Context

Create compound components with shared context:

```typescript
import { createStyledContext, styled, withStaticProperties } from 'tamagui'

const ButtonContext = createStyledContext({
  size: '$4' as SizeTokens,
})

const ButtonFrame = styled(View, {
  name: 'Button',
  context: ButtonContext,
  backgroundColor: '$primary',
  borderRadius: '$4',

  variants: {
    size: {
      '...size': (size, { tokens }) => ({
        padding: tokens.space[size],
      }),
    },
  },
})

const ButtonText = styled(Text, {
  name: 'ButtonText',
  context: ButtonContext,
  color: '$color',

  variants: {
    size: {
      '...size': (size, { tokens }) => ({
        fontSize: tokens.size[size],
      }),
    },
  },
})

export const Button = withStaticProperties(ButtonFrame, {
  Props: ButtonContext.Provider,
  Text: ButtonText,
})

// Usage:
<Button size="$4">
  <Button.Text>Click me</Button.Text>
</Button>
```

## The asChild Pattern

The `asChild` prop passes styles and behavior to a child component:

```typescript
import { Button } from 'tamagui'
import { Link } from 'expo-router'

// Button styles apply to Link
<Button asChild>
  <Link href="/settings">Settings</Link>
</Button>
```

**Common Uses:**
- Wrapping navigation links with styled buttons
- Applying trigger styles to custom elements
- Dialog/Popover/Tooltip triggers

```typescript
// Dialog trigger with custom button
<Dialog.Trigger asChild>
  <Button theme="red">Delete</Button>
</Dialog.Trigger>

// Popover trigger
<Popover.Trigger asChild>
  <Button icon={<MoreHorizontal />} circular />
</Popover.Trigger>
```

**Requirements for asChild targets:**
- Must accept and forward style props
- Must accept `onPress`/`onClick` handlers
- Must be a valid React element

## Size Scaling System

Tamagui's size tokens scale multiple properties from a single `size` prop:

```typescript
const ScaledComponent = styled(View, {
  name: 'ScaledComponent',

  variants: {
    size: {
      '...size': (size, { tokens }) => ({
        height: tokens.size[size] ?? size,
        paddingHorizontal: tokens.space[size],
        borderRadius: tokens.radius[size] ?? tokens.radius.$4,
      }),
    },
  } as const,
})

// size="$2" → small height, small padding, small radius
// size="$6" → large height, large padding, large radius
```

**Size Token Convention:**
| Token | Use Case |
|-------|----------|
| `$2` | Compact/dense UI |
| `$3` | Small elements |
| `$4` | Default (recommended as defaultVariant) |
| `$5` | Large elements |
| `$6` | Extra large/hero |

**Token Spread Patterns:**
```typescript
// Spread size tokens
'...size': (size, { tokens }) => ({ ... })

// Spread space tokens
'...space': (space, { tokens }) => ({ ... })

// Spread radius tokens
'...radius': (radius, { tokens }) => ({ ... })
```

## ThemeableStack

For components that need theme-aware backgrounds with hover/press states:

```typescript
import { ThemeableStack, styled } from 'tamagui'

const InteractiveCard = styled(ThemeableStack, {
  name: 'InteractiveCard',
  padding: '$4',
  borderRadius: '$4',

  // ThemeableStack provides these automatically
  // backgroundColor: '$background',
  // hoverStyle: { backgroundColor: '$backgroundHover' },
  // pressStyle: { backgroundColor: '$backgroundPress' },
})
```

Used by: Card, Checkbox, Group, ListItem, and other interactive containers.

## Cross-Platform Patterns

### Adapt Component

Convert between components based on platform:

```typescript
import { Adapt, Dialog, Sheet } from 'tamagui'

function ResponsiveDialog({ children }) {
  return (
    <Dialog>
      {/* On mobile, convert Dialog.Content to Sheet */}
      <Adapt when="sm" platform="touch">
        <Sheet modal dismissOnSnapToBottom>
          <Sheet.Frame padding="$4">
            <Adapt.Contents />  {/* Renders Dialog content here */}
          </Sheet.Frame>
          <Sheet.Overlay />
        </Sheet>
      </Adapt>

      <Dialog.Portal>
        <Dialog.Overlay />
        <Dialog.Content>{children}</Dialog.Content>
      </Dialog.Portal>
    </Dialog>
  )
}
```

### Platform-Specific Styles

```typescript
<YStack
  padding="$4"
  $platform-web={{ cursor: 'pointer' }}
  $platform-native={{ padding: '$2' }}
  $platform-ios={{ paddingTop: '$6' }}  // Safe area
  $platform-android={{ elevation: 4 }}
/>
```

### Web-Only Features

Some features only work on web:
- `FocusScope` - Focus trapping
- `cursor` property
- CSS animations with `@tamagui/animations-css`
- `outlineStyle`, `outlineOffset`

### Native Adaptations

- Use `Sheet` instead of `Dialog` on mobile for better UX
- `elevation` for Android shadows
- Gesture handlers for swipe interactions

## Accessibility Quick Reference

### Required Elements

| Component | Requirements |
|-----------|--------------|
| Dialog | Must have `Dialog.Title` and `Dialog.Description` |
| Button | Text content or `aria-label` for icon-only |
| Input | Associated `Label` with matching `htmlFor`/`id` |
| Image | `alt` prop or `aria-label` |

### Focus Management

```typescript
// FocusScope for dialogs (web)
import { FocusScope } from 'tamagui'

<Dialog.Content>
  <FocusScope trapped restoreFocus>
    {/* Focus trapped inside, restored on close */}
  </FocusScope>
</Dialog.Content>
```

### Common Patterns

```typescript
// Icon-only button
<Button icon={<MenuIcon />} aria-label="Open menu" />

// Form field with error
<Label htmlFor="email">Email</Label>
<Input
  id="email"
  aria-describedby={error ? 'email-error' : undefined}
  aria-invalid={!!error}
/>
{error && <Text id="email-error">{error}</Text>}

// Loading state
<Button aria-busy={isLoading} disabled={isLoading}>
  {isLoading ? <Spinner /> : 'Submit'}
</Button>
```

## forceMount for Animations

Keep elements mounted for exit animations:

```typescript
<Dialog>
  <Dialog.Portal forceMount>
    <AnimatePresence>
      {open && (
        <Dialog.Content
          key="content"
          animation="medium"
          enterStyle={{ opacity: 0, y: 10 }}
          exitStyle={{ opacity: 0, y: 10 }}
        >
          {children}
        </Dialog.Content>
      )}
    </AnimatePresence>
  </Dialog.Portal>
</Dialog>
```

## Modular Design

For comprehensive guidance on component architecture, file organization, and building component libraries, see the **modular-design** skill. It covers:
- Atomic design structure (atoms, molecules, organisms)
- Compound component patterns
- File naming and export conventions
- Component creation checklists

## Best Practices

1. **Use tokens consistently** - Define all design values as tokens for maintainability
2. **Prefer inline styles for variants** - The compiler optimizes these to static CSS
3. **Use useMedia sparingly** - Prefer inline `$sm={{ }}` syntax when possible
4. **Keep themes focused on colors** - Other values should be tokens
5. **Use enterStyle for mount animations** - SSR-safe and compiler-optimized
6. **Leverage the compiler** - Add `// debug` comment to see optimization output
7. **Use groups for hover effects** - More performant than manual state management
8. **Clear cache after config changes** - Run `bunx expo start -c`
9. **Use full property names** - Avoid shorthands like `ai`, `jc`, `br`, `p`, `px`, `py` as they may not be typed correctly. Use `alignItems`, `justifyContent`, `borderRadius`, `padding`, `paddingHorizontal`, `paddingVertical` instead
10. **Use textProps for Button text styling** - `textTransform` and other text styles don't apply to Button's internal text via `styled()`. Use `textProps={{ style: {...} }}`
11. **Use useTheme() for dynamic theme values** - When passing theme tokens to non-Tamagui components (like icons), access the raw value via `theme.tokenName?.val`

## Common Patterns

### Responsive Stack Direction

```typescript
<XStack
  flexDirection="column"
  $md={{ flexDirection: 'row' }}
  gap="$4"
>
  <Item />
  <Item />
</XStack>
```

### Theme-Aware Component

```typescript
const ThemedCard = styled(YStack, {
  backgroundColor: '$background',
  borderColor: '$borderColor',
  borderWidth: 1,

  $theme-dark: {
    borderColor: '$gray800',
  },
  $theme-light: {
    borderColor: '$gray200',
  },
})
```

### Accessible Button

```typescript
const AccessibleButton = styled(View, {
  tag: 'button',
  role: 'button',
  backgroundColor: '$primary',
  padding: '$3',
  borderRadius: '$2',
  cursor: 'pointer',

  hoverStyle: { backgroundColor: '$primaryHover' },
  pressStyle: { backgroundColor: '$primaryPress' },
  focusVisibleStyle: {
    outlineWidth: 2,
    outlineColor: '$primaryFocus',
    outlineStyle: 'solid',
  },
})
```

## References

For detailed API documentation, see the references folder:
- `references/components.md` - UI component catalog
- `references/config-options.md` - Full configuration reference
- `references/troubleshooting.md` - Common issues and solutions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mattppal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
