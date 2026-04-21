---
name: animation-library
description: Implement modern, performance-conscious animations using Framer Motion, CSS, and scroll-triggered effects. Use when adding page transitions, hover effects, scroll animations, or micro-interactions to websites. Use when this capability is needed.
metadata:
  author: deomiarn
---

# Animation Library

This skill provides patterns for implementing smooth, performant animations that enhance user experience without compromising performance.

## When to Use This Skill

- Adding entrance animations to page elements
- Implementing scroll-triggered animations
- Creating hover and interaction effects
- Building page transitions
- Adding micro-interactions

## Core Principles

### 1. Performance First

**Golden Rules:**
1. Only animate `transform` and `opacity` (GPU-accelerated)
2. Avoid animating `width`, `height`, `top`, `left` (triggers layout)
3. Use `will-change` sparingly and remove after animation
4. Prefer CSS for simple animations, Framer Motion for complex ones

### 2. Framer Motion Setup

**Installation:**
```bash
npm install framer-motion
```

**Basic Usage:**
```tsx
import { motion } from "framer-motion"

// Fade in
<motion.div
  initial={{ opacity: 0 }}
  animate={{ opacity: 1 }}
  transition={{ duration: 0.5 }}
>
  Content
</motion.div>
```

### 3. Common Animation Patterns

**Fade Up (Most Common):**
```tsx
const fadeUp = {
  initial: { opacity: 0, y: 20 },
  animate: { opacity: 1, y: 0 },
  transition: { duration: 0.5, ease: [0.4, 0, 0.2, 1] }
}

<motion.div {...fadeUp}>
  Content
</motion.div>
```

**Scale In:**
```tsx
const scaleIn = {
  initial: { opacity: 0, scale: 0.95 },
  animate: { opacity: 1, scale: 1 },
  transition: { duration: 0.3 }
}
```

**Slide In:**
```tsx
const slideIn = {
  initial: { opacity: 0, x: -20 },
  animate: { opacity: 1, x: 0 },
  transition: { duration: 0.4 }
}
```

**Stagger Children:**
```tsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1
    }
  }
}

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 }
}

<motion.ul variants={container} initial="hidden" animate="show">
  {items.map((item) => (
    <motion.li key={item.id} variants={item}>
      {item.content}
    </motion.li>
  ))}
</motion.ul>
```

### 4. Scroll-Triggered Animations

**Viewport Entry:**
```tsx
<motion.div
  initial={{ opacity: 0, y: 50 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, margin: "-100px" }}
  transition={{ duration: 0.6 }}
>
  Animates when scrolled into view
</motion.div>
```

**Scroll Progress:**
```tsx
import { useScroll, useTransform, motion } from "framer-motion"

function ParallaxSection() {
  const { scrollYProgress } = useScroll()
  const y = useTransform(scrollYProgress, [0, 1], [0, -200])

  return (
    <motion.div style={{ y }}>
      Parallax content
    </motion.div>
  )
}
```

**Element-Specific Scroll:**
```tsx
function ScrollSection() {
  const ref = useRef(null)
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ["start end", "end start"]
  })

  const opacity = useTransform(scrollYProgress, [0, 0.5, 1], [0, 1, 0])
  const scale = useTransform(scrollYProgress, [0, 0.5, 1], [0.8, 1, 0.8])

  return (
    <motion.section ref={ref} style={{ opacity, scale }}>
      Content
    </motion.section>
  )
}
```

### 5. Hover Effects

**Button Hover:**
```tsx
<motion.button
  whileHover={{ scale: 1.02 }}
  whileTap={{ scale: 0.98 }}
  transition={{ type: "spring", stiffness: 400, damping: 17 }}
>
  Click me
</motion.button>
```

**Card Hover:**
```tsx
<motion.div
  className="relative group"
  whileHover={{ y: -5 }}
  transition={{ duration: 0.2 }}
>
  <div className="absolute inset-0 bg-gradient-to-r from-primary to-primary/50 rounded-xl opacity-0 group-hover:opacity-100 transition-opacity blur-xl" />
  <div className="relative bg-card rounded-xl p-6">
    Card content
  </div>
</motion.div>
```

**Image Hover:**
```tsx
<motion.div className="overflow-hidden rounded-xl">
  <motion.img
    src="..."
    whileHover={{ scale: 1.05 }}
    transition={{ duration: 0.4 }}
    className="w-full h-full object-cover"
  />
</motion.div>
```

