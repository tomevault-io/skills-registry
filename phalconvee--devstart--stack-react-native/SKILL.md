---
name: stack-react-native
description: > Use when this capability is needed.
metadata:
  author: phalconVee
---

# React Native / Expo Stack Conventions

## Architecture
- Expo managed workflow (SDK 52+). Expo Router for file-based navigation.
- App directory structure mirrors Expo Router conventions (app/ directory).
- TypeScript strict mode. No `any`.

## Components
- Functional components only. Custom hooks for shared logic.
- StyleSheet.create for all styles. No inline style objects in JSX.
- Platform-specific code: `.ios.tsx` / `.android.tsx` suffixes or `Platform.select()`.
- Use `FlatList` / `FlashList` for lists. Never `ScrollView` with `.map()` for dynamic lists.

## Navigation (Expo Router)
- File-based routing in app/ directory.
- Layouts: `_layout.tsx` for shared navigation structure.
- Typed routes with `expo-router`'s `Link` component.

## Native APIs
- `expo-image` over `Image` for performance.
- `expo-secure-store` for sensitive data.
- `expo-notifications` for push notifications.
- Check platform support before using any native module.

## State Management
- React Query / TanStack Query for server state.
- Zustand for client state.
- No Redux unless PRD explicitly requires it.

## Testing
- Jest + React Native Testing Library.
- Mock native modules in jest.setup.ts.
- Test user interactions (press, type, scroll), not implementation.

---
> Source: [phalconVee/devstart](https://github.com/phalconVee/devstart) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
