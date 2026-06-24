---
name: brand-guidelines
description: Applies Brighten's official brand colors and typography to any sort of artifact that may benefit from having Brighten's look-and-feel. Use it when brand colors or style guidelines, visual formatting, or company design standards apply. Use when this capability is needed.
metadata:
  author: dherbe-digital
---

# Brighten Brand Styling

## Overview
To access Brighten's official brand identity and style resources, use this skill.

**Keywords**: branding, corporate identity, visual identity, post-processing, styling, brand colors, typography, Brighten brand, visual formatting, visual design

## Brand Guidelines

### Colors

**Main Colors:**
- Navy Blue: `#1a2942` - Primary brand color, dark backgrounds
- Light Gray: `#f5f5f5` - Light backgrounds and clean surfaces
- White: `#ffffff` - Text on dark backgrounds, clean accents
- Dark Gray: `#333333` - Primary text on light backgrounds

**Accent Colors:**
- Bright Red: `#e63946` - Primary accent for titles and calls-to-action
- Medium Gray: `#6c757d` - Secondary text and subtle elements

### Typography
- **Headings**: Poppins (with Arial fallback)
- **Body Text**: Lora (with Georgia fallback)
- **Note**: Fonts should be pre-installed in your environment for best results

## Features

### Smart Font Application
- Applies Poppins font to headings (24pt and larger)
- Applies Lora font to body text
- Automatically falls back to Arial/Georgia if custom fonts unavailable
- Preserves readability across all systems

### Text Styling
- Headings (24pt+): Poppins font, Bright Red (#e63946) for emphasis
- Subheadings: Poppins font, Bright Red (#e63946)
- Body text: Lora font, Dark Gray (#333333) on light backgrounds
- Smart color selection based on background
- Preserves text hierarchy and formatting

### Shape and Accent Colors
- Primary shapes: Navy Blue (#1a2942)
- Accent elements: Bright Red (#e63946)
- Maintains visual interest while staying on-brand
- Creates strong contrast with light backgrounds

## Technical Details

### Font Management
- Uses system-installed Poppins and Lora fonts when available
- Provides automatic fallback to Arial (headings) and Georgia (body)
- No font installation required - works with existing system fonts
- For best results, pre-install Poppins and Lora fonts in your environment

### Color Application
- Uses RGB color values for precise brand matching:
  - Navy Blue: RGB(26, 41, 66)
  - Bright Red: RGB(230, 57, 70)
  - Light Gray: RGB(245, 245, 245)
  - Dark Gray: RGB(51, 51, 51)
  - Medium Gray: RGB(108, 117, 125)
  - White: RGB(255, 255, 255)
- Applied via python-pptx's RGBColor class
- Maintains color fidelity across different systems

## Design Principles

### Color Usage Guidelines
1. **Backgrounds**: Use Navy Blue for primary backgrounds, Light Gray for secondary surfaces
2. **Text**: Bright Red for titles and emphasis, Dark Gray for body text
3. **Accents**: Use Bright Red sparingly for maximum impact on calls-to-action
4. **Contrast**: Maintain high contrast ratios for accessibility (Navy Blue + White, Light Gray + Dark Gray)

### Visual Hierarchy
- Large headings in Bright Red create strong focal points
- Navy Blue backgrounds provide professional, corporate feel
- Light backgrounds with dark text ensure readability
- Consistent spacing and alignment reflect brand sophistication

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dherbe-digital) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
