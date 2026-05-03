---
name: astro-i18n-site
description: Build a multilingual Astro site with custom i18n (no Astro i18n plugin). Use when: (1) setting up multilingual routing in Astro with default-locale-no-prefix pattern, (2) creating translation JSON files and helper functions, (3) building language switcher components, (4) generating multilingual static pages with getStaticPaths, (5) creating multilingual blog with Content Collections, (6) adding hreflang tags and SEO for multiple languages, (7) implementing browser language detection and auto-redirect. Covers routing, translations, React language switcher, content collections, Layout/BlogLayout, and SEO. Use when this capability is needed.
metadata:
  author: hiitiger
---

# Astro i18n Site

Lightweight, custom i18n for Astro sites without third-party i18n plugins. Default locale has no URL prefix; non-default locales use `/{lang}/` prefix.

## Architecture Overview

```
src/
├── i18n/
│   ├── index.js              # Helpers: getTranslation, getLangFromUrl, getLocalePath
│   └── locales/
│       ├── en.json            # Default locale (full key set)
│       ├── zh.json            # Non-default (can be partial, falls back to en)
│       └── ...
├── pages/
│   ├── index.astro            # Default locale landing (lang="en")
│   ├── blog/
│   │   ├── index.astro        # Default locale blog index
│   │   └── [slug].astro       # Default locale blog post
│   └── [lang]/
│       ├── index.astro        # Non-default locale landing (getStaticPaths)
│       └── blog/
│           ├── index.astro    # Non-default locale blog index
│           └── [slug].astro   # Non-default locale blog post
├── content/blog/
│   ├── en/slug.md             # English posts
│   ├── zh/slug.md             # Chinese posts (same slug = same article)
│   └── ...
├── layouts/
│   ├── Layout.astro           # Base HTML: meta, hreflang, JSON-LD, navbar
│   └── BlogLayout.astro       # Blog wrapper: article header, prose, share links
└── components/
    ├── Navbar.jsx             # Language switcher + navigation
    └── LanguageBanner.jsx     # Browser language detection banner
```

## Step 1: i18n Core Module

See [references/i18n-core.md](references/i18n-core.md) for full implementation.

**Key design decisions:**
- `getTranslation(lang)` merges target locale over English base — partial translations work
- `getLangFromUrl(url)` extracts lang from URL path, returns `DEFAULT_LANG` if not found
- `getLocalePath(lang)` returns `/` for default, `/{lang}/` for others
- `LANGUAGES` object stores display labels for language switcher UI

## Step 2: Page Routing Pattern

Two parallel page trees: one for default locale (no prefix), one for `[lang]` dynamic routes.

**Default locale pages** — hardcode `lang="en"`:

```astro
---
// src/pages/index.astro
import Layout from '../layouts/Layout.astro';
import LandingPage from '../components/LandingPage.jsx';
import { getTranslation } from '../i18n/index.js';
const t = getTranslation('en');
---
<Layout lang="en" title={t.seoHomeTitle} description={t.seoHomeDescription}>
  <LandingPage client:load lang="en" />
</Layout>
```

**Non-default locale pages** — use `getStaticPaths` to generate all non-default locales:

```astro
---
// src/pages/[lang]/index.astro
import { LANGUAGES, DEFAULT_LANG, getTranslation } from '../../i18n/index.js';

export function getStaticPaths() {
  return Object.keys(LANGUAGES)
    .filter((lang) => lang !== DEFAULT_LANG)
    .map((lang) => ({ params: { lang } }));
}

const { lang } = Astro.params;
const t = getTranslation(lang);
---
<Layout lang={lang} title={t.seoHomeTitle}>
  <LandingPage client:load lang={lang} />
</Layout>
```

## Step 3: Multilingual Blog with Content Collections

See [references/blog-patterns.md](references/blog-patterns.md) for complete routing and collection patterns.

