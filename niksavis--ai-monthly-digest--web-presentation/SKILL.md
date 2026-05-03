---
name: web-presentation
description: HTML/CSS/JS rules for the static slide deck project. Use when this capability is needed.
metadata:
  author: niksavis
---

# Web Presentation Skill

Rules:

- HTML: semantic elements only; headings strictly ordered; labels descriptive; no inline handlers/styles.
- HTML: skip link for keyboard users; ARIA live region for announcements; descriptive aria-labels on buttons.
- HTML: font-display=swap; meta description; images with loading="lazy".
- HTML: all markup must pass djLint (`profile: html`); only H006 and H031 are ignored — all other rules must be satisfied.
- CSS: styles.css only; variables for tokens; no unused selectors; no deprecated/prefixed features.
- CSS: :focus-visible with clear outlines; prefers-reduced-motion support; touch targets minimum 44px.
- JS: addEventListener only; no inline handlers; batch DOM writes; handle missing nodes.
- JS: touch/swipe gestures; URL hash navigation; ARIA live updates on slide changes.
- A11y: prefer native controls; keep keyboard navigation intact.
- Slides: 10 per deck; 1 headline + 2-3 sentences; 1 image + visible credit (original URL + author/host).
- Slides: no eyebrow labels; include a link back to index.html in deck header.
- Slides: source links open in a new tab with rel="noopener".
- Layout: two-column on desktop, stacked on mobile.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/niksavis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
