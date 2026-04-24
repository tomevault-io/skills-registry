---
name: managing-auth-pipeline
description: Handles the authentication flow including Login, Signup, OAuth, and Password Recovery. Use when building auth pages or logic.
metadata:
  author: itsmealee
---

# Authentication Pipeline

## When to use this skill
- Developing the `/auth` directory.
- Implementing "Sign in with Google" or "GitHub".
- Handling session persistence in Next.js.

## Workflow
- [ ] Create auth forms (Signup/Login).
- [ ] Implement `account.create()` and `account.createEmailPasswordSession()`.
- [ ] Set up OAuth redirection using `account.createOAuth2Session()`.
- [ ] Handle logout with `account.deleteSession('current')`.

## Key Logic
- **OAuth**: Ensure redirect URLs are correctly whitespaced in Appwrite Console.
- **Persistence**: Appwrite handles cookies automatically in the browser; use Middleware for route protection.

## Instructions
- **Error Feedback**: Use toast notifications for "Invalid credentials" or "Email already exists".
- **Social Auth**: Prioritize Google/GitHub for premium UX.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itsmealee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
