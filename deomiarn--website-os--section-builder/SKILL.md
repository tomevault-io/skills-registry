---
name: section-builder
description: Build high-quality, conversion-optimized website sections. Use when creating hero sections, feature grids, testimonials, pricing tables, CTAs, and other common website sections with modern design patterns. Use when this capability is needed.
metadata:
  author: deomiarn
---

# Section Builder

This skill provides patterns and guidelines for building website sections that are visually stunning, highly functional, and optimized for conversions.

## When to Use This Skill

- Creating any website section (hero, features, pricing, etc.)
- Implementing custom section designs from inspiration
- Building responsive section layouts
- Adding animations and interactions to sections

## Core Principles

### 1. Section Anatomy

Every section should have:
```tsx
<section className="relative py-section-md overflow-hidden">
  {/* Optional: Background layer */}
  <div className="absolute inset-0 -z-10">
    {/* Gradients, patterns, images */}
  </div>

  {/* Container */}
  <div className="container mx-auto px-4">
    {/* Section header (optional) */}
    <div className="text-center max-w-3xl mx-auto mb-12">
      <span className="text-sm font-medium text-primary">Label</span>
      <h2 className="text-3xl md:text-4xl font-bold mt-2">Headline</h2>
      <p className="text-muted-foreground mt-4">Supporting text</p>
    </div>

    {/* Section content */}
    <div className="grid ...">
      {/* Content */}
    </div>
  </div>
</section>
```

### 2. Hero Section Patterns

**Pattern A: Split Hero (Image + Text)**
```tsx
<section className="min-h-[90vh] flex items-center">
  <div className="container grid lg:grid-cols-2 gap-12 items-center">
    <div className="space-y-6">
      <Badge>New Feature</Badge>
      <h1 className="text-4xl md:text-5xl lg:text-6xl font-bold tracking-tight">
        Compelling headline that <span className="text-primary">captures attention</span>
      </h1>
      <p className="text-xl text-muted-foreground max-w-lg">
        Supporting description that explains the value proposition clearly.
      </p>
      <div className="flex flex-wrap gap-4">
        <Button size="lg">Primary CTA</Button>
        <Button size="lg" variant="outline">Secondary CTA</Button>
      </div>
    </div>
    <div className="relative">
      <Image src="..." alt="..." className="rounded-2xl shadow-2xl" />
    </div>
  </div>
</section>
```

**Pattern B: Centered Hero**
```tsx
<section className="min-h-[90vh] flex items-center justify-center text-center">
  <div className="container max-w-4xl space-y-8">
    <Badge className="mx-auto">Announcement</Badge>
    <h1 className="text-5xl md:text-6xl lg:text-7xl font-bold">
      Bold statement headline
    </h1>
    <p className="text-xl text-muted-foreground max-w-2xl mx-auto">
      Description text that provides context and builds interest.
    </p>
    <div className="flex justify-center gap-4">
      <Button size="lg">Get Started</Button>
    </div>
  </div>
</section>
```

**Pattern C: Video/Media Hero**
```tsx
<section className="relative min-h-screen flex items-center">
  <video
    autoPlay
    muted
    loop
    className="absolute inset-0 w-full h-full object-cover"
  >
    <source src="..." type="video/mp4" />
  </video>
  <div className="absolute inset-0 bg-black/50" />
  <div className="relative container text-white text-center">
    {/* Content */}
  </div>
</section>
```

### 3. Feature Section Patterns

**Grid Layout:**
```tsx
<section className="py-section-lg">
  <div className="container">
    <div className="text-center max-w-2xl mx-auto mb-16">
      <h2>Features</h2>
      <p>Description</p>
    </div>
    <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
      {features.map((feature) => (
        <div key={feature.id} className="p-6 rounded-2xl border bg-card">
          <div className="w-12 h-12 rounded-lg bg-primary/10 flex items-center justify-center mb-4">
            <feature.icon className="w-6 h-6 text-primary" />
          </div>
          <h3 className="text-xl font-semibold mb-2">{feature.title}</h3>
          <p className="text-muted-foreground">{feature.description}</p>
        </div>
      ))}
    </div>
  </div>
</section>
```

