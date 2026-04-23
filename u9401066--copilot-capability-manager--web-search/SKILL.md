---
name: web-search
description: | Use when this capability is needed.
metadata:
  author: u9401066
---

# 網路檢索技能

## 描述
透過網路搜尋相關資源、文獻、技術文檔等資料。

## 觸發條件
- 「搜尋 XXX」
- 「找一下 XXX 的資料」
- 「查找相關文獻」
- 需要外部資料時

## 能力範圍

### 可搜尋資源
- 技術文檔 (MDN, Stack Overflow, GitHub)
- 學術文獻 (PubMed, Google Scholar)
- API 參考文檔
- 最新技術資訊

### 輸出格式
```
🔍 搜尋結果：[關鍵字]

📚 找到 N 筆相關資料：

1. [標題]
   來源：[URL]
   摘要：[簡短摘要]
   相關度：⭐⭐⭐⭐⭐

2. [標題]
   ...
```

## 使用範例
```
「搜尋 Python async 最佳實踐」
「找一下 React 18 新功能」
「查找 Docker Compose 設定範例」
```

## 配合工具
- `fetch_webpage` - 抓取網頁內容
- `mcp_pubmed_search_*` - PubMed 文獻搜尋

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/u9401066) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
