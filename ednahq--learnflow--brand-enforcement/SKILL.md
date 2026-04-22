---
name: brand-style-guide-enforcement
description: CRITICAL - Enforces Enterprise DNA brand guidelines for all new components, features, and UI elements. Must be referenced before ANY UI work. Use when this capability is needed.
metadata:
  author: ednahq
---

# 🎨 Brand Style Guide Enforcement

**⚠️ CRITICAL: This skill MUST be referenced before creating ANY new component, feature, or UI element.**

You are a brand guardian for LearnFlow (Enterprise DNA). Your primary responsibility is to ensure ALL code follows our brand style guide. When creating or modifying UI components, you MUST enforce these standards.

## 🚨 MANDATORY PRE-CHECKLIST

Before writing ANY component code, verify:

- [ ] Colors match brand palette ONLY
- [ ] Typography uses Poppins font family
- [ ] Icons are from approved library (Lucide React)
- [ ] Gradients use brand colors
- [ ] Spacing follows design system
- [ ] No arbitrary colors or fonts

---

## 🎨 BRAND COLOR PALETTE

### Approved Colors ONLY

**Primary Brand Colors:**
- **Blue/Purple**: `#6654f5` → Use: `brand-purple` or `text-brand-purple` or `bg-brand-purple`
- **Pink**: `#ca5a8b` → Use: `brand-pink` or `text-brand-pink` or `bg-brand-pink`
- **Yellow/Gold**: `#f2b347` → Use: `brand-gold` or `text-brand-gold` or `bg-brand-gold`
- **Black**: `#0b0c18` → Use: `brand-black` or CSS variable `--brand-black`
- **White**: `#ffffff` → Use: `white` or `bg-white` or `text-white`

### ❌ FORBIDDEN Colors

**DO NOT USE:**
- Any hex colors not listed above
- Generic colors like `blue-500`, `red-600`, `green-400` (unless for system states like errors)
- Arbitrary Tailwind colors like `bg-indigo-500`, `text-emerald-600`
- Any color that doesn't match the brand palette

### ✅ Color Usage Patterns

**Large Sections:**
```tsx
// ✅ CORRECT: White or black backgrounds
<div className="bg-white">...</div>
<div className="bg-black">...</div>
<div className="bg-brand-black">...</div>

// ❌ WRONG: Brand colors as large backgrounds
<div className="bg-brand-purple">...</div> // Only for accents!
```

**Accents & Highlights:**
```tsx
// ✅ CORRECT: Brand colors as accents
<span className="text-brand-purple">Highlight</span>
<div className="border-brand-pink">...</div>
<button className="bg-brand-purple">...</button>

// ✅ CORRECT: Brand gradient for CTAs
<button className="brand-gradient text-white">...</button>
```

**Gradients:**
```tsx
// ✅ CORRECT: Use predefined gradient classes
<div className="brand-gradient">...</div>
<div className="text-gradient">...</div> // For text gradients
<div className="brand-gradient-light">...</div> // Subtle background

// ❌ WRONG: Custom gradients
<div className="bg-gradient-to-r from-blue-500 to-purple-600">...</div>
```

---

## 🔤 TYPOGRAPHY

### Font Family

**ONLY USE Poppins:**
- Already configured in `src/index.css` as default body font
- Font weights: Light (300), Medium (500), Bold (700)
- NO other fonts allowed (except Georgetown for logo/branding text only)

### Typography Scale

**Headlines:**
```tsx
// ✅ CORRECT: Poppins Medium for headlines
<h1 className="font-medium text-3xl">...</h1> // Poppins Medium
<h2 className="font-medium text-2xl">...</h2>
<h3 className="font-medium text-xl">...</h3>

// Minimum sizes: 30pt (30px) for headlines
```

**Body Copy:**
```tsx
// ✅ CORRECT: Poppins Light for body
<p className="font-light text-base">...</p> // Poppins Light (16pt)
<p className="font-light text-sm">...</p> // Smaller body text

// Minimum size: 16pt (16px) for body text
```

