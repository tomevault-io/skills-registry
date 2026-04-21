---
name: havsan-data-science
description: name: havsan-data-science Use when this capability is needed.
metadata:
  author: atifertugrul-kan
---
﻿---
version: 3.1.6
name: havsan-data-science
description: Python/R veri analizi projeleri için rehber. Jupyter Notebook, pandas, numpy kullanımı ve Dockerize çalışma standartlarını içerir.
---

# 🐍 HAVSAN Data Science Skill

Bu beceri, veri analizi ve bilimsel hesaplama projelerini HAVSAN standartlarına göre yönetir.

## 🔬 Çalışma Standartları
1. **Jupyter Notebook:** Analizler için `.ipynb` dosyalarını kullan.
2. **Dockerize Analiz:** Tüm kütüphaneler (pandas, scipy, scikit-learn) Docker imajı içinde kurulu olmalıdır.
3. **Veri Ayrımı:** Ham veriler (raw) ve işlenmiş veriler (processed) ayrı dizinlerde tutulmalıdır.

## 📂 Klasör Yapısı
```
proje/
├── notebooks/         # Analiz defterleri
├── data/
│   ├── raw/           # Değiştirilemez ham veri
│   └── processed/     # Temizlenmiş veri
├── src/               # Tekrar kullanılabilir Python kodları
└── docker-compose.yml
```

## 📊 Önce Analiz (Docs-First)
Veriyi yüklemeden önce analizin hipotezleri ve yöntemleri `docs/ANALIZ/data-plan.md` altında tanımlanmalıdır.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atifertugrul-kan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
