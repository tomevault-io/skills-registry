---
name: design-system-builder
description: Generates the full visual language (color palette, typography, spacing grid, elevation, interactive states) for a website based on a chosen mood. Supports 8 design moods from warm luxury to playful to corporate. Runs after nextjs-scaffold.
metadata:
  author: avien19
---

# design-system-builder

Applies a cohesive design system to the project. One skill, 8 moods, infinite variations.

## When to use

- Right after `nextjs-scaffold` has set up the project
- When user wants to change the whole site's visual direction
- When restyling an existing site to a new mood

## When NOT to use

- User wants one-off style changes (just edit the file directly)
- User has a detailed design spec from a designer (follow that instead)

## Inputs

From `site-plan.md`:
- `mood` (one of 8 - see `references/palettes.md`)
- `vertical` (for font fine-tuning)

## Output

Updated files:
- `src/app/globals.css` - palette + typography + spacing tokens
- `src/lib/constants.ts` - brand colors as constants (optional)
- Design tokens documented in a comment block

## The 8 moods

### 1. Warm luxury
- Palette: cream `#FDF8F0`, gold `#C9A96E`, charcoal `#2C2418`, taupe `#D4C5B2`
- Fonts: Bodoni Moda (display) + Inter (body)
- Feel: fine dining, spa, boutique

### 2. Dark luxury
- Palette: near-black `#0A0A0A`, gold `#D4AF37`, cream `#F5F0E6`, taupe `#4A4035`
- Fonts: Playfair Display (display) + Inter (body)
- Feel: cocktail bars, jewelers, fine dining

### 3. Coastal luxury
- Palette: white `#FEFEFE`, deep navy `#1B3A5B`, sand `#E8DCC4`, seafoam `#A8C8BF`
- Fonts: Cormorant Garamond (display) + Inter (body)
- Feel: resorts, seafood, yacht clubs

### 4. Earthy luxury
- Palette: deep forest `#2D3E2C`, cream `#F7F3E8`, terracotta `#C87E4F`, moss `#8B9A6B`
- Fonts: Playfair Display (display) + Inter (body)
- Feel: farm-to-table, wellness retreats, eco lodges

### 5. Modern minimal
- Palette: off-white `#FAFAFA`, near-black `#1A1A1A`, single accent (electric blue `#0057FF` or neon green `#00D8A8`)
- Fonts: Inter (single font, multiple weights) OR Space Grotesk
- Feel: SaaS, agencies, portfolios

### 6. Bold editorial
- Palette: pure white `#FFFFFF`, pure black `#000000`, single vivid accent (magenta `#EC008C` or electric yellow `#FFD600`)
- Fonts: oversized serif like Fraunces or Canela (display) + Söhne or Inter (body)
- Feel: magazines, creative studios, restaurants with attitude

### 7. Playful
- Palette: pastel bright - peach `#FFB89C`, mint `#B4E4D0`, butter `#FFE699`, dusty rose `#EDA9B9`
- Fonts: DM Serif Display or Fraunces Soft + Inter or DM Sans
- Feel: cafes, kids brands, casual dining, creator economy

### 8. Corporate
- Palette: navy `#0B2545`, slate `#5C6B7D`, off-white `#F4F6F8`, accent blue `#00B4D8`
- Fonts: Inter (single) OR Source Sans Pro
- Feel: B2B SaaS, finance, healthcare, law

## Procedure

### Step 1: Read mood from site-plan.md

If no mood picked, ask `AskUserQuestion` with the 8 options. Show preview colors.

### Step 2: Update globals.css

Replace the `@theme inline` block with the palette for the chosen mood. Keep the structure from `nextjs-scaffold/templates/globals.css.template`. Do NOT touch the force-override `!important` rules at the bottom.

### Step 3: Update layout.tsx fonts

Replace the `next/font/google` imports to match the mood's font pairing. Use the `variable:` prop to wire them up to CSS vars.

### Step 4: Add spacing + elevation tokens

In `globals.css`, add these tokens (8pt grid + 3-tier elevation):

```css
@theme inline {
  /* 8pt spacing grid */
  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-3: 1rem;     /* 16px */
  --space-4: 1.5rem;   /* 24px */
  --space-5: 2rem;     /* 32px */
  --space-6: 3rem;     /* 48px */
  --space-7: 4rem;     /* 64px */
  --space-8: 6rem;     /* 96px */
  --space-9: 8rem;     /* 128px */

  /* Elevation - adjust shadow color per mood */
  --shadow-sm: 0 1px 2px rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 12px rgb(0 0 0 / 0.08);
  --shadow-lg: 0 12px 24px rgb(0 0 0 / 0.12);
}
```

### Step 5: Add interactive state matrix

For each mood, document the state colors in globals.css comments:

```css
/*
 * Interactive states for this mood (warm luxury):
 * default:  gold
 * hover:    gold-dark
 * focus:    gold + 2px ring
 * active:   gold-dark + slight scale
 * disabled: gold at 40% opacity
 */
```

### Step 6: Reduced motion

Already handled in template globals.css. Verify `@media (prefers-reduced-motion: reduce)` block exists.

### Step 7: Typography scale

Default scale for display fonts (mood-dependent):
- Hero: `text-5xl sm:text-6xl md:text-7xl lg:text-8xl`
- Section heading: `text-3xl md:text-4xl lg:text-5xl`
- Subsection: `text-xl md:text-2xl`
- Body: `text-base` (16px) with `leading-relaxed`
- Small: `text-sm`
- Micro: `text-xs uppercase tracking-[0.2em]` (for labels)

Editorial mood uses LARGER sizes (text-7xl hero minimum). Minimal uses SMALLER (text-4xl hero max).

## Gotchas

1. **The font-override `!important` rules are non-negotiable** - see nextjs-scaffold Step 5.

2. **Color ramp matters** - always include light + dark variants of your accent color (gold + gold-dark). Single-tone accents look flat.

3. **Don't use 8 colors** - the palette limits each mood to 4-5 core colors for a reason. Adding more muddies the brand.

4. **Test contrast** - run the accessibility-performance skill to verify WCAG AA (4.5:1 for body text, 3:1 for large text and UI).

5. **Fonts cost performance** - each `next/font/google` import adds a request. Stick to 2 families max (display + body). Add script only if the mood requires it.

6. **Scripts/cursive fonts look cheap unless done well.** If using Dancing Script for a "luxury" brand, make sure it's sparingly (logo area only). Bodoni italic is a better substitute in most cases.

## References

See `references/palettes.md` for full hex codes, Tailwind configs, and sample mood boards.

---
> Source: [avien19/pro-site-builder](https://github.com/avien19/pro-site-builder) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
