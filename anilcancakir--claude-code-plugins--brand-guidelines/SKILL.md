---
name: brand-guidelines
description: Platform-agnostic brand identity creation using 12 Jungian archetypes. Use when running /my_brand, creating brand.md, selecting color palettes, defining typography, or asking about brand personality and voice guidelines. Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Brand Guidelines Skill

Create comprehensive, platform-agnostic brand guidelines using Jungian archetypes and color psychology.

## Purpose

Define brand IDENTITY that works across ALL platforms:
- Web (any framework)
- Mobile (iOS, Android, Flutter)
- Print (marketing materials)
- Social media

Output: HEX colors and font names, NOT framework-specific code.

## The 12 Brand Archetypes

| Archetype | Core Desire | Brand Voice | Color Direction |
|-----------|-------------|-------------|-----------------|
| **Innocent** | Safety, happiness | Optimistic, simple, honest | Soft pastels, white, sky blue |
| **Sage** | Knowledge, truth | Expert, clear, trustworthy | Deep blues, greens, gray |
| **Explorer** | Freedom, discovery | Adventurous, independent | Earth tones, rust, forest |
| **Outlaw** | Revolution, liberation | Bold, rebellious, provocative | Black, red, orange |
| **Magician** | Transformation | Visionary, inspiring, mysterious | Purple, deep blue, gold |
| **Hero** | Mastery, courage | Empowering, confident, direct | Red, blue, gold |
| **Lover** | Intimacy, experience | Passionate, sensual, elegant | Red, pink, burgundy |
| **Jester** | Joy, fun | Playful, irreverent, entertaining | Bright, varied, yellow |
| **Everyman** | Belonging | Relatable, honest, friendly | Neutral, warm brown, earth |
| **Caregiver** | Service, nurturing | Compassionate, supportive, warm | Soft blue, green, white |
| **Ruler** | Control, power | Authoritative, refined, premium | Navy, gold, burgundy |
| **Creator** | Innovation, expression | Imaginative, artistic, expressive | Varied, artistic, bold |

## Archetype Selection by Product Type

| Product Type | Primary | Secondary | Why |
|--------------|---------|-----------|-----|
| B2B SaaS | Sage | Magician | Trust + transformation |
| Developer Tools | Sage | Creator | Knowledge + innovation |
| E-commerce | Everyman | Caregiver | Accessible + supportive |
| Fintech | Ruler | Sage | Authority + expertise |
| Health/Wellness | Caregiver | Innocent | Care + purity |
| Creative Tools | Creator | Magician | Expression + transformation |
| Enterprise | Hero | Ruler | Achievement + authority |
| Consumer App | Jester | Everyman | Fun + relatable |
| Luxury | Ruler | Lover | Premium + desire |

## Color Palette Creation

### Step 1: Choose Primary Color

Based on archetype, select a primary hue:

| Archetype | Recommended Hues | Psychology |
|-----------|------------------|------------|
| Sage | Blue (200-240) | Trust, expertise, calm |
| Magician | Purple (260-290) | Mystery, transformation |
| Hero | Red (0-15) or Blue (210-230) | Energy, confidence |
| Ruler | Navy (220-240) or Gold (40-50) | Authority, premium |
| Creator | Any bold color | Creativity, uniqueness |

### Step 2: Generate 9-Shade Scale

Use HSL to create shades from a base color:

| Shade | Lightness | Saturation Adjust | Usage |
|-------|-----------|-------------------|-------|
| 50 | 97% | -5% | Lightest backgrounds |
| 100 | 94% | -5% | Light backgrounds |
| 200 | 86% | -3% | Light accents |
| 300 | 74% | 0% | Borders |
| 400 | 60% | 0% | Disabled states |
| 500 | 50% | Base | Primary (base) |
| 600 | 42% | +3% | Hover states |
| 700 | 34% | +5% | Active/pressed |
| 800 | 26% | +5% | Dark accents |
| 900 | 18% | +5% | Darkest |

### Step 3: Add Semantic Colors

| Role | HEX Example | Usage |
|------|-------------|-------|
| Success | #10B981 | Confirmations, positive feedback |
| Warning | #F59E0B | Caution, attention needed |
| Error | #EF4444 | Errors, destructive actions |
| Info | #3B82F6 | Neutral information |

### Step 4: Create Neutral Grays

Add brand tint to neutral grays:
- **Cool tint** (blue): Professional, tech
- **Warm tint** (yellow/brown): Friendly, approachable

## Typography Guidelines

### Font Pairing by Archetype

