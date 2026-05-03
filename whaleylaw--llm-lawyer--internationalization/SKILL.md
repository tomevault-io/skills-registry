---
name: internationalization
description: Use when implementing multi-language support, adding translations, handling locale-specific formatting, or fixing i18n issues - focuses on react-i18next and localization
metadata:
  author: Whaleylaw
---

# Internationalization

## When to Use

- Adding multi-language support to app
- Implementing translation system
- Handling locale-specific formatting (dates, numbers, currency)
- Switching languages dynamically
- Managing translation files

## Process

### 1. Setup i18next

☐ Install i18next and react-i18next
☐ Create i18n configuration file
☐ Initialize i18next with resources
☐ Wrap app with I18nextProvider

```bash
npm install i18next react-i18next i18next-browser-languagedetector
```

```tsx
// lib/i18n.ts
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import LanguageDetector from 'i18next-browser-languagedetector';

import en from '../locales/en.json';
import es from '../locales/es.json';
import fr from '../locales/fr.json';

i18n
  .use(LanguageDetector) // Detect user language
  .use(initReactI18next) // Bind react-i18next
  .init({
    resources: {
      en: { translation: en },
      es: { translation: es },
      fr: { translation: fr },
    },
    fallbackLng: 'en',
    lng: 'en', // Default language
    interpolation: {
      escapeValue: false, // React already escapes
    },
  });

export default i18n;
```

```tsx
// main.tsx
import './lib/i18n';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### 2. Create Translation Files

☐ Create `locales/` directory
☐ Create JSON file for each language
☐ Structure translations by namespace (optional)
☐ Use consistent key naming

```json
// locales/en.json
{
  "welcome": "Welcome",
  "auth": {
    "login": "Log in",
    "signup": "Sign up",
    "email": "Email address",
    "password": "Password",
    "forgotPassword": "Forgot password?"
  },
  "dashboard": {
    "title": "Dashboard",
    "stats": {
      "users": "Total Users",
      "revenue": "Revenue"
    }
  },
  "common": {
    "save": "Save",
    "cancel": "Cancel",
    "delete": "Delete",
    "confirm": "Are you sure?"
  }
}
```

```json
// locales/es.json
{
  "welcome": "Bienvenido",
  "auth": {
    "login": "Iniciar sesión",
    "signup": "Registrarse",
    "email": "Correo electrónico",
    "password": "Contraseña",
    "forgotPassword": "¿Olvidaste tu contraseña?"
  },
  "dashboard": {
    "title": "Panel de control",
    "stats": {
      "users": "Total de usuarios",
      "revenue": "Ingresos"
    }
  },
  "common": {
    "save": "Guardar",
    "cancel": "Cancelar",
    "delete": "Eliminar",
    "confirm": "¿Estás seguro?"
  }
}
```

### 3. Use Translations in Components

☐ Import `useTranslation` hook
☐ Use `t()` function for translations
☐ Handle interpolation for dynamic values
☐ Use pluralization when needed

```tsx
// components/LoginForm.tsx
import { useTranslation } from 'react-i18next';

export function LoginForm() {
  const { t } = useTranslation();

  return (
    <form>
      <h2>{t('auth.login')}</h2>

      <label>{t('auth.email')}</label>
      <input type="email" />

      <label>{t('auth.password')}</label>
      <input type="password" />

      <a href="/forgot-password">{t('auth.forgotPassword')}</a>

      <button type="submit">{t('common.save')}</button>
    </form>
  );
}
```

```tsx
// Interpolation with dynamic values
const { t } = useTranslation();

// Translation: "Hello, {{name}}!"
<p>{t('greeting', { name: userName })}</p>

// Translation: "You have {{count}} message" / "You have {{count}} messages"
<p>{t('messageCount', { count: messages.length })}</p>
```

### 4. Create Language Switcher

☐ Create language selector component
☐ Get current language with `i18n.language`
☐ Change language with `i18n.changeLanguage()`
☐ Persist language preference

```tsx
// components/LanguageSwitcher.tsx
import { useTranslation } from 'react-i18next';

const languages = [
  { code: 'en', name: 'English' },
  { code: 'es', name: 'Español' },
  { code: 'fr', name: 'Français' },
];

