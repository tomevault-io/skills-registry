---
name: vue-admin-generator
description: 管理后台框架生成器。采用Vue3 + Element Plus + Vite + TypeScript + Mock.js 。用于快速创建完整的 Web 管理后台系统。使用场景：(1) 需要创建新的管理后台项目，(2) 用户描述了登录页样式、布局方式(左右布局/顶部导航等)、菜单结构、主题颜色等需求，(3) 用户提供参考图或手稿图,通过分析图片中的布局结构、配色方案、菜单结构来生成后台系统，(4) 需要一个带 Mock 数据的前端项目(无需后端)，(5) 快速搭建管理系统原型。 Use when this capability is needed.
metadata:
  author: wang-q-l
---

# Vue 管理后台生成器

## 概述

根据用户描述的需求(登录页样式、布局方式、菜单结构、主题颜色等)或提供的参考图/手稿图,快速生成一个完整的 Vue3 + Element Plus 管理后台项目,包含完整的项目结构、路由配置、Mock 数据,开箱即用。

**支持两种输入方式:**

1. 文字描述: 用户直接描述需求
2. 参考图/手稿图: 分析图片中的布局结构、配色方案、菜单结构(页面内容不需要实现)

## 工作流程

### 1. 收集需求信息

**支持两种方式提供需求:**

#### 方式一: 文字描述

从用户描述中提取以下信息:

**必需信息:**

- 布局方式: 左右布局(默认) / 顶部导航 / 混合布局
- 菜单项: 需要哪些页面(如: 首页、用户管理、数据统计等)

**可选信息:**

- 主题色: 默认 `#409eff`
- 侧边栏背景色: 默认 `#001529`
- 侧边栏文字色: 默认 `#ffffff`
- 登录页背景: 默认蓝紫渐变
- 其他自定义需求

#### 方式二: 参考图/手稿图

**当用户提供参考图或手稿图时,需要分析以下内容:**

1. **布局结构分析:**
   - 识别整体布局方式(左右布局/顶部导航/混合布局/上下布局等)
   - 确定各个区域的位置:
     - 顶部栏位置和内容(Logo、系统名称、用户信息等)
     - 侧边栏位置(左侧/右侧/无)
     - 菜单位置(顶部/侧边/混合)
     - 内容区域位置
     - 面包屑、标签页等辅助导航的位置

2. **颜色提取:**
   - 顶部栏背景色
   - 侧边栏背景色
   - 菜单文字颜色
   - 菜单激活状态颜色
   - 主题色(按钮、链接等)
   - 内容区域背景色
   - 卡片背景色

3. **菜单结构识别:**
   - 识别菜单项名称
   - 识别菜单层级(一级菜单/二级菜单)
   - 识别菜单图标(如果有)

4. **其他视觉元素:**
   - Logo 样式和位置
   - 圆角大小
   - 间距规范
   - 字体大小

**分析步骤:**

1. 仔细观察图片,识别布局结构
2. 使用取色工具或视觉估计提取主要颜色
3. 列出所有菜单项和层级关系
4. 总结布局特点和配色方案
5. 向用户确认分析结果是否准确

**注意事项:**

- 页面内容区域的具体内容不需要实现,只需要创建空状态页面
- 重点关注布局结构、配色方案、菜单结构
- 如果图片不清晰或信息不完整,询问用户补充

如信息不完整,使用默认值或询问用户。

### 2. 复制模板项目

**重要：项目必须在当前工作目录直接创建，不要创建子目录！**

将 `assets/template/` 目录下的完整项目复制到当前工作目录：

```bash
cp -r assets/template/* ./
```

**注意事项：**

- 直接在当前目录创建项目文件，不要使用 `/path/to/new-project/` 这样的子目录
- 如果当前目录已有文件，先确认是否可以覆盖
- 项目创建后，所有文件应该与技能所在的 `.claude` 目录同级

模板包含:

- 完整的项目配置(package.json, vite.config.ts, tsconfig.json)
- 基础布局组件(登录页、主布局)
- 路由配置
- Mock 数据配置（src/mock.ts）
- 样式文件(包含 Element UI 自定义样式)

### 3. Element UI 自定义样式

项目包含 `src/styles/element-custom.scss` 文件,用于统一定制 Element Plus 组件样式:

**已定制的组件样式:**

