---
name: ui-conventions
description: > Use when this capability is needed.
metadata:
  author: domeniqque-pereira-deel
---

# UI Conventions — Mobile Component Patterns

Standards for building polished, accessible iOS app interfaces with Expo.

## Design Token System

### Colors
```typescript
// src/theme/colors.ts
export const colors = {
  // Semantic colors (use these in components)
  primary: '#007AFF',       // iOS system blue
  secondary: '#5856D6',
  destructive: '#FF3B30',
  success: '#34C759',
  warning: '#FF9500',

  // Text
  text: {
    primary: '#000000',
    secondary: '#3C3C43',    // 60% opacity on iOS
    tertiary: '#8E8E93',
    inverse: '#FFFFFF',
  },

  // Backgrounds
  background: {
    primary: '#FFFFFF',
    secondary: '#F2F2F7',    // iOS grouped background
    tertiary: '#FFFFFF',
    elevated: '#FFFFFF',
  },

  // Borders
  border: {
    default: '#C6C6C8',
    light: '#E5E5EA',
  },
} as const;
```

### Typography
```typescript
// src/theme/typography.ts
import { Platform } from 'react-native';

const fontFamily = Platform.select({
  ios: 'System',
  default: 'System',
});

export const typography = {
  largeTitle:  { fontSize: 34, lineHeight: 41, fontWeight: '700' as const },
  title1:     { fontSize: 28, lineHeight: 34, fontWeight: '700' as const },
  title2:     { fontSize: 22, lineHeight: 28, fontWeight: '700' as const },
  title3:     { fontSize: 20, lineHeight: 25, fontWeight: '600' as const },
  headline:   { fontSize: 17, lineHeight: 22, fontWeight: '600' as const },
  body:       { fontSize: 17, lineHeight: 22, fontWeight: '400' as const },
  callout:    { fontSize: 16, lineHeight: 21, fontWeight: '400' as const },
  subhead:    { fontSize: 15, lineHeight: 20, fontWeight: '400' as const },
  footnote:   { fontSize: 13, lineHeight: 18, fontWeight: '400' as const },
  caption1:   { fontSize: 12, lineHeight: 16, fontWeight: '400' as const },
  caption2:   { fontSize: 11, lineHeight: 13, fontWeight: '400' as const },
} as const;
```

### Spacing
```typescript
// src/theme/spacing.ts
export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
} as const;

export const radius = {
  sm: 8,
  md: 12,
  lg: 16,
  xl: 24,
  full: 9999,
} as const;
```

## Component Patterns

### Base Button
```typescript
// src/components/ui/Button.tsx
import { Pressable, Text, StyleSheet, ActivityIndicator } from 'react-native';
import * as Haptics from 'expo-haptics';

interface ButtonProps {
  title: string;
  onPress: () => void;
  variant?: 'primary' | 'secondary' | 'destructive' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  loading?: boolean;
  disabled?: boolean;
}

export function Button({
  title,
  onPress,
  variant = 'primary',
  size = 'md',
  loading = false,
  disabled = false,
}: ButtonProps) {
  const handlePress = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    onPress();
  };

  return (
    <Pressable
      onPress={handlePress}
      disabled={disabled || loading}
      style={({ pressed }) => [
        styles.base,
        styles[variant],
        styles[`size_${size}`],
        pressed && styles.pressed,
        disabled && styles.disabled,
      ]}
      accessibilityRole="button"
      accessibilityLabel={title}
      accessibilityState={{ disabled, busy: loading }}
    >
      {loading ? (
        <ActivityIndicator color={variant === 'primary' ? '#fff' : colors.primary} />
      ) : (
        <Text style={[styles.text, styles[`text_${variant}`]]}>{title}</Text>
      )}
    </Pressable>
  );
}
```

