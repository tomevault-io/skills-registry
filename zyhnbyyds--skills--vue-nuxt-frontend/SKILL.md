---
name: vue-nuxt-frontend
description: 为 Vue 3、Nuxt 3、Vite、UnoCSS、VueUse、TypeScript 项目提供实现与重构工作流。用于页面开发、组件设计、组合式逻辑抽象、接口接入、Prisma/MySQL/Redis/Supabase 集成，以及联动可维护性规范和目录分层技能完成业务实现。 Use when this capability is needed.
metadata:
  author: zyhnbyyds
---

# Vue / Nuxt Frontend Workflow

这个技能负责具体业务实现和技术选型，不重复定义编码硬规范与目录分层规则。涉及可维护性约束时联动 [code-maintainability-vue](../code-maintainability-vue/SKILL.md)，涉及目录与文件分层时联动 [vue-directory-structure](../vue-directory-structure/SKILL.md)。

## 适用场景

- 为 Vue 3 或 Nuxt 3 项目开发新页面、新模块或新组件
- 在 Vite 项目中实现交互、状态管理、接口接入或组件重构
- 使用 UnoCSS、VueUse、TypeScript 组织前端代码
- 在 Nuxt 项目中落地 Prisma、MySQL、Redis 或 Supabase 相关能力
- 处理前端工程规范、可维护性、性能和交付质量检查

## 默认技术偏好

- 优先使用 Vue 生态，而不是跨框架抽象
- 轻量前端应用、组件库、纯客户端页面优先考虑 Vite
- 需要 SSR、SEO、服务端路由、BFF 能力时优先考虑 Nuxt
- 样式优先使用 UnoCSS，避免随意引入额外样式体系
- 通用交互逻辑优先抽成 composables，优先考虑 VueUse 现成能力
- 默认使用 TypeScript，并补全核心领域类型、接口类型和表单类型
- Nuxt 场景下，关系型查询默认优先 Prisma + MySQL
- 需要缓存、会话、限流或异步队列协作时优先考虑 Redis
- 涉及认证、复杂权限、实时能力或需要托管后端能力时评估 Supabase
- 代码收尾默认执行格式化和静态检查，优先使用项目中的 oxlint、oxfmt 或等价脚本

## 联动规则

- 开始实现前，先按 [code-maintainability-vue](../code-maintainability-vue/SKILL.md) 输出范围、计划、影响和风险
- 涉及目录新增、目录迁移、模块拆分或公共层抽取时，先按 [vue-directory-structure](../vue-directory-structure/SKILL.md) 确定分层方案
- 本技能只保留技术实现决策，不重复维护命名、注释、目录职责的完整规则

## 工作流

### 1. 先判断项目形态

先识别当前需求属于哪一类，再决定实现路径。

- 如果是纯前端交互、组件开发、后台管理页面或轻量应用，优先沿用 Vite 项目结构
- 如果需求涉及 SSR、SEO、服务端接口、鉴权中间层或全栈页面，优先沿用 Nuxt 项目结构
- 如果仓库已有明确框架，不主动切换技术栈，只在现有约束内实现

### 2. 先读现有代码，再决定抽象层级

- 先查看现有目录结构、组件组织方式、命名规则、接口封装方式和样式约定
- 优先复用已有基础组件、composables、services、server routes、stores 和类型定义
- 只有当重复逻辑已经明显出现，或新需求会持续扩展时，才新增抽象层
- 新增抽象前，先检查是否符合 [code-maintainability-vue](../code-maintainability-vue/SKILL.md) 的过度封装约束

### 3. 按场景选择实现策略

#### 页面与组件

- Vue 单文件组件默认优先使用 script setup + TypeScript
- 组件 API 保持稳定，props、emits、slots 命名清晰，避免隐式副作用
- 视图层尽量保持薄，复杂逻辑下沉到 composables 或服务层
- 样式优先使用 UnoCSS 原子类；只有在复用价值明确时再沉淀公共样式封装
- 组件和页面文件的放置方式，优先遵循 [vue-directory-structure](../vue-directory-structure/SKILL.md)

#### 状态与逻辑

