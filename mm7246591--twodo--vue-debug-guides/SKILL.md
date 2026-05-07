---
name: vue-debug-guides
description: Vue 3 runtime errors、warnings、async failures，以及 SSR/hydration issues 的除錯與錯誤處理。診斷或修復 Vue 問題時使用。 Use when this capability is needed.
metadata:
  author: mm7246591
---

Vue 3 runtime issue、warning、async failure 與 hydration bug 的除錯與錯誤處理指南。
開發最佳實務與常見陷阱請使用 `vue-best-practices`。

### 響應性
- 追蹤非預期的重新 render 與 state update → 參考 [reactivity-debugging-hooks](reference/reactivity-debugging-hooks.md)
- 因缺少 .value 存取導致 ref value 沒有更新 → 參考 [ref-value-access](reference/ref-value-access.md)
- 解構 reactive object 後 state 停止更新 → 參考 [reactive-destructuring](reference/reactive-destructuring.md)
- array、Map 或 Set 內的 refs 沒有自動 unwrapping → 參考 [refs-in-collections-need-value](reference/refs-in-collections-need-value.md)
- nested refs 在 template 中 render 成 [object Object] → 參考 [template-ref-unwrapping-top-level](reference/template-ref-unwrapping-top-level.md)
- reactive proxy identity 比較總是 false → 參考 [reactivity-proxy-identity-hazard](reference/reactivity-proxy-identity-hazard.md)
- 第三方 instance 被 proxy 後行為異常 → 參考 [reactivity-markraw-for-non-reactive](reference/reactivity-markraw-for-non-reactive.md)
- watcher 非預期地每個 tick 只觸發一次 → 參考 [reactivity-same-tick-batching](reference/reactivity-same-tick-batching.md)

### 計算屬性
- computed getter 非預期觸發 mutation 或 request → 參考 [computed-no-side-effects](reference/computed-no-side-effects.md)
- 修改 computed value 導致變更消失 → 參考 [computed-return-value-readonly](reference/computed-return-value-readonly.md)
- computed value 在條件邏輯後不再更新 → 參考 [computed-conditional-dependencies](reference/computed-conditional-dependencies.md)
- sort 或 reverse array 破壞原始 state → 參考 [computed-array-mutation](reference/computed-array-mutation.md)
- 將參數傳入 computed property 失敗 → 參考 [computed-no-parameters](reference/computed-no-parameters.md)

### 監聽器
- async operation 以過期資料覆寫新資料 → 參考 [watch-async-cleanup](reference/watch-async-cleanup.md)
- 在 async callback 內建立 watcher → 參考 [watch-async-creation-memory-leak](reference/watch-async-creation-memory-leak.md)
- watcher 對 reactive object property 沒有觸發 → 參考 [watch-reactive-property-getter](reference/watch-reactive-property-getter.md)
- async watchEffect 在 await 後漏追蹤 dependency → 參考 [watcheffect-async-dependency-tracking](reference/watcheffect-async-dependency-tracking.md)
- watcher callback 內讀到過期 DOM → 參考 [watch-flush-timing](reference/watch-flush-timing.md)
- deep watcher 回報相同的 old/new value → 參考 [watch-deep-same-object-reference](reference/watch-deep-same-object-reference.md)
- watchEffect 在 template refs 更新前執行 → 參考 [watcheffect-flush-post-for-refs](reference/watcheffect-flush-post-for-refs.md)

### 元件
- child component 拋出「component not found」錯誤 → 參考 [local-components-not-in-descendants](reference/local-components-not-in-descendants.md)
- click listener 在 custom component 上沒有觸發 → 參考 [click-events-on-components](reference/click-events-on-components.md)
- parent 無法在 script setup 中存取 child ref data → 參考 [component-ref-requires-defineexpose](reference/component-ref-requires-defineexpose.md)
- HTML template parsing 破壞 Vue component syntax → 參考 [in-dom-template-parsing-caveats](reference/in-dom-template-parsing-caveats.md)
- naming collision 導致 render 錯誤 component → 參考 [component-naming-conflicts](reference/component-naming-conflicts.md)
- parent style 沒有套用到 multi-root component → 參考 [multi-root-component-class-attrs](reference/multi-root-component-class-attrs.md)

