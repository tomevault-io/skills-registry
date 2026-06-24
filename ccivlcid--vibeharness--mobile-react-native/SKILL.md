---
name: mobile-react-native
description: >- Use when this capability is needed.
metadata:
  author: ccivlcid
---

# Mobile — React Native + Expo

> **Mobile app only.** Do not apply to web-only projects.
> Verify stack matches `PROJECT_RULES.md`. Record architecture decisions in `docs/ai-dev/`.

## Initial Setup

```bash
npx create-expo-app@latest my-app --template blank-typescript
cd my-app
npx expo install expo-router expo-linking expo-constants
npx expo install nativewind tailwindcss
```

## Directory Structure

```
app/
├── _layout.tsx           # Root layout (navigation)
├── index.tsx             # Home screen
├── (tabs)/
│   ├── _layout.tsx       # Tab navigator
│   ├── home.tsx
│   └── profile.tsx
├── (auth)/
│   ├── login.tsx
│   └── signup.tsx
└── [id].tsx              # Dynamic route
components/
├── ui/                   # Reusable UI components
└── {feature}/            # Feature-specific components
lib/
├── api.ts                # API client (fetch)
└── storage.ts            # AsyncStorage helpers
types/
└── index.ts              # Shared types
```

## Key Patterns

- One component = max 150 lines. Split if exceeded
- Use RN core components first (`View`, `Text`, `Pressable`, `FlatList`)
- Props: define with TypeScript interfaces
- State: useState first, Zustand if complex
- **HTTP requests: use built-in `fetch` only. Do NOT install Axios**
- Navigation: Expo Router (file-based, like Next.js App Router)

## NativeWind Patterns

```tsx
// Layout
<View className="flex-1 px-4">
  <Text className="text-2xl font-bold text-gray-900 dark:text-white">Title</Text>
</View>

// Card
<View className="bg-white dark:bg-gray-800 rounded-xl p-4 shadow-sm">
  <Text className="text-lg font-semibold">{title}</Text>
</View>

// List
<FlatList
  data={items}
  keyExtractor={(item) => item.id}
  renderItem={({ item }) => <ItemCard item={item} />}
  contentContainerClassName="gap-3 p-4"
/>
```

## Running

```bash
npx expo start
# Phone: install "Expo Go" app, scan QR code
# iOS simulator: press "i" | Android emulator: press "a"
```

## Building Screens

1. Create `app/{path}.tsx`
2. Build in order: layout -> components -> data connection
3. Handle empty, loading, error states
4. Test on Expo Go (scan QR with phone)
5. ESLint pass

---
> Source: [ccivlcid/VibeHarness](https://github.com/ccivlcid/VibeHarness) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
