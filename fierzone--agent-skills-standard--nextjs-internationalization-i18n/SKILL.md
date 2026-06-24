---
name: next-js-internationalization-i18n
description: Best practices for multi-language handling, locale routing, and detection middleware. Use when this capability is needed.
metadata:
  author: fierzone
---

# Internationalization (i18n)

## **Priority: P2 (MEDIUM)**

Use Sub-path Routing (`/en`, `/de`) and Server Components for translations.

## Principles

1. **Sub-path Routing**: Use URL segments (e.g., `app/[lang]/page.tsx`) to manage locales.
   - _Why_: SEO friendly, sharable, and cacheable.
2. **Server-Side Translation**: Load dictionary files (`en.json`) in Server Components.
   - _Why_: Reduces client bundle size. No huge JSON blobs sent to browser.
3. **Middleware Detection**: Use `middleware.ts` to detect `Accept-Language` headers and redirect users to their preferred locale.
4. **Type Safety**: Use robust typing for translation keys to prevent broken text UI.

## Implementation Pattern

See [references/i18n_setup.md](references/i18n_setup.md) for Directory Structure, Middleware, and Server Component examples.

## Redirect Handling Strategy

When handling redirects (Authentication, Legacy URLs) in an i18n app:

- **Always preserve locale**: Use `redirect(`/${lang}/login`)` instead of just `/login`.
- **Server Actions**: Return `redirect(...)` from actions.
- **Next Config**: Use `next.config.js` for legacy SEO 301s (e.g., old-site `/about-us` -> `/en/about`).

---
> Source: [fierzone/agent-skills-standard](https://github.com/fierzone/agent-skills-standard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
