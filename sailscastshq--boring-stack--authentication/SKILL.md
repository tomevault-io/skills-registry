---
name: authentication
description: > Use when this capability is needed.
metadata:
  author: sailscastshq
---

# Authentication

The Boring JavaScript Stack uses **session-based authentication** with multiple sign-in methods. The Ascent templates provide production-ready implementations of password auth, magic links, passkeys, two-factor authentication, password reset, and OAuth — all built on Sails.js actions, helpers, and policies.

## When to Use

Use this skill when:

- Implementing signup and login flows (password or magic link)
- Adding passkey (WebAuthn) support with `@simplewebauthn`
- Setting up two-factor authentication (TOTP, email codes, backup codes)
- Building password reset flows with secure token handling
- Integrating OAuth providers (Google, GitHub) via `sails-hook-wish`
- Configuring authentication policies (`is-authenticated`, `is-guest`, `has-partially-logged-in`)
- Understanding the `req.me` / `req.session.userId` pattern and return URL handling
- Working with the User model's auth-related attributes and lifecycle callbacks

## Rules

Read individual rule files for detailed explanations and code examples:

- [rules/getting-started.md](rules/getting-started.md) - Auth architecture, User model overview, policies, req.me, return URL
- [rules/password-auth.md](rules/password-auth.md) - Signup and login flows, password hashing, remember me, validation
- [rules/magic-links.md](rules/magic-links.md) - Token generation/hashing, request/verify actions, auto-signup, security
- [rules/passkeys.md](rules/passkeys.md) - WebAuthn with @simplewebauthn, registration and authentication flows
- [rules/two-factor.md](rules/two-factor.md) - TOTP, email 2FA, backup codes, partial login state, verify-2fa action
- [rules/password-reset.md](rules/password-reset.md) - Forgot/reset flow, token lifecycle, email integration, security
- [rules/oauth.md](rules/oauth.md) - Wish library, Google/GitHub OAuth, redirect/callback, findOrCreate pattern

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sailscastshq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
