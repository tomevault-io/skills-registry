---
name: ai-search-technical-auditor
description: Audit front-end code for AI search readiness. Use when reviewing HTML structure, meta tags, schema markup, and technical elements that affect how AI crawlers understand and index web pages. Use when this capability is needed.
metadata:
  author: ihmissuti
---

# AI Search Technical Auditor

Audit HTML, meta tags, schema markup, and technical implementation to ensure pages are optimized for AI search engine crawlers.

## Understanding AI Crawlers

AI search engines use different crawlers than traditional search engines. Key AI crawlers include:

| Crawler           | Platform   | User Agent Contains | Primary Purpose                     |
| ----------------- | ---------- | ------------------- | ----------------------------------- |
| GPTBot            | OpenAI     | `GPTBot`            | Training data for GPT models        |
| ChatGPT-User      | OpenAI     | `ChatGPT-User`      | Real-time browsing for current info |
| OAI-SearchBot     | OpenAI     | `OAI-SearchBot`     | Indexing for ChatGPT Search         |
| ClaudeBot         | Anthropic  | `ClaudeBot`         | Training Claude and live retrieval  |
| PerplexityBot     | Perplexity | `PerplexityBot`     | Building independent search index   |
| Google-Extended   | Google     | `Google-Extended`   | Training data for Gemini AI         |
| Applebot-Extended | Apple      | `Applebot-Extended` | Training Apple Intelligence         |

## Technical Audit Checklist

### 1. robots.txt Configuration

Check that AI crawlers are allowed:

```txt
# Good - Allow AI crawlers
User-agent: GPTBot
Allow: /

User-agent: ChatGPT-User
Allow: /

User-agent: Google-Extended
Allow: /

User-agent: ClaudeBot
Allow: /

User-agent: PerplexityBot
Allow: /
```

```txt
# Bad - Blocking AI crawlers (unless intentional)
User-agent: GPTBot
Disallow: /
```

**Audit questions:**

- [ ] Are AI crawlers explicitly allowed or blocked?
- [ ] Is the intent to block deliberate and documented?
- [ ] Are important content paths accessible?

### 2. llms.txt Implementation

Check for llms.txt file at domain root (`/llms.txt`):

```markdown
# Company Name

> Brief description of the company/product

Key terms: important concept 1, concept 2, product name

## Docs

- [Documentation](https://example.com/docs.md): API and integration guides

## Product

- [Features](https://example.com/features.md): Platform capabilities

## Optional

- [Blog](https://example.com/blog.md): Industry insights
```

**Audit questions:**

- [ ] Is llms.txt present at domain root?
- [ ] Does it follow the standard format (H1, blockquote, sections)?
- [ ] Are linked URLs valid and accessible?
- [ ] Are linked pages available in clean markdown format?

### 3. HTML Structure

#### Semantic HTML

Check for proper semantic structure:

```html
<!-- Good -->
<article>
  <header>
    <h1>Page Title</h1>
    <p class="summary">TL;DR content here</p>
  </header>
  <section>
    <h2>Section Heading</h2>
    <p>Content...</p>
  </section>
</article>

<!-- Bad -->
<div class="article">
  <div class="title">Page Title</div>
  <div class="content">
    <div class="heading">Section Heading</div>
    <div>Content...</div>
  </div>
</div>
```

**Audit questions:**

- [ ] Uses semantic elements (`<article>`, `<section>`, `<nav>`, `<aside>`)?
- [ ] Heading hierarchy is logical (H1 → H2 → H3)?
- [ ] Only one `<h1>` per page?
- [ ] Main content is in `<main>` element?

#### Content Accessibility

```html
<!-- Good - Content in HTML -->
<h2>Product Features</h2>
<ul>
  <li>Feature one description</li>
  <li>Feature two description</li>
</ul>

<!-- Bad - Content in JavaScript only -->
<div id="features"></div>
<script>
  renderFeatures(); // Content not in initial HTML
</script>
```

**Audit questions:**

- [ ] Key content is in initial HTML (not JavaScript-rendered)?
- [ ] Images have descriptive alt text?
- [ ] Tables have proper headers (`<th>`)?
- [ ] Lists use `<ul>`/`<ol>` elements?

### 4. Meta Tags

#### Essential Meta Tags

```html
<head>
  <!-- Page title - Clear and descriptive -->
  <title>What is GEO? Generative Engine Optimization Guide | Brand</title>

  <!-- Meta description - Summarizes page content -->
  <meta name="description" content="GEO (Generative Engine Optimization)
    is the practice of optimizing content for AI search engines. Learn
    how to improve visibility in ChatGPT, Perplexity, and Google AI Mode.">

  <!-- Canonical URL -->
  <link rel="canonical" href="https://example.com/geo-guide">

  <!-- Language -->
  <html lang="en">

  <!-- Viewport for mobile -->
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
```

**Audit questions:**

- [ ] Title is descriptive and under 60 characters?
- [ ] Meta description summarizes content (150-160 chars)?
- [ ] Canonical URL is set correctly?
- [ ] Language is declared?
- [ ] Viewport meta tag is present?

#### Open Graph Tags

```html
<meta property="og:title" content="What is GEO?" />
<meta property="og:description" content="Guide to Generative Engine Optimization" />
<meta property="og:type" content="article" />
<meta property="og:url" content="https://example.com/geo-guide" />
<meta property="og:image" content="https://example.com/images/geo-guide.jpg" />
```

