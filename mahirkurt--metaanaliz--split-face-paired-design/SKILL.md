---
name: split-face-paired-design
description: Split-face / paired tasarımlarda veri çıkarımı, korelasyon varsayımı ve analiz SOP'u. Use when this capability is needed.
metadata:
  author: mahirkurt
---

# Split-face / Paired Design SOP

## Amaç

Split-face ve diğer eşleştirilmiş (paired) tasarımlarda bağımlı ölçümleri doğru ele almak (çift-kol bağımsız varsayımı yapmamak).

## Girdi

- Çalışma tasarım açıklaması (aynı bireyde iki müdahale vb.)
- Outcome tipi (continuous/binary)
- Varsa paired analiz çıktıları (paired t-test, McNemar, within-person difference)

## Çıktı

```json
{
  "study_id": "...",
  "design": "paired|split_face",
  "outcome": "...",
  "analysis_plan": {
    "preferred": "within_person_difference",
    "correlation_assumption": {"rho": 0.5, "sensitivity": [0.2, 0.5, 0.8]},
    "notes": ["rho raporlanmadıysa duyarlılık analizi yap"]
  },
  "extraction": {
    "n": 30,
    "mean_diff": null,
    "sd_diff": null,
    "arm_means": {"A": {"mean": 1.2, "sd": 0.5}, "B": {"mean": 1.5, "sd": 0.6}}
  }
}
```

## SOP

1. Öncelik: within-person difference (mean_diff, sd_diff).
2. sd_diff yoksa:
   - sdA, sdB ve rho ile sd_diff hesaplanabilir.
3. Paired binary outcomes: discordant pairs üzerinden hesap.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mahirkurt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
