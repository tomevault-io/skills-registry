---
name: vue-best-practices
description: Vue 3 和 Vue.js 的最佳实践，涵盖 TypeScript、vue-tsc 和 Volar。在编写、审查或重构 Vue 组件时使用此技能，确保正确的类型模式。在涉及 Vue 组件、props 提取、包装组件、模板类型检查或 Volar 配置的任务时触发。 Use when this capability is needed.
metadata:
  author: Quirrel-zh
---

# Vue 3 最佳实践

Vue 3 开发的最佳实践，涵盖 TypeScript 配置、组件类型、工具故障排除和测试模式。

## 核心规则

### 能力规则（Capability Rules）

这些规则解决 AI 无法在没有技能的情况下解决的问题，包括版本特定问题、未记录的行为、最新功能或训练数据之外的边缘情况。

#### 1. 提取组件 Props

使用 `vue-component-type-helpers` 从 `.vue` 组件中提取类型：

```bash
npm install -D vue-component-type-helpers
```

```typescript
import type {
  ComponentProps,
  ComponentEmit,
  ComponentSlots,
  ComponentExposed,
} from 'vue-component-type-helpers'
import MyButton from './MyButton.vue'

type Props = ComponentProps<typeof MyButton>
type Emits = ComponentEmit<typeof MyButton>
type Slots = ComponentSlots<typeof MyButton>
type Exposed = ComponentExposed<typeof MyButton>
```

**包装组件模式：**

```typescript
import type { ComponentProps } from 'vue-component-type-helpers'
import BaseButton from './BaseButton.vue'

type BaseProps = ComponentProps<typeof BaseButton>

interface Props extends BaseProps {
  size: 'sm' | 'md' | 'lg'
}

defineProps<Props>()
```

**不要使用：**

```typescript
// ❌ 包含 Vue 内部属性（onUpdate:*, class, style 等）
type Props = InstanceType<typeof MyButton>['$props']
```

详细说明见 [rules/extract-component-props.md](rules/extract-component-props.md)

#### 2. 启用严格模板检查

默认情况下，vue-tsc 不会报告模板中未定义组件的错误。启用 `strictTemplates` 在类型检查期间捕获这些问题。

在包含 Vue 源文件的 tsconfig 中添加 `vueCompilerOptions`（通常是 `tsconfig.app.json`，而不是根 `tsconfig.json`）：

```json
{
  "compilerOptions": {
    "strict": true
  },
  "vueCompilerOptions": {
    "strictTemplates": true
  }
}
```

**可用选项：**

| 选项                     | 默认值  | 效果                                  |
| ------------------------ | ------- | ------------------------------------- |
| `strictTemplates`        | `false` | 启用下面所有 checkUnknown\* 选项      |
| `checkUnknownComponents` | `false` | 对未定义/未注册的组件报错             |
| `checkUnknownProps`      | `false` | 对组件定义中未声明的 props 报错       |
| `checkUnknownEvents`     | `false` | 对未通过 `defineEmits` 声明的事件报错 |

详细说明见 [rules/vue-tsc-strict-templates.md](rules/vue-tsc-strict-templates.md)

#### 3. 透传属性类型检查

在构建组件库时，启用 `fallthroughAttributes` 以获得 IDE 自动完成功能：

```json
{
  "vueCompilerOptions": {
    "fallthroughAttributes": true
  }
}
```

详细说明见 [rules/fallthrough-attributes.md](rules/fallthrough-attributes.md)

#### 4. 严格 CSS 模块类型检查

启用 `strictCssModules` 捕获 CSS 模块类名的拼写错误：

```json
{
  "vueCompilerOptions": {
    "strictCssModules": true
  }
}
```

详细说明见 [rules/strict-css-modules.md](rules/strict-css-modules.md)

#### 5. 数据属性配置

使用 `strictTemplates` 时，`data-*` 属性会导致类型错误。使用 `dataAttributes` 选项允许特定模式：

```json
{
  "vueCompilerOptions": {
    "strictTemplates": true,
    "dataAttributes": ["data-*"]
  }
}
```

详细说明见 [rules/data-attributes-config.md](rules/data-attributes-config.md)

#### 6. Volar 3.0 破坏性变更

Volar 3.0 引入了语言服务器协议的破坏性变更。编辑器需要更新配置。

**VSCode：** 更新 "Vue - Official" 扩展到最新版本。

**NeoVim：** 使用 `vtsls` 而不是 `ts_ls`：

```lua
require('lspconfig').vtsls.setup({})
require('lspconfig').volar.setup({})
```

详细说明见 [rules/volar-3-breaking-changes.md](rules/volar-3-breaking-changes.md)

#### 7. 模块解析 Bundler 迁移问题

