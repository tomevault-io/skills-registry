---
name: design-components
description: Button, card, input ve icon sizing kuralları. Component boyutlandırma standartları. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🧩 Design Components

> Component boyutlandırma standartları.

---

## 🔘 1. Button Sizes

```
Small:   H:32px, P:8px/16px,  Font:14px
Medium:  H:40px, P:12px/24px, Font:16px (Default)
Large:   H:48px, P:14px/32px, Font:18px
XLarge:  H:56px, P:16px/40px, Font:20px
```

### Button States
| State | Stil |
|-------|------|
| Default | Base |
| Hover | Lighten/Darken 10%, Scale 1.02 |
| Active | Scale 0.98 |
| Focus | Ring outline |
| Disabled | Opacity 50% |

---

## 📦 2. Card Sizing

### Padding
| Tip | Padding |
|-----|---------|
| Compact | 16px |
| Default | 24px |
| Spacious | 32px |

### Shadow (Elevation)
```
shadow-sm:  0 1px 3px rgba(0,0,0,0.1)
shadow-md:  0 4px 6px rgba(0,0,0,0.1)
shadow-lg:  0 10px 15px rgba(0,0,0,0.1)
shadow-xl:  0 20px 25px rgba(0,0,0,0.1)
```

---

## 📝 3. Input Fields

```
Height:  40px (default), 48px (large)
Padding: 12px / 16px (V/H)
Border:  1px solid
Radius:  4px veya 8px
```

### Input States
| State | Stil |
|-------|------|
| Default | Border: neutral-300 |
| Focus | Border: primary-500, Ring |
| Error | Border: error-500 |
| Disabled | Background: neutral-100 |

---

## 🎯 4. Icon Sizes

```
16px - Inline with text
20px - Buttons
24px - Standalone
32px - Feature highlights
48px - Hero sections
```

### Icon + Text Spacing
- Icon ve text arası: 8px

---

## 📋 5. Form Layout

```
Label-Input gap:     8px
Input-Input gap:     16px veya 24px
Form section gap:    32px
Submit button margin: 24px top
```

---

## 🔄 Workflow

> **Kaynak:** [Atomic Design Methodology](https://atomicdesign.bradfrost.com/) & [Shadcn UI Component Standards](https://ui.shadcn.com/docs/components/button)

### Aşama 1: Component Definition & Atomic Audit
- [ ] **Inventory**: Mevcut arayüzdeki tekrarlayan elementleri (Button, Input) tespit et ve Atom'lara ayır.
- [ ] **State Mapping**: Her komponentin tüm state'lerini (Default, Hover, Active, Disabled, Loading) tanımla.
- [ ] **Accessibility (A11y)**: Aria-label ve role tanımlarının doğruluğunu denetle.

### Aşama 2: Sizing & Variants Setup
- [ ] **Base Unit Alignment**: Tüm boyutların 8-point grid (Design Tokens) sistemine uygunluğunu doğrula.
- [ ] **Variant Creation**: `Tailwind` veya `CVA` (Class Variance Authority) kullanarak variant yapılarını kur.
- [ ] **Visual Consistency**: Padding ve gap değerlerinin hiyerarşiye uygunluğunu kontrol et.

### Aşama 3: Testing & Documentation
- [ ] **Visual Testing**: Komponentin farklı tarayıcılarda ve viewports'larda görsel bütünlüğünü test et (Storybook).
- [ ] **Unit Testing**: Etkileşimli komponentler (Dropdown, Modal) için logic testleri yaz.
- [ ] **Handoff**: Tasarımın geliştiriciye aktarımı için dokümantasyonu (Design-to-Code) güncelle.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Komponent tek bir sorumluluğa (Single Responsibility) sahip mi? |
| 2 | Tüm variant'lar merkezi bir `tokens` dosyasından mı besleniyor? |
| 3 | Screen reader testleri başarılı mı? |

---
*Design Components v1.5 - With Workflow*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
