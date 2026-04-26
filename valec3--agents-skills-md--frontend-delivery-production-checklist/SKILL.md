---
name: frontend-delivery-production-checklist
description: Standardized guidelines and patterns for Frontend Delivery Production Checklist. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Delivery Production Checklist

## When to use this skill
- Before deploying to production
- Production readiness review
- Performance optimization

## Workflow
- [ ] Run production build
- [ ] Check bundle size
- [ ] Run Lighthouse audit
- [ ] Test on real devices
- [ ] Verify error tracking
- [ ] Check analytics

## Instructions

### Checklist
- [ ] All environment variables set
- [ ] Error boundaries in place
- [ ] Error tracking configured (Sentry)
- [ ] Analytics configured
- [ ] SEO meta tags present
- [ ] Images optimized
- [ ] Bundle size < 200KB (gzipped)
- [ ] Lighthouse score > 90
- [ ] Accessibility tested
- [ ] Security headers configured
- [ ] HTTPS enabled
- [ ] CDN configured

### Performance
```bash
npm run build
npm run preview
lighthouse http://localhost:4173
```

## Resources
- Use production build for testing
- Run Lighthouse before deploy
- Monitor bundle size

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
