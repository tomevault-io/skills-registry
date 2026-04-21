---
name: design-language
description: Three-layer aesthetic-agnostic architecture for swappable design systems Use when this capability is needed.
metadata:
  author: tasty-maker-studio
---

# Design Language Architecture

## When to Use This Skill

- Creating or modifying the contract interface
- Implementing a new design language (e.g., adding Fluent, Carbon)
- Understanding how tokens flow from language → Panda CSS
- Debugging theme/token issues

## The Three Layers

```
┌─────────────────────────────────────────────────────────┐
│ Layer 1: INFRASTRUCTURE (Never Changes)                │
├─────────────────────────────────────────────────────────┤
│ src/contracts/design-language.contract.ts              │
│ - DesignLanguageContract interface                     │
│ - ColorPalettes, SemanticColors types                  │
│ - Typography, Spacing, Shape, Elevation, Motion types  │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ Layer 2: DESIGN LANGUAGE (Swappable)                   │
├─────────────────────────────────────────────────────────┤
│ src/languages/material3.language.ts                    │
│ - Implements DesignLanguageContract                    │
│ - Contains actual token values from M3 theme           │
│ - Defines semantic + semanticDark for theming          │
│                                                        │
│ src/languages/index.ts                                 │
│ - export { material3Language as activeLanguage }       │
│ - CHANGE THIS ONE LINE to swap aesthetics              │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ Layer 3: TRANSFORM → PANDA CSS                         │
├─────────────────────────────────────────────────────────┤
│ src/languages/transform.ts                             │
│ - transformToPandaTheme(language) function             │
│ - Returns { tokens, semanticTokens, textStyles }       │
│ - Used in panda.config.ts                              │
└─────────────────────────────────────────────────────────┘
```

## Contract Interface (Key Types)

```typescript
// src/contracts/design-language.contract.ts

export interface DesignLanguageContract {
  name: string;
  version: string;
  colors: ColorPalettes;        // Tonal palettes (0-100)
  semantic: SemanticColors;     // Light theme semantic tokens
  semanticDark?: SemanticColors; // Dark theme overrides
  typography: TypographyConfig;
  spacing: SpacingScale;
  shape: ShapeConfig;
  elevation: ElevationConfig;
  motion: MotionConfig;
}

export interface SemanticColors {
  primary: string;
  onPrimary: string;
  primaryContainer: string;
  onPrimaryContainer: string;
  // ... all M3 semantic colors
  surface: string;
  onSurface: string;
  surfaceContainerLowest: string;
  surfaceContainerLow: string;
  surfaceContainer: string;
  surfaceContainerHigh: string;
  surfaceContainerHighest: string;
}
```

## Language Implementation Pattern

```typescript
// src/languages/material3.language.ts

import type { DesignLanguageContract } from '../contracts/design-language.contract';

export const material3Language: DesignLanguageContract = {
  name: 'Material Design 3',
  version: '1.0.0',
  
  colors: {
    primary: {
      0: '#000000',
      10: '#102000',
      // ... from docs/material-theme.json palettes
      100: '#FFFFFF',
    },
    // secondary, tertiary, neutral, neutralVariant, error
  },
  
  semantic: {
    // Light theme - from docs/material-theme.json schemes.light
    primary: '#4C662B',
    onPrimary: '#FFFFFF',
    surface: '#F9FAEF',
    onSurface: '#1A1C16',
    // ...
  },
  
  semanticDark: {
    // Dark theme - from docs/material-theme.json schemes.dark
    primary: '#B1D18A',
    onPrimary: '#1E3701',
    surface: '#12140E',
    onSurface: '#E2E3D8',
    // ...
  },
  
  typography: { /* M3 type scale */ },
  spacing: { /* M3 spacing */ },
  shape: { /* M3 shape scale */ },
  elevation: { /* M3 elevation */ },
  motion: { /* M3 motion */ },
};
```

## Transform Pattern

```typescript
// src/languages/transform.ts

import type { DesignLanguageContract } from '../contracts/design-language.contract';

export function transformToPandaTheme(language: DesignLanguageContract) {
  return {
    tokens: {
      colors: flattenPalettes(language.colors),
      fonts: {
        display: { value: language.typography.fonts.display },
        body: { value: language.typography.fonts.body },
      },
      // spacing, radii, shadows...
    },
    
    semanticTokens: {
      colors: Object.fromEntries(
        Object.entries(language.semantic).map(([key, value]) => [
          key,
          {
            value: {
              base: value,
              _dark: language.semanticDark?.[key] ?? value,
            },
          },
        ])
      ),
    },
    
    textStyles: transformTypography(language.typography.scale),
  };
}
```

## Files to Reference

- `docs/design-system-setup-prompt.md` - Full contract interface definition
- `docs/material-theme.json` - M3 color values from Material Theme Builder

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tasty-maker-studio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