**Bento Grid:**
```tsx
<div className="grid grid-cols-4 grid-rows-3 gap-4">
  <div className="col-span-2 row-span-2 bg-card rounded-2xl p-8">
    {/* Large feature */}
  </div>
  <div className="col-span-2 bg-card rounded-2xl p-6">
    {/* Medium feature */}
  </div>
  <div className="bg-card rounded-2xl p-6">
    {/* Small feature */}
  </div>
  <div className="bg-card rounded-2xl p-6">
    {/* Small feature */}
  </div>
</div>
```

### 4. Testimonial Patterns

**Carousel:**
```tsx
<section className="py-section-lg bg-muted/50">
  <div className="container">
    <Carousel>
      {testimonials.map((t) => (
        <Card key={t.id} className="p-8">
          <div className="flex gap-1 mb-4">
            {[...Array(5)].map((_, i) => (
              <Star key={i} className="w-5 h-5 fill-yellow-400 text-yellow-400" />
            ))}
          </div>
          <blockquote className="text-lg mb-6">"{t.quote}"</blockquote>
          <div className="flex items-center gap-4">
            <Avatar src={t.avatar} />
            <div>
              <p className="font-semibold">{t.name}</p>
              <p className="text-sm text-muted-foreground">{t.role}</p>
            </div>
          </div>
        </Card>
      ))}
    </Carousel>
  </div>
</section>
```

**Grid with Masonry:**
```tsx
<div className="columns-1 md:columns-2 lg:columns-3 gap-4 space-y-4">
  {testimonials.map((t) => (
    <Card key={t.id} className="break-inside-avoid">
      {/* Testimonial content */}
    </Card>
  ))}
</div>
```

### 5. Pricing Section

```tsx
<section className="py-section-lg">
  <div className="container">
    <div className="text-center mb-12">
      <h2>Simple, transparent pricing</h2>
      <div className="flex justify-center gap-2 mt-4">
        <Button variant={billing === 'monthly' ? 'default' : 'outline'}>
          Monthly
        </Button>
        <Button variant={billing === 'yearly' ? 'default' : 'outline'}>
          Yearly (Save 20%)
        </Button>
      </div>
    </div>

    <div className="grid md:grid-cols-3 gap-8 max-w-5xl mx-auto">
      {plans.map((plan) => (
        <Card
          key={plan.id}
          className={cn(
            "p-8",
            plan.popular && "border-primary shadow-lg scale-105"
          )}
        >
          {plan.popular && (
            <Badge className="absolute -top-3 left-1/2 -translate-x-1/2">
              Most Popular
            </Badge>
          )}
          <h3 className="text-xl font-bold">{plan.name}</h3>
          <p className="text-muted-foreground mt-2">{plan.description}</p>
          <div className="my-6">
            <span className="text-4xl font-bold">${plan.price}</span>
            <span className="text-muted-foreground">/month</span>
          </div>
          <ul className="space-y-3 mb-8">
            {plan.features.map((f) => (
              <li key={f} className="flex items-center gap-2">
                <Check className="w-5 h-5 text-green-500" />
                <span>{f}</span>
              </li>
            ))}
          </ul>
          <Button className="w-full" variant={plan.popular ? 'default' : 'outline'}>
            Get Started
          </Button>
        </Card>
      ))}
    </div>
  </div>
</section>
```

### 6. CTA Section

**Simple CTA:**
```tsx
<section className="py-section-lg bg-primary text-primary-foreground">
  <div className="container text-center">
    <h2 className="text-3xl md:text-4xl font-bold mb-4">
      Ready to get started?
    </h2>
    <p className="text-primary-foreground/80 max-w-xl mx-auto mb-8">
      Join thousands of satisfied customers today.
    </p>
    <div className="flex justify-center gap-4">
      <Button size="lg" variant="secondary">
        Start Free Trial
      </Button>
    </div>
  </div>
</section>
```

### 7. Responsive Patterns

**Mobile-First Grid:**
```css
/* Stack on mobile, side-by-side on larger screens */
.responsive-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
}

@media (min-width: 768px) {
  .responsive-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .responsive-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

**Tailwind Responsive:**
```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
```

### 8. Visual Enhancements

**Gradient Backgrounds:**
```tsx
{/* Mesh gradient */}
<div className="absolute inset-0 -z-10">
  <div className="absolute top-0 -left-4 w-72 h-72 bg-purple-300 rounded-full mix-blend-multiply filter blur-xl opacity-70 animate-blob" />
  <div className="absolute top-0 -right-4 w-72 h-72 bg-yellow-300 rounded-full mix-blend-multiply filter blur-xl opacity-70 animate-blob animation-delay-2000" />
  <div className="absolute -bottom-8 left-20 w-72 h-72 bg-pink-300 rounded-full mix-blend-multiply filter blur-xl opacity-70 animate-blob animation-delay-4000" />