### Props 與 Emits
- defineProps 中引用變數造成錯誤 → 參考 [prop-defineprops-scope-limitation](reference/prop-defineprops-scope-limitation.md)
- component emit 未宣告 event 造成 warning → 參考 [declare-emits-for-documentation](reference/declare-emits-for-documentation.md)
- defineEmits 用在 function 或 conditional 內 → 參考 [defineEmits-must-be-top-level](reference/defineEmits-must-be-top-level.md)
- defineEmits 同時使用 type 與 runtime arguments → 參考 [defineEmits-no-runtime-and-type-mixed](reference/defineEmits-no-runtime-and-type-mixed.md)
- native event listener 對 click 沒有反應 → 參考 [native-event-collision-with-emits](reference/native-event-collision-with-emits.md)
- 點擊時 component event 觸發兩次 → 參考 [undeclared-emits-double-firing](reference/undeclared-emits-double-firing.md)

### 模板
- 使用 statement 導致 template compilation error → 參考 [template-expressions-restrictions](reference/template-expressions-restrictions.md)
- runtime 出現「Cannot read property of undefined」錯誤 → 參考 [v-if-null-check-order](reference/v-if-null-check-order.md)
- dynamic directive arguments 無法正常運作 → 參考 [dynamic-argument-constraints](reference/dynamic-argument-constraints.md)
- v-else element 總是無條件 render → 參考 [v-else-must-follow-v-if](reference/v-else-must-follow-v-if.md)
- 混用 v-if 與 v-for 造成 precedence bug 與 migration breakage → 參考 [no-v-if-with-v-for](reference/no-v-if-with-v-for.md)
- template function call 修改 state 造成不可預期的 re-render bug → 參考 [template-functions-no-side-effects](reference/template-functions-no-side-effects.md)
- loop 中的 child component 顯示 undefined data → 參考 [v-for-component-props](reference/v-for-component-props.md)
- sort 或 reverse 後 array order 改變 → 參考 [v-for-computed-reverse-sort](reference/v-for-computed-reverse-sort.md)
- list item 非預期消失或交換 state → 參考 [v-for-key-attribute](reference/v-for-key-attribute.md)
- range iteration 發生 off-by-one error → 參考 [v-for-range-starts-at-one](reference/v-for-range-starts-at-one.md)
- v-show 或 v-else 在 template element 上無法運作 → 參考 [v-show-template-limitation](reference/v-show-template-limitation.md)

### Template Refs
- element 被條件隱藏時 ref 變成 null → 參考 [template-ref-null-with-v-if](reference/template-ref-null-with-v-if.md)
- loop 中 ref array index 與 data array 不一致 → 參考 [template-ref-v-for-order](reference/template-ref-v-for-order.md)
- 重構 template ref 名稱時 code 靜默失效 → 參考 [use-template-ref-vue35](reference/use-template-ref-vue35.md)

### 表單與 v-model
- 使用 v-model 時 initial form values 沒有顯示 → 參考 [v-model-ignores-html-attributes](reference/v-model-ignores-html-attributes.md)
- textarea content 變更未更新 ref → 參考 [textarea-no-interpolation](reference/textarea-no-interpolation.md)
- iOS 使用者無法選擇 dropdown 第一個 option → 參考 [select-initial-value-ios-bug](reference/select-initial-value-ios-bug.md)
- parent 與 child component value 不同 → 參考 [define-model-default-value-sync](reference/define-model-default-value-sync.md)
- object property 變更沒有同步到 parent → 參考 [definemodel-object-mutation-no-emit](reference/definemodel-object-mutation-no-emit.md)
- 中文/日文輸入讓即時搜尋或驗證失效 → 參考 [v-model-ime-composition](reference/v-model-ime-composition.md)
- number input 回傳 empty string 而不是 zero → 參考 [v-model-number-modifier-behavior](reference/v-model-number-modifier-behavior.md)
- custom checkbox value 沒有在 form 中 submit → 參考 [checkbox-true-false-value-form-submission](reference/checkbox-true-false-value-form-submission.md)

### 事件與修飾符
- 串接多個 event modifier 產生非預期結果 → 參考 [event-modifier-order-matters](reference/event-modifier-order-matters.md)
- keyboard shortcut 搭配 system modifier key 時沒有觸發 → 參考 [keyup-modifier-timing](reference/keyup-modifier-timing.md)
- keyboard shortcut 在非預期 modifier 組合下觸發 → 參考 [exact-modifier-for-precise-shortcuts](reference/exact-modifier-for-precise-shortcuts.md)
- passive 與 prevent modifier 組合破壞 event behavior → 參考 [no-passive-with-prevent](reference/no-passive-with-prevent.md)

