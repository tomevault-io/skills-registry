---
name: design-tokens
description: 8-point grid spacing, typography scale ve color system. Temel tasarım değişkenleri. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🎨 Design Tokens

> Temel tasarım değişkenleri: spacing, typography, colors.

---

## 📐 1. Spacing System (8-Point Grid)

### Temel Kural
Tüm boşluklar 8'in katları olmalı.

```
4px   - Minimum (micro)
8px   - XS
16px  - SM (icon-text arası)
24px  - MD (card içi)
32px  - LG (component'ler arası)
48px  - XL (section içi gruplar)
64px  - 2XL (section'lar arası)
96px  - 3XL (major section'lar)
128px - 4XL (hero padding)
```

### Padding Yapısı
| Element | Padding |
|---------|---------|
| Card/Container | 24px veya 32px |
| Button | 12px / 24px (V/H) |
| Input | 12px / 16px (V/H) |
| Section (Desktop) | 64px - 96px |
| Section (Mobile) | 32px - 48px |

---

## 🔤 2. Typography Scale

### Font Sizes
```
12px  - Caption / Helper
14px  - Small / Metadata
16px  - Body (Base)
20px  - Lead paragraph
24px  - H4
32px  - H3
40px  - H2
48px  - H1
64px  - Hero
```

### Line Height
| Tip | Oran |
|-----|------|
| Başlıklar (H1-H3) | 1.2 - 1.3 |
| Body text | 1.5 - 1.6 |
| Small text | 1.4 |
| Hero text | 1.1 |

### Font Weight
```
400 - Regular (Body)
500 - Medium (Subtle emphasis)
600 - Semibold (Subheadings, buttons)
700 - Bold (Headings)
800 - Extra bold (Hero)
```

---

## 🎨 3. Color System

### Contrast Ratios (WCAG)
| Tip | Minimum |
|-----|---------|
| Normal text | 4.5:1 |
| Large text (18px+) | 3:1 |
| UI components | 3:1 |
| AAA ideal | 7:1 |

### Palet Yapısı
```
Primary:   50, 100, 200...900, 950 (10 shade)
Secondary: 10 shades
Neutral:   10 shades
Success/Warning/Error/Info: 5 shades
```

### Opacity Scale
```
100% - Default
75%  - Disabled
60%  - Placeholder
40%  - Dividers
20%  - Subtle backgrounds
10%  - Hover overlays
```

---

## 📦 4. Border Radius

```
0px    - Sharp
4px    - Small (buttons)
8px    - Medium (cards) ← Default
16px   - Large (feature cards)
9999px - Full (pills, avatars)
```

---

## 🔄 Workflow

> **Kaynak:** [W3C Design Tokens Format](https://tr.designtokens.org/format/) & [Style Dictionary Best Practices](https://amzn.github.io/style-dictionary/)

### Aşama 1: Audit & Token Hierarchy
- [ ] **Color/Type Audit**: Marka renklerini ve tipografi ölçeğini (Scale) standartlaştır.
- [ ] **Classification**: Tokenlar'ı 3 seviyeye ayır: Primitive (Global), Semantic (Purpose) ve Component-specific.
- [ ] **Naming Convention**: `category-type-item-state` (örn: `action-primary-hover`) formatını uygula.

### Aşama 2: Definition & Tooling
- [ ] **JSON Definition**: Token'ları merkezi bir JSON dosyasında tanımla.
- [ ] **Multi-Platform Export**: `Style Dictionary` kullanarak tokens'ları CSS, JS ve Swift/Kotlin formatlarına dönüştür.
- [ ] **Theme Variation**: Dark/Light mode için semantik eşlemeleri yap.

### Aşama 3: Consumption & Maintenance
- [ ] **Implementation**: Kod içerisinde hardcoded değerleri token değişkenleriyle değiştir.
- [ ] **Version Control**: Token değişikliklerini merkezi tasarım kütüphanesi üzerinden takip et.
- [ ] **Governance**: Yeni eklenen renk veya boşluk değerlerinin sisteme uygunluğunu denetle.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Tüm renkler WCAG erişilebilirlik (Contrast) testinden geçti mi? |
| 2 | Token isimleri geliştirici ve tasarımcı için aynı anlamı taşıyor mu? |
| 3 | Token değişikliği tüm platformlarda otomatik güncelleniyor mu? |

---
*Design Tokens v1.5 - With Workflow*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
