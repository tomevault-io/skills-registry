---
name: blueprint-document-reference
description: 建立和編輯Adobe數位體驗Blueprint檔案的參考。 用於建立新Blueprint、新增Blueprint頁面，或使用者詢問Blueprint結構、區段、範本或參考Adobe Experience League時。 Use when this capability is needed.
metadata:
  author: adobedocs
---


# Blueprint檔案參考

使用此技能在此存放庫中建立或編輯藍圖檔案。 藍圖是可重複的實作，可解決既定的業務問題，並包含架構圖、技術考量及相關的Adobe Experience League檔案連結。

## 何時套用

- 建立新的Blueprint檔案或Blueprint概觀頁面
- 在現有Blueprint中新增或重組區段
- 連結或引述Adobe Experience League檔案
- 使用Blueprint慣例（前言、標題、圖表）對齊新內容

## 快速參考

1. **檔案目的**：藍圖提供系統和資料流程架構，以顯示Adobe Experience Platform和應用程式如何整合。 這些是視覺和技術性的，而不是行銷。
2. **區段**：使用範本中的標準區段；只有在不適用時才省略（請參閱[reference.md](reference.md)）。
3. **Experience League**：偏好將產品檔案、API、護欄和教學課程連結至Experience League。 使用完整的URL；請參閱[reference.md](reference.md)以瞭解URL模式與格式。
4. **存放庫結構**： Blueprint位於`help/blueprints/`下。 新增或移動Blueprint頁面時更新`help/blueprints/TOC.md`。

## 檔案範本

每個Blueprint頁面都應遵循此結構。 僅包含適用的區段。

```markdown
---
title: [Short descriptive title]
description: "[One sentence: what this blueprint shows and why it matters.]"
solution: [Product name, e.g. Real-Time Customer Data Platform, Journey Optimizer]
exl-id: [UUID - leave blank if new, this will be auto-generated as part of the Experience League publishing flow]
---
# [H1 - same as title or expanded]

[1–3 paragraphs: what the blueprint covers, key capabilities, and who it’s for.]

## Applications

* [Product 1]
* [Product 2]

## Use cases

* [Use case 1]
* [Use case 2]

## Prerequisites

[Bullets or short paragraphs: required products, config, or setup.]

## Architecture Diagram

<img src="[path to SVG/image]" alt="[Descriptive alt]" style="width:90%; border:1px solid #4a4a4a" class="modal-image" />

## Guardrails

[Link to Experience League guardrails and any blueprint-specific limits.]

## Implementation patterns

[Optional: named patterns with bullets.]

## Implementation steps

1. [Step with link to Experience League where relevant]
2. ...

## Implementation considerations

[Optional: identity, performance, security, etc.]

## Related documentation

[Grouped links to Experience League: product docs, APIs, tutorials.]
```

針對概觀或中心頁面，請使用較短的結構：簡介、使用案例（或標籤）、架構影像、案例/陣清單、先決條件、護欄、相關檔案。 如需範例，請參閱`help/blueprints/`中的現有概觀。

## Frontmatter

| 欄位 | 必填 | 附註 |
|-------|----------|--------|
| `title` | 是 | 簡短；根據Adobe樣式使用`[!DNL Product Name]`作為產品名稱 |
| `description` | 是 | 一個句子；用於搜尋和卡片 |
| `solution` | 是 | 主要產品（例如Real-Time Customer Data Platform、Journey Optimizer） |
| `exl-id` | 是 | UUID；新頁面請留空 |
| `doc-type` | 概觀 | 針對主要Blueprint概觀頁面使用`overview-page` |
| `kt` | 可選 | 知識基礎文章ID （如果已連結） |

## 引用Adobe Experience League

- **何時連結**：連結至Experience League以取得產品檔案、API參考、護欄、教學課程和設定步驟。 不要重複冗長的程式；摘要並連結。
- **URL格式**：使用完整的URL。 偏好`https://experienceleague.adobe.com/docs/?lang=zh-Hant...`或`https://experienceleague.adobe.com/zh-hant/docs/...`。 對於開發人員檔案，`https://developer.adobe.com/...`也是有效的。
- **連結文字**：使用描述性文字（例如，「[建立結構描述] (url)」而非「按一下這裡」）。 對於連結文字中的產品名稱，請視情況使用`[!DNL Product Name]`。
- **相關檔案區段**：以「相關檔案」區段結束Blueprint，依類別群組連結（例如目的地設定、SDK檔案、設定檔和區段、教學課程）。

如需詳細的URL模式、連結分組和範例，請參閱[reference.md](reference.md)。

## 提交前的檢查清單

- [ ] Frontmatter有`title`、`description`、`solution`、`exl-id`
- [ ] H1符合或適當展開標題
- [ ]呈現架構圖表和替代文字描述性
- [ ]實作步驟連結至程式上線的Experience League
- [ ]護欄區段連結至官方Experience League護欄檔案
- [ ]相關檔案區段包含相關Experience League連結
- [ ]個新頁面或已移動的頁面會反映在`help/blueprints/TOC.md`中

## 其他資源

- 完整範本和章節附註： [reference.md](reference.md)
- 現有的Blueprint： `help/blueprints/` （例如`audience-activation/real-time-lookup.md`、`customer-journeys/journey-optimizer/journey-optimizer-overview.md`）
- 目錄與導覽： `help/blueprints/TOC.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adobedocs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
