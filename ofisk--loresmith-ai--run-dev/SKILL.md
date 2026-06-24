---
name: run-dev
description: Show how to run the development server and client in separate terminals. Use when the user asks how to start dev, run the app, or start development servers. Use when this capability is needed.
metadata:
  author: ofisk
---

# Run development servers

## Terminal 1: Server

```bash
npm run dev:cloudflare
```

This runs `wrangler dev --config wrangler.dev.jsonc --port 8787`

## Terminal 2: Client

```bash
npm start
```

This runs the Vite dev server for the frontend.

## Done

Server runs on `http://localhost:8787`
Client runs on `http://localhost:5173`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ofisk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
