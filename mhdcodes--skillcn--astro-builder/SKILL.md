---
name: astro-builder
description: Comprehensive skill for building websites with Astro.build. Use when the user wants to create a website using Astro, needs to select an appropriate theme from Astro's free themes collection, requires access to Astro documentation, or wants to develop Astro components and pages. This skill handles theme selection, documentation access, project setup, and Astro-specific development patterns including islands architecture, content collections, and view transitions. Use when this capability is needed.
metadata:
  author: mhdcodes
---

# Astro Website Builder

This skill helps you build websites with Astro.build by guiding you through theme selection, documentation access, and development best practices. Use this skill whenever a user mentions building with Astro, wants to create a website, or needs help with Astro-specific features.

## When to Use This Skill

Activate this skill when:

- User mentions "Astro" or "astro.build" in their request
- User wants to build a website and Astro would be appropriate (blogs, documentation, portfolios, marketing sites, content-driven sites)
- User needs to select a theme for their Astro project
- User needs help with Astro components, routing, islands architecture, or configuration
- User wants to integrate React, Vue, Svelte, Solid, or other frameworks with Astro
- User needs access to Astro's documentation or best practices
- User wants to implement content collections, view transitions, or server islands

## Core Astro Concepts

### Islands Architecture

Astro pioneered the **islands architecture** pattern:

- Pages render as static HTML by default (zero JavaScript)
- Interactive components become "islands" of interactivity
- Use `client:*` directives to hydrate specific components
- Available directives: `client:load`, `client:idle`, `client:visible`, `client:media`
- Multiple framework islands can coexist on the same page

### Server-First Approach

- Astro is a **Multi-Page App (MPA)** framework, not an SPA
- Rendering happens on the server (build-time or on-demand)
- Results in 40% faster load times with 90% less JavaScript vs typical SPAs
- Client-side interactivity is opt-in, not mandatory

### Server Islands (New Feature)

- Use `server:defer` directive on Astro components for deferred server rendering
- Allows personalized/dynamic content without blocking page load
- Combines static HTML with dynamic server-generated components
- Perfect for user avatars, personalized content, or slow API calls

## Workflow Overview

When building an Astro website, follow this process:

1. **Understand Requirements** - Determine the type of website and features needed
2. **Select Appropriate Theme** - Choose from Astro's free themes based on requirements
3. **Access Documentation** - Reference Astro docs for specific features
4. **Initialize Project** - Set up the project structure
5. **Develop Components** - Build using Astro's component architecture
6. **Add Interactivity** - Implement islands for client-side functionality
7. **Optimize & Deploy** - Follow best practices for performance

## Step 1: Understand Requirements

First, identify the type of website and its requirements:

**Website Types Best Suited for Astro:**

- Blog/Publication site (excellent fit)
- Portfolio/Showcase (excellent fit)
- Documentation site (use Starlight)
- Marketing/Landing page (excellent fit)
- E-commerce site (good fit)
- Company/Business site (excellent fit)
- Personal website (excellent fit)
- Community sites
- Any content-driven website

**Key Questions to Ask:**

- What is the primary purpose of the site?
- What content types will it have? (blog posts, projects, products, etc.)
- Does it need interactivity? (forms, search, dynamic content)
- What frameworks does the user prefer? (React, Vue, Svelte, Solid, Preact, etc.)
- Does it need specific features? (dark mode, i18n, CMS integration, view transitions)
- Will content be managed via content collections or a headless CMS?
- Does it need on-demand rendering or is static generation sufficient?

## Step 2: Select Appropriate Theme

Use web_fetch to browse free themes with appropriate filters:

**Base URL for themes:**

```
https://astro.build/themes/1/
```

**Available filters:**

- Technology: `?technology[]=react`, `technology[]=vue`, `technology[]=svelte`, `technology[]=tailwind`
- Price: `?price[]=free`, `price[]=premium`
- Features: Content collections, dark mode, i18n, etc.

**Example fetches:**

