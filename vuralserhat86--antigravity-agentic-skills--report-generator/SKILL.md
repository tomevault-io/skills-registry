---
name: report-generator
description: Executive rapor, stakeholder presentation ve comprehensive documentation oluşturma rehberi. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 📄 Report Generator

> Executive rapor ve dokümantasyon rehberi.

---

## 📋 Report Türleri

| Tür | Hedef Kitle | Uzunluk |
|-----|-------------|---------|
| Executive Summary | C-level | 1-2 sayfa |
| Technical Report | Engineers | 5-10 sayfa |
| Project Status | Stakeholders | 1-3 sayfa |
| Analysis Report | Decision makers | 3-5 sayfa |

---

## 🔧 Executive Report Template

```markdown
# [Report Title]

**Date:** [Date]
**Author:** [Name]
**Status:** Draft | Final

---

## Executive Summary
[2-3 paragraf - key points only]

## Background
[Neden bu rapor yazıldı]

## Key Findings
1. **Finding 1**: [Özet]
2. **Finding 2**: [Özet]
3. **Finding 3**: [Özet]

## Recommendations
| # | Recommendation | Priority | Timeline |
|---|----------------|----------|----------|
| 1 | [Action] | High | Q1 |
| 2 | [Action] | Medium | Q2 |

## Next Steps
1. [Step 1]
2. [Step 2]

## Appendix
[Detaylı data, charts]
```

---

## 📊 Visual Elements

### Data Presentation
```
Use charts for:
- Trends → Line chart
- Comparisons → Bar chart
- Parts of whole → Pie chart
- Relationships → Scatter plot
```

### Status Indicators
- 🟢 On track / Positive
- 🟡 At risk / Neutral
- 🔴 Off track / Negative

---

## 🎯 Writing Tips

| Do | Don't |
|-----|-------|
| Lead with insights | Bury key points |
| Use bullet points | Write long paragraphs |
| Include visuals | Text-only walls |
| Action-oriented | Passive voice |
| Specific numbers | Vague statements |

---

*Report Generator v1.1 - Enhanced*

## 🔄 Workflow

> **Kaynak:** [Pandas Reporting](https://pandas.pydata.org/docs/user_guide/style.html) & [WeasyPrint Docs](https://doc.courtbouillon.org/weasyprint/)

### Aşama 1: Data Preparation (Automated)
- [ ] **Validation**: Gelen veriyi (CSV/JSON/SQL) Pydantic veya Pandera ile şema kontrolünden geçir.
- [ ] **Aggregation**: Detay veriyi (Raw Data) özetle (Pivot Table, GroupBy). Asla milyon satırı rapora basma.
- [ ] **Anonymization**: Hassas verileri (PII) maskele veya hashle.

### Aşama 2: Generation Architecture
- [ ] **Template Engine**: Jinja2 (Python) veya Handlebars (JS) kullanarak logik ile sunumu ayır.
- [ ] **Format Agnostic**: İçeriği Markdown veya HTML olarak üret, sonra PDF/Excel'e çevir (Single Source).
- [ ] **Styling**: CSS (Print CSS) kullanarak sayfa yapısını (@page), header/footer'ı yönet.

### Aşama 3: Delivery & Feedback
- [ ] **Compression**: Çıktı dosyalarını (PDF/HTML) sıkıştır.
- [ ] **Distribution**: Raporu otomatik e-posta, Slack veya S3 bucket'a gönder.
- [ ] **Actionable**: Raporun başına "Executive Summary" ve "Action Items" ekle.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Rapor oluşturma süresi kabul edilebilir mi? (Async Job kullanılıyor mu?). |
| 2 | Mobil cihazlarda okunabilir mi? (HTML raporlar için Responsive Design). |
| 3 | Veriler güncel mi? (Rapor tarihi ve Veri çekim zamanı raporda yazıyor mu?). |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