`@vue/tsconfig` 的最新版本将 `moduleResolution` 从 `"node"` 更改为 `"bundler"`。这可能导致 "Cannot find module" 错误。

**修复：** 确保 TypeScript 5.0+ 或添加兼容性配置：

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler",
    "resolvePackageJsonExports": false
  }
}
```

详细说明见 [rules/module-resolution-bundler.md](rules/module-resolution-bundler.md)

#### 8. defineModel 更新事件

使用 `defineModel` 的组件可能在边缘情况下触发带有 `undefined` 的 `@update:model-value` 事件。

**修复：** 使用 `required` 选项（Vue 3.5+）：

```typescript
const model = defineModel<Item>({ required: true })
```

详细说明见 [rules/define-model-update-event.md](rules/define-model-update-event.md)

#### 9. Vue 模板指令注释

Vue Language Tools 支持特殊的指令注释来控制模板中的类型检查行为：

- `@vue-ignore` - 抑制下一行的类型错误
- `@vue-expect-error` - 断言下一行应该有类型错误
- `@vue-skip` - 跳过整个块的类型检查
- `@vue-generic` - 声明模板级别的泛型类型

详细说明见 [rules/vue-directive-comments.md](rules/vue-directive-comments.md)

#### 10. withDefaults 联合类型默认值

使用 `withDefaults` 与联合类型（如 `false | string`）可能会产生虚假的 "Missing required prop" 警告。

**修复：** 使用响应式 Props 解构（Vue 3.5+）：

```typescript
interface Props {
  value: false | string
}

// Vue 3.5+ 推荐方式
const { value = 'default' } = defineProps<Props>()
```

详细说明见 [rules/with-defaults-union-types.md](rules/with-defaults-union-types.md)

#### 11. Vue 3.5+ 深度监听数值深度

Vue 3.5 引入了 `deep: number` 用于控制监听深度，允许监听数组变更而无需深度遍历的性能成本：

```typescript
// Vue 3.5+ only
watch(
  items,
  (newVal) => {
    // 在数组变更时触发（push, pop, splice 等）
  },
  { deep: 1 },
)
```

详细说明见 [rules/deep-watch-numeric.md](rules/deep-watch-numeric.md)

#### 12. Vue Router 类型化路由参数

使用 `unplugin-vue-router` 时，`route.params` 成为所有页面参数类型的联合类型。需要正确使用类型断言或指定路由路径。

**修复：** 传递路由名称到 useRoute：

```typescript
import { useRoute } from 'vue-router/auto'
const route = useRoute('/users/[id]')
console.log(route.params.id) // 正确类型为 string
```

详细说明见 [rules/vue-router-typed-params.md](rules/vue-router-typed-params.md)

### 效率规则（Efficiency Rules）

这些规则提供最佳模式、最佳实践和一致的方法，提高解决方案质量。

#### 1. Vue SSR 中的 HMR 调试

在 SSR 应用中修改 Vue 组件的 `<script setup>` 部分时，HMR 会中断。

**修复：** 确保正确的 SSR 插件配置：

```typescript
// vite.config.ts
export default defineConfig({
  plugins: [vue()],
  ssr: {
    noExternal: ['vue', '@vue/runtime-core', '@vue/runtime-dom'],
  },
})
```

详细说明见 [rules/hmr-vue-ssr.md](rules/hmr-vue-ssr.md)

#### 2. Pinia Store Mocking

使用 Vitest 正确模拟 Pinia stores：

```typescript
import { createTestingPinia } from '@pinia/testing'
import { vi } from 'vitest'

test('component uses store', async () => {
  const wrapper = mount(MyComponent, {
    global: {
      plugins: [
        createTestingPinia({
          createSpy: vi.fn, // @pinia/testing 1.0+ 必需
          initialState: {
            counter: { count: 10 },
          },
        }),
      ],
    },
  })

  const store = useCounterStore()
  expect(store.increment).toHaveBeenCalled()
})
```

详细说明见 [rules/pinia-store-mocking.md](rules/pinia-store-mocking.md)

## 参考资源

- [Vue Language Tools](https://github.com/vuejs/language-tools)
- [vue-component-type-helpers](https://github.com/vuejs/language-tools/tree/master/packages/component-type-helpers)
- [Vue 3 文档](https://vuejs.org/)
- [Vue Language Tools Wiki - Vue Compiler Options](https://github.com/vuejs/language-tools/wiki/Vue-Compiler-Options)

## 使用提示

在提示前加上 `use vue skill` 以获得最可靠的结果：

```
Use vue skill, <你的提示>
```

这明确触发技能并确保 AI 遵循文档化的模式。

---
> Source: [Quirrel-zh/template-vue](https://github.com/Quirrel-zh/template-vue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-25 -->
