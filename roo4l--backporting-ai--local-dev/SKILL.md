---
name: local-dev
description: Run and test the Backporting.ai website locally. Use when starting the dev server, testing UI changes, debugging the site, or verifying changes before committing. Use when this capability is needed.
metadata:
  author: roo4l
---

# Local Development

## Prerequisites

- Node.js >= 18.14
- npm

## Starting the Dev Server

```bash
npm install   # First time only
npm run dev   # Start dev server
```

Server runs at **http://localhost:4321/**

**Important**: Run with unrestricted permissions (`required_permissions: ["all"]`) to avoid sandbox issues with Astro telemetry.

## How Changes Sync

Astro's dev server watches for file changes automatically:

- Edit any file in `src/` → browser auto-reloads
- No manual refresh needed
- Changes appear instantly

## Verifying Changes with Chrome DevTools MCP

After making UI changes, verify using Chrome DevTools MCP Server:

### 1. Check if site is running

Navigate to `http://localhost:4321/` first. If it fails, start the dev server.

### 2. Take screenshots

Use Chrome DevTools MCP to capture screenshots and verify:
- Layout renders correctly
- Styles apply as expected
- Content displays properly

### 3. Inspect elements

Use Chrome DevTools MCP to:
- Check CSS properties
- Debug layout issues
- Verify responsive behavior

**Do not** use Cursor's built-in browser—Chrome DevTools MCP is more capable.

## Available Commands

| Command | Purpose |
|---------|---------|
| `npm run dev` | Start development server |
| `npm run build` | Build for production |
| `npm run preview` | Preview production build |

## Project Structure

```
src/
├── pages/        # Site pages (index, articles, digests)
├── components/   # Reusable Astro components
├── layouts/      # Base layout
├── content/      # Markdown content (digests)
└── data/         # Data utilities and resources
```

## Troubleshooting

**Dev server won't start:**
- Ensure `node_modules/` exists (run `npm install`)
- Check Node.js version: `node --version` (need >= 18.14)

**Changes not appearing:**
- Check terminal for compilation errors
- Verify you're editing files in `src/`
- Hard refresh browser (Cmd+Shift+R)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roo4l) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
