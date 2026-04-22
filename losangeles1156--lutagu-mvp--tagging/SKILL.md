---
name: backend-tagging
description: > Use when this capability is needed.
metadata:
  author: losangeles1156
---

# L1 Tagging Engine Guide

本 Skill 定義地點 (POI) 的分類與標籤生成邏輯。

## 🎯 核心原則 (Core Directives)

1.  **3-5-8 策略**:
    - 所有 AI 生成的 tag 必須嚴格區分為 `Core` (3-4字), `Intent` (5-8字), `Visual` (視覺描述)。
    - 禁止混用。

2.  **L1 vs L3**:
    - **L1** = 主業 (賣什麼)。例如：餐廳、旅館。
    - **L3** = 設施 (有什麼)。例如：WiFi、廁所。
    - 嚴禁將「有廁所」作為 L1 標籤。

3.  **單一真理**:
    - 類別 ID 必須符合 `reference/l1-taxonomy.md` 中的定義。
    - 禁止發明新的主類別 (如 `food` 應為 `dining`)。

## 🧬 資料結構 (Data Structure)

```typescript
// 節點標籤結構
interface NodeTags {
  l1_category: 'dining' | 'shopping' | ...;
  l1_subcategory: string; // e.g., 'ramen'

  // 3-5-8 Tags
  tags_core: string[];    // ['拉麵', '豚骨']
  tags_intent: string[];  // ['深夜拉麵推薦', '濃厚湯頭']
  tags_visual: string[];  // ['日式吧台', '紅色招牌']
}
```

## 🔗 詳細資源

- [3-5-8 策略與分類表](./reference/l1-taxonomy.md)
- [Tag Generator Logic](./reference/tag-generator.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/losangeles1156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