</div>
```

**Noise Texture:**
```css
.noise-bg::before {
  content: "";
  position: absolute;
  inset: 0;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  opacity: 0.03;
  pointer-events: none;
}
```

---

## ⚠️ Visual Quality Requirements (KRITISCH)

This section defines mandatory visual standards. Sections that don't meet these requirements will FAIL the Design Excellence check.

### BANNED Patterns (Auto-Fail)

These patterns result in automatic Design Excellence failure:

| Pattern | Why It's Bad | Example |
|---------|--------------|---------|
| Plain white backgrounds | No atmosphere, generic | `<section className="bg-white">` |
| Generic fonts as display | Looks like AI output | `font-family: Inter, Arial, sans-serif` |
| Cramped spacing | Unprofessional | `py-8`, `py-12` on sections |
| Equal card grids | Cookie-cutter look | `grid-cols-3` with identical cards |
| Generic blue buttons | No brand identity | `bg-blue-600` |
| Untreated images | Stock photo look | `<Image className="rounded-lg">` |

### ❌ BAD: Generic Section (DO NOT DO THIS)

```tsx
// ❌ FAIL - This will fail Design Excellence
<section className="py-12 bg-white">
  <div className="container mx-auto text-center">
    <h2 className="text-2xl font-semibold">Our Features</h2>
    <p className="text-gray-600">Check out what we offer</p>
    <div className="grid grid-cols-3 gap-4 mt-8">
      <div className="p-4 border rounded-lg">
        <h3 className="font-medium">Feature 1</h3>
        <p className="text-sm text-gray-500">Description text</p>
      </div>
      {/* More identical cards */}
    </div>
  </div>
</section>
```

**Why this fails:**
- `py-12` - Too cramped, minimum is `py-24`
- `bg-white` - Plain, no atmosphere
- `text-2xl` - Not dramatic enough for headline
- `font-semibold` - Generic weight
- `grid-cols-3 gap-4` - Cookie-cutter grid
- No background treatment
- No visual hierarchy
- No distinctive elements

### ✅ GOOD: Distinctive Section (DO THIS)

```tsx
// ✅ PASS - This meets Design Excellence standards
<section className="py-32 lg:py-48 relative overflow-hidden">
  {/* Background treatment - atmosphere */}
  <div className="absolute inset-0 bg-gradient-to-br from-primary/5 via-background to-accent/5" />
  <div className="absolute top-20 -right-20 w-96 h-96 bg-primary/10 rounded-full blur-3xl" />

  <Container>
    {/* Section header with dramatic typography */}
    <motion.div
      initial={{ opacity: 0, y: 20 }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true }}
      className="max-w-3xl mb-20"
    >
      <span className="text-sm font-medium text-primary tracking-wider uppercase">
        Why Choose Us
      </span>
      <h2 className="font-display text-4xl md:text-5xl lg:text-6xl font-bold mt-4 tracking-tight">
        Features that set us <span className="text-primary">apart</span>
      </h2>
      <p className="text-lg md:text-xl text-muted-foreground mt-6 max-w-2xl">
        Description with proper hierarchy and breathing room.
      </p>
    </motion.div>

    {/* Asymmetric grid with varied cards */}
    <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
      {features.map((feature, i) => (
        <motion.div
          key={feature.id}
          initial={{ opacity: 0, y: 20 }}
          whileInView={{ opacity: 1, y: 0 }}
          viewport={{ once: true }}
          transition={{ delay: i * 0.1 }}
          className="group relative"
        >
          {/* Card with hover effect */}
          <div className="relative p-8 rounded-2xl bg-card/50 backdrop-blur-sm border-0 shadow-lg transition-all duration-300 group-hover:shadow-xl group-hover:-translate-y-1">
            {/* Gradient border on hover */}
            <div className="absolute -inset-0.5 bg-gradient-to-r from-primary/20 to-accent/20 rounded-2xl opacity-0 group-hover:opacity-100 blur transition" />

            <div className="relative">
              <div className="w-14 h-14 rounded-xl bg-primary/10 flex items-center justify-center mb-6">
                <feature.icon className="w-7 h-7 text-primary" />
              </div>
              <h3 className="text-xl font-bold mb-3">{feature.title}</h3>
              <p className="text-muted-foreground leading-relaxed">{feature.description}</p>
            </div>
          </div>
        </motion.div>
      ))}
    </div>
  </Container>
