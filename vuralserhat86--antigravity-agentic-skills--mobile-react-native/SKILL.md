---
name: mobile-react-native
description: React Native best practices, hooks, navigation ve performance optimization. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 📱 Mobile React Native

> React Native best practices ve performance optimization.

---

## 📁 1. Proje Yapısı

```
src/
├── components/
│   ├── common/        # Reusable
│   └── screens/       # Screen components
├── hooks/             # Custom hooks
├── services/          # API, storage
├── store/             # State (Zustand)
├── navigation/
└── App.tsx
```

---

## ⚡ 2. Performance

```typescript
// FlatList optimizasyonu
<FlatList
  data={items}
  keyExtractor={(item) => item.id}
  removeClippedSubviews={true}
  maxToRenderPerBatch={10}
  windowSize={5}
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  })}
/>

// Memoization
const Component = React.memo(({ data }) => { });
const callback = useCallback(() => {}, [deps]);
const value = useMemo(() => compute(), [deps]);
```

---

## 🔐 3. Secure Storage

```typescript
// ❌ AsyncStorage güvenli değil
// ✅ SecureStore kullan
import * as SecureStore from 'expo-secure-store';

await SecureStore.setItemAsync('token', userToken);
const token = await SecureStore.getItemAsync('token');
```

---

## 🧭 4. Navigation

```typescript
type RootStackParamList = {
  Home: undefined;
  Profile: { userId: string };
};

const Stack = createNativeStackNavigator<RootStackParamList>();
```

---

## 📦 5. State (Zustand)

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

const useAuthStore = create(
  persist(
    (set) => ({
      user: null,
      login: (user) => set({ user }),
      logout: () => set({ user: null }),
    }),
    { name: 'auth-storage' }
  )
);
```

---

## 📱 6. Platform-Specific

```typescript
import { Platform } from 'react-native';

const padding = Platform.select({ ios: 20, android: 0 });

// Dosya bazlı: Button.ios.tsx, Button.android.tsx
```

---

*Mobile React Native v1.1 - Enhanced*

## 🔄 Workflow

> **Kaynak:** [React Native Performance Guide](https://reactnative.dev/docs/performance) & [Expo Guideline](https://docs.expo.dev/)

### Aşama 1: Setup & Architecture
- [ ] **Framework**: Expo (Managed Workflow) ile başla, `expo-router` v3 kullan.
- [ ] **State**: Zustand veya TanStack Query ile server/client state ayrımını yap.
- [ ] **Styling**: NativeWind (Tailwind) veya Restyle ile tutarlı tasarım sistemi kur.

### Aşama 2: Performance Optimization
- [ ] **Lists**: `FlatList` yerine `FlashList` (Shopify) kullan (5x performans).
- [ ] **Images**: `expo-image` ile caching ve blurhash desteği ekle.
- [ ] **Bundle**: `Hermes` engine'i aktifleştir ve bundle size analizi yap.

### Aşama 3: Native Modules & Release
- [ ] **Native**: Gerekirse Custom Native Module (Turbo Modules) yaz.
- [ ] **Updates**: `expo-updates` ile store onayı beklemeden OTA (Over-the-Air) güncelleme yap.
- [ ] **Profiling**: Flipper veya React DevTools ile FPS ve Memory Leak kontrolü yap.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | UI thread (JS thread) 60fps'in altına düşüyor mu? |
| 2 | Uygulama boyutu (APK/IPA) optimize edildi mi? |
| 3 | Android ve iOS davranışları (Navigation, Keyboard) tutarlı mı? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
