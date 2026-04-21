---
name: vue-3-composables
description: 提供标准化的 useTable, useRequest 等 Hooks，用于简化列表页和异步请求的逻辑复用。 Use when this capability is needed.
metadata:
  author: TencentBlueKing
---

# Vue 3 组合式函数最佳实践

为了避免在每个页面重复编写分页、Loading 和请求逻辑，我们推荐使用 Composables。

## 1. useTable (列表页神器)

封装了分页、加载状态、数据获取的通用逻辑。**一行代码搞定所有表格逻辑**。

**使用方式：**
```typescript
const { loading, data, pagination, handlePageChange, handleLimitChange } = useTable(getHostList);
```

**配合 bk-table：**
```html
<bk-table
  :data="data"
  :pagination="pagination"
  v-bkloading="{ isLoading: loading }"
  remote-pagination
  @page-change="handlePageChange"
  @page-limit-change="handleLimitChange"
>
  <bk-table-column prop="name" label="名称" />
</bk-table>
```

> 📦 获取完整 Hook 实现：`skill://vue-composables/assets/useTable.ts`


---
## 📦 可用资源

- `skill://vue-composables/assets/useTable.ts`

> 根据 SKILL.md 中的 IF-THEN 规则判断是否需要加载

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/TencentBlueKing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