- 输入框: 8px 圆角,优化边框和过渡效果
- 按钮: 8px 圆角,500 字重
- 卡片: 12px 圆角,无边框,无阴影
- 对话框: 12px 圆角
- 下拉框/日期选择器: 8px 圆角
- 表格: 8px 圆角,表头背景色 #f1f1f4,表头文字色 #1d2129,表头高度 46px
- 分页器: 6px 圆角
- 消息提示/通知: 8px 圆角
- 抽屉/弹出框/菜单: 统一圆角处理
- 滚动条: 使用 Element Plus 滚动条组件替代原生滚动条,统一视觉风格

**如何修改:**

直接编辑 `src/styles/element-custom.scss` 文件即可全局生效。例如修改输入框圆角:

```scss
.el-input__wrapper {
  border-radius: 12px !important; // 改为 12px
}
```

该文件会在 `main.ts` 中自动导入,无需额外配置。

### 4. 自定义主题颜色

根据用户需求修改 `src/styles/index.css`:

```css
:root {
  --primary-color: #409eff; /* 用户指定的主题色 */
  --sidebar-bg: #001529; /* 用户指定的侧边栏背景色 */
  --sidebar-text: #ffffff; /* 侧边栏文字色 */
  --sidebar-active-bg: #1890ff; /* 激活状态背景色 */
}
```

修改 `src/layout/index.vue` 中的侧边栏颜色变量:

```typescript
const sidebarBg = ref('#001529') // 用户指定的颜色
const sidebarText = ref('#ffffff')
const sidebarActiveText = ref('#1890ff')
```

### 5. 自定义登录页

登录页采用现代化的居中卡片设计:

**布局结构:**

- 整体: 900px 宽度居中卡片,浮动在灰色背景上
- 左侧(50%): 品牌展示区域(渐变背景 + Logo + 标题 + 副标题)
- 右侧(50%): 登录表单区域(白色背景 + 表单)
- 底部: 版权信息(绝对定位在页面底部)

**可自定义配置:**

在 `src/views/login/index.vue` 中修改以下配置:

```typescript
// 系统名称
const systemName = ref('管理后台系统')

// 系统副标题
const systemSubtitle = ref('快速开发前后端分离管理后台')

// 登录标题
const loginTitle = ref('系统登录')

// 底部文字
const footerText = ref('© 2024 All Rights Reserved')
```

**修改左侧背景渐变:**

```scss
.login-left {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
```

**常用渐变配色参考:**

- 蓝紫: `linear-gradient(135deg, #667eea 0%, #764ba2 100%)`
- 蓝绿: `linear-gradient(135deg, #0093E9 0%, #80D0C7 100%)`
- 橙红: `linear-gradient(135deg, #FA8BFF 0%, #2BD2FF 100%)`
- 深蓝: `linear-gradient(135deg, #1e3c72 0%, #2a5298 100%)`

**自定义 Logo:**

替换 `.logo-circle` 中的 SVG 图标,或使用图片:

```vue
<div class="logo-circle">
  <img src="@/assets/logo.png" alt="Logo" style="width: 80px; height: 80px;" />
</div>
```

**功能特性:**

- 记住账号功能(勾选后保存用户名到 localStorage)
- 响应式设计(小屏幕自动隐藏左侧品牌区域)
- 表单验证
- 回车登录

### 6. 配置路由和菜单

**重要原则:为所有菜单项创建完整的路由和页面**

根据用户需求的菜单项,在 `src/router/index.ts` 中添加路由。**必须为每个菜单项创建对应的路由和页面组件**,即使页面内容暂时为空,也要使用空状态模板,确保:

- 所有菜单项都可以点击
- 不会出现 404 或空白页面
- 项目结构完整,便于后续开发

**一级菜单路由示例:**

```typescript
{
  path: '/',
  component: () => import('@/layout/index.vue'),
  redirect: '/dashboard',
  children: [
    {
      path: 'dashboard',
      name: 'Dashboard',
      component: () => import('@/views/dashboard/index.vue'),
      meta: { title: '首页', icon: 'HomeFilled' }
    },
    {
      path: 'users',
      name: 'Users',
      component: () => import('@/views/users/index.vue'),
      meta: { title: '用户管理', icon: 'User' }
    }
  ]
}
```

**带子菜单的路由示例:**

```typescript
{
  path: 'system',
  name: 'System',
  component: () => import('@/views/system/index.vue'),  // 子路由容器
  redirect: '/system/user',
  meta: { title: '系统管理', icon: 'Setting' },
  children: [
    {
      path: 'user',
      name: 'SystemUser',
      component: () => import('@/views/system/user/index.vue'),
      meta: { title: '用户管理', icon: 'User' }
    },
    {
      path: 'role',
      name: 'SystemRole',
      component: () => import('@/views/system/role/index.vue'),
      meta: { title: '角色管理', icon: 'UserFilled' }
    }
  ]
}
```

