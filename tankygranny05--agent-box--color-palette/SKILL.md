---
name: color-palette
description: Display terminal color gradients and palettes for choosing colors. This skill should be used when the user asks to see color options, color gradients, color palettes, ANSI colors, RGB colors, terminal colors, or needs help picking a color for terminal/shell configuration (e.g., Powerlevel10k, zsh themes, prompt colors). Use when this capability is needed.
metadata:
  author: tankygranny05
---

# Color Palette

## Overview

This skill provides a comprehensive color gradient viewer for terminals, displaying 78 vertical color gradients using both ANSI 256 colors and True Color (24-bit RGB). Use it to help users visualize and choose colors for terminal configurations, shell prompts, or any ANSI color selection.

## When to Use

- User asks to see color options, gradients, or palettes
- User needs to pick a color for terminal/shell configuration
- User mentions Powerlevel10k, zsh themes, oh-my-zsh, or prompt colors
- User wants to compare ANSI 256 vs True Color (RGB) options
- User asks about terminal color codes or ANSI escape sequences

## Quick Start

To show all 78 color gradients vertically:

```bash
python ~/.claude/skills/color-palette/scripts/color_gradients.py
```

The script displays:
- **ANSI 256 gradients** (19 gradients): Red, Orange, Yellow, Green, Cyan, Blue, Purple, Magenta, Pink, Earth tones, Grayscale, Rainbow, Pastel
- **True Color RGB gradients** (49 gradients): Smooth color ramps, color families (Teal, Sky Blue, Aquamarine, Turquoise, etc.), Neon variants, Sunset/Sunrise, Fire/Ice, Matrix/Terminal styles

Each gradient shows the text "12:17:00 AM" in that color with the color code on the right:
- `<- 256:N` for ANSI 256 colors
- `<- rgb(R,G,B)` for True Color

## Color Code Reference

**ANSI 256 (256 colors):**
```
\033[38;5;{N}m  where N = 0-255
```

**True Color (16.7 million colors):**
```
\033[38;2;{R};{G};{B}m  where R,G,B each = 0-255
```

## Scripts

### scripts/color_gradients.py

The main color gradient viewer. Run with:
```bash
python ~/.claude/skills/color-palette/scripts/color_gradients.py
```

Output is vertical (one color per line) for easy comparison. Each gradient is numbered (#1/78, #2/78, etc.) for reference.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
