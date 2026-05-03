---
name: vue-tool-developer
description: 开发XHL Tools工具箱的新工具。当用户要添加新工具、创建工具页面、扩展工具分类、或提到"开发工具"、"添加功能"、"新建工具"时使用。特别关注SSG架构限制、设计令牌系统和美观的UI实现。 Use when this capability is needed.
metadata:
  author: starburst-xhl
---

# Vue 工具开发者指南

在 xhl-tools-vue 项目中添加新工具。项目采用 Vue 3 + TypeScript + Vite SSG 架构。

## 快速开发流程

### 1. 确定工具分类

**现有分类** (`src/views/Tools/`):
- `CodecTool/` - 编解码工具(Base64/二维码/AES/JSON)
- `NumberTool/` - 数字工具(秒表/密码生成器/骰子)
- `MediaTool/` - 媒体工具(RPGMVP转换/颜色拾取)
- `MockTool/` - Mock工具
- `ChatTool/` - 聊天工具
- `StringTool/` - 字符串工具

### 2. 创建组件文件

在对应分类目录下创建 `YourToolName.vue`。

**⚠️ SSG 架构注意事项**:
- 组件必须兼容服务端渲染(SSR)
- 不要在 `<script setup>` 顶层使用 `window`/`document`
- 浏览器 API 放在 `onMounted` 钩子中
- 确保组件在 Node.js 环境下可运行

### 3. 注册路由（两处）

**① `src/router/index.ts`** - 添加路由配置:
```typescript
{
  path: 'your-tool-name',
  name: 'YourToolName',
  component: () => import('@/views/Tools/{CategoryTool}/YourToolName.vue'),
  meta: {
    title: '工具名称',
    icon: 'ToolOutlined',
    description: '工具描述'
  }
}
```

**② `src/constants/tool-routes.json`** - 添加 SEO/sitemap 配置:
```json
{
  "path": "/tools/your-tool-name",
  "title": "工具名称",
  "description": "工具描述",
  "icon": "ToolOutlined",
  "category": "category-tool",
  "priority": "0.7",
  "changefreq": "monthly"
}
```

> `priority` 影响 SEO 排名(1.0 最高)，`changefreq` 告诉搜索引擎更新频率

### 4. 添加图标(如需)

在 `src/utils/tool_utils.ts` 的 `iconMap` 中添加新图标导入。

## 设计规范速查

### 使用设计令牌

**✅ 推荐做法**:
```css
.my-component {
  color: var(--color-text-primary);
  font-size: var(--font-size-body);
  padding: var(--spacing-md);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
  transition: var(--transition-fast);
}
```

**❌ 避免硬编码**:
```css
/* 不要这样做 */
.my-component {
  color: #262626;
  padding: 16px;
}
```

**关键设计令牌**:
- 主色: `--color-primary` (#ff9b17)
- 文字: `--color-text-primary/secondary/tertiary`
- 间距: `--spacing-xs` 到 `--spacing-xxxxl` (4px-64px)
- 圆角: `--radius-sm` 到 `--radius-xxl` (4px-20px)
- 阴影: `--shadow-sm/md/lg/xl`

### 布局最佳实践

- 容器使用 `max-width: 400px-600px`
- 使用 Flexbox 布局
- **按钮默认大小**（不指定 size）
- **按钮方形**（不使用 `shape="circle"` 或 `shape="round"`）
- 按钮组使用 `gap` 间距
- 结果区域用 `div` 包裹，**扁平化风格**
- 复制功能使用 `navigator.clipboard.writeText()`

#### 工具页面布局规范

**固定标题 + 可滚动内容**（详见 `DESIGN_SYSTEM.md` 第238-272行）：
- 页面结构：固定标题 + 滚动内容区域
- 滚动容器：`overflow-y: auto` + 隐藏滚动条
- 响应式间距：桌面 24px / 平板 16px / 移动端 8px
- 滚动条隐藏：使用 CSS 隐藏但保持可滚动

**关键 CSS**：
```css
.page-content {
  overflow-y: auto;
  scrollbar-width: none;          /* Firefox */
  -ms-overflow-style: none;       /* IE/Edge */
}
.page-content::-webkit-scrollbar {
  display: none;                  /* Chrome/Safari/Opera */
}
```

### 按钮规范

**✅ 推荐**：
```vue
<!-- 默认大小 + 方形 -->
<a-button type="primary">格式化</a-button>
<a-button>重置</a-button>

<!-- 带图标 -->
<a-button type="primary">
  <template #icon>
    <CopyOutlined/>
  </template>
  复制
</a-button>
```

**❌ 避免**：
```vue
<a-button shape="circle">❌ 圆形按钮</a-button>
<a-button shape="round">❌ 圆角按钮</a-button>
<a-button size="large">❌ 大号按钮（仅页面主CTA可用）</a-button>
```

### 扁平化设计规范

**✅ 推荐：扁平化风格**
```css
.result-section {
  background: var(--color-bg);
  padding: var(--spacing-lg);
  border-radius: var(--radius-lg);
  border: 1px solid var(--color-border-light);
}
```

**❌ 避免：使用阴影和 Card 组件**
```css
/* 不要使用阴影 */
.result-section {
  box-shadow: var(--shadow-sm);  /* ❌ */
}

/* 不要用 a-card */
<a-card>  /* ❌ 使用普通 div */
```

### 交互规范

#### Message 提示规范

**使用 Ant Design Vue 的 `message` 组件**：

```typescript
import { message } from "ant-design-vue";

// 成功提示
message.success("格式化成功");

// 错误提示 - 要具体
message.error("JSON格式不正确");

// 警告提示 - 用于输入问题
message.warning("请输入JSON内容");
```

**文案原则**：
- ✅ 简洁明确：`message.success("格式化成功")`
- ✅ 具体描述：`message.error("JSON格式不正确")`
- ❌ 避免笼统：`message.success("操作成功")`（太模糊）

**类型选择**：
- **success**: 操作成功完成
- **error**: 操作失败（格式错误、解析失败等）
- **warning**: 输入问题（空输入、参数缺失等）
- **info**: 极少使用

#### 按钮状态

**加载状态** - 异步操作：
```vue
<a-button :loading="isLoading">处理</a-button>
```

**禁用状态** - 表单验证：
```vue
<a-button :disabled="!isValid">提交</a-button>
```

## 组件模板

使用 `references/ToolTemplate.vue` 作为起点,或参考以下优秀实现:

**推荐参考**:
- `StopwatchTool.vue` - 时间显示、记录列表、主题色应用
- `ColorPickerTool.vue` - 大尺寸预览、多格式展示、交互反馈

## 命名规范

- 文件名: PascalCase (`StopwatchTool.vue`)
- CSS类名: BEM (`tool-name__element--modifier`)
- 路由 path: kebab-case (`stopwatch-tool`)
- 路由 name: PascalCase (`StopwatchTool`)

## 测试验证

```bash
npm run dev
```

检查项:
- ✅ 路由正常访问
- ✅ 功能正常工作
- ✅ UI符合设计规范
- ✅ 侧边菜单显示正确
- ✅ 工具首页卡片可见
- ✅ 搜索功能可用

## 参考资源

- **设计系统**: `DESIGN_SYSTEM.md` - 完整设计规范
- **项目架构**: `CODEBUDDY.md` - SSG架构、路由配置
- **模板文件**: `references/ToolTemplate.vue` - 基础模板

---
> Source: [starburst-xhl/xhl-tools-vue](https://github.com/starburst-xhl/xhl-tools-vue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
