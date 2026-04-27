---
name: i18n-react-page
description: Add and wire i18n keys through react-i18next for a React page, with en and zh-TW JSON bundles and a language toggle. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:

- add or update a translatable string in the web-app
- add a new key to en.json / zh-TW.json
- wire a page through useTranslation()
- add the LanguageToggle component to a new page
- debug missing translations / fallback language

## Context

The web-app uses react-i18next with two JSON bundles at web-app/src/i18n/en.json and web-app/src/i18n/zh-TW.json. The init module lives at web-app/src/i18n/index.ts and is imported from main.tsx. Language is persisted to localStorage under key 'lang' and the <LanguageToggle /> component flips between 'en' and 'zh-TW'.

Pages call useTranslation() and the t('namespace.key') function. Keys are namespaced by feature: auth.*, board.*, dashboard.*, pricing.*, ai.*, demo.*.

## Operating instructions

When adding a new translatable string:

1. Pick a namespace (re-use an existing one if the key belongs to the same feature).
2. Add the key to BOTH en.json and zh-TW.json in the same location. Do not leave one language untranslated.
3. Use t('ns.key') in the component. For interpolation use t('ns.key', { count: 3 }) with an ICU-style {{count}} placeholder in the JSON.
4. If the page currently has no useTranslation hook, add const { t } = useTranslation(); at the top.
5. If the page needs a language switcher, drop <LanguageToggle /> into the header.

## Reusable prompts / code patterns

### Page hook

    import { useTranslation } from 'react-i18next';
    const { t, i18n } = useTranslation();
    return <h1>{t('dashboard.title')}</h1>;

### Interpolation

    // en.json: "online": "{{count}} online"
    t('board.online', { count: onlineCount });

### Language toggle bridge

LanguageToggle calls i18n.changeLanguage(next) and persists to localStorage. Drop-in usage: <LanguageToggle />.

## Anti-patterns

- Do NOT hard-code English strings inside a page that already uses t() - keep it consistent.
- Do NOT add a key to en.json without the matching zh-TW.json entry - fallback will work but QA will flag it.
- Do NOT call i18n.changeLanguage without also writing localStorage - the next reload will revert.
- Do NOT nest namespaces more than two levels deep - keep the JSON flat.

## References

- realtime_ai_whiteboard/web-app/src/i18n/index.ts - i18next init.
- realtime_ai_whiteboard/web-app/src/i18n/en.json - English bundle.
- realtime_ai_whiteboard/web-app/src/i18n/zh-TW.json - Traditional Chinese bundle.
- realtime_ai_whiteboard/web-app/src/components/LanguageToggle.tsx - toggle component.
- realtime_ai_whiteboard/web-app/src/pages/BoardPage.tsx:22,89-104 - canonical t() usage with interpolation.
- realtime_ai_whiteboard/web-app/src/pages/AuthPage.tsx:11-24 - i18n.changeLanguage + localStorage pattern.

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
