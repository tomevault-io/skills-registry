---
name: responsive-breakpoint-tester
description: Test responsive design across multiple breakpoints and generate TailwindCSS responsive utilities. Use when testing mobile, tablet, desktop layouts or validating responsive components. Use when this capability is needed.
metadata:
  author: agomez356
---

# Responsive Breakpoint Tester

Test and validate responsive design for the FísicaFans blog across all device sizes using TailwindCSS breakpoints.

## TailwindCSS Breakpoints

FísicaFans uses the default TailwindCSS breakpoints:

| Breakpoint | Min Width | Device | CSS Prefix |
|------------|-----------|--------|------------|
| **xs** (implicit) | 0px | Mobile portrait | (no prefix) |
| **sm** | 640px | Mobile landscape | `sm:` |
| **md** | 768px | Tablet | `md:` |
| **lg** | 1024px | Desktop | `lg:` |
| **xl** | 1280px | Large desktop | `xl:` |
| **2xl** | 1536px | Extra large | `2xl:` |

## Testing Instructions

When testing responsive design:

1. **Start with mobile-first**: Design for smallest screen first (320px)
2. **Test all breakpoints**: Verify layout at each TailwindCSS breakpoint
3. **Check edge cases**: Test at exact breakpoint widths (e.g., 640px, 768px)
4. **Validate touch targets**: Buttons/links ≥ 44×44px on mobile
5. **Test orientation**: Check both portrait and landscape modes

## Browser DevTools Testing

### Chrome/Edge DevTools

```bash
# Open DevTools
F12 or Cmd+Option+I (Mac)

# Toggle device toolbar
Cmd+Shift+M (Mac) or Ctrl+Shift+M (Windows)
```

**Test these preset devices:**
- Mobile: iPhone SE (375×667), iPhone 12 Pro (390×844)
- Tablet: iPad (768×1024), iPad Pro (1024×1366)
- Desktop: Nest Hub Max (1280×800)

### Firefox Responsive Design Mode

```bash
# Open Responsive Design Mode
Cmd+Option+M (Mac) or Ctrl+Shift+M (Windows)
```

**Custom dimensions to test:**
- 320px (minimum mobile)
- 375px (iPhone SE)
- 640px (sm breakpoint)
- 768px (md breakpoint - tablet)
- 1024px (lg breakpoint - desktop)
- 1280px (xl breakpoint)
- 1536px (2xl breakpoint)

## Common Responsive Patterns

### Container Padding

```html
<!-- Responsive padding -->
<div class="px-4 sm:px-6 md:px-8 lg:px-12 xl:px-16">
  <!-- Content -->
</div>
```

### Flexible Grid

```html
<!-- 1 column mobile, 2 tablet, 3 desktop -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <!-- Items -->
</div>
```

### Typography Scaling

```html
<!-- Responsive text sizes -->
<h1 class="text-3xl sm:text-4xl md:text-5xl lg:text-6xl font-bold">
  Physics Blog
</h1>

<p class="text-sm sm:text-base md:text-lg">
  Body text that scales
</p>
```

### Flex Direction

```html
<!-- Stack on mobile, row on tablet+ -->
<div class="flex flex-col md:flex-row gap-4">
  <div class="w-full md:w-1/2">Left</div>
  <div class="w-full md:w-1/2">Right</div>
</div>
```

### Hide/Show Elements

```html
<!-- Mobile menu toggle -->
<button class="md:hidden">☰ Menu</button>

<!-- Desktop navigation -->
<nav class="hidden md:flex gap-4">
  <a href="/">Home</a>
  <a href="/blog">Blog</a>
</nav>
```

## Physics Components - Responsive Checklist

### Header.vue

- [ ] Logo size scales: `h-8 sm:h-10 md:h-12`
- [ ] Hamburger menu on mobile (`< md`)
- [ ] Full nav visible on desktop (`≥ md`)
- [ ] Glassmorphism works on all sizes
- [ ] Touch targets ≥ 44×44px

### Hero.vue

- [ ] Text sizes scale appropriately
- [ ] Particle animation performs well on mobile
- [ ] CTA buttons stack on mobile, inline on desktop
- [ ] Padding adjusts: `py-12 md:py-20 lg:py-28`

### PhysicsCategories.vue

- [ ] Grid: 1 col mobile, 2 tablet, 3 desktop
- [ ] Cards maintain aspect ratio
- [ ] Icons/images scale proportionally
- [ ] Hover effects work on touch devices (use active state)

### BlogPostLayout.vue

- [ ] Table of Contents: fixed sidebar on desktop, collapsed on mobile
- [ ] Reading progress bar visible all sizes
- [ ] Code blocks scroll horizontally on mobile
- [ ] Images scale: `max-w-full h-auto`
- [ ] LaTeX equations don't overflow

### Physics Visualizers

- [ ] Canvas resizes: `<canvas ref="canvas" :width="canvasWidth" :height="canvasHeight">`
- [ ] Controls stack vertically on mobile
- [ ] Sliders have adequate thumb size (≥ 44px)
- [ ] Play/Pause buttons large enough for touch
- [ ] Labels don't overflow on small screens

