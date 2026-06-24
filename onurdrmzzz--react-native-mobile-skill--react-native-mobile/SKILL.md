---
name: react-native-mobile
description: Build production-grade React Native mobile apps for iOS and Android with distinctive, high-quality design. Use this skill when the user asks to create mobile apps, mobile UI components, mobile screens, or work with React Native. Focuses on creating visually striking, memorable interfaces that avoid generic "AI slop" aesthetics. Covers bold design choices, UI patterns, navigation, state management, animations, performance optimization, and platform-specific guidelines (iOS Human Interface / Material Design). Suitable for consumer apps, business apps, and enterprise solutions. Use when this capability is needed.
metadata:
  author: onurdrmzzz
---

# React Native Mobile Development

Build distinctive, production-grade mobile applications that feel native and look unforgettable.

## Design Thinking

Before coding, understand the context and commit to a **BOLD aesthetic direction**:

- **Purpose**: What problem does this app solve? Who uses it? (shift workers, shoppers, fitness enthusiasts?)
- **Tone**: Pick a direction and commit fully:
  - **Dark & Premium** — Deep backgrounds, glowing accents, subtle gradients
  - **Light & Airy** — Generous whitespace, soft shadows, muted colors
  - **Vibrant & Playful** — Bold colors, rounded shapes, bouncy animations
  - **Minimal & Editorial** — Typography-focused, restrained palette, precise spacing
  - **Glassmorphic** — Blur effects, transparency, floating layers
  - **Neomorphic** — Soft shadows, extruded elements, tactile feel
  - **Brutalist** — Raw, bold, unconventional layouts
- **Differentiation**: What makes this UNFORGETTABLE? One signature element users will remember.

**CRITICAL**: Generic mobile apps all look the same. Commit to a clear aesthetic and execute with precision. Your app should be recognizable from a screenshot.

## Color System

### Dark Theme (Like your Takvim app)
```tsx
const colors = {
  // Backgrounds - layered depth
  background: '#0a0a0f',      // Deepest layer
  surface: '#12121a',         // Cards, elevated surfaces
  surfaceLight: '#1a1a24',    // Hover states, secondary surfaces
  
  // Text hierarchy
  textPrimary: '#ffffff',
  textSecondary: '#9ca3af',
  textMuted: '#6b7280',
  
  // Accent colors - BOLD and purposeful
  primary: '#6366f1',         // Indigo - primary actions
  
  // Semantic colors - shift types example
  morning: '#22c55e',         // Green - Sabah
  afternoon: '#f59e0b',       // Amber - Akşam  
  night: '#3b82f6',           // Blue - Gece
  dayOff: '#ef4444',          // Red - İzin
  
  // State colors
  success: '#10b981',
  warning: '#f59e0b',
  error: '#ef4444',
  info: '#3b82f6',
};
```

### Light Theme
```tsx
const colors = {
  background: '#f8fafc',
  surface: '#ffffff',
  surfaceLight: '#f1f5f9',
  
  textPrimary: '#0f172a',
  textSecondary: '#64748b',
  textMuted: '#94a3b8',
  
  primary: '#6366f1',
  // ... same accent colors
};
```

### Color Usage Rules
- **Background layering**: Create depth with 2-3 background shades
- **Accent restraint**: Max 4-5 accent colors, each with clear meaning
- **Contrast**: Ensure WCAG AA compliance (4.5:1 for text)
- **Consistency**: Same color = same meaning throughout app

## Typography

### Font Selection
Avoid system defaults. Choose distinctive fonts:

```tsx
// For React Native, use custom fonts or Expo Google Fonts
import { useFonts, Outfit_400Regular, Outfit_600SemiBold, Outfit_700Bold } from '@expo-google-fonts/outfit';

// Or distinctive alternatives:
// - Satoshi - Modern geometric sans
// - Cabinet Grotesk - Friendly, characterful
// - General Sans - Clean, professional
// - Plus Jakarta Sans - Warm, readable
// - Space Grotesk - Technical, unique (use sparingly)
```

### Type Scale
```tsx
const typography = {
  // Display - for hero moments
  display: { fontSize: 32, fontWeight: '700', lineHeight: 40 },
  
  // Headings
  h1: { fontSize: 28, fontWeight: '700', lineHeight: 36 },
  h2: { fontSize: 24, fontWeight: '600', lineHeight: 32 },
  h3: { fontSize: 20, fontWeight: '600', lineHeight: 28 },
  
  // Body
  bodyLarge: { fontSize: 17, fontWeight: '400', lineHeight: 26 },
  body: { fontSize: 15, fontWeight: '400', lineHeight: 24 },
  bodySmall: { fontSize: 13, fontWeight: '400', lineHeight: 20 },
  
  // UI elements
  label: { fontSize: 13, fontWeight: '600', lineHeight: 18, letterSpacing: 0.5 },
  caption: { fontSize: 12, fontWeight: '500', lineHeight: 16 },
};
```

