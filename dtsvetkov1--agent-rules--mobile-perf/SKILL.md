---
name: mobile-perf
description: Optimizes React Native Expo applications for speed, responsiveness, and battery efficiency. Use when fixing lag, optimizing lists, or reducing bundle size. Use when this capability is needed.
metadata:
  author: dtsvetkov1
---

# Mobile Performance Skill

This skill focuses on delivering a "butter-smooth" user experience.

## Focus Areas

- **List Optimization**: Use `LegendList` or highly optimized `FlatList` settings.
- **Animation Performance**: Ensure all animations run on the UI thread using `react-native-reanimated`.
- **Image Handling**: Use `expo-image` for caching and performant loading.
- **JS Bundle Size**: Analyze and reduce the size of the JavaScript bundle.
- **Memory Leaks**: Identify and fix component memory leaks or excessive re-renders.

## Instructions

1. **Profile**: Identify the bottleneck (JS thread vs UI thread).
2. **Optimize Lists**: Use `removeClippedSubviews`, `maxToRenderPerBatch`, and `initialNumToRender`.
3. **Memoization**: Apply `useMemo` and `useCallback` judiciously to prevent unnecessary renders.
4. **Bridge Traffic**: Minimize the data sent across the React Native bridge.

## Recommended Tools

- `Flashlight` for performance testing.
- `react-devtools` for profiling renders.
- `expo-bundle-analyzer` to check bundle composition.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtsvetkov1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
