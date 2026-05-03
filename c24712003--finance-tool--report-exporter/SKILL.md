---
name: report-exporter
description: 將文字或 Markdown 報告轉換為 PDF、Word (DOCX) 或 Markdown 檔案。 Use when this capability is needed.
metadata:
  author: c24712003
---

# 報告匯出器 (Report Exporter)

此技能用於將分析報告或任何文字內容儲存為便於分享的格式（PDF, DOCX, Markdown）。

## 使用指令
1. 當需要儲存、匯出或格式化報告時呼叫此 Skill。
2. 呼叫 `scripts/export_report.py`。

## 參數
- `input_content` (str): 報告的 Markdown 內容。
- `output_format` (str): `pdf`, `docx`, 或 `md`。
- `output_path` (str): 輸出的檔案路徑（包含檔名）。

- `python-docx`

## 報告內容規範 (Content Requirements)
為確保報告品質，所有傳入 `input_content` 的 Markdown 內容**必須**包含以下章節：

1.  **資料來源 (Data Sources)**
    *   詳列分析中引用的新聞、財報、網站連結。
    *   例如：`[1] Yahoo Finance`, `[2] 台塑化 2025 自結財報`。
2.  **使用的分析技能 (Applied AI Skills)**
    *   列出此次分析中具體運用的 AI Skill 名稱。
    *   例如：`tw-supply-chain-intel`, `tw-peer-valuation-benchmark`。

> [!IMPORTANT]
> 若輸入內容缺少上述章節，請在產生檔案前自動於內容末端加上提示：「*注意：本報告未標註資料來源或使用技能*」。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/c24712003) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