**注意:**

- 带子菜单的路由需要创建一个容器组件(如 `views/system/index.vue`),内容为 `<router-view />`
- 必须添加 `redirect` 属性,指向第一个子路由

**常用图标参考:**

- `HomeFilled` - 首页
- `User` - 用户
- `Setting` - 设置
- `Document` - 文档
- `DataAnalysis` - 数据分析
- `ShoppingCart` - 购物车
- `List` - 列表

### 7. 创建页面组件

**重要:必须为所有菜单项创建页面组件**

为每个菜单项创建对应的页面组件,在 `src/views/` 下创建目录和文件。即使页面内容暂时为空,也必须创建空状态页面,确保所有菜单都可以正常访问。

**目录结构示例:**

```
src/views/
├── dashboard/
│   └── index.vue          # 首页(空状态)
├── system/
│   ├── index.vue          # 子路由容器(只包含 <router-view />)
│   ├── user/
│   │   └── index.vue      # 用户管理(空状态)
│   └── role/
│       └── index.vue      # 角色管理(空状态)
└── settings/
    └── index.vue          # 系统配置(空状态)
```

**子路由容器组件(如 system/index.vue):**

```vue
<template>
  <router-view />
</template>

<script setup lang="ts"></script>
```

**页面样式规范:**

- 所有卡片使用 `border: none !important; box-shadow: none !important; border-radius: 12px;`
- 页面容器使用 Flex 布局,使用 `gap` 控制间距
- 参考 page-generator 的样式规范

**空状态页面模板(页面开发中):**

对于暂时没有内容的页面,使用简洁的空状态模板:

```vue
<template>
  <div class="empty-page">
    <div class="empty-content">
      <h2 class="page-title">页面标题</h2>
      <p class="page-subtitle">页面开发中...</p>
    </div>
  </div>
</template>

<script setup lang="ts"></script>

<style scoped lang="scss">
  .empty-page {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #fff;
    border-radius: 12px;
  }

  .empty-content {
    text-align: center;
  }

  .page-title {
    font-size: 24px;
    font-weight: 500;
    color: #303133;
    margin-bottom: 12px;
  }

  .page-subtitle {
    font-size: 14px;
    color: #909399;
  }
</style>
```

**基础列表页面模板:**

```vue
<template>
  <div class="page-container">
    <!-- 筛选卡片 -->
    <el-card class="filter-card">
      <el-form :inline="true" :model="searchForm">
        <div class="filter-form-content">
          <el-form-item label="关键词">
            <el-input
              v-model="searchForm.keyword"
              placeholder="请输入关键词"
              clearable
              style="width: 250px"
            />
          </el-form-item>
          <el-form-item label=" ">
            <div class="filter-buttons">
              <el-button type="primary" @click="handleSearch">搜索</el-button>
              <el-button @click="handleReset">重置</el-button>
            </div>
          </el-form-item>
        </div>
      </el-form>
    </el-card>

    <!-- 数据卡片 -->
    <el-card class="data-card">
      <div class="table-header">
        <el-button type="primary" @click="handleAdd">新增</el-button>
      </div>
      <div class="table-container">
        <el-table :data="tableData" height="100%">
          <el-table-column prop="id" label="ID" width="80" />
          <el-table-column prop="name" label="名称" />
          <el-table-column label="操作" width="150">
            <template #default="{ row }">
              <el-button type="primary" link @click="handleEdit(row)">编辑</el-button>
              <el-button type="danger" link @click="handleDelete(row)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <el-pagination
        v-model:current-page="pagination.page"
        v-model:page-size="pagination.pageSize"
        :total="pagination.total"
        :page-sizes="[10, 20, 50, 100]"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="fetchList"
        @current-change="fetchList"
      />
    </el-card>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive } from 'vue'

  const searchForm = reactive({
    keyword: ''
  })

  const tableData = ref([])

  const pagination = reactive({
    page: 1,
    pageSize: 10,
    total: 0
  })

  const handleSearch = () => {}
  const handleReset = () => {}
  const handleAdd = () => {}
  const handleEdit = (row: any) => {}
  const handleDelete = (row: any) => {}
  const fetchList = () => {}
</script>

<style scoped lang="scss">
  .page-container {
    height: 100%;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  .filter-card {
    flex-shrink: 0;

    :deep(.el-card__body) {
      padding: 12px 20px;
    }

    .filter-form-content {
      display: flex;
      flex-wrap: wrap;
      gap: 16px;
      align-items: center;

      :deep(.el-form-item) {
        margin-bottom: 0;
      }
    }

    .filter-buttons {
      display: flex;

      .el-button:not(:first-child) {
        margin-left: 12px;
      }
    }
  }

  .data-card {
    flex: 1;
    overflow: hidden;
    display: flex;
    flex-direction: column;

    :deep(.el-card__body) {
      padding: 20px;
      height: 100%;
      display: flex;
      flex-direction: column;
    }

    .table-header {
      flex-shrink: 0;
      display: flex;
      margin-bottom: 16px;

      .el-button:not(:first-child) {
        margin-left: 12px;
      }
    }

    .table-container {
      flex: 1;
      overflow: hidden;
    }

    .el-pagination {
      flex-shrink: 0;
      margin-top: 16px;
      justify-content: flex-end;
    }
  }
</style>
```

