---
name: structured-data-generator
description: Generate JSON-LD schema markup for GEO optimization. Use when creating structured data for articles, products, FAQs, how-to guides, organizations, and other content types to improve AI search visibility and rich results. Use when this capability is needed.
metadata:
  author: ihmissuti
---

# Structured Data Generator

Generate JSON-LD schema markup to help AI search engines understand and cite your content correctly.

## Why Schema Matters for GEO

Structured data helps AI systems:

- **Parse content accurately** - Understand what the page is about
- **Extract key facts** - Pull specific data points for answers
- **Attribute correctly** - Cite your brand as the source
- **Display rich results** - Enhanced visibility in search

**Key statistics:**

- Schema markup is used in over 75% of high-performing GEO-optimized pages
- Brands using structured data see up to 35% higher extractability and citation rates
- Schema adoption among top-ranking websites is only 30-40%, creating competitive advantage

## Schema Types for GEO

### Article Schema

**Use for:** Blog posts, news articles, guides, tutorials

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Complete Guide to Generative Engine Optimization",
  "description": "Learn how to optimize content for AI search engines like ChatGPT, Perplexity, and Google AI Mode.",
  "image": "https://example.com/images/geo-guide.jpg",
  "author": {
    "@type": "Person",
    "name": "Author Name",
    "url": "https://example.com/authors/author-name",
    "jobTitle": "Content Strategist"
  },
  "publisher": {
    "@type": "Organization",
    "name": "Company Name",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.png",
      "width": 600,
      "height": 60
    }
  },
  "datePublished": "2026-01-15T08:00:00+00:00",
  "dateModified": "2026-01-30T10:30:00+00:00",
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "https://example.com/geo-guide"
  },
  "keywords": ["GEO", "AI Search", "Content Optimization"],
  "articleSection": "Marketing",
  "wordCount": 2500
}
```

**Required properties:**

- `headline` - Article title
- `author` - Author information
- `datePublished` - Original publish date
- `publisher` - Publishing organization

**Recommended properties:**

- `dateModified` - Last update date (critical for AI freshness signals)
- `description` - Article summary
- `image` - Featured image
- `keywords` - Relevant terms

### FAQPage Schema

**Use for:** FAQ sections, Q&A pages, help documentation

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is Generative Engine Optimization (GEO)?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "GEO (Generative Engine Optimization) is the practice of optimizing content to be discovered, understood, and cited by AI search engines like ChatGPT, Perplexity, and Google AI Mode. It builds on traditional SEO by focusing on brand visibility, citation rate, and AI share of voice."
      }
    },
    {
      "@type": "Question",
      "name": "How does GEO differ from traditional SEO?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "While SEO focuses on ranking in search results and driving clicks, GEO focuses on being mentioned and cited inside AI-generated answers. GEO success is measured by brand visibility percentage, citation frequency, and share of voice in AI responses."
      }
    },
    {
      "@type": "Question",
      "name": "How often should content be updated for GEO?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "High-value pages should be refreshed every 60-90 days to maintain freshness signals. Update statistics, examples, and dates. AI engines prioritize recently updated content when generating answers."
      }
    }
  ]
}
```

**Required properties:**

- `mainEntity` - Array of Question objects
- Each Question needs `name` and `acceptedAnswer`

**Best practices:**

- Keep answers concise (40-200 words)
- Include question keywords in the answer
- Make each answer standalone and quotable

### HowTo Schema

**Use for:** Tutorials, step-by-step guides, processes

