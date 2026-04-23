---
name: note-writer
description: | Use when this capability is needed.
metadata:
  author: u9401066
---

# 筆記撰寫技能 (Note Writer)

## 描述

根據來源材料撰寫結構化筆記，支援多種學術筆記格式和模板。

## 觸發條件

- 「寫筆記」「整理重點」「摘要這篇」
- "write notes", "summarize", "create summary"
- 讀取 PDF 後需要整理內容

## 可用資源

### 筆記模板 (templates/)

```markdown
# templates/literature-note.md

# {title}

## 📋 基本資訊
- **作者**: {authors}
- **年份**: {year}
- **期刊**: {journal}
- **PMID**: {pmid}
- **DOI**: {doi}

## 🎯 研究目的
{objective}

## 📊 研究方法
- **設計**: {study_design}
- **樣本**: {sample_size}
- **介入**: {intervention}
- **對照**: {control}
- **主要指標**: {primary_outcome}

## 📈 主要結果
{results}

## 💡 重點摘要
1. {key_point_1}
2. {key_point_2}
3. {key_point_3}

## ⚠️ 限制
{limitations}

## 🔗 相關連結
- [PubMed]({pubmed_url})
- [PDF]({pdf_path})

## 📝 個人筆記
{personal_notes}

---
*建立時間: {created_at}*
```

### Agent 能力

Agent 會：
1. **識別文章結構** - 自動辨識 IMRAD 格式
2. **提取關鍵資訊** - 作者、年份、研究設計等
3. **摘要重點** - 用簡潔語言總結
4. **格式化引用** - 按指定格式產生引用

## 執行流程

### 1. 單篇筆記模式

```
輸入：PDF 內容 (來自 pdf-reader)
    ↓
Agent 分析文章結構
    ↓
填充筆記模板
    ↓
輸出：.md 筆記檔案
```

### 2. 比較筆記模式

```
輸入：多篇 PDF 內容
    ↓
Agent 提取各篇重點
    ↓
建立比較表格
    ↓
輸出：比較分析筆記
```

### 3. 主題整理模式

```
輸入：多篇相關文獻
    ↓
Agent 按主題分類
    ↓
整理各主題重點
    ↓
輸出：主題式筆記
```

## 輸出格式

### 格式 1：單篇文獻筆記

```markdown
# Remimazolam for ICU Sedation: A Systematic Review

## 📋 基本資訊
- **作者**: Smith J, Lee K, et al.
- **年份**: 2024
- **期刊**: Critical Care Medicine
- **PMID**: 38353755

## 🎯 研究目的
評估 remimazolam 在 ICU 鎮靜的效果與安全性

## 📊 研究方法
- **設計**: 系統性回顧 + Meta 分析
- **樣本**: 15 篇 RCT, 2,340 位病患
- **介入**: Remimazolam
- **對照**: Propofol, Midazolam, Dexmedetomidine

## 📈 主要結果
- 鎮靜達標率：OR 1.23 (95% CI: 1.05-1.44)
- 低血壓發生率：RR 0.78 (0.65-0.94)
- 甦醒時間：MD -15.2 min (p<0.001)

## 💡 重點摘要
1. Remimazolam 鎮靜效果與 propofol 相當
2. 血流動力學更穩定
3. 甦醒更快，適合短期鎮靜

## ⚠️ 限制
- 納入研究異質性高
- 長期使用數據不足
```

### 格式 2：比較分析筆記

```markdown
# 文獻比較：ICU 鎮靜藥物

## 比較表格

| 項目 | Remimazolam | Propofol | Dexmedetomidine |
|------|-------------|----------|-----------------|
| 起效時間 | 1-2 min | 1 min | 5-10 min |
| 代謝 | 酯酶水解 | 肝臟 | 肝臟 |
| 拮抗劑 | Flumazenil | 無 | 無 |
| 低血壓 | + | +++ | ++ |
| 呼吸抑制 | + | +++ | + |

## 各研究重點整理

### Smith 2024
- 大型 RCT，n=500
- 結論：Remimazolam 非劣於 propofol

### Lee 2023
- 回顧性研究，n=200
- 結論：甦醒時間顯著縮短
```

## 使用範例

**範例 1：單篇筆記**
```
用戶：「幫這篇論文寫筆記」
執行：
1. 分析 PDF 內容
2. 使用 literature-note 模板
3. 儲存到 notes/ 目錄
```

**範例 2：比較筆記**
```
用戶：「比較這三篇關於 remimazolam 的研究」
執行：
1. 讀取三篇 PDF
2. 提取關鍵資訊
3. 建立比較表格
4. 產出比較分析筆記
```

## 儲存位置

```
notes/
├── literature/
│   ├── smith-2024-remimazolam-review.md
│   ├── lee-2023-sedation-study.md
│   └── ...
├── comparisons/
│   └── icu-sedation-comparison.md
└── topics/
    └── remimazolam-overview.md
```

## 相關技能

- `pdf-reader` - 讀取 PDF
- `content-validator` - 驗證筆記準確性
- `report-writing` - 組合技能：完整報告撰寫

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/u9401066) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
