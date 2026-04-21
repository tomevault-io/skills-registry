---
name: frontend-app-design
description: Create distinctive, production-grade web interfaces using React, Tailwind, and shadcn/ui. Use when building pages, components, or styling any web UI. Use when this capability is needed.
metadata:
  author: rbrown101010
---

This skill guides creation of distinctive, production-grade web interfaces that avoid generic "AI slop" aesthetics. Implement real working code with exceptional attention to aesthetic details and creative choices.

The user provides frontend requirements: a page, component, or interface to build. They may include context about the purpose, audience, or technical constraints.

## Design Thinking

Before coding, understand the context and commit to a BOLD aesthetic direction:
- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick an extreme: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc. There are so many flavors to choose from. Use these for inspiration but design one that is true to the aesthetic direction.
- **Constraints**: Responsive design. Desktop-first with mobile adaptation. Consider different viewport sizes.
- **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity.

Then implement working React code that is:
- Production-grade and functional
- Visually striking and memorable
- Cohesive with a clear aesthetic point-of-view
- Meticulously refined in every detail

## Web Design Principles

- **Clarity**: Text is legible at every size. Icons are precise. Adornments are subtle.
- **Hierarchy**: Content is organized with clear visual hierarchy. Important elements stand out.
- **Responsiveness**: Layouts adapt gracefully from mobile to desktop.
- **Performance**: Animations are smooth. Images are optimized. Load times are fast.

## Modern Web Aesthetics

Create depth and visual interest through layering and detail:
- **Glass & Blur**: Use `backdrop-blur` for frosted glass effects on overlays, cards, and navigation.
- **Depth & Shadows**: Layered shadows create realistic depth. Use `shadow-sm` through `shadow-2xl` thoughtfully.
- **Subtle Borders**: Thin borders with low opacity to define edges without harshness.
- **Gradients**: Subtle gradient backgrounds and text gradients for visual interest.

## Web Aesthetics Guidelines

Focus on:
- **Typography**: Choose fonts that are beautiful, unique, and interesting. Use Google Fonts for distinctive choices that elevate the interface. Pair a distinctive display font with a refined body font. Import via `<link>` in index.html or CSS `@import`.
- **Color & Theme**: Commit to a cohesive aesthetic. Use Tailwind theme colors for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Use CSS variables for theming.
- **Motion**: Use Tailwind transitions for micro-interactions (hover, focus). Use Framer Motion for complex animations (page transitions, staggered reveals, spring physics). Add subtle feedback on meaningful interactions.
- **Spatial Composition**: Unexpected layouts. Asymmetry. Overlap. Generous negative space OR controlled density. Use CSS Grid and Flexbox for creative layouts.
- **Backgrounds & Visual Details**: Create atmosphere and depth rather than defaulting to solid colors. Use gradients, patterns, subtle textures, and layered transparencies.

## shadcn/ui Components

Leverage the pre-installed shadcn/ui components as building blocks:

**Layout & Structure:**
- `Card` - Container for content sections
- `Separator` - Visual dividers
- `ScrollArea` - Custom scrollbars
- `ResizablePanelGroup` - Split pane layouts
- `Sidebar` - Navigation sidebars

**Navigation:**
- `NavigationMenu` - Top navigation bars
- `Tabs` - Tabbed interfaces
- `Breadcrumb` - Location indicators
- `Pagination` - Page navigation

**Data Display:**
- `Table` - Data tables with sorting/filtering
- `Accordion` - Collapsible content
- `Avatar` - User images
- `Badge` - Status indicators
- `Progress` - Progress bars
- `Skeleton` - Loading placeholders

**Feedback:**
- `Dialog` - Modal dialogs
- `Sheet` - Slide-out panels
- `Toast` (via Sonner) - Notifications
- `Tooltip` - Hover information
- `Alert` - Inline messages

**Forms:**
- `Input`, `Textarea`, `Select` - Form inputs
- `Checkbox`, `RadioGroup`, `Switch` - Toggles
- `Slider` - Range inputs
- `Calendar` - Date selection
- `Command` - Command palette (cmdk)

## Code Patterns

**Responsive Design:**
```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {/* Content adapts to screen size */}
</div>
```

**Glass Effect:**
```tsx
<div className="bg-white/10 backdrop-blur-lg border border-white/20 rounded-xl">
  {/* Frosted glass appearance */}
</div>
```

**Hover Transitions:**
```tsx
<button className="transition-all duration-200 hover:scale-105 hover:shadow-lg">
  Click me
</button>
```

**Gradient Text:**
```tsx
<h1 className="bg-gradient-to-r from-purple-600 to-pink-600 bg-clip-text text-transparent">
  Gradient Heading
</h1>
```

NEVER use generic AI-generated aesthetics like overused font families (Inter, Roboto, system fonts), cliched color schemes (particularly purple gradients on white backgrounds), predictable layouts and component patterns, and cookie-cutter design that lacks context-specific character.

Interpret creatively and make unexpected choices that feel genuinely designed for the context. No design should be the same. Vary between light and dark themes, different fonts, different aesthetics. NEVER converge on common choices (Space Grotesk, for example) across generations.

**IMPORTANT**: Match implementation complexity to the aesthetic vision. Maximalist designs need elaborate code with extensive animations and effects. Minimalist or refined designs need restraint, precision, and careful attention to spacing, typography, and subtle details. Elegance comes from executing the vision well.

Remember: Claude is capable of extraordinary creative work. Don't hold back, show what can truly be created when thinking outside the box and committing fully to a distinctive vision.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rbrown101010) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
