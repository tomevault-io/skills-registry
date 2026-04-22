---
name: arlis-branding-guidelines
description: Complete brand identity system for Arlis - the friendly bureaucracy buddy. Use this skill when creating UI components, documentation, or any user-facing materials. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Arlis Branding Skill

## Brand Identity

### Core Positioning
- **Name**: Arlis
- **Tagline**: "Your Friendly Bureaucracy Buddy" (The Bureaucracy Assassin)
- **Primary Descriptor**: Dedicated Advocate (Social Worker)
- **Personality**: Warm, supportive, competent, trustworthy
- **Voice**: Conversational but professional, like a helpful neighbor who knows the system

### Brand Promise
Arlis transforms bureaucratic complexity into simple, human interactions. We don't just automate—we advocate. Arlis is a **Tireless Advocate** that works for you until the job is done.

---

## Visual Identity

### Logo & Avatar
- **Primary Asset**: `/public/arlis.jpg`
- **Usage Locations**:
  - Sidebar logo (10×10, rounded-xl with amber ring)
  - Chat avatar (8×8, rounded-lg)
  - Favicon (`/app/icon.jpg`)
  - Social sharing (Open Graph images)

### Color Palette

```css
/* Primary - Amber (Warmth & Trust) */
--amber-50: #fffbeb;
--amber-100: #fef3c7;
--amber-400: #fbbf24;  /* Primary accent */
--amber-500: #f59e0b;
--amber-600: #d97706;  /* Primary CTA */
--amber-700: #b45309;
--amber-900: #78350f;

/* Neutral - Zinc (Professional & Modern) */
--zinc-50: #fafafa;
--zinc-100: #f4f4f5;
--zinc-500: #71717a;
--zinc-800: #27272a;
--zinc-900: #18181b;
--zinc-950: #09090b;

/* Semantic Colors */
--success: #22c55e;   /* Green - Completed tasks */
--warning: #f59e0b;   /* Amber - Action required */
--error: #ef4444;     /* Red - Errors */
--info: #3b82f6;      /* Blue - In progress */
```

### Typography

**Primary Font**: Figtree (Sans-serif)
- Friendly, modern, highly readable
- Use for body text and UI elements

**Accent Fonts**:
- **Geist Sans**: Headings and emphasis
- **Geist Mono**: Code, technical details, portal URLs

**Font Scales**:
```css
text-xs: 0.75rem;    /* Labels, metadata */
text-sm: 0.875rem;   /* Body text */
text-base: 1rem;     /* Default */
text-lg: 1.125rem;   /* Subheadings */
text-xl: 1.25rem;    /* Section titles */
text-3xl: 1.875rem;  /* Page headers */
```

---

## UI Components

### Design Principles

1. **Glassmorphism**: Use subtle transparency and blur for depth
   ```tsx
   className="bg-white/50 dark:bg-zinc-900/50 backdrop-blur-xl"
   ```

2. **Rounded Corners**: Friendly, approachable feel
   - Small elements: `rounded-lg` (8px)
   - Cards: `rounded-xl` (12px)
   - Buttons: `rounded-lg` to `rounded-2xl`

3. **Shadows**: Soft, elevated
   ```tsx
   className="shadow-lg shadow-zinc-200/50 dark:shadow-none"
   ```

4. **Micro-animations**: Subtle, purposeful
   - Hover states: `hover:scale-[1.02]`
   - Active indicators: `animate-pulse`
   - Loading: `animate-spin`

### Component Patterns

#### Primary Button (CTA)
```tsx
<Button className="bg-amber-600 hover:bg-amber-700 text-white gap-2 rounded-lg">
  <Plus size={18} /> New Request
</Button>
```

#### Status Badge
```tsx
<Badge variant="outline" className="px-3 py-1 gap-1.5 font-medium">
  <span className="w-2 h-2 rounded-full bg-green-500 animate-pulse" />
  Agent Active
</Badge>
```

#### Card with Amber Accent
```tsx
<Card className="bg-amber-50 dark:bg-amber-900/10 border-amber-200/50 dark:border-amber-900/30">
  {/* Content */}
</Card>
```

---

## Voice & Tone

### Writing Guidelines

**DO**:
- Use "I" and "you" (first/second person)
- Explain jargon in plain English
- Show empathy: "I know this is frustrating..."
- Be specific: "I'll fill out Section 4 now" vs "Processing..."

**DON'T**:
- Use corporate speak or legalese
- Say "please wait" without context
- Apologize excessively
- Use passive voice

### Example Messages

❌ **Bad**: "Your request has been processed. Please check your status."

✅ **Good**: "I've submitted your passport renewal! You'll get a confirmation email within 24 hours. I'll keep checking the portal for updates."

---

## Brand Applications

### README.md
- Hero image at top (200px, centered)
- Use emoji for section headers (🎯, 🚀, 🛠)
- Maintain friendly but professional tone

### UI Components
- Always use Arlis's image for chat avatars
- Amber accents for primary actions
- Zinc for neutral/secondary elements

### Error Messages
```tsx
// ❌ Technical
"Error: HTTP 500 - Internal Server Error"

// ✅ Arlis-style
"Oops! The government portal is having issues. I'll retry in 30 seconds. You can take a break—I've got this."
```

### Success States
```tsx
// ❌ Generic
"Task completed successfully"

// ✅ Arlis-style
"Done! I've uploaded your utility bill to the IRS portal. They confirmed receipt at 2:34 PM."
```

---

## Asset Checklist

When creating new features, ensure:

- [ ] Arlis's image is used for agent avatars
- [ ] Amber (#d97706) is the primary CTA color
- [ ] Text uses Figtree font family
- [ ] Rounded corners (rounded-lg minimum)
- [ ] Micro-animations on interactive elements
- [ ] Dark mode support with zinc palette
- [ ] Friendly, conversational copy

---

## Examples in Codebase

**Sidebar Logo**: `/components/component-example.tsx` (line 38-40)
```tsx
<div className="w-10 h-10 rounded-xl flex items-center justify-center shadow-lg shadow-amber-500/20 overflow-hidden bg-white ring-2 ring-amber-400/20">
  <img src="/arlis.jpg" alt="Arlis" className="w-full h-full object-cover" />
</div>
```

**Chat Avatar**: `/components/component-example.tsx` (ChatMessageRole function)
```tsx
<div className="w-8 h-8 rounded-lg overflow-hidden shrink-0 ring-2 ring-amber-400/20">
  <img src="/arlis.jpg" alt="Arlis" className="w-full h-full object-cover" />
</div>
```

**Metadata**: `/app/layout.tsx`
```tsx
export const metadata: Metadata = {
  title: "Arlis - Your Friendly Bureaucracy Buddy",
  description: "Arlis is your autonomous social worker...",
  openGraph: {
    images: ["/arlis.jpg"],
  },
};
```

---

## Quick Reference

| Element       | Style                                 |
| ------------- | ------------------------------------- |
| Primary Color | Amber 600 (#d97706)                   |
| Background    | Zinc 50 (light) / Zinc 950 (dark)     |
| Accent        | Amber 400 (#fbbf24)                   |
| Success       | Green 500 (#22c55e)                   |
| Border Radius | 8px (lg) to 12px (xl)                 |
| Font          | Figtree (body), Geist Sans (headings) |
| Shadow        | `shadow-lg shadow-zinc-200/50`        |
| Avatar Size   | 8×8 (chat), 10×10 (sidebar)           |

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