**Emphasis:**
```tsx
// ✅ CORRECT: Poppins Bold for emphasis
<span className="font-bold">...</span>
<strong className="font-bold">...</strong>
```

**Text Gradients:**
```tsx
// ✅ CORRECT: Use text-gradient class for brand-colored text
<h1 className="text-gradient">...</h1>
<span className="text-gradient">...</span>

// ❌ WRONG: Custom gradient text
<span className="bg-gradient-to-r from-purple-500 to-pink-500 bg-clip-text">...</span>
```

---

## 🎯 ICONS

### Approved Icon Library: Lucide React

**ONLY USE Lucide React icons:**
```tsx
// ✅ CORRECT: Import from lucide-react
import { ChevronRight, User, Settings, Home } from "lucide-react";

<ChevronRight className="w-5 h-5" />
<User className="w-6 h-6" />
```

**❌ FORBIDDEN Icon Libraries:**
- React Icons (react-icons)
- Heroicons (unless migrating to Lucide)
- Font Awesome
- Material Icons
- Any other icon library

**Icon Usage:**
```tsx
// ✅ CORRECT: Standard icon patterns
<ChevronRight className="w-5 h-5 text-brand-purple" />
<Settings className="w-6 h-6 text-white" />
<User className="w-4 h-4 text-gray-600" />

// Common sizes: w-4 h-4 (16px), w-5 h-5 (20px), w-6 h-6 (24px)
```

---

## 🎨 COMPONENT PATTERNS

### Buttons

```tsx
// ✅ CORRECT: Primary CTA button
<Button className="brand-gradient text-white hover:opacity-90">
  Click Me
</Button>

// ✅ CORRECT: Secondary button
<Button variant="outline" className="border-brand-purple text-brand-purple">
  Secondary
</Button>

// ❌ WRONG: Custom colors
<Button className="bg-blue-500 text-white">...</Button>
```

### Cards

```tsx
// ✅ CORRECT: Card with brand accents
<div className="bg-white rounded-2xl p-6 border border-gray-200">
  <h3 className="text-gradient font-medium text-xl mb-2">Title</h3>
  <p className="font-light text-base text-gray-600">Content</p>
  <div className="h-1 brand-gradient mt-4"></div> // Brand accent
</div>

// ✅ CORRECT: Hover effects with brand colors
<div className="group">
  <div className="absolute inset-0 brand-gradient-light opacity-0 group-hover:opacity-100 transition-opacity"></div>
</div>
```

### Badges/Tags

```tsx
// ✅ CORRECT: Brand-colored badges
<span className="px-3 py-1 rounded-full brand-gradient text-white text-sm">
  Tag
</span>

<span className="px-3 py-1 rounded-full bg-brand-purple/10 text-brand-purple text-sm">
  Tag
</span>
```

---

## 📐 SPACING & LAYOUT

### Border Radius

```tsx
// ✅ CORRECT: Standard border radius
<div className="rounded-lg">...</div>    // 8px
<div className="rounded-xl">...</div>    // 12px
<div className="rounded-2xl">...</div>   // 16px
<div className="rounded-3xl">...</div>   // 24px
<div className="rounded-full">...</div>  // Full circle
```

### Spacing

```tsx
// ✅ CORRECT: Consistent spacing
<div className="p-4 sm:p-6 lg:p-8">...</div> // Responsive padding
<div className="mb-4 sm:mb-6">...</div>      // Responsive margins
<div className="gap-4 sm:gap-6 lg:gap-8">...</div> // Grid gaps
```

---

## 🎯 DESIGN PRINCIPLES

### 1. Color Usage
- **Large sections**: White (`bg-white`) or Black (`bg-black` or `bg-brand-black`)
- **Accents**: Brand colors (`brand-purple`, `brand-pink`, `brand-gold`)
- **Gradients**: Use `brand-gradient` class for CTAs and highlights
- **Never**: Use brand colors for large background areas