```
# Free React-compatible themes
https://astro.build/themes/1/?search=&technology%5B%5D=react&price%5B%5D=free

# Free Tailwind themes
https://astro.build/themes/1/?search=&technology%5B%5D=tailwind&price%5B%5D=free

# All free themes
https://astro.build/themes/1/?price%5B%5D=free
```

**Theme Selection Criteria:**

For **Blogs/Publications**:

- Look for content collections support
- Strong typography and reading experience
- Must support Markdown/MDX
- Should have tags/categories
- RSS feed support
- Consider: astro-paper, astro-blog-template

For **Portfolios**:

- Visual project showcase capabilities
- About/Contact sections
- Smooth animations and transitions
- Image optimization
- Consider themes with view transitions

For **Documentation**:

- **Strongly recommend Starlight** (official Astro docs framework)
- Features: Built-in search, sidebar navigation, dark mode, i18n
- Best for: Technical docs, API references, guides
- Installation: `npm create astro@latest -- --template starlight`

For **Landing Pages**:

- Hero section with CTA
- Features/Benefits sections
- Contact forms
- Conversion-optimized design
- Fast loading for better SEO

For **E-commerce**:

- Product listings and filters
- Shopping cart functionality
- Payment integration ready
- Product detail pages
- Content collections for products

**Action:** Fetch the themes page, analyze available themes, and recommend 2-3 options that best match the user's requirements. Explain why each theme is suitable and highlight key features.

## Step 3: Access Astro Documentation

**Documentation Hierarchy:**

1. **Built-in Reference (First Priority)**: `references/astro_docs.md`
   - Contains core Astro concepts, syntax, and common patterns
   - READ THIS FIRST for most questions

2. **Full LLM-Optimized Docs**: `https://docs.astro.build/llms-full.txt`
   - Comprehensive, LLM-friendly format
   - Use for advanced features and detailed reference

3. **Web Documentation**: `https://docs.astro.build`
   - Official docs site
   - Use for latest updates or visual guides

**When to Reference Documentation:**

- Component syntax questions → Reference astro_docs.md
- Routing and pages → Reference astro_docs.md
- Styling options → Reference astro_docs.md
- Islands architecture → Fetch LLM docs or reference built-in docs
- Content collections → Fetch LLM docs
- View transitions → Fetch LLM docs
- Server islands → Fetch LLM docs
- Integration setup → Fetch LLM docs
- Advanced configuration → Fetch LLM docs
- API details → Fetch LLM docs

## Step 4: Initialize Astro Project

### Using Create Astro CLI (Recommended)

**Standard new project:**

```bash
npm create astro@latest
```

**With specific template:**

```bash
npm create astro@latest -- --template <theme-github-username>/<theme-repo>
```

**With Starlight (for documentation):**

```bash
npm create astro@latest -- --template starlight
```

**With integrations pre-installed:**

```bash
npm create astro@latest -- --add react --add partytown
```

**Supported integrations for `--add` flag:**

- Framework integrations: `react`, `preact`, `vue`, `svelte`, `solid`
- Other integrations: `tailwind`, `partytown`, `mdx`, `sitemap`

### Prerequisites

**Node.js version requirements:**

- `v18.20.8` or higher
- `v20.3.0` or higher
- `v22.0.0` or higher
- Note: `v19` and `v21` are NOT supported

**Recommended tools:**

