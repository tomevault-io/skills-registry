---
name: seo
description: > Use when this capability is needed.
metadata:
  author: jjmendezrodriguez
---

# SEO — Technical / On-page / Structured Data

> Staff Engineer standard: SEO is **engineering discipline** — crawlability, performance, and semantics by default.

---

## 1. Core Principles

```
✅ Every page has unique <title> + meta description
✅ Single <h1> per page, logical heading hierarchy
✅ All images have descriptive alt text
✅ HTTPS everywhere
✅ Canonical URLs prevent duplicate content
✅ robots.txt allows crawling of public pages
❌ Never block CSS/JS needed for rendering
❌ Never noindex important content
❌ Don't rely on client-side rendering for SEO-critical content (use SSR/SSG)
```

---

## 2. robots.txt

```text
# /robots.txt
User-agent: *
Allow: /

Disallow: /admin/
Disallow: /api/
Disallow: /private/

Sitemap: https://example.com/sitemap.xml
```

---

## 3. Title & Meta

```html
<!-- Title: 50-60 chars, keyword near start, unique per page -->
<title>Blue Widgets for Sale | Premium Quality | Example Store</title>

<!-- Description: 150-160 chars, keyword + CTA, unique per page -->
<meta name="description" content="Shop premium blue widgets with free shipping. 30-day returns. Rated 4.9/5 by 10,000+ customers.">

<!-- Canonical — always self-referencing -->
<link rel="canonical" href="https://example.com/current-page">

<!-- Robots — default allows indexing -->
<meta name="robots" content="index, follow">
```

---

## 4. XML Sitemap

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2026-01-15</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
</urlset>
```

Rules: max 50k URLs / 50MB per file. Only canonical, indexable URLs. Update `lastmod` on content change. Submit to Google Search Console.

---

## 5. Heading Structure

```html
<!-- ✅ Single h1, logical hierarchy -->
<h1>Blue Widgets — Premium Quality</h1>
  <h2>Product Features</h2>
    <h3>Durability</h3>
  <h2>Customer Reviews</h2>

<!-- ❌ Multiple h1, skipped levels, wrong order -->
<h4>Products</h4>
<h1>Contact Us</h1>
```

---

## 6. Images

```html
<!-- ✅ Descriptive filename, alt, dimensions, lazy load, WebP -->
<img
  src="blue-widget-side-view.webp"
  alt="Blue widget with chrome finish showing side control panel"
  width="800" height="600"
  loading="lazy"
/>

<!-- ❌ Generic filename, missing alt -->
<img src="IMG_12345.jpg">
```

---

## 7. Structured Data (JSON-LD)

```html
<!-- Organization — on every page or homepage -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Example Co",
  "url": "https://example.com",
  "logo": "https://example.com/logo.png"
}
</script>

<!-- Product page -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Blue Widget Pro",
  "offers": {
    "@type": "Offer",
    "price": "49.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock"
  },
  "aggregateRating": { "@type": "AggregateRating", "ratingValue": "4.8", "reviewCount": "1250" }
}
</script>

<!-- Breadcrumbs -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://example.com" },
    { "@type": "ListItem", "position": 2, "name": "Products", "item": "https://example.com/products" }
  ]
}
</script>
```

Validate at: [Google Rich Results Test](https://search.google.com/test/rich-results) · [Schema.org Validator](https://validator.schema.org/)

---

## 8. International SEO (hreflang)

```html
<link rel="alternate" hreflang="en"      href="https://example.com/page">
<link rel="alternate" hreflang="es"      href="https://example.com/es/page">
<link rel="alternate" hreflang="x-default" href="https://example.com/page">
<html lang="en">
```

---

## 9. URL Guidelines

```
✅ https://example.com/products/blue-widget    (hyphens, lowercase, keywords)
❌ https://example.com/p?id=12345              (parameters)
❌ https://example.com/Products/Blue_Widget    (uppercase, underscores)
```

Max ~75 chars. Keywords included naturally.

---

## 10. Quick Checklist

```
Critical:
[ ] HTTPS enabled
[ ] robots.txt present + allows crawling
[ ] No noindex on important pages
[ ] Unique title tag per page (50-60 chars)
[ ] Single <h1> per page
[ ] Canonical URL on every page

High priority:
[ ] Unique meta description (150-160 chars)
[ ] Sitemap submitted to Search Console
[ ] Mobile viewport meta tag
[ ] Images have alt text, width, height

Medium priority:
[ ] Structured data implemented (Product / Article / FAQ)
[ ] Breadcrumb navigation + JSON-LD
[ ] Descriptive anchor text (no "click here")
[ ] hreflang for multi-language

Ongoing:
[ ] Crawl errors fixed in Search Console
[ ] Core Web Vitals passing (see performance skill)
[ ] Broken links fixed
```

---
> Source: [jjmendezrodriguez/jm-claude-plugin](https://github.com/jjmendezrodriguez/jm-claude-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
