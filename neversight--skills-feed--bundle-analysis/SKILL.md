---
name: bundle-analysis
description: Analyze and optimize JavaScript bundle size, identify large dependencies, and improve load times. Use when bundles are large, investigating performance issues, or optimizing frontend assets. Use when this capability is needed.
metadata:
  author: neversight
---

# Bundle Analysis Skill

This skill helps you analyze and optimize JavaScript bundle sizes for Next.js and web applications.

## When to Use This Skill

- Large bundle sizes (>500KB)
- Slow initial page loads
- High First Contentful Paint (FCP)
- Investigating bundle composition
- Identifying duplicate dependencies
- Optimizing production builds
- Reducing Time to Interactive (TTI)

## Bundle Size Goals

- **Initial bundle**: <200KB (gzipped)
- **First Load JS**: <300KB total
- **Route chunks**: <100KB each
- **Vendor chunks**: <150KB
- **Time to Interactive**: <3s on 3G

## Next.js Bundle Analyzer

### Setup

```bash
# Install bundle analyzer
pnpm add -D @next/bundle-analyzer

# Or as dev dependency in web app
cd apps/web
pnpm add -D @next/bundle-analyzer
```

### Configuration

```typescript
// apps/web/next.config.ts
import type { NextConfig } from "next";
import withBundleAnalyzer from "@next/bundle-analyzer";

const bundleAnalyzer = withBundleAnalyzer({
  enabled: process.env.ANALYZE === "true",
});

const nextConfig: NextConfig = {
  // ... your config
};

export default bundleAnalyzer(nextConfig);
```

### Running Analysis

```bash
# Analyze production bundle
cd apps/web
ANALYZE=true pnpm build

# Opens two HTML reports in browser:
# - client.html: Client-side bundle
# - server.html: Server-side bundle

# Analyze specific environment
ANALYZE=true NODE_ENV=production pnpm build

# Save report to file
ANALYZE=true pnpm build > bundle-report.txt
```

## Reading Bundle Reports

### Bundle Analyzer Output

```
Page                                       Size     First Load JS
┌ ○ /                                      5.2 kB        120 kB
├   /_app                                  0 B           115 kB
├ ○ /404                                   3.1 kB        118 kB
├ λ /api/cars                              0 B           115 kB
├ ○ /blog                                  8.5 kB        128 kB
├ ○ /blog/[slug]                           12.3 kB       132 kB
└ ○ /charts                                45.2 kB       165 kB

+ First Load JS shared by all              115 kB
  ├ chunks/framework-[hash].js             42 kB
  ├ chunks/main-[hash].js                  28 kB
  ├ chunks/pages/_app-[hash].js            35 kB
  └ chunks/webpack-[hash].js               10 kB

○  (Static)  automatically rendered as static HTML
λ  (Server)  server-side renders at runtime
```

**Key Metrics:**
- **Size**: Page-specific JavaScript
- **First Load JS**: Total JS loaded for initial render
- **Shared chunks**: Code shared across pages

### Interactive Report

Open `client.html` or `server.html` in browser:

- **Large boxes**: Heavy dependencies
- **Colors**: Different packages
- **Hover**: See exact sizes
- **Click**: Drill down into dependencies

## Common Issues

### 1. Large Dependencies

```bash
# Find large packages
cd apps/web
pnpm list --depth=0 | sort -k2 -n

# Example output:
# lodash           1.2 MB
# moment           500 KB
# recharts         300 KB
# @heroui/react    250 KB
```

**Solutions:**

```typescript
// ❌ Import entire library
import _ from "lodash";
import moment from "moment";

// ✅ Import only what you need
import debounce from "lodash/debounce";
import groupBy from "lodash/groupBy";

// ✅ Use lighter alternatives
import { format } from "date-fns";  // Instead of moment
import dayjs from "dayjs";          // Smaller than moment
```

### 2. Duplicate Dependencies

```bash
# Check for duplicates
pnpm list <package-name>

# Example: Multiple versions of react
pnpm list react

# Output:
# @sgcarstrends/web
# ├── react@18.3.0
# └─┬ some-package
#   └── react@18.2.0  # Duplicate!
```

**Solutions:**

```json
// package.json
{
  "pnpm": {
    "overrides": {
      "react": "18.3.0",
      "react-dom": "18.3.0"
    }
  }
}
```

### 3. Missing Code Splitting

