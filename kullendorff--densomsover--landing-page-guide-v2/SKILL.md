---
name: landing-page-guide-v2
description: Create distinctive, high-converting landing pages combining proven conversion elements with exceptional design quality. Build beautiful, memorable landing pages with Next.js 14+ and ShadCN UI that avoid generic AI aesthetics. Use when this capability is needed.
metadata:
  author: kullendorff
---

# Landing Page Guide V2

Create distinctive, high-converting landing pages that combine proven conversion elements with exceptional design quality. This skill guides you to build beautiful, memorable landing pages using Next.js 14+ and ShadCN UI.

## Philosophy

> "A landing page must convert visitors AND make them remember your brand. Generic, template-looking pages fail at both."

This skill balances two critical aspects:
1. **Functional Excellence**: Proven conversion elements that drive results
2. **Design Excellence**: Distinctive aesthetics that create lasting impressions

## When to Use This Skill

Use this skill when:
- Building marketing landing pages for products or services
- Creating event registration or webinar sign-up pages
- Developing sales pages or product launches
- Designing portfolio or agency showcase pages
- Building lead generation pages
- Creating campaign-specific landing pages
- Prototyping high-fidelity marketing concepts

## Technical Stack

**Required:**
- Next.js 14+ with App Router
- TypeScript
- Tailwind CSS
- ShadCN UI (heavily customized)

**Optional:**
- Framer Motion for advanced animations
- Analytics integration (Vercel Analytics, Google Analytics)

## The 11 Essential Elements (DESIGNNAS Framework)

Every high-converting landing page should include these elements in order:

### 1. URL with Keywords
**Functional Purpose:** SEO-optimized URL structure
**Implementation:** Use descriptive slugs that match primary keywords

### 2. Company Logo (Header)
**Functional Purpose:** Brand identity and trust
**Design Excellence:**
- Animated logo on page load
- Sticky header with scroll behavior
- Subtle hover effects

```jsx
<header className="sticky top-0 z-50 backdrop-blur-lg border-b">
  <nav className="container mx-auto px-4 py-4 flex items-center justify-between">
    <Logo className="w-32 animate-in fade-in slide-in-from-left-4" />
    {/* Navigation */}
  </nav>
</header>
```

### 3. SEO-Optimized Title & Subtitle (Hero)
**Functional Purpose:** Clear value proposition
**Design Excellence:**
- MASSIVE typography (4-6rem+ on desktop)
- Dramatic font pairing (display + body)
- Gradient text or unique styling
- Staggered animation on load

```jsx
<h1 className="text-5xl md:text-7xl font-bold leading-tight tracking-tight">
  <span className="bg-gradient-to-r from-orange-500 to-red-600 bg-clip-text text-transparent">
    Transform Your Business
  </span>
</h1>
<p className="text-xl md:text-2xl text-gray-600 mt-6 max-w-2xl">
  The only platform you need to scale your operations 10x faster
</p>
```

### 4. Primary CTA (Hero)
**Functional Purpose:** Main call-to-action
**Design Excellence:**
- Impossible to miss (high contrast, large size)
- Micro-interactions on hover
- Loading states for form submissions
- Social proof nearby (badges, logos)

```jsx
<Button
  size="lg"
  className="text-lg px-8 py-6 bg-orange-600 hover:bg-orange-700 hover:scale-105 transition-all shadow-2xl shadow-orange-500/50"
>
  Start Free Trial
  <ArrowRight className="ml-2 animate-pulse" />
</Button>
```

### 5. Social Proof (Hero Section)
**Functional Purpose:** Reviews, ratings, trust indicators
**Design Excellence:**
- Animated counters
- Overlapping avatars
- Company logos marquee
- Star ratings with hover effects

```jsx
<div className="flex items-center gap-6 mt-8">
  <div className="flex -space-x-4">
    {avatars.map((src, i) => (
      <Avatar key={i} className="border-4 border-white">
        <AvatarImage src={src} />
      </Avatar>
    ))}
  </div>
  <div>
    <div className="flex items-center gap-1">
      {[...Array(5)].map((_, i) => (
        <Star key={i} className="fill-yellow-400 text-yellow-400" />
      ))}
    </div>
    <p className="text-sm text-gray-600 mt-1">
      Trusted by 10,000+ businesses
    </p>
  </div>
</div>
```

### 6. Images/Videos (Visual Demonstration)
**Functional Purpose:** Show product in action
**Design Excellence:**
- Device mockups with screen content
- Parallax scroll effects
- 3D transforms and perspective
- Lazy loading with blur-up placeholders

```jsx
<div className="relative perspective-1000">
  <Image
    src="/product-hero.png"
    alt="Product interface"
    width={1200}
    height={800}
    className="rounded-2xl shadow-2xl transform rotate-y-12 hover:rotate-y-0 transition-transform duration-500"
    priority
  />
</div>
```

