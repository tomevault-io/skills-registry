---
name: preview
description: Start the Next.js dev server for local preview, handling port conflicts and corrupted state. Use when this capability is needed.
metadata:
  author: lsimatt2
---

# Preview

When the user invokes /preview, start the Next.js dev server so they can preview the site locally.

## Steps

1. Check if a Next.js dev server is already running by checking for processes listening on port 3000 (`lsof -i :3000`)
2. If a server is already running:
   - Verify it's healthy by curling `http://localhost:3000`
   - If healthy, tell the user the server is already running and they can preview at **http://localhost:3000**
   - If not healthy, kill the process and continue to step 3
3. Check for corrupted `.next` cache that could cause issues:
   - If `.next` directory exists and seems corrupted (e.g., previous server crashed), remove it with `rm -rf .next`
4. Start the dev server with `npm run dev` in the background
5. Wait a few seconds, then verify the server started successfully by curling `http://localhost:3000`
6. Tell the user they can preview the site at **http://localhost:3000**

## Rules

- The dev server runs on port 3000 by default
- If port 3000 is occupied by a non-Next process, inform the user and suggest they free the port
- Always run the dev server in the background so the conversation can continue
- If `npm run dev` fails, try cleaning `.next` and `node_modules/.cache` then retry once
- Always end by telling the user the preview URL: **http://localhost:3000**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lsimatt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