export function LanguageSwitcher() {
  const { i18n } = useTranslation();

  const changeLanguage = (lng: string) => {
    i18n.changeLanguage(lng);
    localStorage.setItem('language', lng);
  };

  return (
    <select
      value={i18n.language}
      onChange={(e) => changeLanguage(e.target.value)}
      className="border rounded px-2 py-1"
    >
      {languages.map((lang) => (
        <option key={lang.code} value={lang.code}>
          {lang.name}
        </option>
      ))}
    </select>
  );
}
```

### 5. Format Dates, Numbers, Currency

☐ Use Intl API for formatting
☐ Create formatting utility functions
☐ Use user's locale for formatting
☐ Handle timezone differences

```tsx
// lib/formatters.ts
export function formatDate(date: Date, locale: string): string {
  return new Intl.DateTimeFormat(locale, {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  }).format(date);
}

export function formatNumber(num: number, locale: string): string {
  return new Intl.NumberFormat(locale).format(num);
}

export function formatCurrency(amount: number, locale: string, currency: string): string {
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency,
  }).format(amount);
}
```

```tsx
// Usage in component
import { useTranslation } from 'react-i18next';
import { formatDate, formatCurrency } from '../lib/formatters';

function Dashboard() {
  const { i18n } = useTranslation();

  const date = new Date();
  const revenue = 12345.67;

  return (
    <div>
      <p>{formatDate(date, i18n.language)}</p>
      <p>{formatCurrency(revenue, i18n.language, 'USD')}</p>
    </div>
  );
}
```

### 6. Handle Pluralization

☐ Define plural forms in translations
☐ Use count parameter in t() function
☐ Handle zero, one, other cases

```json
// locales/en.json
{
  "itemCount_zero": "No items",
  "itemCount_one": "{{count}} item",
  "itemCount_other": "{{count}} items"
}
```

```tsx
// Usage
const { t } = useTranslation();

<p>{t('itemCount', { count: 0 })}</p>  // "No items"
<p>{t('itemCount', { count: 1 })}</p>  // "1 item"
<p>{t('itemCount', { count: 5 })}</p>  // "5 items"
```

### 7. Lazy Load Translations

☐ Split translations by page/feature
☐ Load translations on demand
☐ Use i18next backend plugin
☐ Show loading state while fetching

```tsx
// lib/i18n.ts with lazy loading
import Backend from 'i18next-http-backend';

i18n
  .use(Backend)
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    backend: {
      loadPath: '/locales/{{lng}}/{{ns}}.json',
    },
    fallbackLng: 'en',
    ns: ['common', 'auth', 'dashboard'],
    defaultNS: 'common',
  });
```

### 8. Verify Internationalization Works

☐ Test all languages render correctly
☐ Test language switcher changes UI language
☐ Test date/number/currency formatting for each locale
☐ Test pluralization with different counts
☐ Test missing translation fallbacks
☐ Verify RTL languages (if supported)

## Common Pitfalls

**Hardcoded strings:**
```tsx
// ❌ Wrong - hardcoded string
<button>Save</button>

// ✅ Right - translated
<button>{t('common.save')}</button>
```

**Not using interpolation:**
```tsx
// ❌ Wrong - concatenating strings
<p>{"Hello, " + userName + "!"}</p>

// ✅ Right - using interpolation
<p>{t('greeting', { name: userName })}</p>
```

**Formatting with wrong locale:**
```tsx
// ❌ Wrong - hardcoded locale
const formatted = new Intl.DateTimeFormat('en-US').format(date);

// ✅ Right - using current locale
const { i18n } = useTranslation();
const formatted = new Intl.DateTimeFormat(i18n.language).format(date);
```

## Red Flags

**Never:**
- Hardcode user-facing strings (always use translations)
- Concatenate translated strings (use interpolation)
- Format dates/numbers without locale (use Intl API)
- Skip testing in all supported languages
- Translate technical terms or brand names

**Always:**
- Use `t()` function for all user-facing text
- Structure translations in logical namespaces
- Provide fallback language (usually English)
- Test language switching thoroughly
- Use Intl API for locale-specific formatting
- Persist user's language preference
- Handle missing translations gracefully

---
> Source: [Whaleylaw/llm-lawyer](https://github.com/Whaleylaw/llm-lawyer) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
