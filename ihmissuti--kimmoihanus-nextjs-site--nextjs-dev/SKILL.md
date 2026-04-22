---
name: nextjs-dev
description: Next.js development conventions and patterns for kimmoihanus.com. Use when working on pages, components, or API routes in this project. Use when this capability is needed.
metadata:
  author: ihmissuti
---

# Next.js Development

Development conventions and patterns for the kimmoihanus.com Next.js site.

## When to Use

- Creating new pages or components
- Working with API routes
- Styling with Tailwind CSS
- Working with MDX content

## Project Structure

```
/
├── components/     # React components
├── css/           # Stylesheets (Tailwind, Prism)
├── graphics/      # Graphics markdown files
├── lib/           # Utility functions and hooks
├── pages/         # Next.js pages (file-based routing)
│   ├── api/       # API routes
│   ├── graphics/  # Dynamic graphics pages
│   └── posts/     # Dynamic post pages
├── posts/         # Blog post markdown files
├── prose/         # Static prose content
├── public/        # Static assets
└── tools/         # Additional tools and MCP servers
```

## Conventions

### Pages

- Pages use file-based routing in `/pages/`
- Dynamic routes use `[slug].js` format
- API routes go in `/pages/api/`

### Components

- Components are in `/components/`
- Use functional components with hooks
- Export default the main component

### Styling

- Use Tailwind CSS classes
- Custom styles in `/css/` directory
- Module CSS for component-specific styles (e.g., `graphics.module.css`)

### Content

- Blog posts: `/posts/XXX-slug.md`
- Graphics: `/graphics/slug.md`
- Static prose: `/prose/slug.md`

## Key Libraries

- `next` - Framework
- `next-seo` - SEO configuration
- `next-mdx-remote` - MDX rendering
- `tailwindcss` - Styling
- `gray-matter` - Frontmatter parsing

## Development Commands

```bash
# Start development server
npm run dev

# Build for production
npm run build

# Start production server
npm run start

# Generate sitemap
npm run postbuild
```

## Creating New Pages

1. Add file to `/pages/` directory
2. Export a default React component
3. Optionally export `getStaticProps` or `getServerSideProps`

Example:

```jsx
import Layout from '../components/Layout';
import { NextSeo } from 'next-seo';

export default function MyPage() {
  return (
    <Layout>
      <NextSeo title="My Page" />
      <h1>My New Page</h1>
    </Layout>
  );
}
```

## Creating API Routes

Add file to `/pages/api/`:

```js
export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  // Your logic here
  res.status(200).json({ success: true });
}
```

## Working with MDX Content

Use the lib functions:

```js
import { getPostBySlug, getAllPosts } from '../lib/posts';
import { serialize } from 'next-mdx-remote/serialize';
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihmissuti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
