---
name: performance-optimization
description: Techniques to make apps fast. Use when the app feels slow or Lighthouse scores are low. Use when this capability is needed.
metadata:
  author: wynautbhav
---

# Performance Optimization

## Checklist
1. **Images**: Use WebP/AVIF. Always define width/height to prevent layout shift.
2. **Lazy Loading**: Lazy load components, images, and heavy libraries that aren't needed immediately.
3. **Database**: Check for "N+1" query problems. Use indexes on frequently searched columns.
4. **Caching**: Cache static assets and expensive API responses.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