### 生命週期
- 未移除 event listener 造成 memory leak → 參考 [cleanup-side-effects](reference/cleanup-side-effects.md)
- component mount 前存取 DOM 失敗 → 參考 [lifecycle-dom-access-timing](reference/lifecycle-dom-access-timing.md)
- state change 後讀到過期 DOM value → 參考 [dom-update-timing-nexttick](reference/dom-update-timing-nexttick.md)
- SSR render 與 client hydration 不一致 → 參考 [lifecycle-ssr-awareness](reference/lifecycle-ssr-awareness.md)
- 非同步註冊 lifecycle hook 導致永遠不執行 → 參考 [lifecycle-hooks-synchronous-registration](reference/lifecycle-hooks-synchronous-registration.md)

### 插槽
- 在 slot content 中存取 child component data 得到 undefined value → 參考 [slot-render-scope-parent-only](reference/slot-render-scope-parent-only.md)
- 混用 named slot 與 scoped slot 造成 compilation error → 參考 [slot-named-scoped-explicit-default](reference/slot-named-scoped-explicit-default.md)
- 在 native HTML element 上使用 v-slot 造成 compilation error → 參考 [slot-v-slot-on-components-or-templates-only](reference/slot-v-slot-on-components-or-templates-only.md)
- implicit default slot behavior 造成 content placement 非預期 → 參考 [slot-implicit-default-content](reference/slot-implicit-default-content.md)
- scoped slot props 缺少預期的 name property → 參考 [slot-name-reserved-prop](reference/slot-name-reserved-prop.md)
- wrapper component 破壞 child slot functionality → 參考 [slot-forwarding-to-child-components](reference/slot-forwarding-to-child-components.md)

### Provide/Inject
- async operation 後呼叫 provide 靜默失敗 → 參考 [provide-inject-synchronous-setup](reference/provide-inject-synchronous-setup.md)
- 追蹤 provided value 來源 → 參考 [provide-inject-debugging-challenges](reference/provide-inject-debugging-challenges.md)
- provider 變更時 injected value 沒有更新 → 參考 [provide-inject-reactivity-not-automatic](reference/provide-inject-reactivity-not-automatic.md)
- 多個 component 共用同一個 default object → 參考 [provide-inject-default-value-factory](reference/provide-inject-default-value-factory.md)

### 屬性
- internal 與 fallthrough event handler 同時執行 → 參考 [attrs-event-listener-merging](reference/attrs-event-listener-merging.md)
- explicit attribute 被 fallthrough value 覆寫 → 參考 [fallthrough-attrs-overwrite-vue3](reference/fallthrough-attrs-overwrite-vue3.md)
- wrapper 中 attribute 套用到錯誤 element → 參考 [inheritattrs-false-for-wrapper-components](reference/inheritattrs-false-for-wrapper-components.md)

### Composables
- composable 在 setup context 外或非同步呼叫 → 參考 [composable-call-location-restrictions](reference/composable-call-location-restrictions.md)
- composable reactive dependency 在 input 變更時沒有更新 → 參考 [composable-tovalue-inside-watcheffect](reference/composable-tovalue-inside-watcheffect.md)
- composable 非預期修改外部 state → 參考 [composable-avoid-hidden-side-effects](reference/composable-avoid-hidden-side-effects.md)
- 解構 composable return 導致 reactivity 非預期中斷 → 參考 [composable-naming-return-pattern](reference/composable-naming-return-pattern.md)

### Composition API
- async operation 後 lifecycle hook 靜默失效 → 參考 [composition-api-script-setup-async-context](reference/composition-api-script-setup-async-context.md)
- parent component ref 無法存取 exposed properties → 參考 [define-expose-before-await](reference/define-expose-before-await.md)
- functional-programming patterns 破壞 Vue 預期 reactivity behavior → 參考 [composition-api-not-functional-programming](reference/composition-api-not-functional-programming.md)
- React Hook 心智模型造成 Composition API 用法錯誤 → 參考 [composition-api-vs-react-hooks-differences](reference/composition-api-vs-react-hooks-differences.md)

