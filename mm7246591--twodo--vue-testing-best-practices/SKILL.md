---
name: vue-testing-best-practices
description: 用於 Vue.js 測試。涵蓋 Vitest、Vue Test Utils、component testing、mocking、testing patterns，以及 Playwright E2E 測試。 Use when this capability is needed.
metadata:
  author: mm7246591
---

Vue.js 測試最佳實務、模式與常見陷阱。

### 測試
- 為 Vue 3 專案設定測試基礎設施 → 參考 [testing-vitest-recommended-for-vue](reference/testing-vitest-recommended-for-vue.md)
- 重構 component 內部後測試經常失敗 → 參考 [testing-component-blackbox-approach](reference/testing-component-blackbox-approach.md)
- 測試因 race condition 間歇性失敗 → 參考 [testing-async-await-flushpromises](reference/testing-async-await-flushpromises.md)
- 使用 lifecycle hook 或 inject 的 composable 難以測試 → 參考 [testing-composables-helper-wrapper](reference/testing-composables-helper-wrapper.md)
- 測試中出現「injection Symbol(pinia) not found」錯誤 → 參考 [testing-pinia-store-setup](reference/testing-pinia-store-setup.md)
- async setup component 在測試中沒有 render → 參考 [testing-suspense-async-components](reference/testing-suspense-async-components.md)
- Snapshot 測試在功能壞掉時仍然通過 → 參考 [testing-no-snapshot-only](reference/testing-no-snapshot-only.md)
- 為 Vue app 選擇 end-to-end 測試框架 → 參考 [testing-e2e-playwright-recommended](reference/testing-e2e-playwright-recommended.md)
- 測試需要驗證 computed styles 或真實 DOM event → 參考 [testing-browser-vs-node-runners](reference/testing-browser-vs-node-runners.md)
- 測試以 defineAsyncComponent 建立的 component 失敗 → 參考 [async-component-testing](reference/async-component-testing.md)
- 在 wrapper query 中找不到 teleported modal content → 參考 [teleport-testing-complexity](reference/teleport-testing-complexity.md)

## 參考

- [Vue.js Testing Guide](https://vuejs.org/guide/scaling-up/testing)
- [Vue Test Utils](https://test-utils.vuejs.org/)
- [Vitest Documentation](https://vitest.dev/)
- [Playwright Documentation](https://playwright.dev/)

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