```json
{
  "@context": "https://schema.org",
  "@type": "HowTo",
  "name": "How to Create an llms.txt File for AI Search",
  "description": "Step-by-step guide to creating an llms.txt file that helps AI crawlers discover and understand your content.",
  "image": "https://example.com/images/llms-txt-guide.jpg",
  "totalTime": "PT15M",
  "estimatedCost": {
    "@type": "MonetaryAmount",
    "currency": "USD",
    "value": "0"
  },
  "supply": [
    {
      "@type": "HowToSupply",
      "name": "Text editor"
    },
    {
      "@type": "HowToSupply",
      "name": "Web hosting access"
    }
  ],
  "tool": [
    {
      "@type": "HowToTool",
      "name": "Code editor (VS Code, Cursor)"
    }
  ],
  "step": [
    {
      "@type": "HowToStep",
      "name": "Audit your content",
      "text": "Identify your most important pages: documentation, pricing, policies, and key product pages.",
      "url": "https://example.com/llms-txt-guide#step1",
      "image": "https://example.com/images/step1.jpg"
    },
    {
      "@type": "HowToStep",
      "name": "Create the file structure",
      "text": "Create a new file named llms.txt at your domain root. Start with your brand name as H1 and a blockquote description.",
      "url": "https://example.com/llms-txt-guide#step2"
    },
    {
      "@type": "HowToStep",
      "name": "Add content sections",
      "text": "Organize links into sections like Docs, Product, Support, and Optional. Include brief descriptions for each link.",
      "url": "https://example.com/llms-txt-guide#step3"
    },
    {
      "@type": "HowToStep",
      "name": "Deploy and test",
      "text": "Upload the file and verify it's accessible at https://yourdomain.com/llms.txt. Test by asking AI assistants about your product.",
      "url": "https://example.com/llms-txt-guide#step4"
    }
  ]
}
```

**Required properties:**

- `name` - Title of the how-to
- `step` - Array of HowToStep objects

### Product Schema

**Use for:** Product pages, SaaS offerings, e-commerce

```json
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Analytics Platform Pro",
  "description": "Business intelligence platform that helps companies track, visualize, and act on their data in real-time.",
  "image": "https://example.com/images/product.jpg",
  "brand": {
    "@type": "Brand",
    "name": "Acme Analytics"
  },
  "offers": {
    "@type": "AggregateOffer",
    "lowPrice": "99",
    "highPrice": "499",
    "priceCurrency": "USD",
    "offerCount": "3",
    "offers": [
      {
        "@type": "Offer",
        "name": "Starter",
        "price": "99",
        "priceCurrency": "USD",
        "priceValidUntil": "2026-12-31",
        "availability": "https://schema.org/InStock"
      },
      {
        "@type": "Offer",
        "name": "Professional",
        "price": "299",
        "priceCurrency": "USD",
        "priceValidUntil": "2026-12-31",
        "availability": "https://schema.org/InStock"
      },
      {
        "@type": "Offer",
        "name": "Enterprise",
        "price": "499",
        "priceCurrency": "USD",
        "priceValidUntil": "2026-12-31",
        "availability": "https://schema.org/InStock"
      }
    ]
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.8",
    "reviewCount": "127"
  }
}
```

### Organization Schema

**Use for:** Company pages, about pages, homepage

```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Acme Analytics",
  "alternateName": "Acme",
  "url": "https://acme.com",
  "logo": "https://acme.com/logo.png",
  "description": "Business intelligence platform for enterprises and growing companies.",
  "foundingDate": "2020",
  "founders": [
    {
      "@type": "Person",
      "name": "Jane Smith"
    }
  ],
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "San Francisco",
    "addressRegion": "CA",
    "addressCountry": "US"
  },
  "contactPoint": {
    "@type": "ContactPoint",
    "contactType": "customer service",
    "url": "https://acme.com/contact",
    "email": "support@acme.com"
  },
  "sameAs": ["https://www.linkedin.com/company/acme", "https://twitter.com/acme", "https://github.com/acme"]
}
```

### Person Schema

**Use for:** Author pages, team member pages, expert profiles

```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Jane Smith",
  "jobTitle": "Head of Content Strategy",
  "url": "https://example.com/team/jane-smith",
  "image": "https://example.com/images/jane-smith.jpg",
  "description": "Jane is a content strategist with 10+ years of experience in SEO and GEO optimization.",
  "worksFor": {
    "@type": "Organization",
    "name": "Acme Analytics"
  },
  "sameAs": ["https://www.linkedin.com/in/janesmith", "https://twitter.com/janesmith"],
  "knowsAbout": ["GEO", "SEO", "Content Strategy", "AI Search"]
}
```

### BreadcrumbList Schema

**Use for:** Navigation paths on any page

```json
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Home",
      "item": "https://example.com"
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "Articles",
      "item": "https://example.com/articles"
    },
    {
      "@type": "ListItem",
      "position": 3,
      "name": "GEO Guide",
      "item": "https://example.com/articles/geo-guide"
    }
  ]
}
```

### Service Schema

**Use for:** Service offerings, consulting, agencies

