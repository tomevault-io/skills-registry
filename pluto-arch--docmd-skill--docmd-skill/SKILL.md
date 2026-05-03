---
name: docmd-skill
description: Skills for agents to use docmd(node project) to write and generate static web content Use when this capability is needed.
metadata:
  author: pluto-arch
---

# Introduction

The `docmd-skill` is a collection of skills designed for agents to utilize the `docmd` tool for writing markdown files and generating static web content(html page). This skill set enables agents to create, edit, and manage documentation and web pages efficiently using the capabilities provided by `docmd`.

# Directory Structure

The `docmd-skill` assumes the following directory structure for organizing markdown files and generated static

```
project-root/
│├── docs/   // writeable or updatable markdown files in this folder
│   ├── index.md // home page markdown file
│   ├── about.md // about page markdown file
│   └── posts/   // some category folder
│       ├── index.md // category home page markdown file
│       └── post1.md // a blog post markdown file
│├── assets/ // static assets like images, css, js files
│--docmd.config.js // docmd configuration file
```

# generate-static-web

The `generate-static-web` skill allows agents to convert markdown files located in the `docs/` directory into static HTML pages. The generated pages are saved in the specified output directory (e.g., `site/`).

eg., to generate static web pages, an agent can execute the following command:

```bash
npm docmd build
```

fllowing the configuration in `docmd.config.js`, the markdown files will be processed and the static HTML files will be created in the output directory.

eg., the `docs/index.md` will be converted to `site/index.html`, and `docs/posts/post1.md` will be converted to `site/posts/post1.html`.

# Features

- **Markdown Writing**: Agents can create and edit markdown files with ease, allowing for structured documentation.
- **Static Web Generation**: Convert markdown files into static HTML pages, making it easy to publish content on the output dir.
- **Content Management**: Organize and manage multiple markdown files

# Configuration guide

To use the `docmd-skill`, ensure that you have a `docmd.config.js` file in your project root. This configuration file should specify the input directory (where markdown files are located) and the output directory (where generated HTML files will be saved).

```javascript
// docmd.config.js
module.exports = {
  // --- Core Metadata ---
  siteTitle: "My Documentation",
  siteUrl: "", // e.g. https://mysite.com (Critical for SEO/Sitemap)

  // --- Branding ---
  logo: {
    light: "assets/images/docmd-logo-dark.png",
    dark: "assets/images/docmd-logo-light.png",
    alt: "Logo",
    href: "./",
  },
  favicon: "assets/favicon.ico",

  // --- Source & Output ---
  srcDir: "docs",
  outputDir: "site",

  // --- Theme & Layout ---
  theme: {
    name: "sky", // Options: 'default', 'sky', 'ruby', 'retro'
    defaultMode: "system", // 'light', 'dark', or 'system'
    enableModeToggle: true, // Show mode toggle button
    positionMode: "top", // 'top' or 'bottom'
    codeHighlight: true, // Enable Highlight.js
    // custom CSS Assets ---
    customCss: ["assets/css/global.css"], // e.g. ['assets/css/custom.css']
  },

  // --- Custom Js Assets ---
  customJs: ["assets/js/global.js"],
  // --- Features ---
  search: true, // Built-in offline search
  minify: true, // Minify HTML/CSS/JS in build
  autoTitleFromH1: true, // Auto-generate page title from first H1
  copyCode: true, // Show "copy" button on code blocks
  pageNavigation: true, // Prev/Next buttons at bottom

  // --- Navigation (Sidebar) ---
  navigation: [
    { title: "Introduction", path: "/", icon: "home" },
    {
      title: "Primary Documentation",
      icon: "book-open",
      collapsible: true,
      path: "./posts", // recommended to set path for collapsible items,will link to the posts/index, auto generated
      children: [
        {
          title: "Net 8 Installation", // navigation item title
          path: "./posts/net8-intro", // path to the markdown file without extension,will auto convert to .html by docmd command
          icon: "rocket", // optional icon name from docmd icon set
        },
      ],
    },
  ],

  // --- Plugins ---
  plugins: {
    seo: {
      defaultDescription: "Documentation built with docmd.",
      openGraph: {
        defaultImage: "", // e.g. 'assets/images/og-image.png'
      },
      twitter: {
        cardType: "summary_large_image",
      },
    },
    analytics: {
      googleV4: {
        measurementId: "G-", // Replace with your Google Analytics Measurement ID
      },
    },
    sitemap: {
      defaultChangefreq: "weekly", // e.g. 'daily', 'weekly', 'monthly'
      defaultPriority: 0.8, // Priority between 0.0 and 1.0
    },
    mermaid: {}, // mermaid diagrams support
  },

  // --- Footer ---
  footer:
    "© " +
    new Date().getFullYear() +
    " My Project. Built with [docmd](https://docmd.io).",

  // --- Edit Link ---
  editLink: {
    enabled: false,
    baseUrl: "https://github.com/USERNAME/REPO/edit/main/docs",
    text: "Edit this page",
  },
};
```

# Markdown Structure

Every Markdown (.md) file that docmd processes must begin with YAML frontmatter. Frontmatter is a block of YAML (YAML Ain’t Markup Language) enclosed by triple-dashed lines (---) at the very beginning of your file. It’s used to set metadata for each page.

eg., a markdown file with frontmatter might look like this:

```markdown
---
title: "My Awesome Page Title"
description: "A concise and informative description for this page, used for SEO and potentially in listings."
# You can add other custom fields here if needed for your templates or logic
order: 1 # Example: for custom sorting if you implement such logic
tags:
  - guide
  - advanced
seo:
  title: "Custom SEO Title (optional, defaults to page title)"
  description: "Custom SEO Description (optional, defaults to frontmatter description)"
---
```

# SEO

The `docmd-skill` includes built-in SEO optimization features. By providing a `title` and `description` in the frontmatter of your markdown files, you can ensure that each generated HTML page has appropriate meta tags for search engines. Additionally, the SEO plugin can be configured in `docmd.config.js` to set default values and Open Graph/Twitter Card metadata for enhanced sharing on social media platforms. Write your own SEO information for each page when necessary

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluto-arch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