### 7. Core Benefits/Features (3-6 Items)
**Functional Purpose:** Communicate main value propositions
**Design Excellence:**
- Custom icons (not generic icon libraries)
- Asymmetric layout (not grid)
- Gradient borders or unique card styling
- Hover states with depth

```jsx
<div className="grid md:grid-cols-3 gap-8">
  {features.map((feature, i) => (
    <Card key={i} className="relative group hover:shadow-xl transition-all border-2 border-transparent hover:border-orange-500">
      <div className="absolute -top-4 -right-4 w-8 h-8 bg-gradient-to-br from-orange-500 to-red-600 rounded-full opacity-0 group-hover:opacity-100 transition-opacity" />
      <CardHeader>
        <div className="w-12 h-12 bg-orange-100 rounded-lg flex items-center justify-center mb-4">
          {feature.icon}
        </div>
        <CardTitle>{feature.title}</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="text-gray-600">{feature.description}</p>
      </CardContent>
    </Card>
  ))}
</div>
```

### 8. Customer Testimonials (4-6 Reviews)
**Functional Purpose:** Social proof and credibility
**Design Excellence:**
- Stylized cards with personality
- Gradient borders or shadows
- Client photos and company logos
- Scroll-triggered fade-ins

```jsx
<div className="grid md:grid-cols-2 gap-6">
  {testimonials.map((testimonial, i) => (
    <Card key={i} className="relative p-6 bg-gradient-to-br from-white to-gray-50">
      <div className="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-orange-500 to-red-600" />
      <div className="flex items-center gap-4 mb-4">
        <Avatar className="w-12 h-12">
          <AvatarImage src={testimonial.avatar} />
        </Avatar>
        <div>
          <p className="font-semibold">{testimonial.name}</p>
          <p className="text-sm text-gray-600">{testimonial.role}</p>
        </div>
      </div>
      <p className="text-gray-700 italic">"{testimonial.quote}"</p>
    </Card>
  ))}
</div>
```

### 9. FAQ Section (5-10 Questions)
**Functional Purpose:** Address objections and common questions
**Design Excellence:**
- Smooth accordion animations
- Hover states on questions
- Clean typography hierarchy
- Contextual icons

```jsx
<Accordion type="single" collapsible className="w-full max-w-3xl mx-auto">
  {faqs.map((faq, i) => (
    <AccordionItem key={i} value={`item-${i}`} className="border-b border-gray-200">
      <AccordionTrigger className="text-left hover:text-orange-600 transition-colors">
        {faq.question}
      </AccordionTrigger>
      <AccordionContent className="text-gray-600">
        {faq.answer}
      </AccordionContent>
    </AccordionItem>
  ))}
</Accordion>
```

### 10. Final CTA (Closing Section)
**Functional Purpose:** Last chance for conversion
**Design Excellence:**
- Full-width dramatic section
- Unique background (gradient, pattern, image)
- High-contrast text and button
- Urgency elements (if appropriate)

```jsx
<section className="relative py-24 bg-gradient-to-br from-orange-600 via-red-600 to-purple-700 text-white overflow-hidden">
  <div className="absolute inset-0 bg-[url('/grid.svg')] opacity-10" />
  <div className="container mx-auto px-4 text-center relative z-10">
    <h2 className="text-4xl md:text-5xl font-bold mb-6">
      Ready to Transform Your Business?
    </h2>
    <p className="text-xl mb-8 text-white/90 max-w-2xl mx-auto">
      Join 10,000+ companies already using our platform
    </p>
    <Button size="lg" variant="secondary" className="text-lg px-8 py-6">
      Get Started Free
    </Button>
  </div>
</section>
```

### 11. Footer (Legal & Contact)
**Functional Purpose:** Contact info, legal links, sitemap
**Design Excellence:**
- Multi-column layout
- Refined typography
- Social links with hover effects
- Subtle background

```jsx
<footer className="bg-gray-900 text-gray-300 py-16">
  <div className="container mx-auto px-4">
    <div className="grid md:grid-cols-4 gap-8">
      <div>
        <Logo className="w-32 mb-4" />
        <p className="text-sm">Building the future of business automation</p>
      </div>
      <div>
        <h3 className="font-semibold text-white mb-4">Product</h3>
        <ul className="space-y-2 text-sm">
          <li><a href="/features" className="hover:text-orange-500 transition-colors">Features</a></li>
          <li><a href="/pricing" className="hover:text-orange-500 transition-colors">Pricing</a></li>
        </ul>
      </div>
      {/* More columns */}
    </div>
    <Separator className="my-8 bg-gray-800" />
    <div className="flex flex-col md:flex-row justify-between items-center text-sm">
      <p>© 2025 Company Name. All rights reserved.</p>
      <div className="flex gap-4 mt-4 md:mt-0">
        <a href="/privacy" className="hover:text-orange-500">Privacy</a>
        <a href="/terms" className="hover:text-orange-500">Terms</a>
      </div>
    </div>
  </div>
</footer>
```