- 局部状态优先使用 ref、reactive、computed
- 跨组件复用逻辑优先抽到 composables
- 需要监听浏览器能力、节流、防抖、生命周期封装时优先使用 VueUse
- 不为简单状态过早引入重量级全局状态方案

#### 数据获取

- Vite 项目优先沿用现有请求层，统一处理类型、异常和重试策略
- Nuxt 项目优先使用 useAsyncData、useFetch、server/api 和运行时配置
- 服务端和客户端边界要明确，避免把仅服务端可用的逻辑泄漏到客户端
- 每个异步流程都要明确 loading、error、empty 和 success 四类状态

#### 数据库与服务能力

- Nuxt 中需要关系型数据建模和查询时，优先 Prisma + MySQL
- 需要缓存热点数据、验证码、token、会话或任务协调时，优先 Redis
- 如果需求包含认证、RBAC、存储、实时订阅或希望减少自建后端成本，评估 Supabase
- 选择 Supabase 前先确认是否与现有 Prisma/MySQL 架构重复或冲突

### 4. 实现时的技术约束

- 不引入与当前仓库风格冲突的状态管理、样式系统或请求库
- 不把接口原始响应直接暴露到组件模板层，必要时先做映射
- 不忽略 SSR 安全性、浏览器 API 可用性和 hydration 风险
- 抽象层级、命名和文件职责的硬规则统一参考 [code-maintainability-vue](../code-maintainability-vue/SKILL.md)
- 目录与模块分层统一参考 [vue-directory-structure](../vue-directory-structure/SKILL.md)

### 5. 收尾与验证

完成后至少检查以下项目。

- 类型是否闭合，关键 props、接口响应、表单数据是否有明确类型
- 是否覆盖空态、错误态、加载态和成功态
- 是否存在明显重复逻辑或技术实现层面的抽象失衡
- Nuxt 场景下是否存在服务端客户端边界错误
- 页面在移动端和桌面端是否都能正常使用
- 基础交互是否可访问，包含按钮语义、表单反馈和可聚焦性
- 样式是否遵守现有设计语言，而不是临时拼凑
- 优先运行项目已有检查命令；如果项目没有封装脚本，则运行 oxlint、oxfmt 或等价校验
- 可维护性和目录层面的复查分别交由 [code-maintainability-vue](../code-maintainability-vue/SKILL.md) 与 [vue-directory-structure](../vue-directory-structure/SKILL.md)

## 决策分支

### 何时优先 Nuxt

- 需要 SSR 或 SEO
- 需要服务端路由或服务端中间层
- 需要在同一项目中同时管理页面、接口和数据访问

### 何时优先 Prisma

- 已确定使用 Nuxt 且后端数据主模型是关系型结构
- 需要类型安全查询、迁移管理和明确的数据模型边界

### 何时优先 Redis

- 需要缓存、限流、会话、分布式锁、消息协调或临时态数据

### 何时评估 Supabase

- 需求重点在认证、实时同步、对象存储或快速搭建复杂业务底座
- 团队希望减少自建鉴权和部分基础设施成本

## 产出要求

使用这个技能时，默认应产出以下结果。

- 与现有项目结构一致的实现方案
- 明确的文件修改建议或直接代码实现
- 涉及框架选择时给出简短取舍理由
- 收尾时补充验证步骤和剩余风险

## 可直接使用的提示词

- 使用 vue-nuxt-frontend 技能，在 Nuxt 3 + UnoCSS 项目里实现一个带筛选和分页的用户列表页
- 使用 vue-nuxt-frontend 技能，把这个重复表单逻辑抽成 composable，并保留完整 TypeScript 类型
- 使用 vue-nuxt-frontend 技能，评估当前需求应该走 Prisma + MySQL 还是 Supabase
- 使用 vue-nuxt-frontend 技能，重构这个 Vue 组件，要求保留视觉表现但降低耦合

## 联动技能

- 先用 [code-maintainability-vue](../code-maintainability-vue/SKILL.md) 约束修改范围、计划和硬性编码规范
- 再用 [vue-directory-structure](../vue-directory-structure/SKILL.md) 确认目录和文件分层

---
> Source: [zyhnbyyds/skills](https://github.com/zyhnbyyds/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
