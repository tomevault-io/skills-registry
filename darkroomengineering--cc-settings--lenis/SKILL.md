---
name: lenis
description: | Use when this capability is needed.
metadata:
  author: darkroomengineering
---

# Lenis Smooth Scroll Setup

Set up Lenis for smooth scrolling in a Next.js/React app.

## Installation

```bash
bun add lenis@latest
```

## Basic Setup (Satus already includes this)

### Create Lenis Provider

```tsx
// components/lenis/index.tsx
'use client'

import { ReactLenis } from 'lenis/react'

interface LenisProps {
  children: React.ReactNode
}

export function Lenis({ children }: LenisProps) {
  return (
    <ReactLenis
      root
      options={{
        duration: 1.2,
        easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
        orientation: 'vertical',
        smoothWheel: true,
      }}
    >
      {children}
    </ReactLenis>
  )
}
```

### Add to Layout

```tsx
// app/layout.tsx
import { Lenis } from '@/components/lenis'

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Lenis>{children}</Lenis>
      </body>
    </html>
  )
}
```

## Using Lenis

### Scroll to Element

```tsx
import { useLenis } from 'lenis/react'

function Component() {
  const lenis = useLenis()

  const scrollToSection = () => {
    lenis?.scrollTo('#section-id', { duration: 1.5 })
  }

  return <button onClick={scrollToSection}>Scroll</button>
}
```

### Scroll Events

```tsx
useLenis(({ scroll, velocity, direction }) => {
  // React to scroll
  console.log({ scroll, velocity, direction })
})
```

### With GSAP ScrollTrigger

```tsx
import { useLenis } from 'lenis/react'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

useLenis(() => {
  ScrollTrigger.update()
})
```

## Satus Note

If using Satus starter, Lenis is already configured. Check:
- `components/lenis/index.tsx`
- `app/layout.tsx`

## Remember

- Always fetch latest docs before implementing
- Store any gotchas as learnings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darkroomengineering) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
