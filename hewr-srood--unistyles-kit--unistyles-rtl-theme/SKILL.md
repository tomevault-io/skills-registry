---
name: unistyles-rtl-theme
description: Standardize how Unistyles is used for RTL (Right-to-Left) and theme handling in React Native/Expo projects. Use when working with styles, RTL layouts, theme switching, horizontal ScrollViews, runtime (rt), media queries, variants, Reanimated, or component styling that needs to support both LTR and RTL languages. Use when this capability is needed.
metadata:
  author: hewr-srood
---

# Unistyles RTL & Theme Patterns

This skill captures the Unistyles patterns used for RTL support and theme management in React Native/Expo projects:

- Theme structure with `rtl`, `row`, `scaleX` properties for RTL-aware styling
- Using `useUnistyles()` hook to access theme in components
- Using `StyleSheet.create((theme) => ({ ... }))` for theme-aware styles
- Horizontal ScrollView RTL handling with `transform: [{ scaleX }]`
- Conditional styling based on `theme.rtl` and `theme.row`
- Theme naming convention: `{mode}{Language}` (e.g., `lightEn`, `darkNonEn`)
- Theme switching via `UnistylesRuntime.setTheme()`

The goal is for the agent to **always follow these patterns** when:

- Creating or modifying styled components
- Handling RTL layouts and horizontal scrolling
- Switching themes or languages
- Working with responsive design and theme-aware styles

## Core Concepts

### Theme Structure

Themes are configured with RTL-aware properties:

```ts
export const lightLTR = {
  name: "lightLTR",
  row: "row",              // flexDirection for LTR
  rtl: false,              // RTL flag
  scaleX: 1,              // Transform scale for horizontal elements
  writingDirection: "ltr", // Text direction
  textAlign: {
    left: "left",
    right: "right",
    center: "center",
  },
  colors: { ... },        // Color palette
  // ... other properties
};

export const lightRTL = {
  name: "lightRTL",
  row: "row-reverse",      // flexDirection for RTL
  rtl: true,               // RTL flag
  scaleX: -1,             // Transform scale for horizontal elements
  writingDirection: "rtl", // Text direction
  textAlign: {
    left: "right",         // Swapped for RTL
    right: "left",         // Swapped for RTL
    center: "center",
  },
  colors: { ... },        // Color palette
  // ... other properties
};
```

### Theme Naming Convention

Themes follow the pattern: `{mode}{Direction}` (recommended) or `{mode}{Language}` (legacy)

**Recommended naming:**

- `lightLTR` - Light mode, Left-to-Right
- `darkLTR` - Dark mode, Left-to-Right
- `lightRTL` - Light mode, Right-to-Left
- `darkRTL` - Dark mode, Right-to-Left

**Legacy naming (still supported):**

- `lightEn` / `darkEn` - Light/Dark mode, English (LTR)
- `lightNonEn` / `darkNonEn` - Light/Dark mode, Non-English (RTL)

### Unistyles Configuration

```ts
import { StyleSheet } from "react-native-unistyles";

StyleSheet.configure({
  breakpoints: { ... },
  themes: {
    lightLTR: lightLTR,
    darkLTR: darkLTR,
    lightRTL: lightRTL,
    darkRTL: darkRTL,
    // Legacy naming (optional):
    // lightEn: lightLTR,
    // darkEn: darkLTR,
    // lightNonEn: lightRTL,
    // darkNonEn: darkRTL,
  },
  settings: {
    adaptiveThemes: false,
    initialTheme: () => (defaultLanguage === "en" ? "lightLTR" : "lightRTL"),
  },
});
```

## When to Apply This Pattern

Use this pattern whenever:

- Creating or modifying styled components that need RTL support
- Working with horizontal ScrollViews or carousels
- Handling theme switching (light/dark mode)
- Handling language switching that affects RTL/LTR
- Creating responsive layouts that adapt to theme
- Positioning elements that need RTL-aware placement

## Implementation Patterns

### 1. Using Unistyles Hook

**Always** use `useUnistyles()` hook to access theme in components:

```tsx
import { useUnistyles } from "react-native-unistyles";

const MyComponent = () => {
  const { theme } = useUnistyles();

  return (
    <View style={styles.container}>
      <Text style={{ color: theme.colors.primary }}>Hello</Text>
    </View>
  );
};
```

