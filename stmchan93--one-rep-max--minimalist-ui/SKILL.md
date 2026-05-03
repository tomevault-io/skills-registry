---
name: minimalist-ui
description: Use when working with a design system and workflow for building high-performance, minimalist, dark-mode mobile-friendly UIs.
metadata:
  author: stmchan93
---

# Minimalist High-Performance UI Skill

Use this skill when the user wants to build a premium, data-driven, minimalist mobile UI. This skill ensures consistency across components, typography, and color theory.

## Core Directives

1. **OLED First**: Always use `#000000` as the primary background.
2. **Elevated Surfaces**: Use `#111111` (zinc-900) for cards and inputs. Use `border-white/10` (1 pixel) to define edges. Never use shadows.
3. **Typography Hierarchy**:
    - **Primary Data**: Use `Inter-Black` (900 weight), `6xl` or larger, with `tracking-tighter`.
    - **Labels**: Use `Inter-Bold` (700+ weight), tiny size (`9px-11px`), `uppercase`, and `tracking-widest`. Color should be `zinc-500` or lower opacity.
4. **The Pill Rule**: Every interactive element (buttons, search bars, tags) must have a large border radius. Use `rounded-full` for small elements and `rounded-3xl` for large cards.
5. **Content Constraints**: Keep content centered with a `max-w-4xl` and `self-center` container.

## Component Recipes

### 1. The Hero Stat
```tsx
<View className="items-center py-10 w-full">
    <Text className="text-primary text-9xl font-black tracking-tighter">
        {value}
    </Text>
    <Text className="-mt-4 text-textSecondary text-[10px] uppercase tracking-widest font-bold opacity-60">
        {label}
    </Text>
</View>
```

### 2. The Surface Item
```tsx
<TouchableOpacity className="bg-surface rounded-2xl p-5 flex-row items-center justify-between border border-white/5">
    <View className="flex-row items-center gap-4">
        <View className="w-10 h-10 rounded-full bg-surfaceLight items-center justify-center">
            {icon}
        </View>
        <Text className="text-textPrimary text-lg font-bold">{label}</Text>
    </View>
    <Ionicons name="chevron-forward" size={20} color="#52525b" />
</TouchableOpacity>
```

## Prompting for Iteration
When refining UI using this skill, always ask:
"Is there any visual clutter we can remove to make the data stand out more?"
"Are our primary numbers large enough?"
"Is the safe-area spacing consistent across screens?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stmchan93) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
