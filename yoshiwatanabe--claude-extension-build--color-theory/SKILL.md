---
name: color-theory
description: This skill should be used when the user asks about "color mixing", "what colors make", "color combinations", "RGB values", "complementary colors", "color harmony", "color wheel", or discusses color relationships and theory. Use when this capability is needed.
metadata:
  author: yoshiwatanabe
---

# Color Theory

## Purpose

This skill provides fundamental knowledge about color theory, color mixing, harmonies, and practical applications. It includes a utility script for RGB color calculations, demonstrating how skills can bundle executable tools alongside knowledge.

## Core Color Concepts

### Primary Colors

**Additive (Light) - RGB**
- Red (255, 0, 0)
- Green (0, 255, 0)
- Blue (0, 0, 255)

Used in digital displays, screens, and light-based applications. When combined at full intensity, they create white light.

**Subtractive (Pigment) - RYB**
- Red
- Yellow
- Blue

Traditional artist's color wheel. When mixed, they theoretically create black (in practice, dark brown). Used for paint, ink, and physical pigments.

**Subtractive (Print) - CMYK**
- Cyan
- Magenta
- Yellow
- Key (Black)

Professional printing standard. Cyan, Magenta, and Yellow theoretically create black, but Key (black) is added for true blacks and to save ink.

### Secondary Colors

**RGB Additive System:**
- Red + Green = Yellow (255, 255, 0)
- Red + Blue = Magenta (255, 0, 255)
- Green + Blue = Cyan (0, 255, 255)

**RYB Subtractive System:**
- Red + Yellow = Orange
- Yellow + Blue = Green
- Blue + Red = Purple/Violet

### Tertiary Colors

Created by mixing primary and secondary colors:
- Red-Orange
- Yellow-Orange
- Yellow-Green
- Blue-Green
- Blue-Violet
- Red-Violet

## Color Properties

### Hue
The pure color itself (red, blue, green, etc.). Position on the color wheel.

### Saturation (Chroma)
Intensity or purity of a color:
- **High saturation**: Vivid, intense colors
- **Low saturation**: Muted, grayed-out colors
- **Zero saturation**: Pure gray

### Value (Brightness/Lightness)
How light or dark a color is:
- **High value**: Lighter, toward white (tints)
- **Low value**: Darker, toward black (shades)
- **Middle value**: Pure hue

### Tint, Tone, and Shade

**Tint**: Color + White (lighter version)
```
Pure Red → Pink (red tint)
```

**Shade**: Color + Black (darker version)
```
Pure Blue → Navy (blue shade)
```

**Tone**: Color + Gray (muted version)
```
Pure Green → Sage (green tone)
```

## Color Harmonies

### Complementary Colors
Colors opposite each other on the color wheel. Create high contrast and vibrancy:
- Red ↔ Green
- Blue ↔ Orange
- Yellow ↔ Purple

**Usage**: Headlines, call-to-action buttons, attention-grabbing designs

### Analogous Colors
Colors adjacent on the color wheel (typically 3 colors). Create harmony and unity:
- Red, Red-Orange, Orange
- Blue, Blue-Green, Green
- Yellow, Yellow-Green, Green

**Usage**: Backgrounds, cohesive designs, natural-looking palettes

### Triadic Colors
Three colors evenly spaced on the color wheel. Balanced and vibrant:
- Red, Yellow, Blue (primary triad)
- Orange, Green, Purple (secondary triad)

**Usage**: Playful designs, balanced variety, energetic compositions

### Split-Complementary
Base color plus two colors adjacent to its complement. Softer than complementary:
- Blue, Yellow-Orange, Red-Orange
- Red, Yellow-Green, Blue-Green

**Usage**: High contrast without tension, more nuanced than complementary

### Tetradic (Rectangle)
Two complementary pairs forming a rectangle on the color wheel:
- Red, Green, Blue, Orange

**Usage**: Rich, complex designs with careful balance needed

