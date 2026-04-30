---
name: docusaurus-advanced
description: Deep dive into the Docusaurus configuration, plugins, and custom fields. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Docusaurus Configuration

## Config File
- **File**: `textbook/docusaurus.config.ts`
- **Type**: TypeScript config.

## Integrations
- **API URL**: Exposed to client via `customFields`.
    ```ts
    customFields: {
      apiUrl: process.env.DOCUSAURUS_API_URL || 'http://127.0.0.1:8000',
    }
    ```
- **AuthBar**: A custom navbar item registered in `textbook/src/theme/NavbarItem` (if swizzled) or imported specifically.

## Plugins & Presets
- **Preset**: `classic` (standard docs, blog, pages).
- **Sidebar**: Defined in `textbook/sidebars.ts`.

## Theme Swizzling
- **Custom CSS**: `textbook/src/css/custom.css` (contains specific overrides for dark mode and premium UI).
- **Layout**: `Layout` wrapper is often used in `src/pages` for standalone React pages within Docusaurus.

## MDX
We support MDX for interactive components within documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