### 2. White Space
- Generous padding and margins
- Clean, uncluttered layouts
- Clear visual hierarchy

### 3. Professional Aesthetic
- Modern, clean design
- Consistent spacing
- Strategic use of gradients
- Professional yet approachable

---

## ✅ CODE REVIEW CHECKLIST

When creating or modifying components, ensure:

1. **Colors**
   - [ ] Only brand colors used (`brand-purple`, `brand-pink`, `brand-gold`, `brand-black`, `white`)
   - [ ] No arbitrary Tailwind colors (`blue-500`, `red-600`, etc.)
   - [ ] Gradients use `brand-gradient` or `text-gradient` classes
   - [ ] Large sections are white or black

2. **Typography**
   - [ ] Poppins font family (default, no need to specify)
   - [ ] Headlines use `font-medium` (Poppins Medium)
   - [ ] Body text uses `font-light` (Poppins Light)
   - [ ] Emphasis uses `font-bold` (Poppins Bold)
   - [ ] Text gradients use `text-gradient` class

3. **Icons**
   - [ ] Only Lucide React icons imported
   - [ ] No other icon libraries used
   - [ ] Icons sized consistently (`w-4 h-4`, `w-5 h-5`, `w-6 h-6`)

4. **Components**
   - [ ] Buttons use `brand-gradient` for primary actions
   - [ ] Cards have consistent styling
   - [ ] Interactive elements use brand colors
   - [ ] Hover states use brand colors

5. **Design Principles**
   - [ ] Clean, modern aesthetic
   - [ ] Generous white space
   - [ ] Professional appearance
   - [ ] Strategic gradient usage

---

## 🚨 COMMON VIOLATIONS TO AVOID

### ❌ Color Violations
```tsx
// ❌ WRONG: Arbitrary colors
<div className="bg-blue-500">...</div>
<button className="bg-indigo-600">...</button>
<span className="text-emerald-500">...</span>

// ✅ CORRECT: Brand colors
<div className="bg-brand-purple">...</div>
<button className="brand-gradient">...</button>
<span className="text-brand-purple">...</span>
```

### ❌ Typography Violations
```tsx
// ❌ WRONG: Wrong font weights
<h1 className="font-bold text-3xl">...</h1> // Should be font-medium
<p className="font-normal text-base">...</p> // Should be font-light

// ✅ CORRECT: Proper font weights
<h1 className="font-medium text-3xl">...</h1>
<p className="font-light text-base">...</p>
```

### ❌ Icon Violations
```tsx
// ❌ WRONG: Wrong icon library
import { FaUser } from "react-icons/fa";
import { HomeIcon } from "@heroicons/react/24/outline";

// ✅ CORRECT: Lucide React
import { User, Home } from "lucide-react";
```

---

## 📚 REFERENCE FILES

- **Brand Style Guide**: `docs/BRAND_STYLE_GUIDE.md`
- **CSS Variables**: `src/index.css` (lines 28-35)
- **Tailwind Config**: `tailwind.config.ts` (lines 56-61)
- **Example Components**: 
  - `src/components/home/HeroSection.tsx` (good example)
  - `src/components/navigation/MainNav.tsx` (good example)

---

## 🎯 QUICK REFERENCE

**Colors:**
- Primary: `brand-purple` (#6654f5)
- Accent: `brand-pink` (#ca5a8b)
- Highlight: `brand-gold` (#f2b347)
- Backgrounds: `white`, `black`, `brand-black`
- Gradients: `brand-gradient`, `text-gradient`, `brand-gradient-light`

**Typography:**
- Headlines: `font-medium` (Poppins Medium)
- Body: `font-light` (Poppins Light)
- Emphasis: `font-bold` (Poppins Bold)
- Gradient text: `text-gradient`

**Icons:**
- Library: `lucide-react`
- Sizes: `w-4 h-4`, `w-5 h-5`, `w-6 h-6`

---

**Remember: When in doubt, check the brand style guide and existing components. Consistency is key!**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ednahq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
