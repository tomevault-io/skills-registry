---
name: design-patterns
description: Visual hierarchy, z-index, shadows, animations ve white space kuralları. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🎭 Design Patterns

> Visual hierarchy, layering ve animation kuralları.

---

## ⚠️ Bu Skill vs `arch-patterns`

| Bu Skill | `arch-patterns` |
|----------|-----------------|
| **UI/UX** tasarım | **Sistem** mimarisi |
| Z-index, shadows | Microservices, CQRS |
| Animation, spacing | Database seçimi |

> **Kural:** UI tasarımı → bu skill, Sistem tasarımı → `arch-patterns`

---

## 📊 1. Visual Hierarchy

### Z-Index Scale
```
-1    - Behind content
0     - Base layer
10    - Dropdown menus
20    - Sticky headers
30    - Modal backdrop
40    - Modal content
50    - Tooltips
100   - Toast notifications
```

### Shadows (Elevation)
```
shadow-xs:  0 1px 2px rgba(0,0,0,0.05)
shadow-sm:  0 1px 3px rgba(0,0,0,0.1)
shadow-md:  0 4px 6px rgba(0,0,0,0.1)
shadow-lg:  0 10px 15px rgba(0,0,0,0.1)
shadow-xl:  0 20px 25px rgba(0,0,0,0.15)
```

---

## ⚡ 2. Animation & Transitions

### Duration Scale
```
75ms   - Instant (very subtle)
150ms  - Fast (hover states)
200ms  - Normal (default)
300ms  - Moderate (dropdown, modal)
500ms  - Slow (page transitions)
```

### Easing Functions
| Easing | Kullanım |
|--------|----------|
| ease-out | En yaygın (hover, click) |
| ease-in-out | Modal, dropdown |
| ease-in | Çıkış animasyonları |

---

## 📐 3. White Space Rules

### Content Density
| Tip | Spacing |
|-----|---------|
| Tight | 8-12px (data tables) |
| Normal | 16-24px (default) |
| Relaxed | 32-48px (marketing) |
| Spacious | 64px+ (premium) |

### Reading Width
- Optimal: 60-75 karakter (600-750px)
- Maximum: 90 karakter
- Minimum: 45 karakter

---

## 🔍 4. Focus States

```css
:focus-visible {
  outline: 2px solid var(--primary-500);
  outline-offset: 2px;
}
```

---

## 🔗 İlgili Skill'ler
- `design-tokens` - Spacing, typography
- `design-responsive` - Breakpoints, fluid

---

*Design Patterns v1.1 - Enhanced*

## 🔄 Workflow

> **Kaynak:** [Refactoring UI](https://www.refactoringui.com/) (Visual Hierarchy)

### Aşama 1: Foundation (Hierarchy)
- [ ] **Spacing**: Elemanları `8px` grid sistemine göre yerleştir.
- [ ] **Typography**: Başlık/Gövde oranını (Scale) belirle.
- [ ] **Contrast**: En önemli öğeyi (Primary Button) en yüksek kontrasta koy.

### Aşama 2: Interaction (Feedback)
- [ ] **States**: Hover, Focus, Active, Disabled durumlarını tasarla.
- [ ] **Motion**: 200ms default transition ile mikro-animasyon ekle.
- [ ] **Elevation**: Katmanları `shadow` ve `z-index` ile ayır.

### Aşama 3: Validation
- [ ] **A11y**: Renk kontrast oranları AA standardında mı?
- [ ] **Responsiveness**: Mobilde touch target'lar >44px mi?
- [ ] **Consistency**: Tüm butonlar aynı radius/padding değerine sahip mi?

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Sayfada nereye bakılacağı (Focal Point) net mi? |
| 2 | Focus ring klavye ile gezinirken görünüyor mu? |
| 3 | Animasyonlar performansı (FPS) düşürüyor mu? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