### Square
Four colors evenly spaced on the color wheel:
- Red, Yellow-Green, Blue, Red-Orange

**Usage**: Dynamic designs, needs one color as dominant

### Monochromatic
Variations of a single hue using different tints, shades, and tones:
- Light Blue, Medium Blue, Dark Blue, Navy

**Usage**: Elegant, cohesive, minimalist designs

## RGB Color Mixing

In digital/light-based systems (additive color):

### Mixing Rules
- Red + Green = Yellow
- Red + Blue = Magenta
- Green + Blue = Cyan
- Red + Green + Blue = White
- No light = Black (0, 0, 0)

### Partial Mixing Examples
```
(255, 0, 0) + (0, 255, 0) = (255, 255, 0)   # Red + Green = Yellow
(128, 0, 0) + (0, 128, 0) = (128, 128, 0)   # Dark Red + Dark Green = Olive
(255, 0, 0) + (0, 0, 255) = (255, 0, 255)   # Red + Blue = Magenta
```

### Using the Color Mixer Script

This skill includes a Python utility for RGB calculations. To use it:

```bash
python $CLAUDE_PLUGIN_ROOT/skills/color-theory/scripts/rgb_mixer.py --color1 255,0,0 --color2 0,255,0
```

**Output:**
```
Color 1: RGB(255, 0, 0) - #FF0000
Color 2: RGB(0, 255, 0) - #00FF00
Mixed (50/50): RGB(128, 128, 0) - #808000
```

**Options:**
- `--color1 R,G,B`: First color (required)
- `--color2 R,G,B`: Second color (required)
- `--ratio RATIO`: Mix ratio, e.g., 0.3 means 30% color1 + 70% color2 (default: 0.5)
- `--format FORMAT`: Output format: hex, rgb, or both (default: both)

**Examples:**
```bash
# Equal mix
python rgb_mixer.py --color1 255,0,0 --color2 0,0,255

# 70% red, 30% blue
python rgb_mixer.py --color1 255,0,0 --color2 0,0,255 --ratio 0.7

# Hex output only
python rgb_mixer.py --color1 255,128,0 --color2 0,128,255 --format hex
```

## Practical Applications

### Web Design
**Best practices:**
- Use 60-30-10 rule: 60% dominant color, 30% secondary, 10% accent
- Ensure sufficient contrast for readability (WCAG AA: 4.5:1 for text)
- Test color schemes with colorblind simulators
- Consider cultural color associations

**Common palettes:**
- Professional: Blues and grays
- Energetic: Reds and oranges
- Natural: Greens and browns
- Luxury: Purple, gold, black

### Branding
- Primary brand color: Main identity
- Secondary colors: Support and variation
- Neutral colors: Backgrounds and text
- Accent color: Calls-to-action

### User Interface
- Background: Low saturation, neutral
- Text: High contrast with background
- Primary actions: High saturation, complementary to background
- Secondary actions: Lower saturation
- Errors: Red tones
- Success: Green tones
- Warnings: Yellow/orange tones
- Info: Blue tones

## Color Psychology

### Emotional Associations

**Red**
- Emotions: Energy, passion, danger, urgency
- Usage: Sale signs, calls-to-action, warnings
- Caution: Can increase heart rate, overwhelming in large amounts

**Blue**
- Emotions: Trust, calm, professionalism, stability
- Usage: Corporate branding, healthcare, finance
- Caution: Can feel cold if overused

**Green**
- Emotions: Nature, growth, health, wealth
- Usage: Environmental brands, health products, success messages
- Caution: Can feel stagnant if too muted

**Yellow**
- Emotions: Optimism, happiness, caution, energy
- Usage: Attention-grabbing, cheerful designs, warnings
- Caution: Eye strain if too bright, can feel cheap if overused

**Purple**
- Emotions: Luxury, creativity, spirituality, mystery
- Usage: Beauty products, creative brands, premium offerings
- Caution: Can feel artificial or too feminine

