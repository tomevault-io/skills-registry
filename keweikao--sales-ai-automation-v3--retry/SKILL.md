---
name: retry
description: 重新觸發對話的轉錄和 MEDDIC 分析處理。當案件狀態為 failed 或 pending 時使用此 Skill 重新處理。 Use when this capability is needed.
metadata:
  author: keweikao
---

# /retry - 重試對話轉錄

## 描述
重新觸發對話的轉錄和 MEDDIC 分析處理。支援自動模式（智慧跳過已完成步驟）和完整模式（從頭開始）。

## 使用方式
```
/retry 202601-IC004           # 自動模式（智慧跳過）
/retry IC004                  # 簡短格式
/retry 004                    # 只輸入數字
/retry IC004 --full           # 完整模式（清除 transcript，從頭開始）
```

## 執行流程

**直接執行腳本，不需要額外步驟：**

```bash
bun run scripts/retry-conversation.ts <案件編號> [--full]
```

### 模式說明

| 模式 | 參數 | 行為 |
|------|------|------|
| auto（預設） | 無 | 如果 DB 已有 transcript，跳過下載/壓縮/轉錄，直接執行 MEDDIC 分析 |
| full | `--full` | 清除 transcript，從頭開始（下載→壓縮→轉錄→分析） |

### 何時使用完整模式
- 轉錄結果有問題（亂碼、截斷）
- 懷疑 Whisper 輸出品質不佳
- 音檔有更新需要重新轉錄

## 輸出範例

### 重試成功（自動模式，已有轉錄）
```
重試案件: 202602-IC098
模式: 自動（智慧跳過）
Server: https://sales-ai-server.salesaiautomationv3.workers.dev

案件: 202602-IC098
模式: auto
已有轉錄: 是
重試次數: 2
訊息: 已排入處理佇列（跳過轉錄，直接分析）

耗時: 156ms
```

### 重試成功（完整模式）
```
重試案件: 202602-IC098
模式: 完整（重新轉錄+分析）
Server: https://sales-ai-server.salesaiautomationv3.workers.dev

案件: 202602-IC098
模式: full
已有轉錄: 否
重試次數: 3
訊息: 已排入完整處理佇列（重新轉錄+分析）

耗時: 203ms
```

### 案件狀態不允許重試
```
API 錯誤 (400): 無法重試狀態為 completed 的對話
```

### 找不到案件
```
API 錯誤 (404): 找不到對話記錄
```

## 注意事項

1. **權限限制** - 此功能僅限管理員使用
2. **狀態限制** - 可重試 `failed`、`pending`、`transcribed` 狀態的案件
3. **處理時間** - 自動模式約 1-2 分鐘（跳過轉錄），完整模式約 2-5 分鐘
4. **查詢狀態** - 可使用 `/case <案件編號>` 查詢處理進度
5. **API Token** - 腳本會自動從 `apps/server/.env` 讀取 `API_TOKEN`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keweikao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