## Spacing & Layout

### Spacing Scale (8pt grid)
```tsx
const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
};
```

### Layout Principles
- **Generous padding**: Don't crowd elements. Mobile needs breathing room.
- **Consistent margins**: Screen padding typically 16-20px
- **Visual grouping**: Related items closer together, unrelated items further apart
- **Touch targets**: Minimum 44x44pt for interactive elements

## Shadows & Elevation

### Dark Theme Shadows
```tsx
// Dark themes use lighter/glowing shadows
const darkShadows = {
  sm: {
    shadowColor: '#6366f1',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 2,
  },
  md: {
    shadowColor: '#6366f1',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.15,
    shadowRadius: 12,
    elevation: 4,
  },
  glow: {
    shadowColor: '#6366f1',
    shadowOffset: { width: 0, height: 0 },
    shadowOpacity: 0.4,
    shadowRadius: 20,
    elevation: 8,
  },
};
```

### Light Theme Shadows
```tsx
const lightShadows = {
  sm: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.05,
    shadowRadius: 4,
    elevation: 2,
  },
  md: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.08,
    shadowRadius: 12,
    elevation: 4,
  },
  lg: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 8 },
    shadowOpacity: 0.12,
    shadowRadius: 24,
    elevation: 8,
  },
};
```

## Animation Philosophy

### Principles
- **Purposeful**: Every animation should have meaning
- **Swift**: Mobile users expect snappy responses (200-300ms)
- **Natural**: Use spring physics over linear timing
- **Restrained**: One hero animation per screen, subtle micro-interactions elsewhere

### Reanimated Patterns
```tsx
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
  interpolateColor,
  Easing,
} from 'react-native-reanimated';

// Spring configs
const springConfig = {
  damping: 15,
  stiffness: 150,
  mass: 0.5,
};

// Press feedback - scale down slightly
function PressableCard({ children, onPress }) {
  const scale = useSharedValue(1);
  
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
  }));

  return (
    <Pressable
      onPressIn={() => { scale.value = withSpring(0.97, springConfig); }}
      onPressOut={() => { scale.value = withSpring(1, springConfig); }}
      onPress={onPress}
    >
      <Animated.View style={animatedStyle}>{children}</Animated.View>
    </Pressable>
  );
}

// Staggered list entry
function StaggeredList({ data }) {
  return data.map((item, index) => (
    <Animated.View
      key={item.id}
      entering={FadeInDown.delay(index * 50).springify()}
    >
      <ListItem item={item} />
    </Animated.View>
  ));
}
```

### Gesture-Driven Interactions
```tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';

// Swipeable card with haptic feedback
function SwipeableShiftCard({ shift, onSwipeLeft, onSwipeRight }) {
  const translateX = useSharedValue(0);
  const opacity = useSharedValue(1);

  const gesture = Gesture.Pan()
    .onUpdate((e) => {
      translateX.value = e.translationX;
    })
    .onEnd((e) => {
      if (e.translationX > 100) {
        translateX.value = withTiming(400);
        opacity.value = withTiming(0);
        runOnJS(onSwipeRight)();
      } else if (e.translationX < -100) {
        translateX.value = withTiming(-400);
        opacity.value = withTiming(0);
        runOnJS(onSwipeLeft)();
      } else {
        translateX.value = withSpring(0);
      }
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }],
    opacity: opacity.value,
  }));

  return (
    <GestureDetector gesture={gesture}>
      <Animated.View style={animatedStyle}>
        <ShiftCard shift={shift} />
      </Animated.View>
    </GestureDetector>
  );
}
```

## Component Patterns

### Calendar Day Cell (Like Takvim)
```tsx
interface DayCellProps {
  day: number;
  shiftType?: 'morning' | 'afternoon' | 'night' | 'dayOff';
  isToday?: boolean;
  isSelected?: boolean;
  onPress: () => void;
}

function DayCell({ day, shiftType, isToday, isSelected, onPress }: DayCellProps) {
  const shiftColors = {
    morning: '#22c55e',
    afternoon: '#f59e0b',
    night: '#3b82f6',
    dayOff: '#ef4444',
  };

  return (
    <Pressable onPress={onPress} style={styles.dayCell}>
      <Text style={[styles.dayNumber, isToday && styles.todayText]}>{day}</Text>
      {shiftType && (
        <View style={[
          styles.shiftIndicator,
          { backgroundColor: shiftColors[shiftType] },
          isSelected && styles.selectedIndicator,
        ]}>
          <Text style={styles.shiftLabel}>
            {shiftType[0].toUpperCase()}
          </Text>
        </View>
      )}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  dayCell: {
    width: 44,
    height: 60,
    alignItems: 'center',
    justifyContent: 'center',
    gap: 4,
  },
  dayNumber: {
    fontSize: 15,
    color: '#9ca3af',
  },
  todayText: {
    color: '#fff',
    fontWeight: '600',
  },
  shiftIndicator: {
    width: 28,
    height: 28,
    borderRadius: 8,
    alignItems: 'center',
    justifyContent: 'center',
  },
  selectedIndicator: {
    borderWidth: 2,
    borderColor: '#fff',
  },
  shiftLabel: {
    fontSize: 12,
    fontWeight: '700',
    color: '#fff',
  },
});
```

