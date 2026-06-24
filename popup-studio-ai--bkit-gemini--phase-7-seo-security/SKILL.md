---
name: phase-7-seo-security
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 7: SEO & Security

> Optimize for search and secure your application

## SEO Checklist

### 1. Meta Tags

```typescript
// app/layout.tsx
export const metadata: Metadata = {
  title: {
    template: '%s | My App',
    default: 'My App - Best Product'
  },
  description: 'Description for search engines',
  keywords: ['keyword1', 'keyword2'],
  openGraph: {
    title: 'My App',
    description: 'Description for social sharing',
    images: ['/og-image.png']
  },
  twitter: {
    card: 'summary_large_image'
  }
};
```

### 2. Semantic HTML

- Use proper heading hierarchy (h1 → h2 → h3)
- Add alt text to images
- Use semantic elements (article, section, nav)
- Implement structured data (JSON-LD)

### 3. Performance

- Image optimization (next/image)
- Code splitting
- Lazy loading
- Core Web Vitals monitoring

## Security Checklist

### 1. Headers

```typescript
// next.config.js
const securityHeaders = [
  { key: 'X-Frame-Options', value: 'DENY' },
  { key: 'X-Content-Type-Options', value: 'nosniff' },
  { key: 'Strict-Transport-Security', value: 'max-age=31536000' },
  { key: 'Content-Security-Policy', value: "default-src 'self'" }
];
```

### 2. Input Validation

- Validate all user inputs
- Sanitize HTML content
- Use parameterized queries
- Implement rate limiting

### 3. Authentication

- Secure password hashing
- JWT token expiration
- CSRF protection
- Session management

## Next Phase

After completion: `/phase-8-review`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