```json
{
  "@context": "https://schema.org",
  "@type": "Service",
  "name": "GEO Optimization Consulting",
  "description": "Expert consulting services to improve your brand's visibility in AI search engines.",
  "provider": {
    "@type": "Organization",
    "name": "Acme Consulting"
  },
  "serviceType": "Marketing Consulting",
  "areaServed": {
    "@type": "Country",
    "name": "United States"
  },
  "offers": {
    "@type": "Offer",
    "price": "5000",
    "priceCurrency": "USD",
    "priceSpecification": {
      "@type": "UnitPriceSpecification",
      "price": "5000",
      "priceCurrency": "USD",
      "unitText": "engagement"
    }
  }
}
```

## Implementation Guide

### HTML Placement

Place JSON-LD in the `<head>` section:

```html
<head>
  <title>Page Title</title>
  <meta name="description" content="..." />

  <script type="application/ld+json">
    {
      "@context": "https://schema.org",
      "@type": "Article",
      "headline": "Article Title",
      ...
    }
  </script>
</head>
```

### Multiple Schema Types

You can include multiple schema types on one page using `@graph`:

```json
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "Organization",
      "name": "Company Name",
      "@id": "https://example.com/#organization"
    },
    {
      "@type": "WebPage",
      "name": "Page Title",
      "isPartOf": {
        "@id": "https://example.com/#organization"
      }
    },
    {
      "@type": "Article",
      "headline": "Article Title",
      "author": {
        "@type": "Person",
        "name": "Author Name"
      }
    }
  ]
}
```

## Quick Reference Table

| Content Type | Schema         | Key Properties                                |
| ------------ | -------------- | --------------------------------------------- |
| Blog/Article | Article        | headline, author, datePublished, dateModified |
| FAQ          | FAQPage        | mainEntity (Questions with Answers)           |
| Tutorial     | HowTo          | name, step (HowToSteps)                       |
| Product      | Product        | name, offers, brand                           |
| Company      | Organization   | name, url, logo, description                  |
| Navigation   | BreadcrumbList | itemListElement (ListItems)                   |
| Person       | Person         | name, jobTitle, url                           |
| Service      | Service        | name, provider, description                   |

## Validation Checklist

Before deploying schema markup:

- [ ] JSON syntax is valid (no trailing commas, proper quotes)
- [ ] All required properties are present
- [ ] URLs are absolute (include https://)
- [ ] Dates use ISO 8601 format (2026-01-15T08:00:00+00:00)
- [ ] Text values are properly escaped
- [ ] Images are valid URLs and accessible
- [ ] Schema type matches content type
- [ ] No duplicate conflicting schema on same page
- [ ] dateModified reflects actual last update

## Common Mistakes to Avoid

1. **HTML in text fields** - Use plain text only in Answer text
2. **Relative URLs** - Always use absolute URLs
3. **Mismatched content** - Schema text must match visible content
4. **Missing dateModified** - Critical for AI freshness signals
5. **Invalid JSON** - Test syntax before deployment
6. **Wrong schema type** - Match schema to actual content type

## Testing Tools

1. **Google Rich Results Test** - https://search.google.com/test/rich-results
2. **Schema.org Validator** - https://validator.schema.org/
3. **JSON-LD Playground** - https://json-ld.org/playground/

## Next.js Implementation Example

```tsx
// components/ArticleSchema.tsx
export function ArticleSchema({ article }) {
  const schema = {
    '@context': 'https://schema.org',
    '@type': 'Article',
    headline: article.title,
    description: article.description,
    author: {
      '@type': 'Person',
      name: article.author.name,
      url: article.author.url,
    },
    datePublished: article.publishedAt,
    dateModified: article.updatedAt,
    publisher: {
      '@type': 'Organization',
      name: 'Your Company',
      logo: {
        '@type': 'ImageObject',
        url: 'https://yoursite.com/logo.png',
      },
    },
  };

  return <script type="application/ld+json" dangerouslySetInnerHTML={{ __html: JSON.stringify(schema) }} />;
}
```

Usage in page:

```tsx
export default function ArticlePage({ article }) {
  return (
    <>
      <Head>
        <title>{article.title}</title>
        <ArticleSchema article={article} />
      </Head>
      <article>{/* Article content */}</article>
    </>
  );
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihmissuti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