### 動畫
- DOM node 被重用時 animation 沒有觸發 → 參考 [animation-key-for-rerender](reference/animation-key-for-rerender.md)
- TransitionGroup list update 在負載下感覺卡頓 → 參考 [animation-transitiongroup-performance](reference/animation-transitiongroup-performance.md)

### TypeScript
- mutable prop defaults 在 component instance 間洩漏 state → 參考 [ts-withdefaults-mutable-factory-function](reference/ts-withdefaults-mutable-factory-function.md)
- reactive() generic typing 造成 ref unwrapping mismatch → 參考 [ts-reactive-no-generic-argument](reference/ts-reactive-no-generic-argument.md)
- template ref 在 mount 前或 v-if unmount 後拋出 null access error → 參考 [ts-template-ref-null-handling](reference/ts-template-ref-null-handling.md)
- optional boolean props 表現為 false 而不是 undefined → 參考 [ts-defineprops-boolean-default-false](reference/ts-defineprops-boolean-default-false.md)
- imported defineProps types 因 unresolvable 或 complex type reference 失敗 → 參考 [ts-defineprops-imported-types-limitations](reference/ts-defineprops-imported-types-limitations.md)
- 未指定型別的 DOM event handler 在 strict TypeScript 設定下失敗 → 參考 [ts-event-handler-explicit-typing](reference/ts-event-handler-explicit-typing.md)
- dynamic component ref 觸發 reactive component warning → 參考 [ts-shallowref-for-dynamic-components](reference/ts-shallowref-for-dynamic-components.md)
- union-typed template expression 沒有 narrowing 時 type check 失敗 → 參考 [ts-template-type-casting](reference/ts-template-type-casting.md)

### 非同步元件
- route component 誤用 defineAsyncComponent lazy loading → 參考 [async-component-vue-router](reference/async-component-vue-router.md)
- 載入 component 時發生 network failure 或 timeout → 參考 [async-component-error-handling](reference/async-component-error-handling.md)
- component reactivation 後 template ref 是 undefined → 參考 [async-component-keepalive-ref-issue](reference/async-component-keepalive-ref-issue.md)

### Render Functions
- state change 後 render function output 仍保持 static → 參考 [rendering-render-function-return-from-setup](reference/rendering-render-function-return-from-setup.md)
- 重用 vnode instance 導致 render 錯誤 → 參考 [render-function-vnodes-must-be-unique](reference/render-function-vnodes-must-be-unique.md)
- string component name 被 render 成 HTML element → 參考 [rendering-resolve-component-for-string-names](reference/rendering-resolve-component-for-string-names.md)
- 存取 vnode internals 在 Vue update 後壞掉 → 參考 [render-function-avoid-internal-vnode-properties](reference/render-function-avoid-internal-vnode-properties.md)
- Vue 2 render function patterns 在 Vue 3 中 crash → 參考 [rendering-render-function-h-import-vue3](reference/rendering-render-function-h-import-vue3.md)
- h() 的 slot content 沒有 render → 參考 [rendering-render-function-slots-as-functions](reference/rendering-render-function-slots-as-functions.md)

### KeepAlive
- nested Vue Router routes 導致 child component mount 兩次 → 參考 [keepalive-router-nested-double-mount](reference/keepalive-router-nested-double-mount.md)
- KeepAlive 搭配 Transition animation 時 memory 持續成長 → 參考 [keepalive-transition-memory-leak](reference/keepalive-transition-memory-leak.md)

### 轉場
- JavaScript transition hook 缺少 done callback 導致卡住 → 參考 [transition-js-hooks-done-callback](reference/transition-js-hooks-done-callback.md)
- inline list element 上的 move animation 失敗 → 參考 [transition-group-flip-inline-elements](reference/transition-group-flip-inline-elements.md)
- list item 跳動而不是平滑 animate → 參考 [transition-group-move-animation-position-absolute](reference/transition-group-move-animation-position-absolute.md)
- Vue 2 到 Vue 3 的 TransitionGroup wrapper 變更破壞 layout → 參考 [transition-group-no-default-wrapper-vue3](reference/transition-group-no-default-wrapper-vue3.md)
- nested transition 在完成前被截斷 → 參考 [transition-nested-duration](reference/transition-nested-duration.md)
- scoped style 在可重用 transition wrapper 中停止作用 → 參考 [transition-reusable-scoped-style](reference/transition-reusable-scoped-style.md)
- RouterView transition 在首次 render 時非預期 animate → 參考 [transition-router-view-appear](reference/transition-router-view-appear.md)
- 混用 CSS transition 與 animation 造成 timing issue → 參考 [transition-type-when-mixed](reference/transition-type-when-mixed.md)
- rapid transition swap 期間漏掉 cleanup hook → 參考 [transition-unmount-hook-timing](reference/transition-unmount-hook-timing.md)

