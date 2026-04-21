---
name: fontstock-ui
description: > Use when this capability is needed.
metadata:
  author: luisdavidtf
---

## Core Principles
1. **Material Design 3**: Leveraging `react-native-paper` for all core components.
2. **Consistency**: Use the `src/presentation/theme/` definitions.
3. **Icons**: Use `lucide-react-native` for all icons.

## CRITICAL RULES

### 1. Theming
- **ALWAYS** use `useTheme` hook to access colors.
- **NEVER** hardcode colors.

```typescript
const theme = useTheme();
<Text style={{ color: theme.colors.primary }}>Hello</Text>
```

### 2. Components
- **ALWAYS** prefer Paper components (`Button`, `Card`, `TextInput`) over raw React Native ones unless customizing heavily.
- **ALWAYS** place reusable UI components in `src/presentation/components/ui/`.

### 3. Icons
- **ALWAYS** use `lucide-react-native`.
- **Size**: Default to `size={24}` unless specified.
- **Color**: Use theme colors.

```typescript
import { Box } from 'lucide-react-native';
<Box color={theme.colors.onSurface} size={24} />
```

### 4. Layout
- **Spacing**: Use multiples of 4 or 8 (e.g., 4, 8, 16, 24, 32).
- **Typography**: Use standard variants: `displayLarge`, `headlineMedium`, `bodyMedium`, `labelSmall`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luisdavidtf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
