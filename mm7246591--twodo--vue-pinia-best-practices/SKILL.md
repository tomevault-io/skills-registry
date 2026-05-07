---
name: vue-pinia-best-practices
description: Pinia store、狀態管理模式、store 設定，以及 store 的響應性處理。 Use when this capability is needed.
metadata:
  author: mm7246591
---

Pinia 最佳實務、常見陷阱與狀態管理模式。

### Store 設定
- 啟動時出現「getActivePinia was called」錯誤 → 參考 [pinia-no-active-pinia-error](reference/pinia-no-active-pinia-error.md)
- Setup store 在 DevTools 或 SSR 中缺少 state → 參考 [pinia-setup-store-return-all-state](reference/pinia-setup-store-return-all-state.md)

### 響應性
- 解構 store 後 UI 不再響應式更新 → 參考 [pinia-store-destructuring-breaks-reactivity](reference/pinia-store-destructuring-breaks-reactivity.md)
- 在 template 呼叫 store method 時遺失 context → 參考 [store-method-binding-parentheses](reference/store-method-binding-parentheses.md)

### 狀態模式
- 篩選條件重新整理後重置，或無法分享 → 參考 [state-url-for-ephemeral-filters](reference/state-url-for-ephemeral-filters.md)
- 建置正式環境應用時缺少 DevTools 或一致慣例 → 參考 [state-use-pinia-for-large-apps](reference/state-use-pinia-for-large-apps.md)

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
