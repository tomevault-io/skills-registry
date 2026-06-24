---
name: iblai-marketing-schema-markup
description: When the user wants to add, fix, or optimize schema markup and structured data on their site. Also use when the user mentions "schema markup," "structured data," "JSON-LD," "rich snippets," "schema.org," "FAQ schema," "product schema," "review schema," "breadcrumb schema," "Google rich results," "knowledge panel," "star ratings in search," or "add structured data." Use this whenever someone wants their pages to show enhanced results in Google. For broader SEO issues, see iblai-marketing-seo-audit. For AI search optimization, see iblai-marketing-ai-seo. Use when this capability is needed.
metadata:
  author: iblai
---

# /iblai-marketing-schema-markup

Implement schema.org markup that helps search engines understand content
and unlocks rich results.

## Step 0: Context Check

Read `.agents/product-marketing-context.md` (or `.claude/product-marketing-context.md`
on older setups) first. Only ask for what isn't there.

Pin down before implementing:

1. **Page type** — what kind of page? Primary content? Possible rich results?
2. **Current state** — any existing schema? Errors? Which rich results already appear?
3. **Goals** — which rich results are you targeting? What's the business value?

---

## Core Principles

1. **Accuracy first.** Schema must accurately represent page content. Don't markup content that doesn't exist. Keep updated when content changes.
2. **Use JSON-LD.** Google recommends JSON-LD. Easier to implement and maintain. Place in `<head>` or end of `<body>`.
3. **Follow Google's guidelines.** Only use markup Google supports. Avoid spam tactics. Review eligibility requirements.
4. **Validate everything.** Test before deploying. Monitor Search Console. Fix errors promptly.

---

## Common Schema Types

| Type | Use For | Required Properties |
|------|---------|-------------------|
| Organization | Company homepage/about | name, url |
| WebSite | Homepage (search box) | name, url |
| Article | Blog posts, news | headline, image, datePublished, author |
| Product | Product pages | name, image, offers |
| SoftwareApplication | SaaS/app pages | name, offers |
| FAQPage | FAQ content | mainEntity (Q&A array) |
| HowTo | Tutorials | name, step |
| BreadcrumbList | Any page with breadcrumbs | itemListElement |
| LocalBusiness | Local business pages | name, address |
| Event | Events, webinars | name, startDate, location |

Complete JSON-LD examples: [references/schema-examples.md](references/schema-examples.md).

---

## Quick Reference

### Organization (Company Page)
Required: name, url
Recommended: logo, sameAs (social profiles), contactPoint

### Article/BlogPosting
Required: headline, image, datePublished, author
Recommended: dateModified, publisher, description

### Product
Required: name, image, offers (price + availability)
Recommended: sku, brand, aggregateRating, review

### FAQPage
Required: mainEntity (array of Question/Answer pairs)

### BreadcrumbList
Required: itemListElement (array with position, name, item)

---

## Multiple Schema Types

Combine multiple types on one page using `@graph`:

```json
{
  "@context": "https://schema.org",
  "@graph": [
    { "@type": "Organization", ... },
    { "@type": "WebSite", ... },
    { "@type": "BreadcrumbList", ... }
  ]
}
```

---

## Validation and Testing

### Tools
- **Google Rich Results Test**: https://search.google.com/test/rich-results
- **Schema.org Validator**: https://validator.schema.org/
- **Search Console**: Enhancements reports

### Common Errors

**Missing required properties** — check Google's docs for required fields.

**Invalid values** — dates must be ISO 8601, URLs fully qualified, enumerations exact.

**Mismatch with page content** — schema doesn't match visible content.

---

## Implementation

### Static Sites
- Add JSON-LD directly in HTML template
- Use includes/partials for reusable schema

### Dynamic Sites (React, Next.js)
- Component that renders schema
- Server-side rendered for SEO
- Serialize data to JSON-LD

### CMS / WordPress
- Plugins (Yoast, Rank Math, Schema Pro)
- Theme modifications
- Custom fields → structured data

---

## Output Format

### Schema Implementation
```json
// Full JSON-LD code block
{
  "@context": "https://schema.org",
  "@type": "...",
  // Complete markup
}
```

### Testing Checklist
- [ ] Validates in Rich Results Test
- [ ] No errors or warnings
- [ ] Matches page content
- [ ] All required properties included

---

## Task-Specific Questions

1. What type of page is this?
2. Which rich results are you targeting?
3. What data is available to populate the schema?
4. Existing schema on the page?
5. Tech stack?

---

## Related Skills

- **iblai-marketing-seo-audit**: Overall SEO including schema review
- **iblai-marketing-ai-seo**: AI search optimization (schema helps AI understand content)
- **iblai-marketing-programmatic-seo**: Templated schema at scale
- **iblai-marketing-site-architecture**: Breadcrumb structure and navigation schema planning

---
> Source: [iblai/vibe-marketing](https://github.com/iblai/vibe-marketing) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