### Screen Wrapper
```typescript
// src/components/layout/ScreenWrapper.tsx
import { SafeAreaView, ScrollView, StyleSheet, ViewStyle } from 'react-native';
import { StatusBar } from 'expo-status-bar';

interface ScreenWrapperProps {
  children: React.ReactNode;
  scrollable?: boolean;
  style?: ViewStyle;
  edges?: ('top' | 'bottom' | 'left' | 'right')[];
}

export function ScreenWrapper({
  children,
  scrollable = true,
  style,
  edges = ['top', 'left', 'right'],
}: ScreenWrapperProps) {
  const content = scrollable ? (
    <ScrollView
      contentContainerStyle={styles.scrollContent}
      showsVerticalScrollIndicator={false}
      keyboardShouldPersistTaps="handled"
    >
      {children}
    </ScrollView>
  ) : (
    children
  );

  return (
    <SafeAreaView style={[styles.container, style]} edges={edges}>
      <StatusBar style="auto" />
      {content}
    </SafeAreaView>
  );
}
```

## Component Rules

1. **Always export Props interface** — Every component exports its props type
2. **Haptic feedback on interactions** — Use `expo-haptics` for buttons, toggles, destructive actions
3. **Loading states are mandatory** — Every async action shows a loading indicator
4. **Pressable over TouchableOpacity** — `Pressable` is the modern standard
5. **StyleSheet over inline** — Use `StyleSheet.create()` for anything > 3 props
6. **No hardcoded colors** — Always reference theme tokens
7. **No hardcoded text sizes** — Always reference typography tokens
8. **accessibilityRole on every interactive element**

## iOS-Specific Patterns

### Safe Insets
Always account for notch, home indicator, and status bar:
```typescript
import { useSafeAreaInsets } from 'react-native-safe-area-context';

const insets = useSafeAreaInsets();
// Use insets.top, insets.bottom for padding
```

### iOS System UI
- Use `expo-blur` for frosted glass effects
- Use `expo-haptics` for tactile feedback
- Respect system font sizes with `allowFontScaling`
- Support Dark Mode via `useColorScheme()`
- Bottom sheets: use `@gorhom/bottom-sheet`

### Keyboard Handling
```typescript
import { KeyboardAvoidingView, Platform } from 'react-native';

<KeyboardAvoidingView
  behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
  keyboardVerticalOffset={headerHeight}
>
  {/* form content */}
</KeyboardAvoidingView>
```

## Animation Patterns

### Use `react-native-reanimated` for:
- Shared element transitions
- Gesture-driven animations
- Layout animations
- Complex spring/timing animations

### Use `Animated` (RN built-in) for:
- Simple opacity/scale transitions
- Loading spinners

### Animation Rules
1. Keep animations under 300ms for UI feedback
2. Use spring animations for natural feel
3. Always use `useNativeDriver: true` when possible
4. Test on real device — simulator animations lie
5. Separate animation logic into `useAnimated[Name].ts` hooks

## List Patterns

```typescript
import { FlashList } from '@shopify/flash-list';

<FlashList
  data={items}
  renderItem={({ item }) => <ItemCard item={item} />}
  estimatedItemSize={80}
  keyExtractor={(item) => item.id}
  ItemSeparatorComponent={() => <View style={{ height: 8 }} />}
  ListEmptyComponent={<EmptyState message="No items yet" />}
  ListHeaderComponent={<SectionHeader title="Your Items" />}
  onEndReached={loadMore}
  onEndReachedThreshold={0.5}
/>
```

**Rule**: Use `FlashList` instead of `FlatList` for any list > 20 items.

## Empty States

Every list/data screen must have a meaningful empty state:
```typescript
function EmptyState({ message, actionLabel, onAction }: EmptyStateProps) {
  return (
    <View style={styles.emptyContainer}>
      <Image source={emptyIllustration} style={styles.emptyImage} />
      <Text style={styles.emptyTitle}>{message}</Text>
      {actionLabel && (
        <Button title={actionLabel} onPress={onAction} variant="secondary" />
      )}
    </View>
  );
}
```

## Form Patterns

- Use `react-hook-form` for form state
- Validate with `zod` schemas
- Show inline errors below fields
- Disable submit button until form is valid
- Show loading state during submission
- Handle keyboard dismissal on submit

## Accessibility Checklist

For every component:
- [ ] `accessibilityRole` set correctly
- [ ] `accessibilityLabel` for non-text interactive elements
- [ ] `accessibilityHint` for non-obvious actions
- [ ] Touch targets minimum 44x44pt (iOS HIG)
- [ ] Color contrast ratio ≥ 4.5:1 for text
- [ ] Dynamic Type support (don't disable `allowFontScaling`)
- [ ] VoiceOver tested on real device

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/domeniqque-pereira-deel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