**Content organization:** `src/content/blog/{lang}/{slug}.md` — same slug across languages for hreflang pairing.

**Four page files needed:**
1. `src/pages/blog/index.astro` — default locale blog index
2. `src/pages/blog/[slug].astro` — default locale blog post
3. `src/pages/[lang]/blog/index.astro` — non-default locale blog index
4. `src/pages/[lang]/blog/[slug].astro` — non-default locale blog post

**Key patterns:**
- Filter collection by `id.startsWith('{lang}/')` to get locale-specific posts
- Strip lang prefix from ID to get clean slug: `post.id.replace('{lang}/', '').replace(/\.md$/, '')`
- Use `Intl.DateTimeFormat(lang)` for locale-aware date formatting
- Nested `getStaticPaths` for `[lang]/blog/[slug]` generates all lang+slug combinations

## Step 4: Language Switcher Component

See [references/language-switcher.md](references/language-switcher.md) for complete React implementation.

**Key patterns:**
- Path rewriting: strip existing lang prefix, prepend target lang (or bare path for default)
- Store preference in `localStorage('preferred-lang')` on switch
- Click-outside-to-close dropdown pattern
- Pass `lang` and `currentPath` as props from Astro to React (`client:load`)

## Step 5: Language Detection Banner

Auto-detect browser language and suggest switching if it differs from current page lang.

**Logic flow:**
1. Check if user already has a `preferred-lang` in localStorage — skip if yes
2. Check if banner was previously dismissed — skip if yes
3. Match `navigator.languages` against supported languages
4. Show banner with localized message + switch link
5. On switch: set `preferred-lang`, navigate to localized path
6. On dismiss: set `lang-banner-dismissed`, hide banner

## Step 6: Layout SEO

See [references/layout-seo.md](references/layout-seo.md) for complete Layout.astro and BlogLayout.astro implementations.

The base `Layout.astro` handles all i18n SEO concerns:

**hreflang tags** — auto-generate for all locales + `x-default` pointing to default locale:

```astro
{hreflangPairs?.map(({ lang: l, url }) => (
  <link rel="alternate" hreflang={l} href={url} />
))}
{hreflangPairs && (
  <link rel="alternate" hreflang="x-default"
    href={hreflangPairs.find((p) => p.lang === 'en')?.url ?? '/'} />
)}
```

**Other i18n meta:**
- `<html lang={lang}>` — set document language
- `og:locale` — map lang code to full locale (`zh` → `zh_CN`) via `ogLocaleMap`
- Canonical URL — locale-specific, using `import.meta.env.SITE`
- All text from translation keys, never hardcoded

**Language preference redirect** — inline script on landing page only (see [references/language-switcher.md](references/language-switcher.md) § Landing Page Auto-Redirect for implementation).

## Translation Key Conventions

- camelCase keys: `navFeatures`, `heroTitleLine1`, `seoHomeTitle`
- SEO keys prefixed: `seoHomeTitle`, `seoHomeDescription`, `seoHomeJsonLdDescription`
- Blog keys prefixed: `blogIndexTitle`, `blogBackToBlog`, `blogShareThisPost`
- Banner keys prefixed: `langBannerMessage`, `langBannerSwitch`
- Default locale (en.json) must have all keys — other locales can be partial

## Checklist

- [ ] All user-facing text uses translation keys (no hardcoded strings)
- [ ] Default locale pages exist at root level (no prefix)
- [ ] Non-default locale pages use `[lang]` dynamic routing with `getStaticPaths`
- [ ] hreflang tags include all locales + `x-default`
- [ ] Canonical URLs are locale-specific
- [ ] Blog posts have matching slugs across languages
- [ ] Language switcher preserves current path when switching
- [ ] `localStorage` stores language preference
- [ ] `og:locale` and `<html lang>` set correctly per locale
- [ ] Date formatting uses `Intl.DateTimeFormat` with correct locale

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiitiger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
