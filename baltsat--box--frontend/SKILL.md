---
name: frontend
description: TRIGGER when: building a landing page, marketing site, app UI, prototype, demo, game UI, or any visually-led frontend surface. Covers design composition, visual hierarchy, React/Tailwind patterns, accessibility, motion, and state management. DO NOT TRIGGER when: fixing CSS bugs, tweaking colors, minor style changes, or isolated component fixes within existing design systems. Use when this capability is needed.
metadata:
  author: baltsat
---

# Frontend Development

## Pre-Design Working Model

Before building any visually-led page, write three things:

- **visual thesis**: one sentence — mood, material, energy. e.g. "warm editorial photography, matte surfaces, slow scroll reveals"
- **content plan**: hero → support → detail → CTA (adapt to page type)
- **interaction thesis**: 2–3 motion ideas that change the feel of the page

each section gets one job, one dominant visual idea, one primary takeaway or action.

## Design System Foundations

### Tokens

define early. use CSS variables:

- `--background`, `--surface`, `--primary-text`, `--muted-text`, `--accent`
- typography roles: `display`, `headline`, `body`, `caption`

### Typography

- two typefaces max. one serif/display + one sans for body is the sweet spot.
- use expressive, purposeful fonts. AVOID default stacks (Inter, Roboto, Arial, system-ui) unless the project's design system requires them.
- scale: establish clear hierarchy — display > headline > body > caption with consistent ratios.

### Color

- choose a clear visual direction; define CSS variables.
- one accent color by default. no purple bias. no dark-mode-by-default bias.
- ensure contrast ≥ 4.5:1 for text.

### Spacing

- use consistent spacing scale. tailwind: `p-1`=4px, `p-2`=8px, `p-4`=16px, `p-6`=24px, `p-8`=32px.
- use whitespace, alignment, scale, cropping, and contrast BEFORE adding chrome (borders, shadows, cards).

## Composition Rules

### The First Viewport

treat it as a **poster**, not a document. it must read as one composition.

**hero budget** — the first viewport should usually contain ONLY:

- brand/product name (hero-level signal, not just nav text)
- one headline (2–3 lines desktop, one glance on mobile)
- one short supporting sentence
- one CTA group
- one dominant image/visual

do NOT place stats, schedules, event listings, address blocks, promos, metadata rows, or secondary marketing content in the first viewport.

**brand test**: if the first viewport could belong to another brand after removing the nav, the branding is too weak.

**image test**: if the first viewport still works after removing the image, the image is too weak.

### Hero

- full-bleed by default. edge-to-edge visual plane or background. no inherited page gutters, no framed container, no shared max-width — constrain only the inner text/action column.
- do NOT use inset heroes, side-panel heroes, rounded media cards, tiled collages, or floating image blocks unless the design system requires it.
- no hero overlays: no detached labels, floating badges, promo stickers, info chips, or callout boxes on top of hero media.
- all text over imagery must maintain strong contrast and clear tap targets.
- keep text column narrow, anchored to a calm area of the image.

**viewport budget**: if sticky/fixed header exists, it counts against the hero. combined header + hero must fit initial viewport. use `calc(100svh - var(--header-h))` or overlay the header.

### Sections

- one job per section. one headline, usually one short supporting sentence.
- one dominant visual idea per section.
- no section should need many tiny UI devices to explain itself.

### Cards

**default: no cards.** never use cards in the hero.

cards are allowed ONLY when the card IS the interaction container. if removing border, shadow, background, or radius does not hurt interaction or understanding — it should not be a card.

use sections, columns, dividers, lists, and media blocks instead.

### Clutter

avoid: pill clusters, stat strips, icon rows, boxed promos, schedule snippets, multiple competing text blocks, logo clouds, floating dashboards.

## Page Types

### Landing Pages

default narrative sequence:

1. **hero** — brand/product, promise, CTA, one dominant visual
2. **support** — one concrete feature, offer, or proof point
3. **detail** — atmosphere, workflow, product depth, or story
4. **social proof** — credibility (optional)
5. **final CTA** — convert, start, visit, or contact

max six sections. one H1. one primary CTA above the fold.

### Apps & Dashboards

default to Linear-style restraint:

- calm surface hierarchy
- strong typography and spacing
- few colors, dense but readable information
- minimal chrome
- cards only when the card IS the interaction

organize around:

- primary workspace
- navigation
- secondary context / inspector
- one clear accent for action or state

avoid:

- dashboard-card mosaics
- thick borders on every region
- decorative gradients behind routine product UI
- multiple competing accent colors
- ornamental icons that don't improve scanning

if a panel can become plain layout without losing meaning, remove the card treatment.

## Imagery

imagery must do narrative work, not decoration.

- use at least one strong, real-looking image for brands, venues, editorial, lifestyle products.
- prefer in-situ photography over abstract gradients or fake 3D objects.
- choose/crop images with a stable tonal area for text overlay.
- do not use images with embedded signage, logos, or typographic clutter fighting the UI.
- do not generate images with built-in UI frames, splits, cards, or panels.
- multiple moments → multiple images, not one collage.
- decorative texture alone is NOT a visual anchor.

## Copy

### Marketing (landing pages, branded surfaces)

