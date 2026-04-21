---
name: i18n-implementation-pattern
description: Guidelines for implementing internationalization with next-intl 4.x Use when this capability is needed.
metadata:
  author: archer102125220
---

# i18n Implementation Pattern

## 🎯 When to Use This Skill

Use this skill when:
- Creating new pages with i18n
- Implementing translations in Server Components
- Fixing i18n-related errors
- **Getting "locale not set" errors**
- Migrating from next-intl 3.x to 4.x

## 📋 Correct Pattern (next-intl 4.x)

### Server Component Pattern (MANDATORY)

```typescript
import { getTranslations, setRequestLocale } from 'next-intl/server';

type Props = {
  params: Promise<{ locale: string }>;
};

export default async function Page({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ MUST call before getTranslations
  
  const t = await getTranslations('pages.myPage');
  
  return <h1>{t('title')}</h1>;
}
```

**Critical Rules**:
1. ✅ Page MUST receive `params: Promise<{ locale: string }>`
2. ✅ MUST `await params` before accessing `locale`
3. ✅ MUST call `setRequestLocale(locale)` BEFORE `getTranslations`
4. ✅ MUST `await getTranslations()`

---

## ✅ Correct Examples

### Example 1: Basic Page with Translation

```typescript
// ✅ CORRECT - Full pattern
// app/[locale]/dashboard/page.tsx
import { getTranslations, setRequestLocale } from 'next-intl/server';

type Props = {
  params: Promise<{ locale: string }>;
};

export default async function DashboardPage({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ BEFORE getTranslations
  
  const t = await getTranslations('pages.dashboard');
  
  return (
    <div>
      <h1>{t('title')}</h1>
      <p>{t('description')}</p>
    </div>
  );
}
```

---

### Example 2: Multiple Translation Namespaces

```typescript
// ✅ CORRECT - Multiple namespaces
export default async function Page({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ Call once
  
  const t = await getTranslations('pages.home');
  const tCommon = await getTranslations('common');
  
  return (
    <div>
      <h1>{t('title')}</h1>
      <button>{tCommon('buttons.submit')}</button>
    </div>
  );
}
```

---

### Example 3: Page with Dynamic Params

```typescript
// ✅ CORRECT - With dynamic params
type Props = {
  params: Promise<{ locale: string; id: string }>;
};

export default async function ProductPage({ params }: Props) {
  const { locale, id } = await params;
  setRequestLocale(locale);  // ✅ BEFORE getTranslations
  
  const t = await getTranslations('pages.product');
  const product = await getProduct(id);
  
  return (
    <div>
      <h1>{t('title', { name: product.name })}</h1>
      <p>{t('description')}</p>
    </div>
  );
}
```

---

### Example 4: Layout with Translation

```typescript
// ✅ CORRECT - Layout pattern
// app/[locale]/layout.tsx
import { setRequestLocale } from 'next-intl/server';

type Props = {
  children: React.ReactNode;
  params: Promise<{ locale: string }>;
};

export default async function LocaleLayout({ children, params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ Set locale for layout
  
  return (
    <html lang={locale}>
      <body>{children}</body>
    </html>
  );
}
```

---

## ❌ Common Mistakes

### Mistake 1: Missing setRequestLocale

```typescript
// ❌ WRONG - Missing setRequestLocale
export default async function Page({ params }: Props) {
  const { locale } = await params;
  const t = await getTranslations('pages.myPage');  // ❌ Error: locale not set
  return <h1>{t('title')}</h1>;
}

// ✅ CORRECT
export default async function Page({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ MUST call first
  const t = await getTranslations('pages.myPage');
  return <h1>{t('title')}</h1>;
}
```

---

### Mistake 2: Not awaiting params

