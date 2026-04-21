---
name: docusaurus-conventions
description: Coding conventions, patterns, and best practices for this Docusaurus v3 project. Includes component structure, MDX patterns, theming, and configuration guidelines. Use when this capability is needed.
metadata:
  author: ngtanthanh-qc
---

# Project Conventions for Docusaurus Blog

## Tech Stack
- Docusaurus 3.9.2 with React 18
- TypeScript for components
- Firebase for authentication
- Algolia DocSearch for search
- Deployed on Vercel

## File Conventions

### Blog Posts
- Folder format: `blog/YYYY-MM-DD-slug-name/index.mdx`
- Author: always `kaizen` (defined in `blog/authors.yml`)
- Use `{/* truncate */}` after intro for list preview
- Tags should be lowercase, kebab-case

### Documentation
- Numbered prefix folders: `01-category-name/`
- Each category has `_category_.json` for sidebar config
- Use `.mdx` extension for pages with JSX/components, `.md` for pure markdown
- Images go in local `img/` subfolder

### Components
- Auth components: `src/components/Auth/`
- Theme customizations: `src/theme/`
- Custom pages: `src/pages/`
- Utilities: `src/utils/`

## MDX Features Available
- Mermaid diagrams (maxTextSize: 90000)
- Live code blocks
- KaTeX math expressions
- Image zoom plugin
- Admonitions (tip, note, warning, danger, info)

## Configuration
- Main config: `docusaurus.config.js`
- Sidebars: `sidebars.js` (auto-generated from filesystem)
- Environment: `.env.local` (never commit)
- i18n: English (default) and Vietnamese

## Build & Deploy
- `npm run build` for production build
- `npm run typecheck` for TypeScript checking
- Vercel auto-deploys from main branch
- Node.js >= 20.0 required

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngtanthanh-qc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