**Audit questions:**

- [ ] OG title and description are set?
- [ ] OG image is specified and valid?
- [ ] OG URL matches canonical?

### 5. Schema Markup (JSON-LD)

#### Schema Type Selection

| Content Type | Recommended Schema | Required Properties                 |
| ------------ | ------------------ | ----------------------------------- |
| Article/Blog | Article            | headline, author, datePublished     |
| FAQ          | FAQPage            | mainEntity (Questions with Answers) |
| Tutorial     | HowTo              | name, step (HowToSteps)             |
| Product      | Product            | name, offers, brand                 |
| Company      | Organization       | name, url, logo                     |
| Navigation   | BreadcrumbList     | itemListElement                     |

#### Schema Validation Checklist

**Audit questions:**

- [ ] Schema type matches content type?
- [ ] JSON-LD is valid (no syntax errors)?
- [ ] Required properties are populated?
- [ ] Dates are in ISO 8601 format?
- [ ] URLs are absolute, not relative?
- [ ] dateModified reflects actual last update?

### 6. Page Speed

AI crawlers have tight time budgets. Target these performance metrics:

| Metric                         | Target  | Critical for            |
| ------------------------------ | ------- | ----------------------- |
| Time to First Byte (TTFB)      | < 200ms | All crawlers            |
| First Contentful Paint (FCP)   | < 0.4s  | AI citation eligibility |
| Largest Contentful Paint (LCP) | < 2.5s  | Full page analysis      |
| First Input Delay (FID)        | < 100ms | Interactivity           |
| Cumulative Layout Shift (CLS)  | < 0.1   | Visual stability        |

**The 0.4s Speed Signal:**
Fast-loading pages are 3x more likely to be cited by ChatGPT. Real-time AI agents may abandon pages that take longer to render.

**Audit questions:**

- [ ] TTFB under 200ms?
- [ ] FCP under 0.4s (ideally)?
- [ ] LCP under 2.5s?
- [ ] No render-blocking JavaScript?
- [ ] Images are optimized and lazy-loaded?
- [ ] CSS is minified?

### 7. Mobile Friendliness

```html
<!-- Viewport meta tag -->
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

**Audit questions:**

- [ ] Viewport meta tag is present?
- [ ] Content is readable without zooming?
- [ ] Touch targets are appropriately sized?
- [ ] No horizontal scrolling required?

### 8. URL Structure

```
Good URLs:
/guides/geo-optimization
/products/analytics-platform
/docs/api-reference

Bad URLs:
/page.php?id=123&cat=5
/guides/geo-optimization-best-practices-guide-2024-updated-version-3
/p/12345
```

**Audit questions:**

- [ ] URLs are descriptive and readable?
- [ ] URLs are not excessively long?
- [ ] URLs use hyphens, not underscores?
- [ ] No query parameters for primary content?

### 9. Content Rendering

**Server-Side vs Client-Side:**

- Use Server-Side Rendering (SSR) or Static Site Generation (SSG)
- Client-side JavaScript rendering may leave content invisible to AI crawlers
- Test how AI systems see your pages, not just how browsers render them

**Audit questions:**

- [ ] Server-side or static rendering in place?
- [ ] Critical content visible without JavaScript?
- [ ] No JavaScript-dependent key information?

## Audit Report Template

```markdown
# AI Search Technical Audit Report

**URL:** [Page URL]
**Date:** [Audit Date]
**Overall Score:** [X/100]

## Summary

[Brief summary of findings]

## Critical Issues (Fix Immediately)

- [ ] Issue 1
- [ ] Issue 2

## Warnings (Fix Soon)

- [ ] Warning 1
- [ ] Warning 2

## Passed Checks

- [x] Check 1
- [x] Check 2

## Performance Metrics

| Metric | Current | Target | Status |
| ------ | ------- | ------ | ------ |
| TTFB   | Xms     | <200ms | ✓/✗    |
| FCP    | Xs      | <0.4s  | ✓/✗    |
| LCP    | Xs      | <2.5s  | ✓/✗    |

## Recommendations

1. [Priority 1 recommendation]
2. [Priority 2 recommendation]
3. [Priority 3 recommendation]

## Next Audit Date

[Date for follow-up audit]
```

## Quick Audit Checklist

For fast assessments, check these critical items:

- [ ] **robots.txt**: AI crawlers allowed
- [ ] **llms.txt**: Present and properly formatted
- [ ] **Semantic HTML**: Proper structure with H1/H2/H3
- [ ] **Schema**: Appropriate type implemented
- [ ] **Speed**: FCP under 0.4s
- [ ] **Rendering**: Server-side rendering in place
- [ ] **Mobile**: Viewport meta tag present
- [ ] **Meta tags**: Title, description, canonical set

## Validation Tools

After implementing fixes, validate using:

1. **Schema:** Google Rich Results Test, Schema.org Validator
2. **HTML:** W3C Validator
3. **Speed:** Google PageSpeed Insights, WebPageTest
4. **Mobile:** Google Mobile-Friendly Test
5. **robots.txt:** Google Search Console robots.txt Tester
6. **llms.txt:** Manual verification at yourdomain.com/llms.txt

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihmissuti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
