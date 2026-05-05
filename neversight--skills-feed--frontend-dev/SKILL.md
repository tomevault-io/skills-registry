---
name: frontend-dev
description: 前端开发规范，包含 Vue 3 编码规范、UI 风格约束、TypeScript 规范等 Use when this capability is needed.
metadata:
  author: neversight
---

# 前端开发规范

> 参考来源: Vue 官方风格指南、Element Plus 最佳实践

---

## UI 风格约束

### 严格禁止（常见 AI 风格）

- ❌ 蓝紫色霓虹渐变、发光描边、玻璃拟态
- ❌ 大面积渐变、过多装饰性几何图形
- ❌ 赛博风、暗黑科技风、AI 风格 UI
- ❌ UI 文案中使用 emoji

### 后台系统（默认风格）

| 要素 | 要求 |
|------|------|
| 主题 | 使用组件库默认主题 |
| 配色 | 黑白灰为主 + 1 个主色点缀 |
| 动效 | 克制，仅保留必要交互反馈 |

---

## 技术栈

| 层级 | Vue（首选） | React（备选） |
|------|------------|--------------|
| 框架 | Vue 3 + TypeScript | React 18 + TypeScript |
| 构建 | Vite | Vite |
| 路由 | Vue Router 4 | React Router 6 |
| 状态 | Pinia | Zustand |
| UI 库 | Element Plus | Ant Design |

---

## Vue 编码规范

### 组件基础

```vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import type { User } from '@/types'

// Props & Emits
const props = defineProps<{ userId: number }>()
const emit = defineEmits<{ (e: 'update', value: string): void }>()

// 响应式状态
const loading = ref(false)
const user = ref<User | null>(null)

// 计算属性
const displayName = computed(() => user.value?.name ?? '未知用户')

// 生命周期
onMounted(async () => { await fetchUser() })

// 方法
async function fetchUser() {
  loading.value = true
  try {
    user.value = await api.getUser(props.userId)
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="user-card">
    <h3>{{ displayName }}</h3>
  </div>
</template>

<style scoped>
.user-card { padding: 16px; }
</style>
```

### 命名约定

| 类型 | 约定 | 示例 |
|------|------|------|
| 组件文件 | PascalCase.vue | `UserCard.vue` |
| Composables | useXxx.ts | `useAuth.ts` |
| Store | useXxxStore.ts | `useUserStore.ts` |

---

## 状态管理（Pinia）

```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null)
  const token = ref<string>('')

  const isLoggedIn = computed(() => !!token.value)

  async function login(username: string, password: string) {
    const res = await api.login(username, password)
    token.value = res.token
    user.value = res.user
  }

  return { user, token, isLoggedIn, login }
})
```

---

## 交互状态处理

**必须处理的状态**: loading、empty、error、disabled、submitting

```vue
<template>
  <el-skeleton v-if="loading" :rows="5" animated />
  <el-result v-else-if="error" icon="error" :title="error">
    <template #extra>
      <el-button @click="fetchData">重试</el-button>
    </template>
  </el-result>
  <el-empty v-else-if="list.length === 0" description="暂无数据" />
  <template v-else>
    <!-- 正常内容 -->
  </template>
</template>
```

---

## TypeScript 规范

```typescript
// types/user.ts
export interface User {
  id: number
  username: string
  role: 'admin' | 'user'
}

export interface ApiResponse<T = unknown> {
  code: number
  message: string
  data: T
}
```

---

## 性能优化

| 场景 | 方案 |
|------|------|
| 大列表 | 虚拟滚动 |
| 路由 | 懒加载 `() => import()` |
| 计算 | 使用 `computed` 缓存 |
| 大数据 | 使用 `shallowRef` |

```typescript
// 路由懒加载
const routes = [
  { path: '/dashboard', component: () => import('@/views/Dashboard.vue') }
]

// 请求防抖
import { useDebounceFn } from '@vueuse/core'
const debouncedSearch = useDebounceFn((keyword) => api.search(keyword), 300)
```

---

## 目录结构

```
src/
├── api/                 # API 请求
├── components/          # 通用组件
├── composables/         # 组合式函数
├── router/              # 路由配置
├── stores/              # Pinia stores
├── types/               # TypeScript 类型
├── utils/               # 工具函数
├── views/               # 页面组件
├── App.vue
└── main.ts
```

---

## 详细参考

完整规范见 `references/frontend-style.md`，包含：
- 完整 UI 风格约束
- Vue 3 编码规范详解
- Pinia 状态管理
- API 请求封装
- 性能优化详解

---

> 📋 本回复遵循：`frontend-dev` - [具体章节]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
