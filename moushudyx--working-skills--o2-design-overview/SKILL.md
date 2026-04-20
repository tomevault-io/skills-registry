---
name: o2-design-overview
description: 关于 O2 Design 组件库的整体叙述 Use when this capability is needed.
metadata:
  author: moushudyx
---

O2Design 基于 choerodon-ui 二次开发而来, 是一个 React 组件库

O2Design 的核心是响应式数据，通过响应式数据驱动视图变化。底层是使用 `Vue2.0`+`React Hooks`+`Typescript` 封装实现类似 Vue3 的组合式API

```jsx
import {designPage, reactive} from 'o2-design'
// designPage 是创建组件的简易方法
// 这个 (props) => XXX 的函数我们称为 setup 函数
const CountButton = designPage((props) => {
  // props 是传入 CountButton 组件的参数

  // 这里可以使用 onMounted 等生命周期函数, 非常像 Vue 但是不能直接使用 React Hooks
  // 生命周期函数不能用任何 setup 以外的地方, 底下的 render 函数里也不行

  // reactive 类似 Vue2 中的功能, 也具有同样的坑
  const state = reactive({
    count: 100, // 如果 state.count 发生变化, 会触发整个 CountButton 组件更新
  })

  // 返回的内容只能是 { refer: {}, render: () => JSX } 或者 () => JSX 这两种
  // 组件更新时, 只会重新调用这个 render 函数, 上面的部分不会再次调用
  return () => (
    <div>
      <button onClick={() => state.count++}>count:{state.count}</button>
    </div>
  )
})
export default CountButton
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/moushudyx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