## Aesthetic Directions by Type

### SaaS Product
**Options:**
1. **Minimalist & Professional**
   - Clean white/gray backgrounds
   - Sans-serif fonts (but not Inter!)
   - Subtle shadows and spacing
   - Calm, confident tone

2. **Tech-Forward**
   - Gradient backgrounds
   - Modern geometric shapes
   - Dark mode aesthetics
   - Futuristic feel

3. **Bold & Confident**
   - High contrast colors
   - Large typography
   - Dramatic shadows
   - Statement design

### E-commerce
**Options:**
1. **Luxury/Premium**
   - Elegant serif fonts
   - Monochrome or muted colors
   - Large product photography
   - Refined details

2. **Energetic/Youth**
   - Bold, vibrant colors
   - Playful fonts
   - Dynamic layouts
   - Fun interactions

3. **Natural/Sustainable**
   - Earth tones
   - Organic shapes
   - Natural photography
   - Calm, trustworthy vibe

### Service/Agency
**Options:**
1. **Creative/Bold**
   - Asymmetric layouts
   - Unique typography
   - Unexpected color combinations
   - Artistic expression

2. **Editorial**
   - Magazine-style layouts
   - Large imagery
   - Elegant typography
   - Story-driven

3. **Minimalist/Portfolio**
   - Generous white space
   - Focus on work samples
   - Refined typography
   - Sophisticated simplicity

### Event/Webinar
**Options:**
1. **Exciting/Dynamic**
   - Animated countdown timers
   - Energetic colors
   - Movement and energy
   - FOMO elements

2. **Professional/Conference**
   - Clean, organized layout
   - Speaker showcases
   - Schedule displays
   - Credible, trustworthy

3. **Community/Friendly**
   - Warm colors
   - People-focused imagery
   - Inviting tone
   - Social proof emphasis

## Responsive Design Requirements

**Mobile-First Approach:**
```jsx
// Stack on mobile, side-by-side on desktop
<div className="flex flex-col md:flex-row gap-8">
  {/* Content */}
</div>

// Responsive text sizes
<h1 className="text-3xl sm:text-4xl md:text-5xl lg:text-6xl">
  Title
</h1>

// Responsive padding
<section className="px-4 md:px-8 lg:px-16 py-12 md:py-20">
  {/* Content */}
</section>
```

**Breakpoint Strategy:**
- Mobile: 375px-640px (base styles)
- Tablet: 640px-1024px (sm:, md:)
- Desktop: 1024px+ (lg:, xl:, 2xl:)

**Test at:**
- 375px (iPhone SE)
- 768px (iPad)
- 1280px (Laptop)
- 1920px (Desktop)

## ShadCN Components to Install

```bash
npx shadcn-ui@latest add button
npx shadcn-ui@latest add card
npx shadcn-ui@latest add accordion
npx shadcn-ui@latest add badge
npx shadcn-ui@latest add avatar
npx shadcn-ui@latest add separator
npx shadcn-ui@latest add input
npx shadcn-ui@latest add form
```

**CRITICAL:** Don't use ShadCN defaults. Heavily customize:
- Change color schemes to match aesthetic
- Modify border radius, shadows
- Add unique hover states
- Customize typography

## Design Quality Validation

Before considering a landing page complete, verify:

**Design Quality ⭐**
- [ ] Aesthetic direction chosen and consistently executed
- [ ] Distinctive display font (NOT Inter/Roboto/Arial)
- [ ] Clear hierarchy with dramatic scale differences
- [ ] Color palette defined as CSS variables
- [ ] Background is NOT plain white (use gradient, pattern, or texture)
- [ ] Animations implemented (staggered page load, scroll-triggered reveals)
- [ ] ShadCN components heavily customized (not default styling)
- [ ] Passes "does this look AI-generated?" test - it should NOT

**Functional Requirements 🔧**
- [ ] All 11 essential elements present
- [ ] Clear value proposition in hero
- [ ] Working CTA buttons with proper tracking
- [ ] Social proof elements (reviews, logos, stats)
- [ ] Mobile-responsive (test at 375px, 768px, 1280px)
- [ ] Fast page load (<3s on 3G)
- [ ] SEO metadata configured
- [ ] Analytics tracking implemented

