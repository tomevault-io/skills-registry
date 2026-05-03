---
name: project-guidelines
description: Apply the gilow-wedding project coding guidelines for TS/JS/MD files (concise, readable, simple, Tailwind-first, auth checks on queries/mutations). Use when this capability is needed.
metadata:
  author: jonlow
---

# Project Guidelines (gilow-wedding)

- This is a wedding guest invite website that will be shown to about ~100 unique guests, each with their own guest url eg localhost:3000/unique-guest-slug

- The only other unique pages are the public homepage (/) and the secure dashboard (/dash)

## Core guidelines
- Be concise.
- Prioritize developer experience and readability.
- Prefer simple, direct solutions over scalability; the guest list stays under ~100 entries.
- Avoid over-engineering and unnecessary abstractions.
- Favor clear naming and minimal configuration.
- All queries and mutations should check the user session and validate inputs simply.
- Break layouts into small, reusable components where it improves clarity.
- Use Tailwind CSS for styling; avoid custom CSS unless absolutely necessary.


## Auth guidlines
- Keep it simple. There will only ever be 1 user to login and a small surface area of people visiting the site.
- Prioritise fixing critical security flaws with the above context in mind

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jonlow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