## Responsive Testing Bash Commands

### Using Playwright (if installed)

```bash
# Install Playwright
npm install -D @playwright/test

# Test homepage across devices
npx playwright test --project=Mobile
npx playwright test --project=Tablet
npx playwright test --project=Desktop
```

### Using Puppeteer (manual script)

```bash
# Create test script
cat > test-responsive.js << 'EOF'
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  const breakpoints = [
    { name: 'Mobile', width: 375, height: 667 },
    { name: 'Tablet', width: 768, height: 1024 },
    { name: 'Desktop', width: 1280, height: 800 },
  ];

  for (const bp of breakpoints) {
    await page.setViewport({ width: bp.width, height: bp.height });
    await page.goto('http://localhost:4321');
    await page.screenshot({ path: `screenshot-${bp.name}.png` });
    console.log(`✓ Tested ${bp.name} (${bp.width}×${bp.height})`);
  }

  await browser.close();
})();
EOF

# Run test
node test-responsive.js
```

## CSS Grid Debugging

```css
/* Add to global.css temporarily */
* {
  outline: 1px solid red;
}

/* Or use Tailwind debug classes */
<div class="debug-screens">
  <!-- Shows current breakpoint in corner -->
</div>
```

## Lighthouse Mobile Testing

```bash
# Test mobile performance
npx lighthouse http://localhost:4321 \
  --only-categories=performance \
  --emulated-form-factor=mobile \
  --throttling.rttMs=150 \
  --throttling.throughputKbps=1638.4 \
  --throttling.cpuSlowdownMultiplier=4 \
  --view
```

## Common Responsive Issues

### Issue 1: Horizontal Scroll on Mobile

**Cause**: Fixed-width element exceeds viewport

**Fix**:
```html
<!-- Bad -->
<div class="w-1200">Content</div>

<!-- Good -->
<div class="w-full max-w-screen-xl mx-auto px-4">Content</div>
```

### Issue 2: Text Too Small on Mobile

**Cause**: No responsive typography

**Fix**:
```html
<!-- Bad -->
<p class="text-lg">Text</p>

<!-- Good -->
<p class="text-sm sm:text-base md:text-lg">Text</p>
```

### Issue 3: Images Overflow

**Cause**: Missing responsive classes

**Fix**:
```html
<!-- Bad -->
<img src="..." width="1200">

<!-- Good -->
<img src="..." class="w-full h-auto max-w-full">
```

### Issue 4: Touch Targets Too Small

**Cause**: Buttons < 44×44px

**Fix**:
```html
<!-- Bad -->
<button class="px-2 py-1">Click</button>

<!-- Good -->
<button class="px-4 py-3 min-w-[44px] min-h-[44px]">Click</button>
```

### Issue 5: Canvas Not Responsive

**Cause**: Fixed canvas dimensions

**Fix**:
```vue
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const canvas = ref<HTMLCanvasElement>()
const canvasWidth = ref(800)
const canvasHeight = ref(600)

function resizeCanvas() {
  if (!canvas.value) return
  const container = canvas.value.parentElement
  canvasWidth.value = container?.clientWidth || 800
  canvasHeight.value = Math.min(canvasWidth.value * 0.75, 600)
}

onMounted(() => {
  resizeCanvas()
  window.addEventListener('resize', resizeCanvas)
})

onUnmounted(() => {
  window.removeEventListener('resize', resizeCanvas)
})
</script>

<template>
  <canvas
    ref="canvas"
    :width="canvasWidth"
    :height="canvasHeight"
    class="w-full h-auto"
  />
</template>
```

## Responsive Images Best Practices

### Astro Image Component

```astro
---
import { Image } from 'astro:assets'
import heroImage from '../assets/hero.png'
---

<Image
  src={heroImage}
  alt="Physics Hero"
  widths={[320, 640, 768, 1024, 1280]}
  sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
  loading="lazy"
/>
```

### Picture Element for Art Direction

```html
<picture>
  <source media="(max-width: 640px)" srcset="/hero-mobile.webp">
  <source media="(max-width: 1024px)" srcset="/hero-tablet.webp">
  <img src="/hero-desktop.webp" alt="Hero" class="w-full h-auto">
</picture>
```

## Testing Checklist

Before deploying, verify:

- [ ] **320px**: Content readable, no horizontal scroll
- [ ] **375px (iPhone SE)**: Buttons touchable, text legible
- [ ] **640px (sm)**: Layout transitions correctly
- [ ] **768px (md - tablet)**: Two-column layouts work
- [ ] **1024px (lg - desktop)**: Full navigation visible
- [ ] **1280px (xl)**: Content doesn't exceed max-width
- [ ] **Portrait & Landscape**: Both orientations tested
- [ ] **Touch devices**: Hover effects don't break functionality
- [ ] **Slow 3G**: Page loads in < 10s (use Chrome throttling)

## Reference

For complete breakpoint configuration, see [tailwind-breakpoints.md](tailwind-breakpoints.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agomez356) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