```typescript
// ❌ Import heavy component directly
import Chart from "@/components/Chart";

export default function Page() {
  return <Chart data={data} />;
}

// ✅ Dynamic import with code splitting
import dynamic from "next/dynamic";

const Chart = dynamic(() => import("@/components/Chart"), {
  loading: () => <div>Loading chart...</div>,
  ssr: false,  // Client-side only if needed
});

export default function Page() {
  return <Chart data={data} />;
}
```

### 4. Large JSON/Data Files

```typescript
// ❌ Import large JSON files
import brands from "@/data/car-brands.json";  // 500KB

// ✅ Load dynamically
export async function getBrands() {
  const response = await fetch("/api/brands");
  return response.json();
}

// ✅ Or use dynamic import
export async function getBrands() {
  const { default: brands } = await import("@/data/car-brands.json");
  return brands;
}
```

## Optimization Techniques

### 1. Tree Shaking

```typescript
// ✅ Named imports enable tree shaking
import { Button, Card } from "@heroui/react";

// ❌ Default import includes everything
import HeroUI from "@heroui/react";
```

**Verify tree shaking:**

```json
// package.json
{
  "sideEffects": false  // Enable aggressive tree shaking
}

// Or specify side-effect files
{
  "sideEffects": ["*.css", "*.scss"]
}
```

### 2. Code Splitting by Route

```typescript
// Next.js automatically code-splits by route
// Each page becomes a separate chunk

// app/page.tsx -> chunk for /
// app/blog/page.tsx -> chunk for /blog
// app/charts/page.tsx -> chunk for /charts

// ✅ Additional manual splitting
const HeavyComponent = dynamic(() => import("./HeavyComponent"));
```

### 3. Lazy Loading

```typescript
// ✅ Load components on interaction
"use client";

import { useState } from "react";
import dynamic from "next/dynamic";

const CommentForm = dynamic(() => import("./CommentForm"));

export default function BlogPost() {
  const [showComments, setShowComments] = useState(false);

  return (
    <div>
      <article>{/* post content */}</article>
      <button onClick={() => setShowComments(true)}>
        Show Comments
      </button>
      {showComments && <CommentForm />}
    </div>
  );
}
```

### 4. Optimize Dependencies

```bash
# Replace heavy packages with lighter alternatives

# ❌ moment (500KB)
pnpm remove moment
# ✅ date-fns (30KB) or dayjs (7KB)
pnpm add date-fns

# ❌ lodash (full library)
# ✅ lodash-es (ESM with tree-shaking)
pnpm add lodash-es

# ❌ axios (for simple requests)
# ✅ native fetch or ky (12KB)
pnpm add ky
```

### 5. Image Optimization

```typescript
// ✅ Use Next.js Image component
import Image from "next/image";

export default function Logo() {
  return (
    <Image
      src="/logo.png"
      alt="Logo"
      width={200}
      height={100}
      priority  // For above-fold images
      quality={75}  // Reduce quality if acceptable
    />
  );
}

// ✅ Use modern formats
// - WebP: 25-35% smaller than JPEG/PNG
// - AVIF: 50% smaller than JPEG (if supported)
```

### 6. Font Optimization

```typescript
// app/layout.tsx
import { Inter } from "next/font/google";

// ✅ Load only needed weights and subsets
const inter = Inter({
  subsets: ["latin"],
  weight: ["400", "600", "700"],  // Only load what you use
  display: "swap",
  preload: true,
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

## Advanced Analysis

### Webpack Bundle Analyzer

```bash
# For custom webpack configs
pnpm add -D webpack-bundle-analyzer

# View detailed dependency tree
ANALYZE=true pnpm build
```

### Source Map Explorer

```bash
# Analyze source maps
pnpm add -D source-map-explorer

# Generate production build with source maps
pnpm build

# Analyze
npx source-map-explorer '.next/static/chunks/*.js'
```

### Bundle Buddy

```bash
# Visualize bundle relationships
npx bundle-buddy '.next/**/*.js.map'
```

## Monitoring Bundle Size

### CI Bundle Size Check

```yaml
# .github/workflows/bundle-size.yml
name: Bundle Size Check

on:
  pull_request:
    branches: [main]