### 2. Creating Theme-Aware Styles

**Always** use `StyleSheet.create((theme) => ({ ... }))` pattern:

```tsx
import { StyleSheet } from "react-native-unistyles";

const styles = StyleSheet.create((theme) => ({
  container: {
    flexDirection: theme.row, // Automatically handles RTL
    paddingHorizontal: 16,
    backgroundColor: theme.colors.background,
  },
  text: {
    color: theme.colors.primary,
    textAlign: theme.rtl ? "right" : "left",
  },
}));
```

### 3. Horizontal ScrollView RTL Handling

**Critical pattern** for horizontal ScrollViews in RTL layouts:

```tsx
const styles = StyleSheet.create((theme) => ({
  scrollViewContainer: {
    transform: [{ scaleX: theme.scaleX }], // Use theme.scaleX directly
    gap: 10,
  },
  scrollViewContent: {
    paddingHorizontal: 16,
    gap: 10,
  },
  // Items inside ScrollView also need transform
  scrollItem: {
    transform: [{ scaleX: theme.scaleX }], // Use theme.scaleX directly
    // ... other styles
  },
}));
```

**Why this works:**

- The ScrollView container is flipped horizontally with `scaleX: -1` in RTL (via `theme.scaleX`)
- Each item inside is flipped back with `scaleX: -1` to appear correctly
- This maintains proper scrolling direction while keeping content readable
- **Always use `theme.scaleX` instead of conditional logic** - it's cleaner and more maintainable

**Example from PaymentMethodsSelector:**

```tsx
<ScrollView
  style={styles.methodsRowContainer}  // Has transform: scaleX
  horizontal
  showsHorizontalScrollIndicator={false}
  contentContainerStyle={styles.methodsRow}
>
  {items.map((item) => (
    <View style={styles.paymentMethodCompact(...)}>  // Also has transform: scaleX
      {/* content */}
    </View>
  ))}
</ScrollView>
```

### 4. FlexDirection Pattern

**Always** use `theme.row` instead of hardcoding `"row"` or `"row-reverse"`:

```tsx
const styles = StyleSheet.create((theme) => ({
  row: {
    flexDirection: theme.row, // ✅ Correct
    alignItems: "center",
    gap: 12,
  },
  // ❌ Don't do this:
  // flexDirection: "row",
}));
```

### 5. Conditional Padding/Margin

Use conditional properties for RTL-aware spacing:

```tsx
const styles = StyleSheet.create((theme) => ({
  input: {
    paddingRight: theme.rtl ? 16 : hasIcon ? 50 : 16,
    paddingLeft: theme.rtl ? (hasIcon ? 50 : 16) : 16,
  },
  icon: {
    [theme.rtl ? "left" : "right"]: 16, // Dynamic property name
  },
}));
```

### 6. Conditional Positioning

Use conditional positioning for absolute/fixed elements:

```tsx
const styles = StyleSheet.create((theme) => ({
  badge: {
    position: "absolute",
    top: -6,
    right: theme.rtl ? undefined : -6,
    left: theme.rtl ? -6 : undefined,
  },
  drawer: {
    ...(theme.rtl ? { left: 0 } : { right: 0 }),
  },
}));
```

### 7. Text Alignment

**Always use `theme.textAlign.left`** instead of conditional logic:

```tsx
const styles = StyleSheet.create((theme) => ({
  text: {
    textAlign: theme.textAlign.left, // ✅ Correct - automatically "left" in LTR, "right" in RTL
  },
  textRight: {
    textAlign: theme.textAlign.right, // Automatically "right" in LTR, "left" in RTL
  },
  textCenter: {
    textAlign: theme.textAlign.center, // Always "center"
  },
}));
```

**Why this is better:**

- `theme.textAlign.left` is already configured correctly in the theme (swaps in RTL)
- No need for conditional logic: `theme.rtl ? "right" : "left"`
- More maintainable and consistent with theme structure

### 8. Conditional Icon Directions

```tsx
import { Icon } from "lucide-react-native";

<Icon
  name={theme.rtl ? "arrow-left" : "arrow-right"}
  size={20}
  color={theme.colors.primary}
/>;
```

### 9. Conditional Border Radius

