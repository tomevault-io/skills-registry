---
name: vue-cohesion-coupling
description: Vue组件设计最佳实践 - 快速创建高质量、可维护的Vue组件。包含完整代码示例、反模式避坑指南、组件通信模式、代码审查清单。Use whenever: (1) Creating or modifying Vue components, (2) Deciding component structure and communication patterns, (3) Refactoring messy or tightly-coupled components, (4) Implementing props/events/Vuex communication, (5) Reviewing Vue code for quality issues, (6) Asking 'how should I structure this component?' or 'is this component well-designed?'. Helps avoid common mistakes like over-coupling, mixed responsibilities, and direct component access. Use when this capability is needed. Use when this capability is needed.
metadata:
  author: tomevault-io
---

# Vue高内聚低耦合设计指南

本skill提供Vue.js组件设计中高内聚低耦合的完整指导，帮助创建可维护、可测试、可复用的组件架构。

## 核心概念

**高内聚**：组件内部功能紧密相关，职责单一明确
**低耦合**：组件之间依赖关系最小化，相互独立

## 快速参考

### 高内聚实现要点
- 单一职责原则：每个组件只负责一个功能
- 封装内部状态：不暴露不必要的实现细节
- 明确的接口：通过props和events与外部交互

### 低耦合实现要点
- Props向下传递数据，Events向上传递操作
- 使用Vuex管理全局共享状态
- 使用依赖注入避免props逐层传递
- 使用插槽提供灵活的内容分发

## 使用指南

### 组件设计阶段

**评估组件职责**：
- 组件是否只负责一个明确的功能？
- 组件名称是否清晰描述其职责？
- 组件是否可以在其他场景复用？

**设计组件接口**：
- Props：定义需要接收的数据
- Events：定义触发的操作
- Slots：定义可扩展的内容区域

### 组件实现阶段

**实现高内聚**：
- 封装组件内部状态和逻辑
- 只暴露必要的props和events
- 保持组件方法的单一职责

**实现低耦合**：
- 避免直接访问其他组件实例
- 避免通过`$parent`、`$children`访问组件
- 使用事件总线或Vuex进行跨组件通信

### 代码审查阶段

**检查高内聚**：
- 组件是否包含多个不相关的功能？
- 组件内部状态是否被外部直接修改？
- 组件接口是否清晰明确？

**检查低耦合**：
- 组件之间是否存在直接依赖？
- 是否正确使用props和events通信？
- 是否合理使用Vuex管理共享状态？

## 详细参考文档

### 设计模式和实现策略
参见 [patterns.md](references/patterns.md) 了解：
- 高内聚设计模式（单一职责、封装、接口设计）
- 低耦合设计模式（通信模式、状态管理、依赖注入）
- 组件拆分策略（按功能、按层级、容器/展示分离）
- 常见反模式及解决方案

### 完整代码示例
参见 [examples.md](references/examples.md) 查看：
- Todo应用完整实现（展示高内聚低耦合）
- 用户管理组件（使用Vuex的示例）
- 容器组件与展示组件分离
- 组件通信模式实践

### 最佳实践和检查清单
参见 [best-practices.md](references/best-practices.md) 获取：
- 组件设计原则（SOLID原则在Vue中的应用）
- 组件通信最佳实践（props、events、v-model、.sync）
- 状态管理策略（何时使用Vuex、模块化、持久化）
- 组件复用策略（mixins、组合式函数、插槽）
- 性能优化技巧
- 代码审查检查清单

## 常见使用场景

### 场景1：设计新组件

**步骤**：
1. 明确组件的单一职责
2. 设计组件接口（props、events、slots）
3. 实现组件内部逻辑
4. 确保组件可独立测试

**参考**：[patterns.md](references/patterns.md) 的设计模式部分

### 场景2：重构现有组件

**步骤**：
1. 识别组件的多个职责
2. 按职责拆分成多个子组件
3. 使用props和events重新组织通信
4. 提取共享逻辑到mixins或组合式函数

**参考**：[patterns.md](references/patterns.md) 的组件拆分策略

### 场景3：实现组件通信

**选择通信方式**：
- 父子组件：使用props和events
- 兄弟组件：使用事件总线或Vuex
- 跨层级：使用provide/inject或Vuex
- 复杂场景：使用Vuex集中管理状态

**参考**：[best-practices.md](references/best-practices.md) 的组件通信部分

### 场景4：代码审查

**检查要点**：
- 组件职责是否单一？
- 是否正确使用props和events？
- 是否存在过度耦合？
- 是否遵循最佳实践？

**参考**：[best-practices.md](references/best-practices.md) 的代码审查检查清单

## 实践建议

### DO（推荐做法）
- ✅ 为每个组件定义清晰的职责
- ✅ 使用props和events进行组件通信
- ✅ 封装组件内部状态和逻辑
- ✅ 使用计算属性缓存复杂计算
- ✅ 合理拆分大型组件
- ✅ 使用Vuex管理全局状态

### DON'T（避免做法）
- ❌ 在组件中混合多个不相关的功能
- ❌ 直接访问其他组件的实例
- ❌ 通过`$parent`、`$children`访问组件
- ❌ 直接修改props的值
- ❌ 在组件中直接修改Vuex状态（应通过mutation）
- ❌ 过度使用mixins导致命名冲突

## 进阶主题

### 组合式API vs Options API
- Vue 2.7+ 和 Vue 3 支持组合式API
- 组合式API更适合逻辑复用和代码组织
- 参见 [best-practices.md](references/best-practices.md) 的组合式函数部分

### TypeScript支持
- 使用TypeScript可以增强类型安全
- 明确定义props和events的类型
- 参见项目中的TypeScript组件示例

### 性能优化
- 合理使用v-if和v-show
- 使用计算属性缓存
- 组件懒加载
- 列表渲染优化
- 参见 [best-practices.md](references/best-practices.md) 的性能优化部分

## Resources

### scripts/
Executable code (Python/Bash/etc.) that can be run directly to perform specific operations.

**Examples from other skills:**
- PDF skill: `fill_fillable_fields.py`, `extract_form_field_info.py` - utilities for PDF manipulation
- DOCX skill: `document.py`, `utilities.py` - Python modules for document processing

**Appropriate for:** Python scripts, shell scripts, or any executable code that performs automation, data processing, or specific operations.

**Note:** Scripts may be executed without loading into context, but can still be read by Claude for patching or environment adjustments.

### references/
Documentation and reference material intended to be loaded into context to inform Claude's process and thinking.

**Examples from other skills:**
- Product management: `communication.md`, `context_building.md` - detailed workflow guides
- BigQuery: API reference documentation and query examples
- Finance: Schema documentation, company policies

**Appropriate for:** In-depth documentation, API references, database schemas, comprehensive guides, or any detailed information that Claude should reference while working.

### assets/
Files not intended to be loaded into context, but rather used within the output Claude produces.

**Examples from other skills:**
- Brand styling: PowerPoint template files (.pptx), logo files
- Frontend builder: HTML/React boilerplate project directories
- Typography: Font files (.ttf, .woff2)

**Appropriate for:** Templates, boilerplate code, document templates, images, icons, fonts, or any files meant to be copied or used in the final output.

---

**Any unneeded directories can be deleted.** Not every skill requires all three types of resources.

---
> Source: [caobingsheng/skills](https://github.com/caobingsheng/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

---
> Source: [tomevault-io/skills-registry](https://github.com/tomevault-io/skills-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
