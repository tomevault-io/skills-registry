---
name: cinematic-intro
description: Build a cinematic Netflix-style intro overlay for a Next.js portfolio using Framer Motion, with session-only playback, reduced-motion support, and clean transition into the homepage. Use when this capability is needed.
metadata:
  author: subhan2117
---

# Cinematic Intro Skill

Use this skill when asked to implement or refine a Netflix-style cinematic intro for a Next.js portfolio.

## Core requirements

- Fullscreen overlay on first load.
- Center text: “Subhan Nadeem”.
- Blur → sharp + fade-in + subtle scale (premium, fast).
- Brief hold, then fade + slight upward motion.
- Clean transition into homepage.
- Play once per session (sessionStorage).
- Use Framer Motion.
- Respect prefers-reduced-motion.
- Export a reusable component and explain where to mount it.

## Implementation checklist

1. Create a client component that renders a fixed, fullscreen overlay.
2. Use `sessionStorage` to ensure one play per browser session.
3. Use `useReducedMotion` from Framer Motion to bypass animation.
4. Animate text blur → sharp and fade-in with a subtle scale.
5. Hold briefly, then fade out and move slightly upward.
6. Unmount overlay after completion and reveal the page underneath.
7. Mount component at the top of `app/layout.jsx` or in the root `page.jsx`.

## Recommended structure

- Component: `app/components/CinematicIntro.jsx`
- Mount: `app/layout.jsx` (preferred for all routes) or `app/page.jsx` (home only)

## Example component (adjust durations to taste)

```jsx
"use client";

import { useEffect, useState } from "react";
import { AnimatePresence, motion, useReducedMotion } from "framer-motion";

const STORAGE_KEY = "cinematicIntroSeen";

export default function CinematicIntro() {
  const prefersReducedMotion = useReducedMotion();
  const [show, setShow] = useState(false);

  useEffect(() => {
    if (typeof window === "undefined") return;
    const seen = sessionStorage.getItem(STORAGE_KEY);
    if (!seen) {
      sessionStorage.setItem(STORAGE_KEY, "1");
      setShow(true);
    }
  }, []);

  useEffect(() => {
    if (!show) return;
    const totalMs = prefersReducedMotion ? 0 : 1800;
    const timer = setTimeout(() => setShow(false), totalMs);
    return () => clearTimeout(timer);
  }, [show, prefersReducedMotion]);

  if (prefersReducedMotion) {
    return null;
  }

  return (
    <AnimatePresence>
      {show ? (
        <motion.div
          key="intro"
          initial={{ opacity: 1 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          transition={{ duration: 0.4 }}
          style={{
            position: "fixed",
            inset: 0,
            zIndex: 9999,
            background: "#0b0b0b",
            display: "flex",
            alignItems: "center",
            justifyContent: "center",
          }}
        >
          <motion.div
            initial={{ opacity: 0, scale: 0.985, filter: "blur(12px)" }}
            animate={{
              opacity: 1,
              scale: 1,
              filter: "blur(0px)",
              transition: {
                duration: 0.55,
                ease: [0.22, 1, 0.36, 1],
              },
            }}
            exit={{
              opacity: 0,
              y: -12,
              transition: { duration: 0.35, ease: "easeInOut" },
            }}
            style={{
              color: "#f2f2f2",
              fontSize: "clamp(2rem, 6vw, 4.5rem)",
              letterSpacing: "0.08em",
              textTransform: "uppercase",
            }}
          >
            Subhan Nadeem
          </motion.div>
        </motion.div>
      ) : null}
    </AnimatePresence>
  );
}
```

## Mounting guidance

- In `app/layout.jsx`, render `<CinematicIntro />` before `{children}` to cover all pages.
- If the intro is only for the homepage, mount in `app/page.jsx` above the hero section.

## Notes

- Use a slightly warm off-white text color for a premium look.
- If you need exact timing: 0.55s in, 0.6s hold, 0.35s out.
- If you need multiple route support, leave it in `layout.jsx` but keep session storage key stable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/subhan2117) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
