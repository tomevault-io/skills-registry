---
name: seo-technical
description: Implement technical SEO including schema markup, sitemaps, robots.txt, Core Web Vitals optimization, and metadata. Use when setting up SEO foundations or optimizing website performance for search engines. Use when this capability is needed.
metadata:
  author: deomiarn
---

# Technical SEO

This skill covers the technical aspects of SEO implementation for Next.js websites.

## When to Use This Skill

- Setting up metadata and Open Graph tags
- Implementing schema markup (JSON-LD)
- Creating sitemaps and robots.txt
- Optimizing Core Web Vitals
- Implementing canonical URLs

## Core Implementation

### 1. Next.js Metadata API

**Static Metadata (layout.tsx or page.tsx):**
```tsx
import type { Metadata } from "next"

export const metadata: Metadata = {
  title: {
    default: "Site Name",
    template: "%s | Site Name"
  },
  description: "Site description for search results",
  keywords: ["keyword1", "keyword2"],
  authors: [{ name: "Author Name" }],
  creator: "Creator Name",
  metadataBase: new URL("https://example.com"),
  alternates: {
    canonical: "/",
  },
  openGraph: {
    type: "website",
    locale: "en_US",
    url: "https://example.com",
    siteName: "Site Name",
    title: "Site Name",
    description: "Site description",
    images: [
      {
        url: "/og-image.jpg",
        width: 1200,
        height: 630,
        alt: "Site preview image"
      }
    ]
  },
  twitter: {
    card: "summary_large_image",
    title: "Site Name",
    description: "Site description",
    images: ["/og-image.jpg"],
    creator: "@twitterhandle"
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      "max-video-preview": -1,
      "max-image-preview": "large",
      "max-snippet": -1
    }
  },
  verification: {
    google: "google-verification-code",
    yandex: "yandex-verification-code"
  }
}
```

**Dynamic Metadata (for pages with dynamic content):**
```tsx
import type { Metadata } from "next"

type Props = {
  params: { slug: string }
}

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const post = await getPost(params.slug)

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      type: "article",
      publishedTime: post.publishedAt,
      authors: [post.author.name],
      images: [
        {
          url: post.image,
          width: 1200,
          height: 630,
          alt: post.title
        }
      ]
    }
  }
}
```

### 2. Schema Markup (JSON-LD)

**Organization Schema (layout.tsx):**
```tsx
export default function RootLayout({ children }) {
  const organizationSchema = {
    "@context": "https://schema.org",
    "@type": "Organization",
    name: "Company Name",
    url: "https://example.com",
    logo: "https://example.com/logo.png",
    sameAs: [
      "https://twitter.com/company",
      "https://linkedin.com/company/company",
      "https://facebook.com/company"
    ],
    contactPoint: {
      "@type": "ContactPoint",
      telephone: "+1-800-555-1234",
      contactType: "customer service"
    }
  }

  return (
    <html>
      <head>
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{ __html: JSON.stringify(organizationSchema) }}
        />
      </head>
      <body>{children}</body>
    </html>
  )
}
```

**Website Schema with Search:**
```tsx
const websiteSchema = {
  "@context": "https://schema.org",
  "@type": "WebSite",
  name: "Site Name",
  url: "https://example.com",
  potentialAction: {
    "@type": "SearchAction",
    target: {
      "@type": "EntryPoint",
      urlTemplate: "https://example.com/search?q={search_term_string}"
    },
    "query-input": "required name=search_term_string"
  }
}
```

