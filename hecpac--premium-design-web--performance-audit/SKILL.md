---
name: performance-audit
description: Perform performance audits on React/Next.js components focusing on Core Web Vitals. Use when the user asks for performance review, optimization check, speed audit, LCP/CLS/INP analysis, or wants to improve page performance. Use when this capability is needed.
metadata:
  author: hecpac
---

# Performance Audit Skill

## Quick Start

When performing a performance audit:

1. Read the component/page file(s)
2. Check against the checklist below
3. Provide findings with impact levels
4. Suggest specific optimizations

## Audit Checklist

### Images (LCP Impact)
- [ ] Uses `next/image` (not `<img>`)
- [ ] Has `sizes` attribute for responsive images
- [ ] Above-fold images have `priority={true}`
- [ ] Below-fold images have `loading="lazy"`
- [ ] Has unique `blurDataURL` (not generic)
- [ ] Uses local WebP images (not remote URLs)
- [ ] Dimensions are specified (`width`, `height`)

### Layout Stability (CLS Impact)
- [ ] Images have explicit dimensions
- [ ] Fonts use `display: swap`
- [ ] No layout shifts from dynamic content
- [ ] Skeleton loaders for async content
- [ ] Reserved space for ads/embeds

### JavaScript (INP Impact)
- [ ] Heavy components use `dynamic()` import
- [ ] Event handlers are not blocking
- [ ] Uses `useCallback` for stable references
- [ ] Debounces expensive operations
- [ ] Avoids re-renders (React.memo where needed)

### Data Fetching (TTFB Impact)
- [ ] Uses `generateStaticParams` for SSG
- [ ] Has `revalidate` for ISR
- [ ] Data is cached appropriately
- [ ] No unnecessary API calls

### Bundle Size
- [ ] Tree-shaking friendly imports
- [ ] No `import * from` large libraries
- [ ] Client components are minimal
- [ ] Heavy deps are lazy loaded

## Impact Levels

- 🔴 **High Impact**: Directly affects Core Web Vitals (images, fonts, layout shifts)
- 🟡 **Medium Impact**: Affects perceived performance (bundle size, hydration)
- 🟢 **Low Impact**: Optimization opportunity (caching, preloading)

## Report Template

```markdown
# Performance Audit: [Component/Page Name]

## Core Web Vitals Assessment
- LCP: [Good/Needs Improvement/Poor]
- CLS: [Good/Needs Improvement/Poor]
- INP: [Good/Needs Improvement/Poor]

## High Impact Issues 🔴
1. [Issue]: [Location]
   - Impact: [LCP/CLS/INP]
   - Fix: [Code example]

## Medium Impact Issues 🟡
1. [Issue]: [Location]
   - Impact: [Description]
   - Fix: [Code example]

## Optimization Opportunities 🟢
1. [Opportunity]: [Description]
   - Benefit: [Expected improvement]
```

## Common Fixes

### Remote Image → Local Optimized

```tsx
// ❌ Before - Remote URL
<Image src="https://images.unsplash.com/photo-xxx" ... />

// ✅ After - Local WebP
<Image src="/images/project-hero.webp" ... />
```

### Missing Image Optimization

```tsx
// ❌ Before
<Image src={image} alt="..." />

// ✅ After
<Image
  src={image}
  alt="Descriptive alt"
  width={800}
  height={600}
  sizes="(max-width: 768px) 100vw, 50vw"
  loading={index === 0 ? "eager" : "lazy"}
  priority={index === 0}
  placeholder="blur"
  blurDataURL={uniqueBlurData}
/>
```

### Lazy Load Heavy Components

```tsx
// ❌ Before - Always loaded
import { HeavyChart } from "./HeavyChart";

// ✅ After - Lazy loaded
import dynamic from "next/dynamic";
const HeavyChart = dynamic(() => import("./HeavyChart"), {
  loading: () => <Skeleton className="h-[400px]" />,
  ssr: false,
});
```

### Add ISR to Static Pages

```tsx
// ❌ Before - No revalidation
export default function Page() { ... }

// ✅ After - ISR every hour
export const revalidate = 3600;
export default function Page() { ... }
```

### Tree-Shake Large Libraries

```tsx
// ❌ Before - Imports entire library
import * as icons from "lucide-react";

// ✅ After - Import only what's needed
import { ArrowUpRight, ChevronDown } from "lucide-react";
```

## Tools Reference

### Analyze Bundle Size
```bash
ANALYZE=true npm run build
```

### Check Core Web Vitals
- Dev overlay shows real-time metrics
- Vercel Speed Insights in production
- Chrome DevTools Lighthouse

### Generate Blur Placeholders
```bash
# Using plaiceholder
npx plaiceholder ./public/images/*.webp
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hecpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