### 8. 配置 Mock 数据

**重要：不使用 vite-plugin-mock 插件，直接使用 Mock.js**

所有 Mock 配置都在 `src/mock.ts` 文件中，该文件已在模板中包含。

**Mock 配置示例：**

```typescript
import Mock from 'mockjs'

// 配置 Mock
Mock.setup({
  timeout: '200-600'
})

// Mock 开关（默认开启，手动修改为 false 可关闭）
const MOCK_ENABLED = true

if (MOCK_ENABLED) {
  // 登录接口
  Mock.mock(/\/user\/login/, 'post', () => {
    return {
      code: 200,
      data: {
        token: 'mock-token-' + Date.now()
      },
      message: '登录成功'
    }
  })

  // 列表接口示例
  Mock.mock(/\/api\/list/, 'get', () => {
    return {
      code: 200,
      data: {
        list: Mock.mock({
          'list|10': [
            {
              'id|+1': 1,
              name: '@cname',
              createTime: '@datetime'
            }
          ]
        }).list,
        total: 100
      }
    }
  })

  console.log('[Mock] Mock 数据已启用')
}
```

**注意事项：**

- Mock 配置已在 `main.ts` 中自动导入
- 开发和生产环境都使用 Mock 数据
- 如需关闭 Mock，修改 `MOCK_ENABLED` 为 `false`
- 详细配置参考 `references/mock-configuration.md`

### 9. 调整布局(如需要)

如用户需要非默认的左右布局,修改 `src/layout/index.vue`:

**顶部导航布局:** 参考 `references/layout-configuration.md`

**混合布局:** 参考 `references/layout-configuration.md`

### 10. 生成项目说明

创建简单的 README.md 说明如何运行项目:

```markdown
# 项目名称

## 安装依赖

\`\`\`bash npm install \`\`\`

## 运行项目

\`\`\`bash npm run dev \`\`\`

## 构建项目

\`\`\`bash npm run build \`\`\`

## 默认账号

- 用户名: admin
- 密码: 123456
```

### 11. 验证构建并启动项目

项目创建完成后,按以下顺序执行命令:

**步骤 1: 安装依赖**

```bash
npm install
```

**步骤 2: 验证构建(重要!)**

```bash
npm run build
```

这一步非常重要,可以提前发现:

- TypeScript 类型错误
- 导入路径错误
- 语法错误
- 配置问题

**如果构建失败:**

1. 根据错误信息修复问题
2. 再次运行 `npm run build` 验证
3. 重复此过程直到构建成功

只有构建成功后,才进行下一步。

**步骤 3: 启动开发服务器**

```bash
npm run dev
```

**注意事项:**

- 服务器配置了 `host: true`,可以从外部访问
- 默认端口为 3000,浏览器会自动打开
- 如果端口被占用,Vite 会自动使用下一个可用端口
- 如果 build 失败,需要先修复错误再启动 dev 服务器
- IDE 可能显示类型错误（如 "找不到模块 node:url"），这不影响实际构建和运行

## 进阶自定义

如用户需要更复杂的自定义,参考以下文档:

- **主题深度定制:** 查看 `references/theme-customization.md`
- **布局调整:** 查看 `references/layout-configuration.md`
- **路由和菜单配置:** 查看 `references/router-menu-configuration.md`
- **Mock 数据配置:** 查看 `references/mock-data-guide.md`

## 注意事项

1. 确保用户已安装 Node.js (建议 v18+)
2. 项目使用 pnpm/npm/yarn 均可
3. 所有数据均为 Mock 数据,无需后端
4. 默认端口为 3000,可在 vite.config.ts 中修改
5. 路由使用 history 模式,部署时需配置服务器

---
> Source: [wang-q-l/ai-web](https://github.com/wang-q-l/ai-web) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
