---
name: vue-directory-structure
description: 用于 Vue 和 Nuxt 项目的目录结构设计、文件分层、模块职责划分和就近组织规则。适合在新建模块、整理目录、拆分页面、沉淀公共组件、控制文件职责时使用。 Use when this capability is needed.
metadata:
  author: zyhnbyyds
---

# Vue Directory Structure

## 适用场景

- 新建 Vue 或 Nuxt 模块前，需要先设计目录和文件分层
- 现有目录出现职责混乱、同类文件分散、复用边界不清
- 页面逐渐复杂，需要决定哪些逻辑应就近放置，哪些应沉淀为公共层
- 需要控制 components、composables、services、types、utils、stores 的边界

## 核心目标

- 目录按职责分层，而不是按临时需求堆积
- 同类逻辑集中，跨目录跳转尽量少
- 局部逻辑就近收敛，公共逻辑只在确有复用价值时上提
- 目录名、文件名和职责一一对应，降低维护者的定位成本

## 默认分层规则

### 页面层

- pages 负责路由页面或页面入口
- 页面文件只负责页面编排、页面态和模块组合，不承载过多细碎实现
- 单页面专属的小组件、hooks、types 可以就近放在页面同级或子目录

### 组件层

- components 放可复用组件，不放一次性页面碎片
- 单页面内部使用的组件优先 colocate，就近放在对应页面目录
- 基础 UI 组件和业务组件分层，避免全部堆在同一个 components 目录

### 逻辑层

- composables 放跨组件复用或可独立复用的状态逻辑
- services 放接口编排、数据访问、领域动作，不放纯视图状态
- utils 放纯函数和无业务上下文工具，不放依赖页面状态的逻辑
- stores 只放真正跨区域共享且有持续价值的状态

### 类型层

- types 放跨文件共享的领域类型
- 仅单文件使用的简单类型优先就近定义
- 不为了一两个类型单独制造过深层级

## 决策规则

### 何时就近放置

- 只被当前页面或当前模块使用
- 逻辑强依赖页面上下文，脱离页面没有意义
- 上提后只会增加理解路径，不会提升复用价值

### 何时上提为公共层

- 至少已有两个稳定使用点
- 命名和职责已经足够抽象稳定
- 提取后能减少重复且不会隐藏业务语义

### 何时拆模块

- 单文件已经难以维持清晰主流程
- 页面中同时存在多个相对独立的业务区块
- 拆分后能让职责边界更清楚，而不是只把复杂度转移到更多文件

## 目录命名规则

- 目录名表达职责，不表达临时状态
- 避免 common2、temp、new、other、misc、test2 这类名称
- 同类目录命名保持统一，例如 composables、services、types，不混用 hooks、logic、helper 等近义名
- 文件名优先体现业务语义，其次才是技术形式

## 推荐组织方式

### 小型模块

- 一个页面文件
- 少量就近组件
- 少量就近类型或 composable

### 中型模块

- 页面入口
- components 子目录放模块内复用组件
- composables 子目录放模块内复用逻辑
- types 或 constants 子目录按需补充

### 大型模块

- 先按业务子域拆分，再在子域内部做页面、组件、逻辑分层
- 不直接在顶层堆满 components、services、utils 子目录后把所有子域混在一起

## Nuxt 场景补充

- server/api、server/services、server/utils 与前端目录分开管理
- 页面展示逻辑和服务端数据访问逻辑不要混在同一层级
- composables 中如果依赖服务端上下文，要明确客户端可用边界

## 整理目录时的执行流程

### 第一步：盘点现状

- 列出当前目录、文件职责、重复点和跨目录跳转链路

### 第二步：划分边界

- 明确哪些保留就近，哪些提升为模块级，哪些才有资格进入全局公共层

### 第三步：输出迁移方案

- 写清楚目录变更、文件移动、导入路径变化和潜在影响

### 第四步：实施最小迁移

- 只做有明确收益的目录调整，不顺手大洗牌

### 第五步：复查维护成本

- 检查主流程是否更容易定位
- 检查是否减少了无意义跳转
- 检查是否出现新的空壳目录或职责不明文件

## 完成标准

- 维护者能快速判断每类文件该放在哪里
- 目录名和文件职责一致
- 页面主流程优先就近可见
- 公共层不承载一次性逻辑
- 目录调整后导入关系更清楚，而不是更混乱

## 可直接使用的提示词

- 使用 vue-directory-structure 技能，为这个 Vue 模块设计清晰的目录分层
- 使用 vue-directory-structure 技能，判断当前逻辑该就近放页面还是抽到 composable
- 使用 vue-directory-structure 技能，整理这个 Nuxt 模块的目录并给出最小迁移计划

## 联动技能

- 需要对实现本身施加硬性可维护性约束时，联动使用 [code-maintainability-vue](../code-maintainability-vue/SKILL.md)
- 需要落地具体 Vue 或 Nuxt 页面开发时，联动使用 [vue-nuxt-frontend](../vue-nuxt-frontend/SKILL.md)

---
> Source: [zyhnbyyds/skills](https://github.com/zyhnbyyds/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
