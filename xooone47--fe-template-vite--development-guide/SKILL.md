---
name: development-guide
description: Provides development guidelines based on the project architecture, including directory structure, code style, naming conventions, and best practices. Invoke when user needs development guidance or code review.
metadata:
  author: xooone47
---

# 开发守则

## 1. 项目架构概述

本项目是基于 Vite 的前端模板项目，使用以下技术栈：

- **框架**: React 18 + TypeScript
- **构建工具**: Vite
- **代码规范**: ESLint
- **测试框架**: Jest
- **状态管理**: Recoil + Zustand
- **网络请求**: Axios
- **UI 库**: Antd
- **CSS 预处理器**: Less
- **包管理器**: pnpm

## 2. 目录结构规范

### 2.1 整体目录结构

```
fe-template-vite/
├── bin/                # 命令行工具
├── configs/            # 配置文件
│   ├── __mocks__/      # 测试 mock 文件
│   ├── babel.config.js # Babel 配置
│   ├── jest.config.js  # Jest 配置
│   └── postcss.config.js # PostCSS 配置
├── scripts/            # 脚本文件
├── src/                # 源代码
│   ├── apis/           # API 请求相关
│   ├── assets/         # 静态资源
│   ├── components/     # 组件
│   ├── hooks/          # 自定义 hooks
│   ├── store/          # 状态管理
│   ├── styles/         # 全局样式
│   ├── types/          # TypeScript 类型定义
│   ├── utils/          # 工具函数
│   └── index.tsx       # 应用入口
├── .eslintrc.js        # ESLint 配置
├── .gitignore          # Git 忽略文件
├── package.json        # 项目配置和依赖
├── tsconfig.json       # TypeScript 配置
└── vite.config.js      # Vite 配置
```

### 2.2 目录职责说明

- **src/apis/**: 存放 API 请求相关代码，包括请求拦截、响应处理等
- **src/assets/**: 存放静态资源，如图片、图标等
- **src/components/**: 存放组件，按功能模块组织
- **src/hooks/**: 存放自定义 React Hooks
- **src/store/**: 存放状态管理相关代码
- **src/styles/**: 存放全局样式文件
- **src/types/**: 存放 TypeScript 类型定义
- **src/utils/**: 存放工具函数

## 3. 代码风格规范

### 3.1 缩进和空格

- 使用 **4 个空格** 进行缩进
- 禁止使用 Tab 键进行缩进
- 操作符两侧添加空格
- 逗号后添加空格

### 3.2 引号和分号

- 使用 **单引号** 定义字符串
- **必须** 使用分号结束语句
- JSX 中的属性使用双引号

### 3.3 代码长度

- 单行代码长度不超过 **120** 个字符
- 单个文件行数不超过 **800** 行
- 单个函数语句数不超过 **30** 个

### 3.4 空行和换行

- 不同逻辑块之间添加空行
- 函数定义之间添加空行
- 多行代码的逗号放在行尾
- 对象和数组的大括号使用换行对齐

## 4. 命名规范

### 4.1 文件命名

- **组件文件**: 使用 PascalCase 命名，如 `Home.tsx`
- **样式文件**: 使用 kebab-case 命名，如 `styles.module.less`
- **工具函数文件**: 使用 camelCase 命名，如 `utils.ts`
- **类型定义文件**: 使用 PascalCase 命名，如 `types.ts`

### 4.2 变量命名

- **常量**: 使用 UPPER_SNAKE_CASE 命名，如 `MAX_COUNT`
- **变量**: 使用 camelCase 命名，如 `userName`
- **函数**: 使用 camelCase 命名，如 `formatDate`
- **类**: 使用 PascalCase 命名，如 `UserService`
- **接口**: 使用 PascalCase 命名，如 `UserInterface`
- **类型别名**: 使用 PascalCase 命名，如 `UserType`

### 4.3 组件命名

- 组件名称使用 PascalCase
- 组件目录名与组件名保持一致
- 组件导出使用默认导出

## 5. 开发流程规范

### 5.1 依赖管理

- **必须** 使用 pnpm 作为包管理器
- 安装依赖: `pnpm install`
- 添加依赖: `pnpm add <package>`
- 添加开发依赖: `pnpm add -D <package>`

### 5.2 开发命令

- 启动开发服务器: `pnpm start`
- 构建项目: `pnpm build`
- 运行测试: `pnpm test`
- 代码检查: `pnpm lint`
- 类型检查: `pnpm lint-type`

### 5.3 代码提交规范

- 使用 husky 进行 git 钩子管理
- 提交前会自动运行 lint-staged 进行代码检查
- 推送前会自动运行 lint 和 test 命令

## 6. 最佳实践

### 6.1 组件开发

- **优先使用函数式组件**和 React Hooks
- 使用 TypeScript 为组件添加类型定义
- 使用 CSS Modules 避免样式冲突
- 合理拆分组件，保持组件职责单一
- 使用 React.memo 优化组件性能

### 6.2 状态管理

- 简单状态使用 React useState
- 复杂状态使用 Recoil 或 Zustand
- 合理设计状态结构，避免过度嵌套
- 使用 useImmer 简化状态更新

### 6.3 API 请求

- 使用封装后的 axios 实例进行请求
- 统一处理请求错误和响应
- 使用 async/await 处理异步请求
- 合理使用请求缓存

### 6.4 错误处理

- 使用 try/catch 捕获异步错误
- 统一处理 API 错误
- 提供友好的错误提示
- 避免使用 console.log 进行调试

### 6.5 性能优化

- 使用 React.lazy 和 Suspense 进行代码分割
- 合理使用 useMemo 和 useCallback 缓存计算结果和函数
- 避免在渲染过程中创建新对象和函数
- 优化图片资源，使用适当的格式和大小

## 7. 代码示例

### 7.1 组件示例

```tsx
import React from 'react';
import styles from './styles.module.less';

const Home: React.FC = () => {
    return (
        <div className={styles.container}>
            <h1>Home Page</h1>
        </div>
    );
};

export default Home;
```

### 7.2 API 请求示例

```ts
import { get, post } from './request';

export const fetchUsers = () => {
    return get('/api/users');
};

export const createUser = (userData: any) => {
    return post('/api/users', userData);
};
```

### 7.3 工具函数示例

```ts
export const formatDate = (date: any): string => {
    return new Date(date).toISOString().split('T')[0];
};

export const sleep = (ms: number): Promise<void> => {
    return new Promise(resolve => setTimeout(resolve, ms));
};
```

## 8. 常见问题和解决方案

### 8.1 依赖冲突

**问题**: 安装依赖时出现版本冲突
**解决方案**: 使用 `pnpm dedupe` 解决依赖冲突

### 8.2 类型错误

**问题**: TypeScript 编译时出现类型错误
**解决方案**: 检查类型定义，确保类型一致性

### 8.3 构建失败

**问题**: 项目构建失败
**解决方案**: 检查代码是否符合 ESLint 规范，修复类型错误

### 8.4 测试失败

**问题**: 测试运行失败
**解决方案**: 检查测试用例，确保测试逻辑正确

## 9. 总结

本开发守则基于项目的实际架构和代码风格制定，旨在规范开发流程，提高代码质量，确保项目的可维护性和可扩展性。

所有开发人员在参与项目开发时，应严格遵守本守则的各项规范，确保代码风格一致，开发流程规范。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xooone47) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
