---
name: better-auth-jwt-skill
description: Use when configuring authentication flows or handling identity tokens between frontend and backend.
metadata:
  author: s-952205
---

# Better-Auth JWT Skill

## Instructions
1. **JWT Plugin**: Configure the JWT plugin in Better Auth to issue self-contained tokens.
2. **Secret Sync**: Ensure `BETTER_AUTH_SECRET` is identical in both Frontend and Backend environment variables.
3. **Client Session**: Use Better Auth hooks to manage login/logout and retrieve the current token.
4. **Token Refresh**: Handle token expiration by guiding the user back to the login flow if the session dies.

## Examples
- **Plugin Config**: `plugins: [ jwt({ secret: process.env.BETTER_AUTH_SECRET }) ]`
- **Client Hook**: `const { data: session } = authClient.useSession();`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/s-952205) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