```tsx
const styles = StyleSheet.create((theme) => ({
  card: {
    borderTopRightRadius: theme.rtl ? 0 : 8,
    borderBottomRightRadius: theme.rtl ? 0 : 8,
    borderTopLeftRadius: theme.rtl ? 8 : 0,
    borderBottomLeftRadius: theme.rtl ? 8 : 0,
  },
}));
```

### 10. Conditional JustifyContent/AlignItems

```tsx
const styles = StyleSheet.create((theme) => ({
  container: {
    justifyContent: theme.rtl ? "flex-end" : "flex-start",
    alignItems: theme.rtl ? "flex-end" : "flex-start",
  },
}));
```

### 11. Theme Switching

**Always** use `UnistylesRuntime.setTheme()` for theme changes:

```tsx
import { UnistylesRuntime } from "react-native-unistyles";

// When language changes
const changeLanguage = (lang: string) => {
  const theme = lang === "en" ? "lightLTR" : "lightRTL";
  UnistylesRuntime.setTheme(theme);
};

// When theme mode changes
const changeTheme = (mode: "light" | "dark", isRTL: boolean) => {
  const theme = `${mode}${isRTL ? "RTL" : "LTR"}`;
  UnistylesRuntime.setTheme(theme);
};
```

**Pattern from useChangeLanguage:**

```tsx
const changeLanguage = async (lang: string) => {
  const theme = lang === "en" ? "lightLTR" : "lightRTL";
  UnistylesRuntime.setTheme(theme);
  // ... other language change logic
};
```

**Pattern from useThemeChange:**

```tsx
useEffect(() => {
  const direction = language === "en" ? "LTR" : "RTL";
  if (theme === "system") {
    UnistylesRuntime.setTheme(`${osTheme ?? "light"}${direction}`);
  } else {
    UnistylesRuntime.setTheme(`${theme}${direction}`);
  }
}, [language, theme, osTheme]);
```

### 12. Responsive Helpers via Runtime (`rt`)

Instead of putting insets, screen size, or orientation on the theme, use the **Unistyles runtime** (`rt`).

`useUnistyles()` can return both theme and runtime:

```tsx
const { theme, rt } = useUnistyles();
```

`StyleSheet.create` also receives `rt` as the second argument:

```tsx
const styles = StyleSheet.create((theme, rt) => ({
  container: {
    paddingTop: rt.insets.top,
    paddingBottom: rt.insets.bottom,
    width: rt.screen.width,
    height: rt.screen.height,
    flexDirection: rt.orientation === "landscape" ? theme.row : "column",
  },
  landscapeOnly: {
    display: rt.orientation === "landscape" ? "flex" : "none",
  },
}));
```

**Use `rt` for:**

- **Insets**: `rt.insets.top`, `rt.insets.bottom`, `rt.insets.left`, `rt.insets.right`
- **Screen**: `rt.screen.width`, `rt.screen.height`
- **Orientation**: `rt.orientation` (e.g. `"portrait" | "landscape"`)

Keep these responsive concerns in `rt` rather than baking them into the theme object.

### Keyboard Avoiding with `ime` Inset