| Archetype | Heading Style | Body Style | Examples |
|-----------|---------------|------------|----------|
| Sage | Geometric sans or serif | Clean sans | Inter, Source Sans Pro |
| Magician | Modern sans | System stack | Manrope, Plus Jakarta Sans |
| Hero | Bold sans | Clean sans | Montserrat, Roboto |
| Ruler | Elegant serif | Refined sans | Playfair Display, Lora |
| Creator | Display/artistic | Readable sans | Space Grotesk, Poppins |
| Everyman | Friendly sans | System stack | Nunito, Open Sans |
| Jester | Playful/rounded | Friendly sans | Nunito, Quicksand |

### Type Scale (4px Baseline)

| Name | Size | Weight | Line Height | Usage |
|------|------|--------|-------------|-------|
| xs | 12px | 400 | 16px | Captions, footnotes |
| sm | 14px | 400 | 20px | Labels, metadata |
| base | 16px | 400 | 24px | Body text |
| lg | 18px | 400 | 28px | Lead paragraphs |
| xl | 20px | 600 | 28px | Small headings |
| 2xl | 24px | 600 | 32px | Section headings |
| 3xl | 30px | 700 | 36px | Page titles |
| 4xl | 36px | 700 | 40px | Hero headings |
| 5xl | 48px | 700 | 48px | Display |

## Voice & Tone by Archetype

### Sage Voice
- **Tone**: Expert but accessible
- **Do**: Explain complex topics simply, cite evidence, be precise
- **Don't**: Be condescending, use jargon without explanation

### Magician Voice
- **Tone**: Visionary and inspiring
- **Do**: Paint possibilities, use transformative language
- **Don't**: Over-promise, be vague about how

### Hero Voice
- **Tone**: Empowering and confident
- **Do**: Challenge and motivate, celebrate achievement
- **Don't**: Be aggressive, diminish struggles

### Ruler Voice
- **Tone**: Authoritative and refined
- **Do**: Be confident, use premium language
- **Don't**: Be elitist, talk down to users

### Creator Voice
- **Tone**: Imaginative and expressive
- **Do**: Inspire creativity, celebrate uniqueness
- **Don't**: Be pretentious, dismiss simplicity

### Everyman Voice
- **Tone**: Relatable and honest
- **Do**: Use everyday language, be authentic
- **Don't**: Try too hard to be cool, be generic

## brand.md Output Format

```markdown
# Brand Guidelines

> [Project Name] Brand Identity

## Brand Personality

**Primary Archetype**: [Name] (70%)
[Brief description]

**Secondary Archetype**: [Name] (30%)
[Brief description]

## Core Values

1. **[Value]**: [What it means]
2. **[Value]**: [What it means]
3. **[Value]**: [What it means]

## Color Palette

### Primary Colors

| Name | HEX | Usage |
|------|-----|-------|
| Primary | #XXXXXX | CTAs, key elements |
| Secondary | #XXXXXX | Supporting elements |
| Accent | #XXXXXX | Highlights |

### Primary Scale

| Shade | HEX | Usage |
|-------|-----|-------|
| 50 | #XXXXXX | Lightest backgrounds |
| ... | ... | ... |
| 900 | #XXXXXX | Darkest |

### Semantic Colors

| Role | HEX | Usage |
|------|-----|-------|
| Success | #XXXXXX | Confirmations |
| Warning | #XXXXXX | Cautions |
| Error | #XXXXXX | Errors |
| Info | #XXXXXX | Information |

## Typography

### Font Stack
- **Headings**: [Font Name]
- **Body**: [Font Name]
- **Monospace**: [Font Name]

### Type Scale
[Type scale table]

## Voice & Tone

### Brand Voice
- [Attribute]: [Description]

### Tone by Context
| Context | Tone | Example |
|---------|------|---------|
| Marketing | [Tone] | "[Example]" |
| UI | [Tone] | "[Example]" |
| Errors | [Tone] | "[Example]" |

### Writing Guidelines

**Do:**
- [Guideline]

**Don't:**
- [Anti-pattern]
```

## Color Accessibility

Ensure WCAG AA compliance:
- **Normal text**: 4.5:1 contrast ratio
- **Large text**: 3:1 contrast ratio
- **UI elements**: 3:1 contrast ratio

Test combinations:
- Light text on dark backgrounds
- Dark text on light backgrounds
- Buttons and interactive elements

## References

For detailed archetype color palettes and examples:
- [references/brand-archetypes.md](references/brand-archetypes.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