- VS Code with [Official Astro extension](https://marketplace.visualstudio.com/items?itemName=astro-build.astro-vscode)
- Terminal for CLI access

### Project Structure Setup

After initialization, ensure proper structure:

```
project/
├── src/
│   ├── components/          # Reusable components
│   ├── layouts/             # Page layouts
│   ├── pages/               # Routes (REQUIRED - file-based routing)
│   │   ├── index.astro      # Homepage (/)
│   │   ├── about.astro      # About page (/about)
│   │   ├── blog/
│   │   │   └── [...slug].astro  # Dynamic blog routes
│   │   ├── 404.astro        # Custom 404 page
│   │   └── 500.astro        # Custom 500 page (on-demand rendering only)
│   ├── content/             # Content collections (recommended for blogs)
│   │   └── config.ts        # Content collections config
│   ├── assets/              # Optimized images and assets
│   └── styles/              # Global styles (optional)
├── public/                  # Static assets (copied as-is)
│   ├── favicon.svg
│   └── robots.txt
├── astro.config.mjs         # Astro configuration
├── package.json             # Dependencies and scripts
└── tsconfig.json            # TypeScript config
```

**Critical Notes:**

- The `src/pages/` directory is **REQUIRED** for routing to work
- Only `src/pages/` is a reserved directory - others are conventional but optional
- Files in `public/` are served as-is (not processed or optimized)
- Files in `src/` are processed, optimized, and bundled

## Step 5: Develop Components and Pages

### Astro Component Structure

Every Astro component has two parts:

1. **Component Script** (Frontmatter) - between `---` fences
2. **Component Template** - HTML with Astro syntax

**Basic component example:**

```astro
---
// Component Script - runs at build time (server-side)
// Import other components
import Button from './Button.astro';

// Define props with TypeScript
interface Props {
  title: string;
  description?: string;
}
const { title, description = "Default description" } = Astro.props;

// Fetch data (this is safe - runs server-side only)
const data = await fetch('API_URL').then(r => r.json());
---

<!-- Component Template - HTML output -->
<article>
  <h2>{title}</h2>
  {description && <p>{description}</p>}
  <Button />
</article>

<style>
  /* Scoped styles - only apply to this component */
  article {
    padding: 1rem;
    border: 1px solid #ccc;
  }
</style>
```

### Component Development Best Practices

1. **Props with TypeScript:**

```astro
---
interface Props {
  title: string;
  count?: number;
  items: string[];
}
const { title, count = 0, items } = Astro.props;
---
```

2. **Default values:**

```astro
---
const {
  greeting = "Hello",
  name = "Astronaut"
} = Astro.props;
---
```

3. **Component imports:**

```astro
---
import Layout from '../layouts/Layout.astro';
import Card from '../components/Card.astro';
import { Icon } from 'astro-icon';
---
```

### Slots for Component Composition

**Default slot:**

```astro
<!-- Wrapper.astro -->
<div class="wrapper">
  <slot /> <!-- children go here -->
</div>
```

**Named slots:**

```astro
<!-- Layout.astro -->
<div>
  <header>
    <slot name="header" /> <!-- for header content -->
  </header>
  <main>
    <slot /> <!-- default slot -->
  </main>
  <footer>
    <slot name="footer" /> <!-- for footer content -->
  </footer>
</div>
```

**Using named slots:**

```astro
<Layout>
  <h1 slot="header">My Site</h1>
  <p>Main content here</p>
  <p slot="footer">© 2026</p>
</Layout>
```

**Slot fallback content:**

```astro
<slot>
  <p>This shows if no content is provided</p>
</slot>
```

### Page Development Pattern

**Simple page:**

```astro
---
import Layout from '../layouts/Layout.astro';
const title = "My Page";
---
<Layout title={title}>
  <h1>{title}</h1>
  <p>Content here</p>
</Layout>
```

**Page with data fetching:**

```astro
---
import Layout from '../layouts/Layout.astro';

const response = await fetch('https://api.example.com/data');
const data = await response.json();
---
<Layout title="Data Page">
  <ul>
    {data.map(item => (
      <li>{item.name}</li>
    ))}
  </ul>
</Layout>
```

**Dynamic route with getStaticPaths:**

```astro
---
// src/pages/blog/[slug].astro
export async function getStaticPaths() {
  const posts = await fetchBlogPosts();
  return posts.map(post => ({
    params: { slug: post.slug },
    props: { post }
  }));
}

const { post } = Astro.props;
---
<h1>{post.title}</h1>
<p>{post.content}</p>
```

## Step 6: Adding Interactivity with Islands

### The Islands Pattern

By default, **ALL components render to static HTML with zero JavaScript**. To add interactivity:

1. **Install framework integration:**

```bash
npx astro add react
# or
npx astro add vue
# or
npx astro add svelte
# or
npx astro add solid
# or
npx astro add preact
```

2. **Create framework component:**

```jsx
// src/components/Counter.jsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

3. **Use with client directive:**

```astro
---
import Counter from '../components/Counter.jsx';
---

<!-- Static - no JavaScript sent -->
<Counter />

<!-- Interactive - hydrates immediately -->
<Counter client:load />

<!-- Interactive - hydrates when idle -->
<Counter client:idle />

<!-- Interactive - hydrates when visible -->
<Counter client:visible />

<!-- Interactive - hydrates based on media query -->
<Counter client:media="(max-width: 768px)" />

<!-- Only JavaScript, no server rendering -->
<Counter client:only="react" />
```

### Client Directives Explained

- **`client:load`** - Hydrate immediately on page load (high priority)
- **`client:idle`** - Hydrate when browser is idle (recommended for most)
- **`client:visible`** - Hydrate when component enters viewport (best for below-fold)
- **`client:media="(query)"`** - Hydrate when media query matches
- **`client:only="framework"`** - Skip server rendering, client-side only

**Performance tip:** Use `client:idle` or `client:visible` by default. Only use `client:load` for critical interactive elements.

### Server Islands (New Feature)

For dynamic server-rendered content that can load after the main page:

```astro
---
import UserAvatar from '../components/UserAvatar.astro';
import RecentPosts from '../components/RecentPosts.astro';
---

<header>
  <!-- Deferred server rendering - won't block page load -->
  <UserAvatar server:defer>
    <!-- Fallback content shown while loading -->
    <div slot="fallback" class="skeleton-avatar"></div>
  </UserAvatar>
</header>

<main>
  <h1>Welcome!</h1>
  <!-- Immediate content loads first -->
</main>

<aside>
  <!-- Another server island with fallback -->
  <RecentPosts server:defer>
    <div slot="fallback">Loading posts...</div>
  </RecentPosts>
</aside>
```

## Step 7: Content Collections (Recommended for Blogs)

Content collections provide type-safe content management:

**1. Define schema in `src/content/config.ts`:**

```typescript
import { defineCollection, z } from "astro:content";

const blog = defineCollection({
  type: "content",
  schema: z.object({
    title: z.string(),
    description: z.string(),
    publishDate: z.date(),
    tags: z.array(z.string()),
    image: z.string().optional(),
  }),
});

export const collections = { blog };
```

**2. Create content in `src/content/blog/`:**

```markdown
---
title: "My First Post"
description: "This is my first blog post"
publishDate: 2026-02-06
tags: ["astro", "blogging"]
---

Content here in Markdown!
```

**3. Query in pages:**

```astro
---
import { getCollection } from 'astro:content';

const blogPosts = await getCollection('blog');
const sortedPosts = blogPosts.sort((a, b) =>
  b.data.publishDate.valueOf() - a.data.publishDate.valueOf()
);
---

{sortedPosts.map(post => (
  <article>
    <h2><a href={`/blog/${post.slug}`}>{post.data.title}</a></h2>
    <p>{post.data.description}</p>
  </article>
))}
```

**4. Render individual posts:**

```astro
---
// src/pages/blog/[...slug].astro
import { getCollection } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.slug },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await post.render();
---

