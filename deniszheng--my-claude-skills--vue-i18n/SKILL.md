---
name: vue-i18n
description: Use when configuring internationalization in Vue3 projects, handling multi-language switching, or resolving i18n-related issues. Also use when working with vue-i18n, $t(), t(), locale, fallbackLocale, translate, language switch, i18n setup, or any i18n/translation/localization tasks in Vue applications.
metadata:
  author: DenisZheng
---

# Vue I18n Configuration

## Overview
Best practices for internationalization in Vue3 projects using vue-i18n@9 for multi-language support.

## When to Use
- Adding i18n support to new Vue3 projects
- Implementing language switching (Chinese/English/Japanese)
- Components not updating after language change
- Needing TypeScript type hints
- Optimizing performance with dynamic language loading
- Handling plurals, dates, numbers, or HTML content
- SSR hydration mismatches

## Quick Reference

| Feature | Key Config | Code |
|---------|-----------|------|
| Basic Setup | `legacy: false` | `createI18n({ legacy: false, locale: 'zh', messages: {...} })` |
| Language Switch | `locale.value` | `locale.value = 'en'` |
| Component Usage | `useI18n()` | `const { t } = useI18n()` |
| Persistence | localStorage | `localStorage.setItem('locale', locale)` |
| Plural | pipe `\|` | `"item": "no items \| one item \| {n} items"` |
| Date Format | `$d()` | `$d(date, 'short')` |
| Number Format | `$n()` | `$n(1234.56, 'currency')` |

## Core Pattern

### 1. Installation
```bash
npm install vue-i18n@9
```

### 2. Create i18n Instance
```javascript
// src/i18n/index.js
import { createI18n } from 'vue-i18n'
import en from './locales/en.json'
import zh from './locales/zh.json'

const i18n = createI18n({
  legacy: false,  // Required for Vue3
  locale: 'zh',
  fallbackLocale: 'en',
  messages: { en, zh },
  globalInjection: true
})

export default i18n
```

### 3. Register in Entry
```javascript
// main.js
import i18n from './i18n'
app.use(i18n)
```

### 4. Use in Components
```vue
<script setup>
import { useI18n } from 'vue-i18n'
const { t, locale } = useI18n()
</script>

<template>
  <h1>{{ t('header.title') }}</h1>
  <button @click="locale = 'en'">English</button>
</template>
```

## Language Switcher Component

```vue
<script setup>
import { useI18n } from 'vue-i18n'
const { locale } = useI18n()

const switchLang = (lang) => {
  locale.value = lang
  localStorage.setItem('locale', lang)
  document.documentElement.lang = lang
}
</script>

<template>
  <button @click="switchLang('zh')">中文</button>
  <button @click="switchLang('en')">English</button>
</template>
```

## Plurals

```json
// en.json
{
  "cart": {
    "item": "No items | One item | {n} items"
  }
}
// zh.json
{
  "cart": {
    "item": "无商品 | {n}件商品 | {n}件商品"
  }
}
```

```vue
<p>{{ t('cart.item', 5) }}</p>
```

## HTML Content

```vue
<i18n-t keypath="message.terms" tag="p">
  <template #link>
    <a href="/terms">Terms of Service</a>
  </template>
</i18n-t>
```

```json
{ "message": { "terms": "Please accept our {link}" } }
```

## Dynamic Language Loading

```javascript
export async function loadLanguageAsync(lang) {
  if (i18n.global.locale.value === lang) return

  if (!i18n.global.messages.value[lang]) {
    const messages = await import(`./locales/${lang}.json`)
    i18n.global.setLocaleMessage(lang, messages.default)
  }
  i18n.global.locale.value = lang
}
```

## Common Mistakes

### ❌ Forgetting legacy: false
```javascript
// Wrong - Vue2 mode
const i18n = createI18n({ locale: 'zh' })

// Correct - Vue3 mode
const i18n = createI18n({ legacy: false, locale: 'zh' })
```

### ❌ Components not reacting to language change
```vue
<!-- Wrong: static reference in template -->
<h1>{{ $t('title') }}</h1>

<!-- Correct: use reactive -->
<script setup>
const { t } = useI18n()
</script>
<h1>{{ t('title') }}</h1>
```

### ❌ Hardcoded language codes
```javascript
// Wrong
if (lang === 'zh') { ... }

// Correct
const supportedLangs = ['zh', 'en', 'ja']
if (supportedLangs.includes(lang)) { ... }
```

### ❌ Using wrong plural syntax
```json
// Wrong
"items": "{count} items"

// Correct (pipe separator)
"items": "No items | One item | {n} items"
```

### ❌ Missing fallbackLocale
```javascript
// Wrong
const i18n = createI18n({ locale: 'zh', messages: { zh } })

// Correct
const i18n = createI18n({ locale: 'zh', fallbackLocale: 'en', messages: { en, zh } })
```

## TypeScript Support

```typescript
// src/i18n/types.ts
export type MessageSchema = {
  header: { title: string; login: string }
  home: { welcome: string }
  errors: { required: string }
}

// In component
const { t } = useI18n<[MessageSchema]>()
const title = t('header.title')  // Has type hint
```

## Project Structure

```
src/
├── i18n/
│   ├── index.js          # i18n configuration
│   ├── types.ts          # TypeScript types
│   └── locales/
│       ├── en.json
│       └── zh.json
├── components/
│   └── LanguageSwitcher.vue
└── main.js
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/DenisZheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
