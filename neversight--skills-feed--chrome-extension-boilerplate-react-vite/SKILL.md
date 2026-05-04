---
name: chrome-extension-boilerplate-react-vite
description: Chrome Extension with React, Vite, and TypeScript. Use when this capability is needed.
metadata:
  author: neversight
---

# Chrome Extension Boilerplate

Chrome Extension Boilerplate with React + Vite + TypeScript.

## Tech Stack

- **Framework**: React
- **Build Tool**: Vite
- **Language**: TypeScript
- **Package Manager**: pnpm

## Setup

### 1. Clone the Template

```bash
git clone --depth 1 https://github.com/Jonghakseo/chrome-extension-boilerplate-react-vite.git .
```

If the directory is not empty:

```bash
git clone --depth 1 https://github.com/Jonghakseo/chrome-extension-boilerplate-react-vite.git _temp_template
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
pnpm install
```

## Build

```bash
pnpm build
```

## Development

```bash
pnpm dev
```

## Loading Extension

1. Build the extension
2. Open Chrome and go to `chrome://extensions/`
3. Enable "Developer mode"
4. Click "Load unpacked" and select the `dist` folder

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