**Orange**
- Emotions: Enthusiasm, confidence, warmth, playfulness
- Usage: Call-to-action buttons, youth brands, food
- Caution: Can feel cheap or unprofessional

**Pink**
- Emotions: Feminine, youthful, romance, sweetness
- Usage: Beauty, fashion, targeted demographics
- Caution: Gender stereotyping, can feel frivolous

**Black**
- Emotions: Sophistication, power, elegance, mystery
- Usage: Luxury brands, minimalist design, text
- Caution: Can feel heavy, ominous, or oppressive

**White**
- Emotions: Purity, cleanliness, simplicity, innocence
- Usage: Minimalist design, healthcare, weddings
- Caution: Can feel sterile, empty, or stark

**Gray**
- Emotions: Neutral, professional, timeless, balance
- Usage: Technology, professional services, backgrounds
- Caution: Can feel boring, depressing, or lifeless

## Color Accessibility

### WCAG Contrast Requirements

**Normal Text (< 18pt or < 14pt bold):**
- Level AA: 4.5:1 contrast ratio minimum
- Level AAA: 7:1 contrast ratio minimum

**Large Text (≥ 18pt or ≥ 14pt bold):**
- Level AA: 3:1 contrast ratio minimum
- Level AAA: 4.5:1 contrast ratio minimum

### Color Blindness Considerations

**Types:**
- Protanopia: Red-blind (1% of males)
- Deuteranopia: Green-blind (1% of males)
- Tritanopia: Blue-blind (very rare)
- Achromatopsia: Complete color blindness (extremely rare)

**Best practices:**
- Never use color alone to convey information
- Use patterns, icons, or labels alongside color
- Test with color blindness simulators
- Prefer blue-orange over red-green contrasts
- Ensure sufficient brightness contrast

## Quick Reference Tables

### Common Color Combinations

| Palette Type | Colors | Use Case |
|--------------|--------|----------|
| Corporate | Navy, Gray, White | Professional, trustworthy |
| Nature | Green, Brown, Beige | Organic, environmental |
| Energy | Red, Orange, Yellow | Exciting, dynamic |
| Calm | Blue, Teal, White | Healthcare, wellness |
| Luxury | Purple, Gold, Black | Premium, exclusive |
| Modern | Black, White, Accent | Minimalist, clean |

### RGB to Hex Conversion

| RGB | Hex | Color Name |
|-----|-----|------------|
| (255, 0, 0) | #FF0000 | Pure Red |
| (0, 255, 0) | #00FF00 | Pure Green |
| (0, 0, 255) | #0000FF | Pure Blue |
| (255, 255, 0) | #FFFF00 | Yellow |
| (255, 0, 255) | #FF00FF | Magenta |
| (0, 255, 255) | #00FFFF | Cyan |
| (255, 255, 255) | #FFFFFF | White |
| (0, 0, 0) | #000000 | Black |
| (128, 128, 128) | #808080 | Gray |

## Additional Resources

For comprehensive color information and advanced techniques:

- **`scripts/rgb_mixer.py`** - RGB color mixing calculator with ratio support
- **`references/color-spaces.md`** - Detailed explanation of HSL, HSV, LAB color spaces
- **`references/cultural-colors.md`** - Color symbolism across different cultures

## Implementation Notes

This skill demonstrates:
- **Auto-activation**: Triggers on color-related queries
- **Bundled script**: Includes executable Python utility for color calculations
- **Progressive disclosure**: Core theory here (~1,700 words), detailed color spaces in references
- **Practical focus**: Balances theory with real-world applications
- **Tool integration**: Shows how to reference and use bundled scripts

When using this skill:
1. Provide color theory fundamentals directly
2. Reference the RGB mixer script for calculations
3. Load color-spaces reference for advanced topics (HSL, HSV, LAB)
4. Consult cultural-colors reference for international projects
5. Always consider accessibility requirements in recommendations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoshiwatanabe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