Unistyles exposes a special **keyboard inset** `rt.insets.ime` which animates with the keyboard. You can use it to build a keyboard-avoiding view without extra libraries ([docs](https://www.unistyl.es/v3/guides/avoiding-keyboard)).

```tsx
import { TextInput, View } from "react-native";
import { StyleSheet } from "react-native-unistyles";

const KeyboardAvoidingView = () => {
  return (
    <View style={styles.container}>
      <TextInput style={styles.input} />
    </View>
  );
};

const styles = StyleSheet.create((theme, rt) => ({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "flex-end",
    backgroundColor: theme.colors.background,
    paddingHorizontal: 16,
    paddingTop: rt.insets.top,
    transform: [
      {
        translateY: rt.insets.ime * -1,
      },
    ],
  },
  input: {
    width: "100%",
  },
}));
```

Key points:

- Use **`rt.insets.ime`** for keyboard offset; Unistyles will recalc styles when it changes.
- Combine it with `rt.insets.top`/`bottom` for safe-area-aware padding.

### Reanimated Integration

Unistyles works seamlessly with `react-native-reanimated` and has dedicated helpers for it ([docs](https://www.unistyl.es/v3/guides/reanimated)).

#### 1. Access theme in worklets with `useAnimatedTheme`

Do **not** read the theme in worklets via `useUnistyles` or `UnistylesRuntime.getTheme()`. Instead, use `useAnimatedTheme` from `react-native-unistyles/reanimated`, which returns a `SharedValue` and updates worklets correctly:

```tsx
import { useAnimatedTheme } from "react-native-unistyles/reanimated";
import Animated, { useAnimatedStyle } from "react-native-reanimated";

export const MyAnimatedComponent = () => {
  const theme = useAnimatedTheme(); // SharedValue

  const animatedStyles = useAnimatedStyle(() => ({
    backgroundColor: theme.value.colors.background,
  }));

  return <Animated.View style={[styles.container, animatedStyles]} />;
};
```

#### 2. Animating variant colors with `useAnimatedVariantColor`

If you define variants with color props, you can animate them:

```tsx
const styles = StyleSheet.create((theme, rt) => ({
  box: {
    height: 100,
    width: 100,
    variants: {
      variant: {
        primary: {
          backgroundColor: theme.colors.primary,
        },
        secondary: {
          backgroundColor: theme.colors.secondary,
        },
      },
    },
  },
}));
```

```tsx
import { useAnimatedVariantColor } from "react-native-unistyles/reanimated";
import Animated, { useAnimatedStyle, withTiming } from "react-native-reanimated";

const color = useAnimatedVariantColor(styles.box, "backgroundColor");

const animatedStyles = useAnimatedStyle(() => ({
  backgroundColor: withTiming(color.value, { duration: 300 }),
}));
```

`useAnimatedVariantColor` tracks theme and breakpoint changes and animates to the new color automatically.

#### 3. Merging Unistyles + Reanimated styles

Never spread Unistyles styles into a `useAnimatedStyle` object:

```tsx
// ❌ Bad
const animatedStyles = useAnimatedStyle(() => ({
  ...styles.container,
  opacity: withTiming(1),
}));
```

Instead, keep them **separate** and merge at the component level:

```tsx
// ✅ Good
const animatedStyles = useAnimatedStyle(() => ({
  opacity: withTiming(1),
}));

return <Animated.View style={[styles.container, animatedStyles]} />;
```

This lets Unistyles and Reanimated manage their own style layers without fighting over the same style object.

### Media Queries

Unistyles has a built-in **media query utility** `mq` for pixel-perfect responsive styling ([docs](https://www.unistyl.es/v3/references/media-queries)).

#### Basic usage

```tsx
import { StyleSheet, mq } from "react-native-unistyles";

const styles = StyleSheet.create((theme) => ({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: theme.colors.background,
    // Override background based on width ranges
    backgroundColor: {
      [mq.only.width(240, 380)]: theme.colors.background,
      [mq.only.width(380)]: theme.colors.primary,
    },
  },
}));
```

#### Combining width & height

```tsx
const styles = StyleSheet.create((theme) => ({
  container: {
    backgroundColor: theme.colors.background,
    backgroundColor: {
      [mq.width(240, 380).and.height(300)]: theme.colors.background,
      [mq.width(380).and.height(300)]: theme.colors.secondary,
    },
  },
}));
```

#### Using breakpoints with media queries

You can mix breakpoints with media queries; **media queries win** when both match:

```tsx
const styles = StyleSheet.create((theme) => ({
  container: {
    backgroundColor: {
      sm: theme.colors.background,
      // This mq will override the `sm` breakpoint when it matches
      [mq.only.width(200, "xl")]: theme.colors.primary,
    },
  },
}));
```

Key ideas:
- Use `mq.only.width(...)` / `mq.only.height(...)` for single dimension.
- Use `mq.width(...).and.height(...)` to combine.
- You can pass raw numbers or breakpoint names (`"sm"`, `"md"`, `"xl"`, etc.).

### Variants

Variants let you define reusable “modes” for a style (e.g. color, size) and then select them at runtime ([docs](https://www.unistyl.es/v3/references/variants)). They work great for base components and can be combined with media queries and breakpoints.

#### Basic variant definition

```tsx
const styles = StyleSheet.create((theme) => ({
  container: {
    backgroundColor: theme.colors.background,
    variants: {
      color: {
        primary: {
          backgroundColor: theme.colors.primary,
        },
        secondary: {
          backgroundColor: theme.colors.secondary,
        },
      },
      size: {
        small: { width: 100, height: 40 },
        medium: { width: 160, height: 44 },
        large: { width: 220, height: 48 },
      },
    },
  },
}));
```

#### Selecting variants with `useVariants`

```tsx
const Button = ({ color = "primary", size = "medium" }) => {
  styles.useVariants({ color, size });
  return <View style={styles.container} />;
};
```

TypeScript will infer valid values for `color` and `size` from your `variants` definition.

#### Boolean variants and defaults

```tsx
const styles = StyleSheet.create((theme) => ({
  container: {
    variants: {
      tone: {
        true: { backgroundColor: theme.colors.success },
        false: { backgroundColor: theme.colors.error },
        default: { backgroundColor: theme.colors.warning },
      },
    },
  },
}));

// Examples:
styles.useVariants({ tone: true });      // -> success
styles.useVariants({ tone: false });     // -> error
styles.useVariants({ tone: undefined }); // -> default (if defined)
```

You can also pass component props directly to `useVariants`, or use `UnistylesVariants<typeof styles>` to infer props from your stylesheet.

### 13. Conditional Animation Directions

```tsx
import {
  FadeInLeft,
  FadeInRight,
  FadeOutLeft,
  FadeOutRight,
} from "react-native-reanimated";

<Animated.View
  entering={theme.rtl ? FadeInRight : FadeInLeft}
  exiting={theme.rtl ? FadeOutRight : FadeOutLeft}
>
  {/* content */}
</Animated.View>;
```

### 14. Conditional List Inversion

For horizontal lists/carousels:

```tsx
<FlatList
  horizontal
  inverted={theme.rtl} // Invert list direction in RTL
  data={items}
  renderItem={({ item }) => <Item data={item} />}
/>
```

## Common Patterns Summary


| Use Case              | Pattern                                                                     |
| --------------------- | --------------------------------------------------------------------------- |
| FlexDirection         | `flexDirection: theme.row`                                                  |
| Horizontal ScrollView | `transform: [{ scaleX: theme.scaleX }]` on container and items              |
| Text Alignment        | `textAlign: theme.textAlign.left` (or `.right`, `.center`)                  |
| Padding/Margin        | Conditional `paddingLeft`/`paddingRight` based on `theme.rtl`               |
| Absolute Positioning  | `right: theme.rtl ? undefined : value, left: theme.rtl ? value : undefined` |
| Icon Direction        | `name={theme.rtl ? "arrow-left" : "arrow-right"}`                           |
| Border Radius         | Conditional `borderTopLeftRadius`/`borderTopRightRadius`                    |
| Animation Direction   | Conditional `entering`/`exiting` props                                      |
| List Inversion        | `inverted={theme.rtl}` on FlatList/ScrollView                               |


## Anti-Patterns to Avoid

❌ **Don't hardcode flexDirection:**

```tsx
// ❌ Bad
flexDirection: "row";

// ✅ Good
flexDirection: theme.row;
```

❌ **Don't forget transform on ScrollView items:**

```tsx
// ❌ Bad - only container has transform
<ScrollView style={{ transform: [{ scaleX: theme.rtl ? -1 : 1 }] }}>
  <View>{/* content appears flipped */}</View>
</ScrollView>

// ✅ Good - both container and items use theme.scaleX
<ScrollView style={{ transform: [{ scaleX: theme.scaleX }] }}>
  <View style={{ transform: [{ scaleX: theme.scaleX }] }}>
    {/* content appears correctly */}
  </View>
</ScrollView>
```

❌ **Don't use conditional textAlign:**

```tsx
// ❌ Bad - conditional logic
textAlign: theme.rtl ? "right" : "left";

// ✅ Good - use theme.textAlign helper
textAlign: theme.textAlign.left;
```

❌ **Don't use direct theme access without hook:**

```tsx
// ❌ Bad - won't update on theme change
const color = lightLTR.colors.primary;

// ✅ Good - reactive to theme changes
const { theme } = useUnistyles();
const color = theme.colors.primary;
```

❌ **Don't hardcode theme names:**

```tsx
// ❌ Bad
UnistylesRuntime.setTheme("lightLTR");

// ✅ Good - compute based on current state
const theme = `${mode}${isRTL ? "RTL" : "LTR"}`;
UnistylesRuntime.setTheme(theme);
```

## Examples

### Example: RTL-Aware Card Component

```tsx
import React from "react";
import { View, Text } from "react-native";
import { StyleSheet, useUnistyles } from "react-native-unistyles";

export const Card = ({ title, children }) => {
  const { theme } = useUnistyles();

  return (
    <View style={styles.card}>
      <View style={styles.header}>
        <Text style={styles.title}>{title}</Text>
        <Icon
          name={theme.rtl ? "arrow-left" : "arrow-right"}
          size={20}
          color={theme.colors.primary}
        />
      </View>
      {children}
    </View>
  );
};

const styles = StyleSheet.create((theme) => ({
  card: {
    backgroundColor: theme.colors.white,
    borderRadius: 12,
    padding: 16,
    borderWidth: 1,
    borderColor: theme.colors.background100,
  },
  header: {
    flexDirection: theme.row,
    alignItems: "center",
    justifyContent: "space-between",
    marginBottom: 12,
  },
  title: {
    fontSize: 16,
    fontWeight: 600,
    color: theme.colors.background,
    textAlign: theme.textAlign.left,
  },
}));
```

### Example: Horizontal ScrollView with RTL

```tsx
import React from "react";
import { ScrollView, View } from "react-native";
import { StyleSheet, useUnistyles } from "react-native-unistyles";

export const HorizontalList = ({ items }) => {
  const { theme } = useUnistyles();

  return (
    <ScrollView
      style={styles.scrollContainer}
      horizontal
      showsHorizontalScrollIndicator={false}
      contentContainerStyle={styles.scrollContent}
    >
      {items.map((item) => (
        <View key={item.id} style={styles.item}>
          {/* Item content */}
        </View>
      ))}
    </ScrollView>
  );
};

const styles = StyleSheet.create((theme) => ({
  scrollContainer: {
    transform: [{ scaleX: theme.scaleX }],
  },
  scrollContent: {
    paddingHorizontal: 16,
    gap: 10,
  },
  item: {
    transform: [{ scaleX: theme.scaleX }], // Flip back using theme.scaleX
    padding: 12,
    borderRadius: 8,
    backgroundColor: theme.colors.background50,
    minWidth: 100,
  },
}));
```

### Example: Theme Switcher Hook

```tsx
import { useEffect } from "react";
import { useColorScheme } from "react-native";
import { UnistylesRuntime } from "react-native-unistyles";

export const useThemeChange = (
  language: string,
  themeMode: "light" | "dark" | "system",
) => {
  const osTheme = useColorScheme();

  useEffect(() => {
    const isRTL = language !== "en";
    const mode = themeMode === "system" ? (osTheme ?? "light") : themeMode;
    const theme = `${mode}${isRTL ? "NonEn" : "En"}`;

    UnistylesRuntime.setTheme(theme);
  }, [language, themeMode, osTheme]);
};
```

## Porting to Other Projects

When using this pattern in a new project:

1. **Configure Unistyles:**
  - Set up themes with `rtl`, `row`, `scaleX`, `textAlign` properties
  - Configure theme names following `{mode}{Direction}` pattern (e.g., `lightLTR`, `lightRTL`)
  - Set up `StyleSheet.configure()` with all themes
2. **Create theme objects:**
  - Define LTR theme (e.g., `lightLTR`, `darkLTR`)
  - Define RTL theme (e.g., `lightRTL`, `darkRTL`)
  - Include only **styling concerns** on the theme: `rtl`, `row`, `scaleX`, `writingDirection`, `textAlign`, colors, spacing tokens
3. **Use runtime (`rt`) for responsive data:**
  - Use `rt.insets` for safe-area insets
  - Use `rt.screen` for width/height
  - Use `rt.orientation` for portrait/landscape specific styles
  - Wire this via `StyleSheet.create((theme, rt) => ({ ... }))`
4. **Use patterns consistently:**
  - Always use `useUnistyles()` hook (prefer `{ theme, rt } = useUnistyles()`)
  - Always use `StyleSheet.create((theme, rt) => ({ ... }))` when you need responsive data
  - Always use `theme.row` for flexDirection
  - Always apply transform pattern for horizontal ScrollViews
5. **Handle theme switching:**
  - Use `UnistylesRuntime.setTheme()` when language or mode changes
  - Compute theme name based on current language and mode

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hewr-srood) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