### Teleport
- DOM 中找不到 Teleport target element → 參考 [teleport-target-must-exist](reference/teleport-target-must-exist.md)
- teleported content 破壞 SSR hydration → 參考 [teleport-ssr-hydration](reference/teleport-ssr-hydration.md)
- scoped style 沒有套用到 teleported content → 參考 [teleport-scoped-styles-limitation](reference/teleport-scoped-styles-limitation.md)

### Suspense
- 需要處理 Suspense component 的 async error → 參考 [suspense-no-builtin-error-handling](reference/suspense-no-builtin-error-handling.md)
- 搭配 server-side rendering 使用 Suspense → 參考 [suspense-ssr-hydration-issues](reference/suspense-ssr-hydration-issues.md)
- Suspense 下 async component loading/error UI 被忽略 → 參考 [async-component-suspense-control](reference/async-component-suspense-control.md)

### SSR
- server 與 client render 的 HTML 不一致 → 參考 [ssr-hydration-mismatch-causes](reference/ssr-hydration-mismatch-causes.md)
- shared singleton store 造成 request 之間 user state 洩漏 → 參考 [state-ssr-cross-request-pollution](reference/state-ssr-cross-request-pollution.md)
- browser-only API 在 universal code path 中讓 server rendering crash → 參考 [ssr-platform-specific-apis](reference/ssr-platform-specific-apis.md)

### 效能
- parent 傳入 unstable props 導致 list children 不必要地重新 render → 參考 [perf-props-stability-update-optimization](reference/perf-props-stability-update-optimization.md)
- computed object 即使值等價仍重新觸發 effects → 參考 [perf-computed-object-stability](reference/perf-computed-object-stability.md)

### SFC（Single File Components）
- 嘗試從 component script block 使用 named exports → 參考 [sfc-named-exports-forbidden](reference/sfc-named-exports-forbidden.md)
- 變更後 template 中的變數沒有更新 → 參考 [sfc-script-setup-reactivity](reference/sfc-script-setup-reactivity.md)
- scoped style 沒有套用到 child component element → 參考 [sfc-scoped-css-child-component-styling](reference/sfc-scoped-css-child-component-styling.md)
- scoped style 沒有套用到 dynamic v-html content → 參考 [sfc-scoped-css-dynamic-content](reference/sfc-scoped-css-dynamic-content.md)
- scoped style 沒有套用到 slot content → 參考 [sfc-scoped-css-slot-content](reference/sfc-scoped-css-slot-content.md)
- Tailwind class 以動態方式建立時遺失 → 參考 [tailwind-dynamic-class-generation](reference/tailwind-dynamic-class-generation.md)
- recursive component 因 name conflict 沒有 render → 參考 [self-referencing-component-name](reference/self-referencing-component-name.md)

### Plugins
- 除錯 global properties 為何造成 naming conflict → 參考 [plugin-global-properties-sparingly](reference/plugin-global-properties-sparingly.md)
- plugin 無法運作或 inject 回傳 undefined → 參考 [plugin-install-before-mount](reference/plugin-install-before-mount.md)
- plugin global properties 在 setup-based components 中無法使用 → 參考 [plugin-prefer-provide-inject-over-global-properties](reference/plugin-prefer-provide-inject-over-global-properties.md)
- plugin type augmentation 錯誤破壞 ComponentCustomProperties typing → 參考 [plugin-typescript-type-augmentation](reference/plugin-typescript-type-augmentation.md)

### App 設定
- mount call 後 app configuration methods 無法運作 → 參考 [configure-app-before-mount](reference/configure-app-before-mount.md)
- 從 mount() 串接 app config 失敗，因為 mount 回傳 component instance → 參考 [mount-return-value](reference/mount-return-value.md)
- Vite 中 require.context-based component auto-registration 失敗 → 參考 [dynamic-component-registration-vite](reference/dynamic-component-registration-vite.md)

---
> Source: [mm7246591/TwoDo](https://github.com/mm7246591/TwoDo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