- write in product language, not design commentary.
- let the headline carry the meaning.
- supporting copy: one short sentence.
- cut repetition between sections.
- do not leak prompt language or design commentary into UI.
- if deleting 30% of the copy improves the page, keep deleting.

### Utility (dashboards, admin tools, product UI)

- prioritize orientation, status, action over promise, mood, brand voice.
- start with the working surface: KPIs, charts, filters, tables, status.
- section headings = what the area IS or what the user CAN DO. e.g. "selected KPIs", "plan status", "search metrics", "top segments", "last sync".
- avoid aspirational hero lines, metaphors, campaign language on product surfaces.
- supporting text: explain scope, behavior, freshness, or decision value in one sentence.
- litmus: if an operator scans only headings, labels, and numbers — can they understand the page?

**rule**: if a sentence could appear in a homepage hero or ad, rewrite it until it sounds like product UI.

## Motion

use motion to create presence and hierarchy, not noise.

ship at least 2–3 intentional motions for visually-led work:

- one entrance sequence in the hero
- one scroll-linked, sticky, or depth effect
- one hover/reveal/layout transition that sharpens affordance

if the project already uses framer-motion, prefer it for: section reveals, shared layout transitions, scroll-linked transforms, sticky storytelling, carousels that advance narrative, menus/drawers/modals. otherwise use CSS transitions/animations or Web Animations API — do not add framer-motion without explicit approval.

### Motion Rules

- noticeable in a quick recording
- smooth on mobile
- fast and restrained
- consistent across the page
- removed if ornamental only

## React Patterns

### Components

```tsx
interface Props {
  title: string;
  onClick?: () => void;
}

export function Card({ title, onClick }: Props) {
  return (
    <div className="p-4 rounded-lg border" onClick={onClick}>
      {title}
    </div>
  );
}
```

### Conditional Rendering

```tsx
if (loading) return <Spinner />;
if (error) return <Error message={error} />;
return <Content data={data} />;
```

### React Best Practices

- prefer `useEffectEvent`, `startTransition`, `useDeferredValue` when appropriate.
- do NOT add `useMemo`/`useCallback` by default unless already used in the codebase; follow React Compiler guidance.
- `React.lazy()` + `Suspense` for route-level code splitting.
- `@tanstack/react-virtual` for long lists.

## Tailwind

### Responsive

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
```

### Dark Mode

```tsx
<div className="bg-white dark:bg-gray-900">
```

### Common Patterns

```tsx
// button
'inline-flex items-center justify-center rounded-md px-4 py-2 text-sm font-medium';

// center
'flex items-center justify-center';

// full-bleed hero
'relative min-h-svh w-full overflow-hidden';

// constrained content inside full-bleed
'mx-auto max-w-7xl px-4 sm:px-6 lg:px-8';
```

## State Management

### Local

```tsx
const [count, setCount] = useState(0);
```

### Zustand (shared)

```tsx
const useStore = create<Store>((set) => ({
  count: 0,
  inc: () => set((s) => ({ count: s.count + 1 })),
}));
```

### TanStack Query (server)

```tsx
const { data, isLoading } = useQuery({
  queryKey: ['users'],
  queryFn: () => fetch('/api/users').then((r) => r.json()),
});
```

## Forms (react-hook-form + zod)

```tsx
const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

const { register, handleSubmit } = useForm({
  resolver: zodResolver(schema),
});
```

## shadcn/ui

components install to `src/components/ui/`. customize in place.

if shadcn MCP tools are available:

- `mcp__shadcn__search_items_in_registries` — find components
- `mcp__shadcn__view_items_in_registries` — see component code
- `mcp__shadcn__get_add_command_for_items` — get install command

## Accessibility

### Required

- images need `alt`
- buttons need text or `aria-label`
- forms need `<label>`
- contrast ≥ 4.5:1
- `focus:ring-2` for focus rings
- escape closes modals

### Semantic HTML

```tsx
<button> not <div onClick>
<a href> for navigation
<nav>, <main>, <header>, <footer>
```

## Viewport Heuristics

when building, keep these in mind for responsive sanity:

- landing pages: hero should fill first viewport on desktop (1440px) and mobile (375px)
- dashboards: primary workspace readable at 1440px, key data visible at 375px
- tap targets ≥ 44px on mobile
- if `agent-browser` is available, take viewport snapshots to gut-check layout

## Design Defaults

these are starting-point defaults, not rules — override when the design calls for it:

- no cards unless the card IS the interaction
- full-bleed hero, not boxed/center-column
- one dominant idea per section
- brand louder than headline on branded pages
- max two typefaces, one accent color
- no purple-on-white (classic LLM default)
- desktop + mobile must both work

## Common Anti-Patterns

patterns that signal the design went off-track:

- generic SaaS card grid as first impression
- beautiful image with weak brand presence
- strong headline with no clear action
- busy imagery behind text
- carousel with no narrative purpose
- app UI made of stacked cards instead of layout
- aspirational marketing copy on operational dashboards

## Self-Check Questions

quick gut-check while building (NOT a gate — AR handles quality assurance):

- is the brand/product unmistakable in the first screen?
- is there one strong visual anchor?
- can the page be understood by scanning headlines only?
- does each section have one job?
- does motion improve hierarchy or atmosphere?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baltsat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