</section>
```

**Why this passes:**
- `py-32 lg:py-48` - Generous whitespace
- Gradient background with blur shapes - atmosphere
- `text-5xl lg:text-6xl` - Dramatic headline
- `font-display` - Distinctive font
- Staggered animations
- Card hover effects with gradient border
- Backdrop blur and shadow depth
- Visual hierarchy (label → headline → description → features)

### Required Visual Elements

Every section MUST have at least 3 of these:

| Element | Implementation |
|---------|----------------|
| Background treatment | Gradient, blur shapes, pattern, or distinctive solid color |
| Dramatic typography | `text-4xl` minimum for headlines, distinctive font |
| Generous spacing | `py-24` minimum, prefer `py-32` or more |
| Visual hierarchy | Clear label → heading → body → content flow |
| Animation | Entrance animations with stagger |
| Hover states | Interactive feedback on cards/buttons |
| Depth | Shadows, blur, overlapping elements |
| Image treatment | Shadow, blur glow, overlay, or masking |

### Typography Scale

| Element | Mobile | Desktop | Notes |
|---------|--------|---------|-------|
| Section Label | text-sm | text-sm | Uppercase, tracking-wider, text-primary |
| Section Headline | text-3xl | text-5xl/6xl | font-display, font-bold, tracking-tight |
| Section Description | text-base | text-lg/xl | text-muted-foreground, max-w constraint |
| Card Title | text-lg | text-xl | font-bold |
| Card Description | text-sm | text-base | text-muted-foreground |

### Spacing Scale

| Element | Value | Tailwind |
|---------|-------|----------|
| Section padding (min) | 96px | py-24 |
| Section padding (preferred) | 128px | py-32 |
| Section padding (dramatic) | 192px | py-48 |
| Content gap | 32-64px | gap-8 to gap-16 |
| Card padding | 32px | p-8 |
| Element spacing | 16-24px | space-y-4 to space-y-6 |

---

## Quality Checklist

**Visual Quality (Design Excellence):**
- [ ] Background has atmosphere (not plain white/gray)
- [ ] Typography is dramatic (4xl+ headlines, distinctive font)
- [ ] Spacing is generous (py-24+ on sections)
- [ ] Has 3+ distinctive visual elements
- [ ] No banned patterns present

**Technical Quality:**
- [ ] Section has clear visual hierarchy
- [ ] Content is readable at all breakpoints
- [ ] Proper spacing between elements
- [ ] CTAs are prominent and action-oriented
- [ ] Images are optimized and have alt text
- [ ] Animations enhance, not distract
- [ ] Accessible color contrast
- [ ] Mobile-first responsive design

---

## 📦 shadcnblocks Integration

Dieser Abschnitt beschreibt den Workflow für die Nutzung von shadcnblocks als Layout-Template.

### Workflow

1. **Download Component** (exakter Befehl aus page-shapes):
   ```bash
   pnpm dlx shadcn add @shadcnblocks/feature-grid-2
   ```

2. **Custom Styles entfernen** (siehe Checkliste unten)

3. **Design System anwenden** (Design Tokens verwenden)

4. **Container centern** (IMMER `mx-auto max-w-7xl`)

5. **Playwright Screenshot + AI-Analyse** gegen Style-Bild

### Setup-Anforderungen

**components.json Registry:**
```json
{
  "registries": {
    "@shadcnblocks": {
      "url": "https://shadcnblocks.com/r/{name}",
      "headers": {
        "Authorization": "Bearer ${SHADCNBLOCKS_API_KEY}"
      }
    }
  }
}
```

**Environment Variable:**
```bash
export SHADCNBLOCKS_API_KEY=your_api_key_here
```

---

## 🔄 Custom Styles Entfernen - Checkliste

Bei JEDER shadcnblocks-Component müssen diese Styles entfernt/ersetzt werden:

| Entfernen | Ersetzen mit |
|-----------|--------------|
| `text-3xl`, `text-4xl` etc. auf h1/h2 | `font-display text-4xl` aus Design Tokens |
| `text-gray-600`, `text-slate-500` | `text-muted-foreground` |
| `bg-white`, `bg-gray-50` | `bg-background`, `bg-muted` |
| `text-blue-600`, `text-indigo-500` | `text-primary` |
| `font-semibold`, `font-medium` | Design Token Weights |
| `py-12`, `py-16` | Minimum `py-24`, bevorzugt `py-32` |
| `gap-4`, `gap-6` | Minimum `gap-8` |
| `rounded-lg` | `rounded-xl` oder `rounded-2xl` |
| Inline `style={}` | Tailwind Classes |
| `max-w-6xl`, `max-w-5xl` | `max-w-7xl` (Standard Container) |

### Container Centering (PFLICHT)

Alle Container MÜSSEN zentriert sein:
```tsx
// ✅ RICHTIG
<div className="container mx-auto max-w-7xl px-4">