### 6. Page Transitions

**Layout Animations:**
```tsx
import { AnimatePresence, motion } from "framer-motion"

// In layout.tsx or _app.tsx
<AnimatePresence mode="wait">
  <motion.main
    key={pathname}
    initial={{ opacity: 0, y: 20 }}
    animate={{ opacity: 1, y: 0 }}
    exit={{ opacity: 0, y: -20 }}
    transition={{ duration: 0.3 }}
  >
    {children}
  </motion.main>
</AnimatePresence>
```

**Shared Layout:**
```tsx
// Tab switching with smooth morphing
<motion.div layoutId="activeTab" className="bg-primary" />
```

### 7. Micro-Interactions

**Checkbox Animation:**
```tsx
<motion.svg viewBox="0 0 24 24">
  <motion.path
    d="M5 12l5 5L20 7"
    fill="none"
    stroke="currentColor"
    strokeWidth={2}
    initial={{ pathLength: 0 }}
    animate={{ pathLength: isChecked ? 1 : 0 }}
    transition={{ duration: 0.3, ease: "easeOut" }}
  />
</motion.svg>
```

**Loading States:**
```tsx
const loadingDots = {
  animate: {
    transition: {
      staggerChildren: 0.1
    }
  }
}

const dot = {
  animate: {
    y: [0, -10, 0],
    transition: {
      repeat: Infinity,
      duration: 0.6
    }
  }
}

<motion.div className="flex gap-1" variants={loadingDots} animate="animate">
  <motion.span variants={dot} className="w-2 h-2 bg-primary rounded-full" />
  <motion.span variants={dot} className="w-2 h-2 bg-primary rounded-full" />
  <motion.span variants={dot} className="w-2 h-2 bg-primary rounded-full" />
</motion.div>
```

### 8. CSS-Only Animations

For simpler animations, CSS can be more performant:

**Keyframe Animation:**
```css
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fadeIn 0.5s ease-out forwards;
}
```

**Stagger with CSS:**
```css
.stagger-item {
  opacity: 0;
  animation: fadeIn 0.5s ease-out forwards;
}

.stagger-item:nth-child(1) { animation-delay: 0ms; }
.stagger-item:nth-child(2) { animation-delay: 100ms; }
.stagger-item:nth-child(3) { animation-delay: 200ms; }
.stagger-item:nth-child(4) { animation-delay: 300ms; }
```

**Tailwind Animations:**
```tsx
// Add to tailwind.config.ts
animation: {
  "fade-in": "fadeIn 0.5s ease-out forwards",
  "slide-up": "slideUp 0.5s ease-out forwards",
  "scale-in": "scaleIn 0.3s ease-out forwards",
}
```

### 9. Reduced Motion

**Always respect user preferences:**
```tsx
import { useReducedMotion } from "framer-motion"

function Component() {
  const prefersReducedMotion = useReducedMotion()

  return (
    <motion.div
      initial={{ opacity: 0, y: prefersReducedMotion ? 0 : 20 }}
      animate={{ opacity: 1, y: 0 }}
    >
      Content
    </motion.div>
  )
}
```

**CSS:**
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 10. Recommended Timing Values

| Type | Duration | Easing |
|------|----------|--------|
| Micro-interactions | 150-200ms | ease-out |
| Hover states | 200-300ms | ease-in-out |
| Page transitions | 300-500ms | ease-in-out |
| Scroll animations | 400-600ms | ease-out |
| Complex sequences | 600-1000ms | custom |

**Easing Functions:**
```tsx
const easings = {
  easeOut: [0, 0, 0.2, 1],
  easeIn: [0.4, 0, 1, 1],
  easeInOut: [0.4, 0, 0.2, 1],
  spring: { type: "spring", stiffness: 300, damping: 20 }
}
```

## Quality Checklist

- [ ] Animations only use transform/opacity
- [ ] Respects prefers-reduced-motion
- [ ] Animations have purpose (not decoration only)
- [ ] Timing feels snappy, not sluggish
- [ ] Stagger delays are subtle (50-100ms)
- [ ] Page doesn't feel slow or laggy
- [ ] Mobile performance is tested
- [ ] Animations enhance, not distract

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
