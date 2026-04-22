---
name: dev
description: Start the Next.js development server Use when this capability is needed.
metadata:
  author: sammii-hk
---

# Development Server

Start the Next.js development server with Turbopack.

## Steps

1. **Check for existing server** on port 3000:

   ```bash
   lsof -i :3000
   ```

2. **If port is in use**, ask user if they want to kill it

3. **Start dev server**:
   ```bash
   pnpm dev
   ```

The server runs at http://localhost:3000

## Notes

- Uses Turbopack for faster hot reloading
- Run in background if user wants to continue working
- Ctrl+C or `pkill -f "next dev"` to stop

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
