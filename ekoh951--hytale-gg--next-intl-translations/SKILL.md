---
name: next-intl-translations
description: | Use when this capability is needed.
metadata:
  author: ekoh951
---

# next-intl Translations

## Architecture Overview

- **Locales**: `en` (default), `es` — defined in `i18n/locales.ts`
- **Messages**: Split into 11 domain files per locale under `messages/{locale}/`
- **Merge**: `i18n/request.ts` loads all files via `Promise.all` and merges them. Survey keys are **deep-merged** from 3 files.
- **Root layout**: `messages={null}` on `NextIntlClientProvider` — zero translation JSON shipped globally
- **Scoped providers**: Only routes with client-side translation needs get a `NextIntlClientProvider` with `pick()`

## Message Files

```
messages/
├── en/
│   ├── common.json      (navigation, footer, metadata)
│   ├── auth.json         (auth)
│   ├── home.json         (home.hero, reviews)
│   ├── servers.json      (server, categories, regions, servers)
│   ├── creators.json     (creators)
│   ├── branding.json     (branding)
│   ├── profile.json      (profile, settings)
│   ├── submit.json       (submit)
│   ├── survey-common.json  (survey.listing, survey.common)
│   ├── survey-player.json  (survey.player)
│   └── survey-owner.json   (survey.owner)
└── es/  (mirrors en/ structure exactly)
```

### Rules for Message Files

1. **Both locales must have identical key structure** — run `npx @lingual/i18n-check@latest --source en --locales messages` to verify
2. **Each file has unique top-level keys** except survey files which share the `survey` top-level key with different sub-keys
3. **Adding a new domain file** requires updating `i18n/request.ts` to import and spread it into the merged object
4. **Never use `experimental.messages` precompilation** — it bypasses the custom deep-merge in `request.ts` and breaks survey translations

## Component Patterns

### Server Components (preferred)

Use `useTranslations` from `next-intl` directly — translations resolve on the server, zero JS shipped.

```tsx
// No 'use client' directive
import { useTranslations } from 'next-intl';

export function Footer() {
  const t = useTranslations('footer');
  return <p>{t('copyright', { year: 2026 })}</p>;
}
```

### Client Components — Donut Pattern

When a component needs interactivity AND translations, split into server wrapper + client shell. Pass pre-translated strings as props.

```tsx
// header.tsx (Server Component — resolves translations)
import { useTranslations } from 'next-intl';
import { HeaderClient } from './header-client';

export function Header() {
  const t = useTranslations('navigation');
  return <HeaderClient labels={{ signIn: t('signIn'), surveys: t('surveys') }} />;
}

// header-client.tsx (Client Component — receives string props)
'use client';
export function HeaderClient({ labels }: { labels: { signIn: string; surveys: string } }) {
  // useState, useAuth, etc. — no useTranslations needed
}
```

### Client Components — Scoped Provider

When client components must call `useTranslations` themselves (e.g., dynamic survey rendering), wrap with a scoped `NextIntlClientProvider` in a parent server layout/page.

```tsx
// app/[locale]/survey/[slug]/layout.tsx
import { pick } from 'es-toolkit';
import { NextIntlClientProvider } from 'next-intl';
import { getMessages, setRequestLocale } from 'next-intl/server';

export default async function SurveySlugLayout({
  children,
  params,
}: {
  children: React.ReactNode;
  params: Promise<{ locale: string; slug: string }>;
}) {
  const { locale } = await params;
  setRequestLocale(locale);
  const messages = await getMessages({ locale });

  return (
    <NextIntlClientProvider messages={pick(messages as Record<string, unknown>, ['survey'])}>
      {children}
    </NextIntlClientProvider>
  );
}
```

### Decision Flowchart

1. Component has NO interactivity? → **Server Component** with `useTranslations`
2. Component has interactivity but few labels? → **Donut pattern** (server wrapper passes string props)
3. Component has interactivity AND many dynamic translation keys? → **Scoped provider** in parent layout/page

## Critical Rules

- **Every layout/page** in `app/[locale]/` must call `setRequestLocale(locale)` — required for static rendering
- **Always pass `{ locale }` explicitly** to `getMessages()` and `getTranslations()` in layouts/pages
- **Root layout uses `messages={null}`** — never pass all messages globally
- **Use `pick()` from `es-toolkit`** to scope provider messages to only needed keys (e.g., `['survey']`)
- **Localized pathnames** must be configured in BOTH `i18n/routing.ts` AND `next.config.ts` rewrites — see `references/pathnames.md`

## Verification

After any translation change, run:

```bash
npx @lingual/i18n-check@latest --source en --locales messages
```

Expected output: "No missing keys found!" — fix any reported gaps before committing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekoh951) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
