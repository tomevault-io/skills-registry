---
name: development-workflow
description: Procedures for running, building, and previewing the application. Use when this capability is needed.
metadata:
  author: jalexisg
---

# Development Workflow

This skill describes how to interact with the project's build system (Vite).

## ⚡️ Common Commands

### Start Development Server
Run the local development server with hot module replacement (HMR).
```bash
npm run dev
```
*   **Port**: Usually `http://localhost:5173`
*   **Usage**: Use this for all active development.

### Build for Production
Compile the app into static files for deployment.
```bash
npm run build
```
*   **Output**: `dist/` directory.
*   **Artifacts**: HTML, CSS, and JS bundles (minified).

### Preview Production Build
Locally preview the production build to ensure everything works as expected before deploying.
```bash
npm run preview
```
*   **Prerequisite**: Must run `npm run build` first.

## 📦 Dependencies
To install new packages:
```bash
npm install <package-name>
```

To install dev dependencies:
```bash
npm install -D <package-name>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jalexisg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