### Bottom Tab Bar (Custom)
```tsx
function CustomTabBar({ state, descriptors, navigation }) {
  return (
    <View style={styles.tabBar}>
      {state.routes.map((route, index) => {
        const { options } = descriptors[route.key];
        const isFocused = state.index === index;
        
        const icons = {
          Today: 'calendar',
          Calendar: 'grid',
          Settings: 'settings',
        };

        return (
          <Pressable
            key={route.key}
            onPress={() => navigation.navigate(route.name)}
            style={styles.tabItem}
          >
            <Animated.View
              style={[
                styles.tabIconContainer,
                isFocused && styles.tabIconFocused,
              ]}
            >
              <Icon
                name={icons[route.name]}
                size={22}
                color={isFocused ? '#6366f1' : '#6b7280'}
              />
            </Animated.View>
            <Text style={[
              styles.tabLabel,
              isFocused && styles.tabLabelFocused,
            ]}>
              {route.name}
            </Text>
          </Pressable>
        );
      })}
    </View>
  );
}

const styles = StyleSheet.create({
  tabBar: {
    flexDirection: 'row',
    backgroundColor: '#12121a',
    paddingBottom: 24, // Safe area
    paddingTop: 12,
    borderTopWidth: 1,
    borderTopColor: '#1f1f2e',
  },
  tabItem: {
    flex: 1,
    alignItems: 'center',
    gap: 4,
  },
  tabIconContainer: {
    width: 44,
    height: 32,
    alignItems: 'center',
    justifyContent: 'center',
    borderRadius: 16,
  },
  tabIconFocused: {
    backgroundColor: '#6366f120',
  },
  tabLabel: {
    fontSize: 11,
    color: '#6b7280',
  },
  tabLabelFocused: {
    color: '#6366f1',
    fontWeight: '600',
  },
});
```

### Shift Detail Card
```tsx
function ShiftDetailCard({ shift, style }) {
  const shiftColors = {
    morning: { bg: '#22c55e20', text: '#22c55e', label: 'Sabah' },
    afternoon: { bg: '#f59e0b20', text: '#f59e0b', label: 'Akşam' },
    night: { bg: '#3b82f620', text: '#3b82f6', label: 'Gece' },
    dayOff: { bg: '#ef444420', text: '#ef4444', label: 'İzin' },
  };

  const config = shiftColors[shift.type];

  return (
    <View style={[styles.card, { backgroundColor: config.bg }, style]}>
      <View style={styles.cardHeader}>
        <Text style={styles.dateText}>{shift.date}</Text>
        <View style={[styles.badge, { backgroundColor: config.text }]}>
          <Text style={styles.badgeText}>{shift.type[0].toUpperCase()}</Text>
        </View>
      </View>
      <Text style={[styles.shiftName, { color: config.text }]}>{config.label}</Text>
      <Text style={styles.timeText}>{shift.startTime} - {shift.endTime}</Text>
    </View>
  );
}
```

## Anti-Patterns (AVOID)

❌ **Generic color schemes** — Don't use #007AFF blue everywhere
❌ **System fonts only** — San Francisco/Roboto are fine but boring
❌ **Flat design without depth** — Use shadows, layers, subtle gradients
❌ **Identical component styling** — Each app should have personality
❌ **Ignoring dark mode** — Dark themes need different shadow/contrast approaches
❌ **Over-animating** — Not everything needs to bounce
❌ **Tiny touch targets** — Min 44pt, ideally 48pt
❌ **No visual hierarchy** — Use size, weight, color to guide the eye

## Quality Checklist

Before shipping, verify:
- [ ] Color palette is cohesive with clear hierarchy
- [ ] Typography scale is consistent throughout
- [ ] Touch targets are minimum 44x44pt
- [ ] Animations are smooth 60fps
- [ ] Dark/Light themes both look intentional
- [ ] Loading states are elegant (skeleton > spinner)
- [ ] Empty states are designed, not afterthoughts
- [ ] Error states are helpful, not scary
- [ ] The app is recognizable from a single screenshot

## References

For detailed code examples:
- **Screen Templates**: See `references/screen-templates.md`
- **UI Components**: See `references/ui-components.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onurdrmzzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