**Article Schema (blog posts):**
```tsx
const articleSchema = {
  "@context": "https://schema.org",
  "@type": "Article",
  headline: post.title,
  description: post.excerpt,
  image: post.image,
  datePublished: post.publishedAt,
  dateModified: post.updatedAt,
  author: {
    "@type": "Person",
    name: post.author.name,
    url: post.author.url
  },
  publisher: {
    "@type": "Organization",
    name: "Site Name",
    logo: {
      "@type": "ImageObject",
      url: "https://example.com/logo.png"
    }
  },
  mainEntityOfPage: {
    "@type": "WebPage",
    "@id": `https://example.com/blog/${post.slug}`
  }
}
```

**Product Schema:**
```tsx
const productSchema = {
  "@context": "https://schema.org",
  "@type": "Product",
  name: product.name,
  image: product.images,
  description: product.description,
  sku: product.sku,
  brand: {
    "@type": "Brand",
    name: product.brand
  },
  offers: {
    "@type": "Offer",
    url: `https://example.com/products/${product.slug}`,
    priceCurrency: "USD",
    price: product.price,
    availability: product.inStock
      ? "https://schema.org/InStock"
      : "https://schema.org/OutOfStock",
    seller: {
      "@type": "Organization",
      name: "Store Name"
    }
  },
  aggregateRating: product.reviews?.length ? {
    "@type": "AggregateRating",
    ratingValue: product.averageRating,
    reviewCount: product.reviews.length
  } : undefined
}
```

**FAQ Schema:**
```tsx
const faqSchema = {
  "@context": "https://schema.org",
  "@type": "FAQPage",
  mainEntity: faqs.map((faq) => ({
    "@type": "Question",
    name: faq.question,
    acceptedAnswer: {
      "@type": "Answer",
      text: faq.answer
    }
  }))
}
```

**Breadcrumb Schema:**
```tsx
const breadcrumbSchema = {
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  itemListElement: [
    {
      "@type": "ListItem",
      position: 1,
      name: "Home",
      item: "https://example.com"
    },
    {
      "@type": "ListItem",
      position: 2,
      name: "Blog",
      item: "https://example.com/blog"
    },
    {
      "@type": "ListItem",
      position: 3,
      name: post.title,
      item: `https://example.com/blog/${post.slug}`
    }
  ]
}
```

**Local Business Schema:**
```tsx
const localBusinessSchema = {
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  name: "Business Name",
  image: "https://example.com/image.jpg",
  "@id": "https://example.com",
  url: "https://example.com",
  telephone: "+1-800-555-1234",
  address: {
    "@type": "PostalAddress",
    streetAddress: "123 Main St",
    addressLocality: "City",
    addressRegion: "State",
    postalCode: "12345",
    addressCountry: "US"
  },
  geo: {
    "@type": "GeoCoordinates",
    latitude: 40.7128,
    longitude: -74.0060
  },
  openingHoursSpecification: [
    {
      "@type": "OpeningHoursSpecification",
      dayOfWeek: ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"],
      opens: "09:00",
      closes: "17:00"
    }
  ]
}
```

### 3. Sitemap

**app/sitemap.ts:**
```tsx
import { MetadataRoute } from "next"

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const baseUrl = "https://example.com"

  // Static pages
  const staticPages = [
    "",
    "/about",
    "/services",
    "/contact",
  ].map((route) => ({
    url: `${baseUrl}${route}`,
    lastModified: new Date(),
    changeFrequency: "monthly" as const,
    priority: route === "" ? 1 : 0.8,
  }))

  // Dynamic pages (e.g., blog posts)
  const posts = await getAllPosts()
  const blogPages = posts.map((post) => ({
    url: `${baseUrl}/blog/${post.slug}`,
    lastModified: new Date(post.updatedAt),
    changeFrequency: "weekly" as const,
    priority: 0.6,
  }))

  return [...staticPages, ...blogPages]
}
```

### 4. Robots.txt

**app/robots.ts:**
```tsx
import { MetadataRoute } from "next"

export default function robots(): MetadataRoute.Robots {
  const baseUrl = "https://example.com"

  return {
    rules: [
      {
        userAgent: "*",
        allow: "/",
        disallow: [
          "/api/",
          "/admin/",
          "/private/",
          "/_next/",
        ],
      },
    ],
    sitemap: `${baseUrl}/sitemap.xml`,
  }
}
```

### 5. Core Web Vitals Optimization

**LCP (Largest Contentful Paint) - Target: < 2.5s**
```tsx
// Preload critical images
<link
  rel="preload"
  href="/hero-image.jpg"
  as="image"
  fetchPriority="high"
/>

// Priority loading for hero images
<Image
  src="/hero.jpg"
  alt="Hero"
  priority
  fetchPriority="high"
/>

// Preconnect to external origins
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossOrigin="anonymous" />
```

**CLS (Cumulative Layout Shift) - Target: < 0.1**
```tsx
// Always set dimensions on images
<Image
  src="/image.jpg"
  alt="Description"
  width={800}
  height={600}
/>

// Or use aspect ratio
<div className="aspect-video relative">
  <Image src="/image.jpg" alt="Description" fill />
</div>

// Reserve space for dynamic content
<div className="min-h-[400px]">
  {/* Dynamic content */}
</div>
```

**FID/INP (Input Delay) - Target: < 100ms**
```tsx
// Defer non-critical JavaScript
<Script src="/analytics.js" strategy="lazyOnload" />

// Use dynamic imports for heavy components
const HeavyComponent = dynamic(() => import("./HeavyComponent"), {
  loading: () => <Skeleton />,
  ssr: false
})
```

### 6. Image Optimization

```tsx
import Image from "next/image"

// Responsive image with proper sizes
<Image
  src="/image.jpg"
  alt="Descriptive alt text for SEO"
  width={1200}
  height={630}
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,..."
/>

// next.config.js - enable WebP/AVIF
module.exports = {
  images: {
    formats: ["image/avif", "image/webp"],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
}
```

### 7. Canonical URLs

```tsx
// In metadata
export const metadata: Metadata = {
  alternates: {
    canonical: "https://example.com/page",
  },
}

// For paginated content
export async function generateMetadata({ searchParams }) {
  const page = searchParams.page || 1
  return {
    alternates: {
      canonical: "https://example.com/blog", // Always point to page 1
    },
  }
}
```

### 8. Internal Linking

```tsx
// Use Next.js Link for internal navigation
import Link from "next/link"

<Link href="/about">About Us</Link>

// Descriptive anchor text (good for SEO)
<Link href="/services/web-design">
  Professional Web Design Services
</Link>

// Avoid generic text
// Bad: <Link href="/services">Click here</Link>
// Good: <Link href="/services">Explore our services</Link>
```

## SEO Audit Checklist

- [ ] Every page has unique title and description
- [ ] All images have alt text
- [ ] Schema markup validates (use Google's Rich Results Test)
- [ ] Sitemap includes all important pages
- [ ] Robots.txt doesn't block important content
- [ ] Canonical URLs prevent duplicate content
- [ ] Core Web Vitals pass (use PageSpeed Insights)
- [ ] Mobile-friendly (use Mobile-Friendly Test)
- [ ] No broken links (use Screaming Frog or similar)
- [ ] HTTPS enabled
- [ ] No mixed content warnings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
