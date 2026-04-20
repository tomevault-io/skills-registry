---
name: seo-validation
description: SEO and meta tag validation for search engine optimization and social sharing. Checks title, meta description, Open Graph tags, Twitter Cards, favicon, canonical URLs, and structured data. Use when user says "check SEO", "validate meta tags", or "search engine". Use when this capability is needed.
metadata:
  author: ai-enhanced-engineer
---

# SEO Validation Skill

## Purpose

Validate essential SEO elements and meta tags to ensure optimal search engine visibility and social media sharing presentation.

---

## When to Use

**Trigger phrases**:
- "check SEO"
- "validate meta tags"
- "search engine optimization"
- "social sharing preview"
- "Open Graph tags"
- "Twitter Card"

**Use cases**:
- Pre-deployment SEO audit
- After updating page content/titles
- Ensuring social media previews display correctly
- Client deliverable compliance check

---

## Validation Checklist

### 1. Essential Meta Tags

#### Title Tag
**Requirements**:
- Must exist in `<head>`
- Length: 50-60 characters (optimal for Google SERP)
- Descriptive and unique
- Includes primary keyword
- Format: `Primary Keyword - Brand Name` or `Page Title | Brand`

**Validation**:
```bash
grep -n '<title>' index.html
```

**Check**:
- ✅ Exists
- ✅ Length ≤60 characters
- ✅ Not generic ("Home", "Welcome")
- ✅ Includes brand/company name

**Common issues**:
- Title too long (truncated in search results)
- Generic title ("Home Page")
- Missing title entirely
- Title same across all pages (if multi-page site)

---

#### Meta Description
**Requirements**:
- Must exist in `<head>`
- Length: 150-160 characters (optimal for Google SERP)
- Compelling and action-oriented
- Includes primary and secondary keywords
- Unique per page

**Validation**:
```bash
grep -n '<meta name="description"' index.html
```

**Check**:
- ✅ Exists
- ✅ Length between 150-160 characters
- ✅ Descriptive (not just keywords)
- ✅ Includes call-to-action or value proposition

**Common issues**:
- Missing meta description (Google generates one from content)
- Too short (<120 chars, underutilizes space)
- Too long (>160 chars, truncated in SERP)
- Keyword stuffing without natural language

---

#### Viewport Meta Tag
**Requirements**:
- Must exist for mobile responsiveness
- Standard value: `width=device-width, initial-scale=1.0`

**Validation**:
```bash
grep -n '<meta name="viewport"' index.html
```

**Check**:
- ✅ Exists
- ✅ Contains `width=device-width`
- ✅ Contains `initial-scale=1.0`

**Common issues**:
- Missing entirely (mobile users see desktop layout zoomed out)
- Incorrect values (user-scalable=no is accessibility issue)

---

#### Robots Meta Tag
**Requirements**:
- Optional but recommended
- Standard value: `index, follow` (allow indexing and link following)
- Use `noindex` only for staging/private pages

**Validation**:
```bash
grep -n '<meta name="robots"' index.html
```

**Check**:
- ✅ If missing, assume default (index, follow)
- ⚠️ If `noindex`, verify intentional (staging environment?)
- ⚠️ If `nofollow`, understand implications (link equity lost)

---