<article>
  <h1>{post.data.title}</h1>
  <time>{post.data.publishDate.toLocaleDateString()}</time>
  <Content />
</article>
```

## Step 8: View Transitions

Add smooth page transitions with minimal code:

**1. Add to layout:**

```astro
---
import { ViewTransitions } from 'astro:transitions';
---
<html>
  <head>
    <ViewTransitions />
  </head>
  <body>
    <slot />
  </body>
</html>
```

**2. Customize transitions on elements:**

```astro
<img src="photo.jpg" transition:name="hero" />
<h1 transition:animate="slide">Title</h1>
```

**3. Control transition behavior:**

```astro
---
// Disable transitions for this page
export const transitionDisable = true;
---
```

## Configuration & Optimization

### Essential Astro Config Options

```javascript
// astro.config.mjs
import { defineConfig } from "astro/config";
import react from "@astrojs/react";
import tailwind from "@astrojs/tailwind";

export default defineConfig({
  // Site URL for sitemaps and canonical URLs
  site: "https://example.com",

  // Base path if deploying to subdirectory
  base: "/docs",

  // Trailing slash preference
  trailingSlash: "always", // or 'never' or 'ignore'

  // Output mode
  output: "static", // or 'server' or 'hybrid'

  // Integrations
  integrations: [react(), tailwind()],

  // Image optimization
  image: {
    domains: ["example.com"],
  },

  // Markdown options
  markdown: {
    shikiConfig: {
      theme: "dracula",
    },
  },
});
```

### Performance Best Practices

1. **Keep islands minimal:**
   - Only use `client:*` directives when truly needed
   - Prefer static HTML whenever possible
   - Use `client:idle` or `client:visible` over `client:load`

2. **Optimize images:**

```astro
---
import { Image } from 'astro:assets';
import heroImage from '../assets/hero.jpg';
---
<Image src={heroImage} alt="Hero" width={800} height={600} />
```

3. **Use content collections for type safety:**
   - Define schemas for all content
   - Get autocomplete and type checking
   - Validate content at build time

4. **Implement proper caching headers:**
   - Configure in your hosting platform
   - Use aggressive caching for static assets

5. **Minimize client-side JavaScript:**
   - Astro's default is zero JS - keep it that way
   - Only add interactivity where needed
   - Consider using `<script>` tags for simple interactions

## Troubleshooting

### Common Issues & Solutions

**"Module not found" errors:**

- Check import paths are correct
- Verify integrations are installed: `npm install`
- Check `astro.config.mjs` has correct integration setup
- Try clearing node_modules and reinstalling

**"Page not rendering":**

- Verify file is in `src/pages/` directory
- Check for syntax errors in frontmatter (between `---`)
- Ensure proper file extension (`.astro`, `.md`, `.mdx`)
- Check for TypeScript errors if using TS

**"Styles not applying":**

- Remember: scoped styles only apply to component's own HTML
- Use `<style is:global>` for global styles
- Check Tailwind configuration if using Tailwind
- Verify CSS is being imported correctly

**"Component not interactive":**

- Add `client:*` directive to framework components
- Ensure integration is installed (e.g., `@astrojs/react`)
- Check browser console for JavaScript errors
- Verify the component actually needs client-side JS

**"Content collection errors":**

- Check schema in `src/content/config.ts`
- Verify frontmatter matches schema
- Ensure `type: 'content'` is set correctly
- Check file is in correct collection folder

**"Node.js version errors":**

- Verify you're using Node.js v18.20.8+, v20.3.0+, or v22+
- Avoid v19 and v21 (not supported)
- Use nvm to manage Node versions

## Key Differences from Original Skill

**Major Updates:**

1. **Server Islands**: New `server:defer` directive for personalized content
2. **Node.js Requirements**: Updated version requirements (v19, v21 not supported)
3. **Content Collections**: Emphasized as best practice for content management
4. **View Transitions**: Built-in support for smooth page transitions
5. **Installation Flags**: New `--add` flag for installing integrations during setup
6. **500 Error Pages**: New support for custom 500 pages with error props
7. **Islands Architecture**: More detailed explanation of the core concept
8. **Performance Focus**: Stronger emphasis on zero-JS defaults
9. **TypeScript**: Better TypeScript integration and examples
10. **Starlight**: Highlighted as official solution for documentation sites

## Example Workflows

### Example 1: Building a Blog with Content Collections

**User:** "I want to build a blog with Astro and React"

**Workflow:**

1. Identify requirements: Blog site, needs React support, wants type-safe content
2. Fetch themes: `web_fetch https://astro.build/themes/1/?technology[]=react&price[]=free`
3. Recommend theme: Suggest astro-blog-template or similar
4. Initialize project:
   ```bash
   npm create astro@latest my-blog -- --template <theme-name> --add react
   ```