**Technical Requirements 🔧**
- [ ] Next.js 14+ with App Router
- [ ] TypeScript types defined for all props/data
- [ ] Tailwind CSS for all styling
- [ ] Metadata configured for SEO
- [ ] Images optimized with Next.js Image component
- [ ] Responsive design tested (mobile-first approach)
- [ ] WCAG AA accessibility standards met
- [ ] Reduced motion support (@media prefers-reduced-motion)

## Common Patterns

### Hero Section Pattern
```jsx
export default function HeroSection() {
  return (
    <section className="relative min-h-screen flex items-center justify-center overflow-hidden">
      {/* Background */}
      <div className="absolute inset-0 bg-gradient-to-br from-orange-50 to-red-50 -z-10" />

      {/* Content */}
      <div className="container mx-auto px-4 py-20">
        <div className="max-w-4xl mx-auto text-center">
          {/* Badge */}
          <Badge variant="outline" className="mb-6">
            🎉 New: AI-Powered Features
          </Badge>

          {/* Title */}
          <h1 className="text-5xl md:text-7xl font-bold leading-tight mb-6">
            Build Landing Pages That{" "}
            <span className="bg-gradient-to-r from-orange-600 to-red-600 bg-clip-text text-transparent">
              Actually Convert
            </span>
          </h1>

          {/* Subtitle */}
          <p className="text-xl text-gray-600 mb-8 max-w-2xl mx-auto">
            Combine proven conversion frameworks with exceptional design quality
          </p>

          {/* CTA */}
          <div className="flex flex-col sm:flex-row gap-4 justify-center">
            <Button size="lg" className="text-lg">
              Start Free Trial
            </Button>
            <Button size="lg" variant="outline" className="text-lg">
              Watch Demo
            </Button>
          </div>

          {/* Social Proof */}
          <div className="mt-12 flex items-center justify-center gap-8">
            {/* Avatars, ratings, etc. */}
          </div>
        </div>
      </div>
    </section>
  );
}
```

### Features Section Pattern
```jsx
const features = [
  {
    icon: <Zap className="w-6 h-6 text-orange-600" />,
    title: "Lightning Fast",
    description: "Deploy in seconds, not hours"
  },
  // More features...
];

export default function FeaturesSection() {
  return (
    <section className="py-24 bg-white">
      <div className="container mx-auto px-4">
        <div className="text-center mb-16">
          <h2 className="text-4xl font-bold mb-4">
            Everything You Need to Succeed
          </h2>
          <p className="text-xl text-gray-600 max-w-2xl mx-auto">
            All the tools and features to build, launch, and grow your business
          </p>
        </div>

        <div className="grid md:grid-cols-3 gap-8">
          {features.map((feature, i) => (
            <Card key={i} className="group hover:shadow-xl transition-all">
              <CardHeader>
                <div className="w-12 h-12 bg-orange-100 rounded-lg flex items-center justify-center mb-4 group-hover:bg-orange-200 transition-colors">
                  {feature.icon}
                </div>
                <CardTitle>{feature.title}</CardTitle>
              </CardHeader>
              <CardContent>
                <p className="text-gray-600">{feature.description}</p>
              </CardContent>
            </Card>
          ))}
        </div>
      </div>
    </section>
  );
}
```

## Anti-Patterns to Avoid

❌ **Don't:**
- Use default ShadCN styling without customization
- Choose Inter, Roboto, or Arial fonts
- Create generic purple gradient backgrounds
- Make all sections full-width with centered content
- Use cookie-cutter layouts that look "AI-generated"
- Ignore mobile responsiveness
- Skip accessibility considerations
- Forget to test on real devices

✅ **Do:**
- Choose a bold aesthetic direction and commit to it
- Use distinctive typography that matches your tone
- Create visual interest with backgrounds, patterns, textures
- Vary section layouts (asymmetric, overlapping, unexpected)
- Test on multiple devices and screen sizes
- Ensure keyboard navigation works
- Add loading states and error handling
- Optimize images and performance

## Performance Optimization

```jsx
// Use Next.js Image optimization
import Image from 'next/image'

<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1920}
  height={1080}
  priority // for above-the-fold images
  placeholder="blur"
  blurDataURL="data:image/..." // use plaiceholder library
/>

// Lazy load below-the-fold content
import dynamic from 'next/dynamic'

const TestimonialsSection = dynamic(() => import('./TestimonialsSection'), {
  loading: () => <div>Loading...</div>
})

// Defer non-critical JavaScript
<Script
  src="https://analytics.example.com/script.js"
  strategy="lazyOnload"
/>
```

## Remember

The goal is to create landing pages that:
1. **Convert** - Include all proven conversion elements
2. **Delight** - Memorable design that stands out
3. **Perform** - Fast, accessible, SEO-optimized
4. **Scale** - Maintainable code and design system

Always start by choosing a clear aesthetic direction, then execute it with precision across all 11 essential elements.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kullendorff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
