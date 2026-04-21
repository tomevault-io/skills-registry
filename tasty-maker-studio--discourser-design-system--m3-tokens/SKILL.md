---
name: m3-tokens
description: Material Design 3 color system, semantic tokens, and theme values from the project's Material Theme Builder export Use when this capability is needed.
metadata:
  author: tasty-maker-studio
---

# Material Design 3 Token System

## When to Use This Skill

- Implementing semantic color tokens
- Setting up light/dark theme switching
- Understanding M3 color roles
- Referencing the project's actual color values

## Source of Truth

All colors come from `docs/material-theme.json`, exported from Material Theme Builder.

**Seed Color:** `#63A002` (Discourser/TastyMakers green)

## Key Color Values

### Light Theme (schemes.light)

```json
{
  "primary": "#4C662B",
  "onPrimary": "#FFFFFF",
  "primaryContainer": "#CDEDA3",
  "onPrimaryContainer": "#354E16",
  
  "secondary": "#586249",
  "onSecondary": "#FFFFFF",
  "secondaryContainer": "#DCE7C8",
  "onSecondaryContainer": "#404A33",
  
  "tertiary": "#386663",
  "onTertiary": "#FFFFFF",
  "tertiaryContainer": "#BCECE7",
  "onTertiaryContainer": "#1F4E4B",
  
  "surface": "#F9FAEF",
  "onSurface": "#1A1C16",
  "surfaceVariant": "#E1E4D5",
  "onSurfaceVariant": "#44483D",
  
  "surfaceContainerLowest": "#FFFFFF",
  "surfaceContainerLow": "#F3F4E9",
  "surfaceContainer": "#EEEFE3",
  "surfaceContainerHigh": "#E8E9DE",
  "surfaceContainerHighest": "#E2E3D8",
  
  "outline": "#75796C",
  "outlineVariant": "#C5C8BA",
  
  "error": "#BA1A1A",
  "onError": "#FFFFFF",
  "errorContainer": "#FFDAD6",
  "onErrorContainer": "#93000A"
}
```

### Dark Theme (schemes.dark)

```json
{
  "primary": "#B1D18A",
  "onPrimary": "#1E3701",
  "primaryContainer": "#354E16",
  "onPrimaryContainer": "#CDEDA3",
  
  "secondary": "#BFCBAD",
  "onSecondary": "#29331D",
  "secondaryContainer": "#404A33",
  "onSecondaryContainer": "#DCE7C8",
  
  "tertiary": "#A0D0CB",
  "onTertiary": "#003734",
  "tertiaryContainer": "#1F4E4B",
  "onTertiaryContainer": "#BCECE7",
  
  "surface": "#12140E",
  "onSurface": "#E2E3D8",
  "surfaceVariant": "#44483D",
  "onSurfaceVariant": "#C5C8BA",
  
  "surfaceContainerLowest": "#0D0F09",
  "surfaceContainerLow": "#1A1C16",
  "surfaceContainer": "#1E201A",
  "surfaceContainerHigh": "#282B24",
  "surfaceContainerHighest": "#33362F",
  
  "outline": "#8F9285",
  "outlineVariant": "#44483D",
  
  "error": "#FFB4AB",
  "onError": "#690005",
  "errorContainer": "#93000A",
  "onErrorContainer": "#FFDAD6"
}
```

## Semantic Token Pattern in Panda CSS

```typescript
// In transform.ts or panda.config.ts

semanticTokens: {
  colors: {
    primary: {
      value: { base: '#4C662B', _dark: '#B1D18A' }
    },
    onPrimary: {
      value: { base: '#FFFFFF', _dark: '#1E3701' }
    },
    surface: {
      value: { base: '#F9FAEF', _dark: '#12140E' }
    },
    onSurface: {
      value: { base: '#1A1C16', _dark: '#E2E3D8' }
    },
    // ... all semantic colors
  }
}
```

## Using Semantic Tokens in Recipes

```typescript
// Always use semantic names, never raw hex values
buttonRecipe = defineRecipe({
  variants: {
    variant: {
      filled: {
        bg: 'primary',           // ✅ Semantic
        color: 'onPrimary',      // ✅ Semantic
        // bg: '#4C662B',        // ❌ Raw hex
      },
    },
  },
});
```

## Dark Mode Activation

```html
<!-- In HTML -->
<html data-theme="dark">

<!-- Or via class -->
<html class="dark">
```

```typescript
// In panda.config.ts conditions
conditions: {
  light: '[data-theme=light] &, .light &',
  dark: '[data-theme=dark] &, .dark &'
}
```

## M3 Color Role Usage Guide

| Role | Use For |
|------|---------|
| `primary` | Main action buttons, FABs, active states |
| `onPrimary` | Text/icons on primary color |
| `primaryContainer` | Less prominent primary elements |
| `secondary` | Secondary actions, less emphasis |
| `tertiary` | Accent, complementary elements |
| `surface` | Card backgrounds, sheets |
| `surfaceContainer*` | Elevated surfaces at different levels |
| `outline` | Borders, dividers |
| `error` | Error states, destructive actions |

## Files to Reference

- `docs/material-theme.json` - Complete export with all palettes
- `src/languages/material3.language.ts` - Implementation (once created)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tasty-maker-studio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