```typescript
// ❌ WRONG - Not awaiting params
export default async function Page({ params }: Props) {
  const { locale } = params;  // ❌ Should await params
  setRequestLocale(locale);
  const t = await getTranslations('pages.myPage');
  return <h1>{t('title')}</h1>;
}

// ✅ CORRECT
export default async function Page({ params }: Props) {
  const { locale } = await params;  // ✅ Await params
  setRequestLocale(locale);
  const t = await getTranslations('pages.myPage');
  return <h1>{t('title')}</h1>;
}
```

---

### Mistake 3: Wrong Props Type

```typescript
// ❌ WRONG - Synchronous params
type Props = {
  params: { locale: string };  // ❌ Should be Promise
};

// ✅ CORRECT
type Props = {
  params: Promise<{ locale: string }>;  // ✅ Promise
};
```

---

### Mistake 4: setRequestLocale After getTranslations

```typescript
// ❌ WRONG - setRequestLocale after getTranslations
export default async function Page({ params }: Props) {
  const { locale } = await params;
  const t = await getTranslations('pages.myPage');  // ❌ Error
  setRequestLocale(locale);  // ❌ Too late
  return <h1>{t('title')}</h1>;
}

// ✅ CORRECT
export default async function Page({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ BEFORE getTranslations
  const t = await getTranslations('pages.myPage');
  return <h1>{t('title')}</h1>;
}
```

---

## 📝 Checklist

### Before Creating Page

- [ ] Page receives `params: Promise<{ locale: string }>`
- [ ] Awaited params before accessing locale
- [ ] Called `setRequestLocale(locale)` BEFORE `getTranslations`
- [ ] Awaited `getTranslations()`

### When Fixing Errors

- [ ] Verified `setRequestLocale` is called
- [ ] Verified `setRequestLocale` is called BEFORE `getTranslations`
- [ ] Verified params is awaited
- [ ] Verified Props type uses `Promise<{ locale: string }>`

---

## 💡 Pro Tips

### Tip 1: Order Matters

The order is critical:

```typescript
// ✅ CORRECT ORDER
const { locale } = await params;     // 1. Await params
setRequestLocale(locale);            // 2. Set locale
const t = await getTranslations();  // 3. Get translations
```

---

### Tip 2: Call setRequestLocale Once

You only need to call `setRequestLocale` once per page/layout:

```typescript
// ✅ CORRECT - Call once
export default async function Page({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ Once
  
  const t1 = await getTranslations('namespace1');
  const t2 = await getTranslations('namespace2');  // ✅ No need to call setRequestLocale again
  
  return <div>...</div>;
}
```

---

### Tip 3: Use Type-Safe Translation Keys

```typescript
// ✅ GOOD - Type-safe keys
const t = await getTranslations('pages.home');
t('title');  // ✅ Type-checked

// ❌ BAD - String literals
t('pages.home.title');  // ❌ Not type-checked
```

---

### Tip 4: Translation with Variables

```typescript
// ✅ GOOD - With variables
const t = await getTranslations('pages.product');
<h1>{t('title', { name: product.name })}</h1>

// messages/en.json
{
  "pages": {
    "product": {
      "title": "Product: {name}"
    }
  }
}
```

---

### Tip 5: next-intl 3.x vs 4.x Migration

**next-intl 3.x** (Old):
```typescript
// ❌ OLD - next-intl 3.x
export default async function Page({ params: { locale } }: Props) {
  const t = await getTranslations('pages.myPage');
  return <h1>{t('title')}</h1>;
}
```

**next-intl 4.x** (New):
```typescript
// ✅ NEW - next-intl 4.x
export default async function Page({ params }: Props) {
  const { locale } = await params;
  setRequestLocale(locale);  // ✅ Required in 4.x
  const t = await getTranslations('pages.myPage');
  return <h1>{t('title')}</h1>;
}
```

---

## 🔗 Related Rules

- `.agent/rules/i18n.md`
- `GEMINI.md` - Internationalization section
- `CLAUDE.md` - i18n Implementation section

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archer102125220) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
