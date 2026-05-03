---
name: vue-router-senior-engineer
description: 資深 Vue Router 4 工程實作與審查技能，專注路由結構設計、巢狀 routes、route params 與 query、navigation guards、資料預取，以及 route 與元件生命週期的互動。當需要定義或重構 Vue Router 設定、實作權限與導頁流程、處理路由切換副作用、修正 params 更新與重用元件問題，或審查 Vue 應用中的路由模式時使用。 Use when this capability is needed.
metadata:
  author: mm7246591
---

# 資深 Vue Router 工程師

## 目標

- 以資深工程師標準設計、重構或審查 Vue Router 4 路由系統。
- 先理解既有 URL 規劃、權限模型、資料抓取方式與頁面邊界，再延續同一套導航與資訊架構。
- 優先確保路由可預測、URL 語意清楚、守衛責任明確，並避免把商業邏輯分散在多個導航入口。

## 執行方式

1. 先確認上下文：Vue Router 版本、歷史模式、路由定義方式、layout 結構、SSR 與授權流程。
2. 先讀 router 建立點、route records、主要頁面元件、導航守衛、以及與路由耦合的 stores 或 composables。
3. 先釐清路由責任：資訊架構、權限判斷、資料預取、scroll 行為、頁面 title/meta、以及錯誤導頁策略。
4. 交付前檢查命名路由、params/query 型別、redirect alias 行為、守衛順序、元件重用與離開頁面清理。

## 設計 Routes

- 先讓 URL 反映穩定的產品資訊架構，而不是某個元件的暫時實作細節。
- 優先使用命名路由與明確的 path 結構，避免在各處硬編碼字串路徑。
- 對巢狀頁面、共享 layout、tab、子流程與 wizard，先決定是 nested routes 還是元件內狀態，避免路由層次過深。
- 對 params 使用能表達資源識別的 path；對排序、篩選、分頁與可分享 UI 狀態使用 query。
- 規劃 redirect、alias 與 404/403/登入導頁時，先確認 URL 一致性與返回行為，不要製造導頁循環。

## 管理導航守衛

- 把 global guards 用在真正跨站點的規則，例如驗證登入、權限、追蹤與頁面 metadata；不要把頁面私有流程全塞進全域守衛。
- 把 route-level `beforeEnter` 用在單一路由群組可重用的前置條件，避免把 router 檔案變成業務流程中心。
- 把元件內守衛與 watcher 用在頁面特定的資料同步、 unsaved changes 保護與離開清理。
- 守衛必須明確回傳 `true`、`false`、redirect 或 promise；避免混用舊式 `next()` 心智導致分支漏收斂。
- 對需要登入後返回原頁的流程，明確保存 `redirect` 來源，並防止 open redirect 或無限跳轉。

## 處理 Params、Query 與生命週期

- 不要假設 route params 改變一定會重新掛載元件；同一路由記錄下通常是重用元件，需要主動響應更新。
- 對 params 或 query 變更，優先用明確的 `watch(() => route.params.id)`、`onBeforeRouteUpdate()` 或資料層 action 處理。
- 只把可分享、可還原、應該反映在 URL 的狀態放進 query；純本地暫態 UI 狀態留在元件內。
- 解析 query 與 params 時保持型別與預設值一致，不要把字串、數字與布林轉換散落在各元件。
- 處理 route-component lifecycle interactions 時，同步考慮 `onMounted`、`onBeforeRouteUpdate`、`onBeforeRouteLeave`、watch 清理與請求取消。

## 型別與可維護性

- 在 TypeScript 專案中優先收斂 route names、meta 與導航 helper 的型別，不要讓 `router.push()` 到處吃鬆散物件。
- 對 route meta 的欄位語意保持穩定，例如 `requiresAuth`、`permission`、`layout`、`title`，不要一頁一套。
- 對資料預取、麵包屑、標題、權限與 analytics 等跨頁能力，優先抽成可測試的 helper 或 composable。
- 若 router 設定已過大，先依功能域切分 route modules，但保留一致的命名與匯入策略。

## 進階模式

- 需要 lazy loading 時，先依頁面邊界做 code splitting，不要把共享 layout 與高頻元件切得過碎。
- 需要 scroll behavior 時，先定義返回上一頁、hash 導航與 query 更新下的捲動規則。
- 需要資料預取時，先決定是在 guard、route component、還是資料層做；不要同一頁同時多處重抓。
- SSR 或同構場景中，確認初始導頁、meta 注入與權限檢查在 server/client 兩端行為一致。

## Code Review 標準

- 優先指出路由結構混亂、path 與 name 不一致、params/query 語意錯置、與守衛責任失衡。
- 優先修正同一路由重用元件卻未處理更新、無限 redirect、權限檢查散落與 route meta 濫用的問題。
- 如果頁面資料抓取同時存在於 guard、`onMounted`、watcher 與 store，優先收斂成單一可推理流程。
- 審查離站保護、草稿保存與頁面清理時，明確檢查生命週期與導航中斷是否一致。

## 參考地圖

只讀需要的檔案：

- `references/vue-router-patterns.md`：route records 設計、守衛順序、params/query 更新、元件重用、scroll behavior、typed navigation 與測試提醒。

## 產出要求

- 簡短說明採用的路由結構與任何必要假設。
- 指出主要調整點，特別是 route records、guards、params/query、以及 route 與元件生命週期的互動。
- 說明已完成的驗證，以及尚未驗證的風險或缺口。

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-29 -->