// ❌ FALSCH
<div className="max-w-6xl">
<div className="container">  // ohne mx-auto
```

---

## 📊 Layout-Empfehlungslogik

Statt Fragen zu stellen, gibt der Workflow Empfehlungen basierend auf Content-Menge:

### Features/Services

| Anzahl Items | Empfohlenes Layout |
|--------------|-------------------|
| 3-4 | Horizontal Row oder 2x2 Grid |
| 5-6 | Grid 3x2 oder 2x3 |
| 7-9 | Grid 3x3 |
| 10-12 | Grid 4x3 oder 3x4 |
| 13+ | Grid 4x4 oder Bento mit Pagination |

**Responsive Breakpoints:**
- Desktop (lg): Max 4 Spalten
- Tablet (md): Max 3 Spalten
- Mobile: Max 2 Spalten

### Team

| Anzahl Members | Empfohlenes Layout |
|----------------|-------------------|
| 1-2 | Side-by-side oder Featured |
| 3-4 | Grid 2x2 oder 4x1 Row |
| 5-6 | Featured Lead + Grid 4x1 darunter |
| 7+ | Grid 4x2 oder Carousel |

### Testimonials

| Anzahl | Empfohlenes Layout |
|--------|-------------------|
| 1 | Single Quote, zentriert, gross |
| 2-3 | 3-Column Grid |
| 4+ | Carousel oder Masonry |

### Pricing

| Anzahl Plans | Empfohlenes Layout |
|--------------|-------------------|
| 2 | Side-by-side, einer highlighted |
| 3 | 3-Column, mittlerer highlighted |
| 4+ | Carousel oder Comparison Table |

### Beispiel-Output (Shape-Pages Phase 1)

```
════════════════════════════════════════════════════════════════
                    PAGE OVERVIEW: HOME
════════════════════════════════════════════════════════════════

┌───────────────────────────────────────────────────────────────┐
│ 1. HERO                                                       │
│ Split-Layout mit Text links, grossem Bild rechts.             │
│ Volle Viewport-Höhe, sofort sichtbarer CTA.                   │
└───────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────┐
│ 2. SERVICES (12 Items)                                        │
│ Grid mit Icon-Cards. 4 Spalten Desktop, 2 Mobile.             │
│ Jede Card: Icon + Titel + kurze Beschreibung.                 │
└───────────────────────────────────────────────────────────────┘

... weitere Sections ...

════════════════════════════════════════════════════════════════
```

---

## 🎯 Playwright Screenshot + AI-Analyse

Nach jeder Section-Implementation:

1. **Screenshot erstellen:**
   ```
   mcp__playwright__browser_navigate → URL
   mcp__playwright__browser_take_screenshot → fullPage: false, element spezifisch
   ```

2. **AI-Analyse gegen Style-Bild:**
   - Layout-Vergleich: Stimmt Grid/Spalten?
   - Spacing-Vergleich: Ähnliche Abstände?
   - Proportionen: Cards/Elemente ähnlich gross?
   - Typografie: Headlines/Body ähnliche Hierarchie?

3. **Feedback geben:**
   ```
   ✓ Layout passt (4x3 Grid wie im Bild)
   ✗ Cards zu eng → gap-8 auf gap-12 erhöhen
   ✗ Headlines zu klein → text-3xl auf text-4xl
   ```

4. **Iterieren bis Match**

### Wichtig bei Style-Bild Analyse

- Das Bild zeigt den GEWÜNSCHTEN Style, nicht die exakte Anzahl Items
- Beispiel: Bild zeigt 6 Cards → Du implementierst trotzdem für 12 wenn das der Content ist
- Nur Layout, Spacing, Proportionen übernehmen - NICHT die Farben/Fonts (die kommen aus Design Tokens)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