5. Set up content collections for blog posts
6. Create blog post schema with title, date, description, tags
7. Build listing page that queries all posts
8. Create dynamic route for individual posts
9. Add React components for interactive elements (likes, comments) with `client:visible`
10. Implement view transitions for smooth navigation
11. Configure site URL and metadata

### Example 2: Documentation Site with Starlight

**User:** "I need documentation site with search and dark mode"

**Workflow:**

1. Identify: Documentation site, needs search and dark mode
2. **Strongly recommend Starlight** (official Astro docs framework)
3. Initialize:
   ```bash
   npm create astro@latest my-docs -- --template starlight
   ```
4. Configure sidebar in `astro.config.mjs`:
   ```js
   starlight({
     title: "My Docs",
     sidebar: [
       { label: "Guides", autogenerate: { directory: "guides" } },
       { label: "Reference", autogenerate: { directory: "reference" } },
     ],
   });
   ```
5. Create content in `src/content/docs/`
6. Dark mode works automatically
7. Search works automatically (powered by Pagefind)
8. Customize theme colors if needed

### Example 3: E-commerce Site with Product Collections

**User:** "Build an e-commerce store for selling art prints"

**Workflow:**

1. Identify requirements: Product showcase, shopping cart, payment integration
2. Fetch themes for e-commerce or portfolio
3. Initialize project with React (for cart interactivity)
4. Set up content collection for products:
   ```typescript
   const products = defineCollection({
     type: "content",
     schema: z.object({
       name: z.string(),
       price: z.number(),
       image: z.string(),
       category: z.enum(["abstract", "landscape", "portrait"]),
       inStock: z.boolean(),
     }),
   });
   ```
