---
name: mantis-react-admin
description: React 19 admin dashboard with Material UI v7, Vite 7, and MUI X Charts. Use when this capability is needed.
metadata:
  author: neversight
---

# Mantis React Admin

A free React admin dashboard with Material UI v7, React 19, Vite 7, and MUI X Charts.

## Tech Stack

- **Framework**: React 19
- **Build Tool**: Vite 7
- **UI Library**: Material UI v7, MUI X Charts
- **Styling**: Emotion CSS-in-JS
- **Routing**: React Router v7
- **State**: SWR for data fetching
- **Package Manager**: yarn
- **Output**: `dist` directory
- **Dev Port**: 5173

## Setup

### 1. Clone the Template

```bash
git clone --depth 1 https://github.com/Eng0AI/mantis-react-admin-template.git .
```

If the directory is not empty:

```bash
git clone --depth 1 https://github.com/Eng0AI/mantis-react-admin-template.git _temp_template
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
yarn install
```

## Build

```bash
yarn build
```

## Deploy

### Vercel (Recommended)

```bash
vercel pull --yes -t $VERCEL_TOKEN
vercel build --prod -t $VERCEL_TOKEN
vercel deploy --prebuilt --prod --yes -t $VERCEL_TOKEN
```

### Netlify

```bash
netlify deploy --prod --dir=dist
```

## Development

```bash
yarn start
```

Opens at http://localhost:5173

## Linting

```bash
yarn lint        # Check for issues
yarn lint:fix    # Auto-fix issues
yarn prettier    # Format code
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
