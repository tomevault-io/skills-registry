---
name: gatsby-ecommerce-netlify
description: Gatsby e-commerce starter with styled components for Netlify. Use when this capability is needed.
metadata:
  author: eng0ai
---

# Gatsby E-commerce (Netlify)

An e-commerce starter with styled components.

## Tech Stack

- **Framework**: Gatsby
- **Styling**: Styled Components
- **Package Manager**: npm

## Setup

### 1. Clone the Template

```bash
git clone --depth 1 https://github.com/netlify-templates/gatsby-ecommerce-theme.git .
```

If the directory is not empty:

```bash
git clone --depth 1 https://github.com/netlify-templates/gatsby-ecommerce-theme.git _temp_template
mv _temp_template/* _temp_template/.* . 2>/dev/null || true
rm -rf _temp_template
```

### 2. Remove Git History (Optional)

```bash
rm -rf .git
git init
```

### 3. Install Dependencies

```bash
npm install
```

## Build

```bash
npm run build
```

## Deploy to Netlify

```bash
netlify deploy --prod
```

## Development

```bash
npm run develop
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eng0ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
