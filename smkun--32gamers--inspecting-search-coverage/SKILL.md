---
name: inspecting-search-coverage
description: | Use when this capability is needed.
metadata:
  author: smkun
---

# Inspecting Search Coverage

Audits SEO surface areas for static HTML + Firebase sites. This codebase uses vanilla JavaScript with client-side Firebase rendering, which creates specific crawlability challenges.

## Quick Start

### Check Current SEO State

```bash
# Find all HTML files
find . -name "*.html" -not -path "./OLD/*"

# Check for meta tags
grep -l "meta name=\"description\"" *.html

# Check for structured data
grep -l "application/ld+json" *.html

# Verify robots.txt exists
ls -la robots.txt sitemap.xml
```

### Add Missing Meta Description

```html
<!-- In <head> section of index.html -->
<meta name="description" content="32Gamers Club - Cyberpunk gaming portal with curated apps and tools for the gaming community." />
```

## Key Concepts

| Element | Status | Impact |
|---------|--------|--------|
| Meta description | Missing | Critical - affects SERP snippets |
| Open Graph tags | Missing | Social sharing broken |
| robots.txt | Missing | Crawl control unavailable |
| sitemap.xml | Missing | Discovery inefficient |
| JSON-LD | Missing | No rich results |
| Canonical URL | Missing | Duplicate content risk |

## Common Patterns

### Protect Admin Pages from Indexing

**When:** Admin panel at `/firebase-admin.html` should not appear in search results.

```html
<!-- Add to firebase-admin.html <head> -->
<meta name="robots" content="noindex, nofollow" />
```

### Client-Side Rendering SEO Risk

**When:** Apps loaded via Firebase SDK after page render.

```javascript
// Current pattern in app.js - content not in initial HTML
const querySnapshot = await window.firebase.getDocs(
    window.firebase.collection(window.firebase.db, 'apps')
);
```

**Impact:** Search engines may not execute JavaScript or wait for Firebase data.

## Audit Checklist

Copy and track progress:
- [ ] Meta description on all public pages
- [ ] Open Graph tags (og:title, og:description, og:image)
- [ ] Twitter Card tags
- [ ] robots.txt created with admin exclusions
- [ ] sitemap.xml with public URLs
- [ ] JSON-LD Organization schema
- [ ] Canonical URLs on all pages
- [ ] noindex on admin/private pages

## See Also

- [Technical SEO](references/technical.md) - robots.txt, sitemaps, canonicals
- [On-Page SEO](references/on-page.md) - meta tags, headings, semantic HTML
- [Content SEO](references/content.md) - descriptions, alt text, page titles
- [Programmatic SEO](references/programmatic.md) - dynamic page generation
- [Schema Markup](references/schema.md) - JSON-LD, structured data
- [Competitive SEO](references/competitive.md) - comparison pages, alternatives

## Related Skills

- See the **vanilla-javascript** skill for client-side rendering patterns
- See the **firebase** skill for Firestore data loading
- See the **crafting-page-messaging** skill for writing meta descriptions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/smkun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
