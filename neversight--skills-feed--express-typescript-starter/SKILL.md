---
name: express-typescript-starter
description: Express + TypeScript starter with Biome, Docker, and Vitest. Use when this capability is needed.
metadata:
  author: neversight
---

# Express TypeScript Starter

Express + TypeScript starter with Biome linting, Docker support, and Vitest testing.

## Tech Stack

- **Framework**: Express.js
- **Language**: TypeScript
- **Linting**: Biome
- **Testing**: Vitest
- **Package Manager**: npm

## Setup

### 1. Clone the Template

```bash
git clone --depth 1 https://github.com/edwinhern/express-typescript.git .
```

If the directory is not empty:

```bash
git clone --depth 1 https://github.com/edwinhern/express-typescript.git _temp_template
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

## Development

```bash
npm run dev
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