jobs:
  bundle-size:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "pnpm"

      - name: Install dependencies
        run: pnpm install

      - name: Build
        run: pnpm -F @sgcarstrends/web build

      - name: Analyze bundle
        run: |
          BUNDLE_SIZE=$(du -sh apps/web/.next/static | cut -f1)
          echo "Bundle size: $BUNDLE_SIZE"

          # Fail if bundle exceeds limit
          SIZE_KB=$(du -sk apps/web/.next/static | cut -f1)
          if [ $SIZE_KB -gt 500 ]; then
            echo "Bundle size exceeds 500KB!"
            exit 1
          fi

      - name: Comment PR
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '📦 Bundle size: ${{ env.BUNDLE_SIZE }}'
            })
```

### Bundle Size Tracking

```bash
# Track bundle size over time
echo "$(date +%Y-%m-%d),$(du -sk apps/web/.next/static | cut -f1)" >> bundle-history.csv

# Plot with gnuplot or spreadsheet
```

## Performance Budgets

### next.config.ts

```typescript
// apps/web/next.config.ts
const nextConfig: NextConfig = {
  // Warn if bundles exceed limits
  onDemandEntries: {
    maxInactiveAge: 25 * 1000,
    pagesBufferLength: 2,
  },

  // Set performance budgets
  experimental: {
    optimizeCss: true,
    optimizePackageImports: ["@heroui/react", "recharts"],
  },
};
```

### Lighthouse CI

```yaml
# .lighthouserc.json
{
  "ci": {
    "collect": {
      "startServerCommand": "pnpm start",
      "url": ["http://localhost:3000", "http://localhost:3000/charts"]
    },
    "assert": {
      "assertions": {
        "categories:performance": ["error", { "minScore": 0.9 }],
        "resource-summary:script:size": ["error", { "maxNumericValue": 300000 }],
        "total-byte-weight": ["error", { "maxNumericValue": 500000 }]
      }
    }
  }
}
```

## Best Practices

### 1. Measure Before Optimizing

```bash
# ✅ Always measure first
ANALYZE=true pnpm build

# Identify actual bottlenecks
# Don't prematurely optimize
```

### 2. Prioritize Critical Path

```typescript
// ✅ Load critical resources first
// Above-fold content, essential JS/CSS

// ❌ Don't block initial render
// Defer analytics, chat widgets, etc.
```

### 3. Use External CDN

```typescript
// For heavy third-party libraries
// Load from CDN instead of bundling

// next.config.ts
const nextConfig: NextConfig = {
  experimental: {
    externalDir: true,
  },
};
```

### 4. Regular Audits

```bash
# ✅ Run bundle analysis regularly
# - Before each release
# - When adding dependencies
# - After major refactors

ANALYZE=true pnpm build
```

## Troubleshooting

### Bundle Size Not Reducing

```bash
# Issue: Optimizations not working
# Solution: Check production build

# Ensure building for production
NODE_ENV=production pnpm build

# Verify minification
cat .next/static/chunks/main-*.js | head -n 1

# Should be minified (single line)
```

### Analyzer Not Opening

```bash
# Issue: Bundle analyzer not showing reports
# Solution: Check environment variable

# Ensure ANALYZE is set
echo $ANALYZE  # Should print "true"

# Run explicitly
cd apps/web
ANALYZE=true pnpm build

# Check for errors in build output
```

### Large Unexplained Bundle

```bash
# Issue: Bundle larger than expected
# Solution: Investigate with source-map-explorer

pnpm build
npx source-map-explorer '.next/static/chunks/*.js' --html bundle-report.html

# Open bundle-report.html to see breakdown
```

## References

- Next.js Bundle Analyzer: https://www.npmjs.com/package/@next/bundle-analyzer
- Webpack Bundle Analyzer: https://github.com/webpack-contrib/webpack-bundle-analyzer
- Web.dev Bundle Size: https://web.dev/your-first-performance-budget/
- Next.js Optimization: https://nextjs.org/docs/app/building-your-application/optimizing
- Related files:
  - `apps/web/next.config.ts` - Next.js configuration
  - Root CLAUDE.md - Performance guidelines

## Best Practices Summary

1. **Analyze Regularly**: Check bundle size before releases
2. **Code Split**: Use dynamic imports for heavy components
3. **Tree Shake**: Use named imports, mark side effects
4. **Optimize Dependencies**: Replace heavy packages with lighter alternatives
5. **Lazy Load**: Defer non-critical resources
6. **Monitor**: Set up CI checks and performance budgets
7. **Use CDN**: Offload heavy third-party libraries
8. **Image Optimization**: Use Next.js Image with WebP/AVIF

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