#### Canonical URL
**Requirements**:
- Recommended to prevent duplicate content issues
- Points to the preferred version of the page
- Absolute URL (includes https://)

**Validation**:
```bash
grep -n '<link rel="canonical"' index.html
```

**Check**:
- ✅ Exists (or document why not)
- ✅ Absolute URL (https://example.com/page)
- ✅ Self-referencing on primary page

**Common issues**:
- Missing canonical (Google may choose wrong version)
- Relative URL instead of absolute
- Pointing to different domain (cross-domain canonicalization)

---

### 2. Open Graph Tags (Social Sharing)

**Purpose**: Control how links appear when shared on Facebook, LinkedIn, Slack, etc.

#### Required Open Graph Tags

**og:title**:
```bash
grep -n 'property="og:title"' index.html
```
- Should match or be similar to `<title>`
- Length: 60-90 characters

**og:description**:
```bash
grep -n 'property="og:description"' index.html
```
- Should match or be similar to meta description
- Length: 150-200 characters

**og:image**:
```bash
grep -n 'property="og:image"' index.html
```
- **Must be absolute URL** (https://example.com/image.jpg)
- Recommended size: 1200×630px (Facebook, LinkedIn)
- Format: JPG, PNG
- File size: <1MB

**og:url**:
```bash
grep -n 'property="og:url"' index.html
```
- Canonical URL of the page
- Absolute URL

**og:type**:
```bash
grep -n 'property="og:type"' index.html
```
- Common values: `website`, `article`, `profile`
- Default: `website` for static sites

**Validation checklist**:
- ✅ All 5 tags present (title, description, image, url, type)
- ✅ og:image is absolute URL (not relative)
- ✅ og:image file exists and is optimized
- ✅ og:url matches canonical URL

**Common issues**:
- Relative URLs for og:image (Facebook can't fetch)
- Missing og:image (Facebook uses first image found, often wrong)
- og:description too short or missing

---

### 3. Twitter Card Tags

**Purpose**: Control how links appear on Twitter/X.

#### Required Twitter Card Tags

**twitter:card**:
```bash
grep -n 'name="twitter:card"' index.html
```
- Common values: `summary`, `summary_large_image`
- Recommended: `summary_large_image` (better visual)

**twitter:title**:
```bash
grep -n 'name="twitter:title"' index.html
```
- Should match `<title>` or `og:title`
- Length: 70 characters max

**twitter:description**:
```bash
grep -n 'name="twitter:description"' index.html
```
- Should match meta description or `og:description`
- Length: 200 characters max

**twitter:image**:
```bash
grep -n 'name="twitter:image"' index.html
```
- **Must be absolute URL**
- Recommended size: 1200×628px (summary_large_image)
- Format: JPG, PNG, WebP, GIF

**Optional**:
- `twitter:site` - Twitter handle of website (@yourhandle)
- `twitter:creator` - Twitter handle of content creator

**Validation checklist**:
- ✅ twitter:card specified
- ✅ twitter:title, twitter:description, twitter:image present
- ✅ twitter:image is absolute URL
- ⚠️ If twitter:site exists, includes @ symbol

**Fallback behavior**:
- Twitter will use Open Graph tags if Twitter Card tags are missing
- Best practice: Include both for maximum compatibility

---

### 4. Favicon & Icons

**Favicon** (browser tab icon):
```bash
grep -n '<link rel="icon"' index.html
```
- Standard: `<link rel="icon" href="/favicon.ico" type="image/x-icon">`
- Modern: `<link rel="icon" href="/favicon.png" type="image/png">`
- Sizes: 16×16, 32×32, 48×48 (or multi-size .ico)

**Apple Touch Icon** (iOS home screen):
```bash
grep -n '<link rel="apple-touch-icon"' index.html
```
- Size: 180×180px (minimum)
- Format: PNG
- Example: `<link rel="apple-touch-icon" href="/apple-touch-icon.png">`

**Validation checklist**:
- ✅ Favicon link exists
- ✅ Favicon file exists (check /favicon.ico or specified path)
- ⚠️ Apple touch icon exists (iOS users benefit)
- ⚠️ Sizes attribute for multiple resolutions (optional)

**Common issues**:
- Missing favicon (browser shows generic icon)
- Broken favicon link (404)
- Low-resolution favicon (blurry on high-DPI displays)

---

### 5. Structured Data (JSON-LD)

**Purpose**: Help search engines understand page content (rich snippets, knowledge graph).

**Common schemas** for static sites:
- `Organization` - Company info, logo, social profiles
- `Person` - Personal website, portfolio
- `WebSite` - Site-wide search functionality
- `Article` - Blog posts (if applicable)

**Validation**:
```bash
grep -n 'application/ld+json' index.html
```

**Example - Organization Schema**:
```json
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Company Name",
  "url": "https://example.com",
  "logo": "https://example.com/logo.png",
  "sameAs": [
    "https://twitter.com/company",
    "https://linkedin.com/company/company"
  ]
}
</script>
```

**Validation checklist**:
- ⚠️ JSON-LD schema present (optional but recommended)
- ✅ Valid JSON syntax (if present)
- ✅ @context and @type specified
- ✅ URLs are absolute

**Tools for validation**:
- Google Rich Results Test (use WebFetch to check)
- Schema.org validator

---

## Output Format

```markdown
# SEO Validation Report

**Date**: [current date]
**File Reviewed**: index.html
**Overall Status**: ✅ PASS / ⚠️ ISSUES FOUND / ❌ CRITICAL ISSUES

---

## 1. Essential Meta Tags

### ✅ Title Tag
- **Content**: "Professional Web Design Services | YourCompany"
- **Length**: 52 characters ✅
- **Status**: PASS

### ❌ Meta Description
- **Content**: "We build websites."
- **Length**: 18 characters ❌ (too short, optimal: 150-160)
- **Issue**: Too short, not compelling, missing keywords
- **Recommendation**:
  ```html
  <meta name="description" content="Professional web design and development services for small businesses. Custom websites, responsive design, and SEO optimization. Get a free quote today.">
  ```
- **Status**: FAIL

### ✅ Viewport Meta Tag
- **Content**: `width=device-width, initial-scale=1.0`
- **Status**: PASS

### ⚠️ Canonical URL
- **Status**: Missing
- **Recommendation**:
  ```html
  <link rel="canonical" href="https://yourcompany.com/">
  ```
- **Priority**: Medium

### ✅ Robots Meta Tag
- **Status**: Not specified (defaults to index, follow) ✅

---

## 2. Open Graph Tags

### ❌ Missing Open Graph Tags
**Status**: FAIL - Social sharing will not display optimally

**Missing tags**:
- og:title
- og:description
- og:image
- og:url
- og:type

**Recommendation**: Add complete Open Graph meta tags
```html
<meta property="og:title" content="Professional Web Design Services | YourCompany">
<meta property="og:description" content="Custom websites for small businesses. Responsive design, SEO optimization, and expert development.">
<meta property="og:image" content="https://yourcompany.com/og-image.jpg">
<meta property="og:url" content="https://yourcompany.com/">
<meta property="og:type" content="website">
```

**Priority**: High (affects social sharing appearance)

---

## 3. Twitter Card Tags

### ❌ Missing Twitter Card Tags
**Status**: FAIL - Twitter will fall back to Open Graph (if present)

**Recommendation**: Add Twitter Card meta tags
```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Professional Web Design Services | YourCompany">
<meta name="twitter:description" content="Custom websites for small businesses. Responsive design, SEO optimization, and expert development.">
<meta name="twitter:image" content="https://yourcompany.com/twitter-image.jpg">
```

**Priority**: Medium (Twitter will use OG tags as fallback)

---

## 4. Favicon & Icons

### ✅ Favicon
- **Status**: PASS
- **Location**: `<link rel="icon" href="/favicon.png" type="image/png">`

### ⚠️ Apple Touch Icon
- **Status**: Missing
- **Recommendation**:
  ```html
  <link rel="apple-touch-icon" href="/apple-touch-icon.png">
  ```
- **Priority**: Low (iOS users only)

---

## 5. Structured Data

### ⚠️ JSON-LD Schema
- **Status**: Not present
- **Recommendation**: Add Organization schema
  ```html
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "YourCompany",
    "url": "https://yourcompany.com",
    "logo": "https://yourcompany.com/logo.png"
  }
  </script>
  ```
- **Priority**: Low (enhances search results but not critical)

---

## Summary

**Critical Issues**: 2
- Meta description too short and non-descriptive
- Missing Open Graph tags

**High Priority**: 1
- Add Open Graph tags for social sharing

**Medium Priority**: 2
- Add canonical URL
- Add Twitter Card tags

**Low Priority**: 2
- Add Apple Touch Icon
- Add JSON-LD structured data

**SEO Readiness**: ⚠️ NEEDS IMPROVEMENT

**Next Steps**:
1. Rewrite meta description (150-160 chars, include keywords and CTA)
2. Add complete Open Graph tag set
3. Add Twitter Card tags
4. Add canonical URL
5. Consider adding structured data for enhanced search results

---

## Social Sharing Preview

**Facebook/LinkedIn**: ❌ Will display poorly (missing OG tags)
**Twitter**: ❌ Will display poorly (missing Twitter Card tags)
**Slack/Discord**: ❌ Will display poorly (missing OG tags)

**Recommendation**: Implement all Open Graph and Twitter Card tags, then test with:
- [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [Twitter Card Validator](https://cards-dev.twitter.com/validator)
```

---

## Validation Process

1. **Read index.html** (and other HTML files if multi-page)
2. **Extract all meta tags** from `<head>` section
3. **Check each category** against requirements
4. **Measure character counts** for title and description
5. **Verify absolute URLs** for og:image, twitter:image, canonical
6. **Document findings** with pass/fail status
7. **Provide specific code snippets** for missing/incorrect tags
8. **Generate summary** with prioritized action items

---

## Character Count Guidelines

| Element | Minimum | Optimal | Maximum |
|---------|---------|---------|---------|
| Title | 30 | 50-60 | 70 (truncated after) |
| Meta Description | 120 | 150-160 | 200 (truncated after) |
| og:title | 30 | 60-90 | 100 |
| og:description | 120 | 150-200 | 300 |
| twitter:title | 30 | 50-70 | 70 |
| twitter:description | 120 | 150-200 | 200 |

---

## Best Practices

### Title Tag
✅ **Good**: "Professional Web Design Services | YourCompany"
❌ **Bad**: "Home"
❌ **Bad**: "Welcome to YourCompany's Official Website for Web Design Services" (too long)

### Meta Description
✅ **Good**: "Custom websites for small businesses. Responsive design, SEO optimization, and expert development. Get a free quote today."
❌ **Bad**: "We build websites." (too short)
❌ **Bad**: "web design, websites, development, SEO, responsive" (keyword stuffing)

### Open Graph Image
✅ **Good**: `https://example.com/images/og-image.jpg` (absolute URL)
❌ **Bad**: `/images/og-image.jpg` (relative URL - won't work)
❌ **Bad**: `og-image.jpg` (relative URL - won't work)

---

## Testing Tools

After implementation, validate with:

1. **Google Search Console** - Submit sitemap, monitor indexing
2. **Facebook Sharing Debugger** - Test Open Graph tags
   - URL: https://developers.facebook.com/tools/debug/
3. **Twitter Card Validator** - Test Twitter Card tags
   - URL: https://cards-dev.twitter.com/validator
4. **Google Rich Results Test** - Validate structured data
   - URL: https://search.google.com/test/rich-results
5. **Schema.org Validator** - Validate JSON-LD
   - URL: https://validator.schema.org/

*Note: Use WebFetch to check these tools if needed*

---

## Remember

- **Be specific**: Provide exact code snippets for fixes
- **Measure character counts**: Use string length to validate title/description
- **Absolute URLs only**: For og:image, twitter:image, og:url, canonical
- **Prioritize**: Critical issues (missing meta description, OG tags) before nice-to-haves (structured data)
- **Test after implementation**: Social sharing debuggers reveal issues code inspection can't catch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-enhanced-engineer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