5. Create product listing page with filters
6. Build product detail pages with dynamic routes
7. Add React shopping cart component with `client:load`
8. Integrate payment processor (Stripe, etc.)
9. Optimize product images with Astro's Image component
10. Implement view transitions for smooth browsing

## Resources Reference

### Documentation Sources

- **Primary**: `references/astro_docs.md` (read first)
- **Comprehensive**: `https://docs.astro.build/llms-full.txt`
- **Official**: `https://docs.astro.build`

### Theme Resources

- **Themes Gallery**: `https://astro.build/themes/`
- **Free Themes**: `https://astro.build/themes/1/?price[]=free`

### Community

- **Discord**: `https://astro.build/chat`
- **GitHub**: `https://github.com/withastro/astro`

## Success Criteria

An Astro project is set up correctly when:

✅ Project structure follows Astro conventions  
✅ `src/pages/` directory exists with at least `index.astro`  
✅ Appropriate theme selected based on requirements  
✅ Components follow Astro syntax patterns  
✅ TypeScript interfaces defined for component props  
✅ Client-side interactivity only added where needed with proper directives  
✅ Images optimized using `<Image>` component from `astro:assets`  
✅ Content collections configured if blog/content-heavy site  
✅ Site runs successfully with `npm run dev`  
✅ Build completes without errors: `npm run build`  
✅ Configuration includes site URL and proper integrations

## Key Reminders

1. **Always read `references/astro_docs.md` FIRST** before answering questions
2. **Fetch themes** from the Astro themes page when user needs a starting point
3. **Use web_fetch for full docs** when needed for advanced topics
4. **Islands are opt-in** - components are static by default (zero JS)
5. **Content collections** - Best practice for blogs and content-driven sites
6. **Server islands** - New feature for personalized content without blocking
7. **View transitions** - Easy to add smooth navigation
8. **Starlight** - Official solution for documentation (use instead of generic themes)
9. **Type safety** - Use TypeScript for props and content schemas
10. **Performance** - It should be nearly impossible to build a slow Astro site

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhdcodes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
