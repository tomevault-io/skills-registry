---
name: scaffold-auth-route
description: Create a new auth-protected route with server-side validation. Use for user-facing pages needing authentication. Use when this capability is needed.
metadata:
  author: oungseik
---

Generate a new authenticated SvelteKit route following repo patterns.

Create:
- `+page.svelte` with SNUGGLE components and {#await} for data fetching
- `+page.server.ts` with session validation using Better Auth
- Proper error handling for unauthenticated users

Route conventions:
- Use kebab-case naming (e.g., /my-dashboard)
- Check session in load function: `ctx.locals.session`
- Redirect to login if no session
- Use Svelte 5 runes like $props for props
- Integrate with Tailwind/shadcn for consistent UI

Reference [template.md](template.md) for route files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oungseik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
