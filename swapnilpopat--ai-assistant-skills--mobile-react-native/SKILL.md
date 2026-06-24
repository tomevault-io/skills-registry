---
name: mobile-react-native
description: React Native and Expo best practices for performant mobile apps. Use when building React Native features, optimizing lists, or integrating native platform behavior. Use when this capability is needed.
metadata:
  author: SwapnilPopat
---

# Mobile React Native

Coverage of the JetBrains `react-native-skills` entry.

## When to Use

- Building React Native or Expo screens and components
- Optimizing list or scroll performance
- Implementing animations with Reanimated or gesture handling
- Working with images, fonts, or native modules
- Reviewing monorepo boundaries for native dependencies

## Coverage Areas

### List Performance

- Virtualization strategy for large lists
- Memoized item components and stable callbacks
- Avoiding inline objects and repeated expensive work inside render items
- Image optimization and heterogeneous item type handling

### Animation

- Prefer transform and opacity for GPU-friendly animation
- Use derived animation values where computation belongs in the animation layer
- Coordinate gestures and animation primitives deliberately

### Navigation

- Prefer native navigators when they satisfy the product requirements

### UI Patterns

- Optimized image components and gallery patterns
- `Pressable` over older touch primitives when appropriate
- Safe area handling in scrollable layouts
- Native menus and modal patterns
- `onLayout` and explicit measurement patterns
- Styling systems such as `StyleSheet.create` or Nativewind

### State Management

- Minimize subscriptions and callback churn
- Dispatcher-style updates for clearer component contracts
- Fallback handling on initial render
- React Compiler-aware patterns when relevant

### Rendering

- Wrap text in `Text` components
- Avoid fragile falsy `&&` rendering patterns in JSX

### Monorepo And Configuration

- Keep native dependencies in the app package
- Keep dependency versions aligned across the workspace
- Use config plugins for fonts and other native config
- Keep design-system imports and expensive object creation organized

## Working Rules

1. Treat list performance as a first-order concern for mobile UIs; prefer virtualization and memoized item rendering.
2. Animate transform and opacity before heavier layout-affecting properties.
3. Prefer native navigation primitives when they satisfy the use case.
4. Use optimized media components and explicit safe-area handling.
5. Keep native dependencies and config close to the app package in monorepos.
6. Verify behavior on realistic device sizes, not only simulator defaults.

## Anti-Patterns

- Rendering large feeds with unbounded `ScrollView`
- Inline style objects and unstable callbacks in hot list paths
- Measuring layout repeatedly when `onLayout` or cached dimensions would do
- Spreading native configuration across unrelated workspace packages

## Further Reading

- React Native Skills: https://github.com/JetBrains/skills/tree/main/react-native-skills

---
> Source: [SwapnilPopat/ai-assistant-skills](https://github.com/SwapnilPopat/ai-assistant-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
