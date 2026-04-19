---
name: quick-api
description: Create a quick Express.js API with TypeScript Use when this capability is needed.
metadata:
  author: makifars
---

# Create Express API with TypeScript

Scaffold a minimal Express.js API with TypeScript.

## Steps:

1. Ask for the project name if not provided
2. Create project directory and initialize: `npm init -y`
3. Install dependencies:
   ```bash
   npm install express cors dotenv
   npm install -D typescript ts-node @types/node @types/express @types/cors nodemon
   ```
4. Create `tsconfig.json` with sensible defaults
5. Create folder structure:
   ```
   src/
   ├── index.ts        # Entry point with basic Express setup
   ├── routes/         # Route handlers
   └── middleware/     # Custom middleware
   ```
6. Add scripts to package.json: `dev`, `build`, `start`
7. Create a sample health check endpoint at `/api/health`
8. Add `.env.example` and `.gitignore`

## Inform user:

- Run `npm run dev` to start development server
- Server runs on `http://localhost:3000` by default

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/makifars) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
