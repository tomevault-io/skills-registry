---
name: vue-router-best-practices
description: Vue Router 4 模式、navigation guard、route params，以及 route component 生命週期互動。 Use when this capability is needed.
metadata:
  author: mm7246591
---

Vue Router 最佳實務、常見陷阱與導覽模式。

### Navigation Guard
- 在相同 route 但不同 params 之間導覽 → 參考 [router-beforeenter-no-param-trigger](reference/router-beforeenter-no-param-trigger.md)
- 在 beforeRouteEnter guard 中存取 component instance → 參考 [router-beforerouteenter-no-this](reference/router-beforerouteenter-no-this.md)
- Navigation guard 呼叫 API 但沒有 await → 參考 [router-guard-async-await-pattern](reference/router-guard-async-await-pattern.md)
- 使用者陷入無限 redirect 迴圈 → 參考 [router-navigation-guard-infinite-loop](reference/router-navigation-guard-infinite-loop.md)
- Navigation guard 使用已棄用的 next() 函式 → 參考 [router-navigation-guard-next-deprecated](reference/router-navigation-guard-next-deprecated.md)

### Route 生命週期
- 在相同 route 間導覽時資料過期 → 參考 [router-param-change-no-lifecycle](reference/router-param-change-no-lifecycle.md)
- Component unmount 後 event listener 仍然存在 → 參考 [router-simple-routing-cleanup](reference/router-simple-routing-cleanup.md)

### 設定
- 建置正式環境單頁應用程式 → 參考 [router-use-vue-router-for-production](reference/router-use-vue-router-for-production.md)

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
