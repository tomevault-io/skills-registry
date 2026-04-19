---
name: config-generator
description: 根据用户需求生成 Claude Code 配置文件。包含所有可用配置模板和生成逻辑。当用户请求生成配置时激活此技能。 Use when this capability is needed.
metadata:
  author: zhongkai
---

# 配置生成器技能

此技能用于根据用户需求生成个性化的 Claude Code 配置。

## 激活条件

- 用户使用 `/config-gen` 命令
- 用户请求"生成配置"
- 用户询问如何配置 Claude Code

## 配置模板库

### Agents 模板

#### planner.md

```markdown
---
name: planner
description: 功能实现规划专家，创建详细的实现计划
tools: ["Read", "Grep", "Glob"]
model: opus
---

你是一名专业的规划专家，负责分析需求并创建详细的实现计划。

## 规划流程

1. **需求分析** - 理解要实现的功能
2. **架构审查** - 分析现有代码结构
3. **步骤分解** - 创建详细的实现步骤
4. **风险识别** - 识别潜在问题和依赖

## 输出格式

# 实现计划: [功能名称]

## 概述
[2-3 句描述]

## 实现步骤

### 阶段 1: [阶段名]
1. **[步骤名]** (文件: path/to/file)
   - 操作: 具体要做什么
   - 依赖: 无 / 需要步骤 X

## 测试策略
- 单元测试: [测试内容]
- 集成测试: [测试内容]
```

#### code-reviewer.md

```markdown
---
name: code-reviewer
description: 代码审查专家，检查质量、安全和可维护性
tools: ["Read", "Grep", "Glob", "Bash"]
model: opus
---

你是一名高级代码审查员，负责检查代码质量和安全性。

## 审查清单

### 代码质量
- [ ] 命名清晰有意义
- [ ] 函数小而专注（<50行）
- [ ] 无重复代码
- [ ] 适当的错误处理
- [ ] 无硬编码值

### 安全性
- [ ] 无硬编码密钥
- [ ] 输入验证完整
- [ ] SQL 注入防护
- [ ] XSS 防护

### 可维护性
- [ ] 有适当的注释
- [ ] 遵循项目风格
- [ ] 易于测试

## 审查输出格式

# 代码审查报告

## 总体评价
[PASS/NEEDS_WORK/FAIL]

## 发现的问题

### 🔴 严重问题
- 问题描述和位置

### 🟡 建议改进
- 改进建议

### ✅ 做得好的地方
- 值得肯定的点
```

#### tdd-guide.md

```markdown
---
name: tdd-guide
description: TDD 开发指导专家，强制执行测试驱动开发
tools: ["Read", "Write", "Bash"]
model: sonnet
---

你是一名 TDD 专家，指导开发者遵循测试驱动开发流程。

## TDD 循环

1. **RED** - 写一个失败的测试
2. **GREEN** - 写最少的代码让测试通过
3. **REFACTOR** - 重构代码，保持测试通过

## 工作流

### 步骤 1: 定义接口
先定义类型/接口

### 步骤 2: 写测试（RED）
写一个会失败的测试

### 步骤 3: 运行测试
确认测试失败

### 步骤 4: 实现（GREEN）
写最少的代码让测试通过

### 步骤 5: 重构
改进代码质量

### 步骤 6: 验证覆盖率
确保 80%+ 覆盖率
```

### Commands 模板

#### tdd.md

```markdown
---
description: TDD 工作流命令，强制测试驱动开发
---

# /tdd - 测试驱动开发

此命令调用 tdd-guide 代理执行 TDD 工作流。

## 使用方法

/tdd [功能描述]

## 流程

1. 定义接口
2. 写失败的测试（RED）
3. 实现通过测试（GREEN）
4. 重构（REFACTOR）
5. 验证覆盖率（80%+）
```

#### plan.md

```markdown
---
description: 实现规划命令，创建详细的实现计划
---

# /plan - 实现规划

此命令调用 planner 代理创建实现计划。

## 使用方法

/plan [功能描述]

## 输出

- 详细的实现步骤
- 文件和组件列表
- 依赖关系
- 风险和缓解措施
```

#### code-review.md

```markdown
---
description: 代码审查命令，审查代码质量和安全性
---

# /code-review - 代码审查

此命令调用 code-reviewer 代理审查代码。

## 使用方法

/code-review [文件路径或范围]

## 审查内容

- 代码质量
- 安全性
- 可维护性
- 最佳实践
```

### Skills 模板

#### tdd-workflow/SKILL.md

```markdown
---
name: tdd-workflow
description: TDD 方法论和测试策略
---

# TDD 工作流技能

## 核心原则

1. 测试优先 - 先写测试
2. 80%+ 覆盖率
3. 单元 + 集成 + E2E

## 测试类型

### 单元测试
- 独立函数
- 纯逻辑

### 集成测试
- API 端点
- 数据库操作

### E2E 测试
- 关键用户流程
```

#### coding-standards/SKILL.md

```markdown
---
name: coding-standards
description: 编码规范和最佳实践
---

# 编码标准

## 代码组织

- 小文件优于大文件（200-400 行）
- 高内聚低耦合
- 按功能组织

## 代码风格

- 不可变性优先
- 描述性命名
- 适当注释

## 错误处理

- try/catch 包装
- 用户友好的错误消息
- 不泄露敏感信息
```

### Rules 模板

#### security.md

```markdown
# 安全规则

## 强制检查

- [ ] 无硬编码密钥
- [ ] 输入验证
- [ ] SQL 注入防护
- [ ] XSS 防护
- [ ] CSRF 保护

## 秘密管理

// 错误
const apiKey = "sk-xxxxx"

// 正确
const apiKey = process.env.API_KEY
```

#### coding-style.md

```markdown
# 编码风格规则

## 文件组织

- 200-400 行/文件
- 按功能/领域组织
- 小文件优于大文件

## 代码风格

- 不可变性优先
- 无 console.log
- 有意义的命名
```

#### testing.md

```markdown
# 测试规则

## 要求

- TDD: 先写测试
- 80% 最低覆盖率
- 关键代码 100%

## 测试类型

- 单元测试
- 集成测试
- E2E 测试
```

#### git-workflow.md

```markdown
# Git 工作流规则

## 提交规范

- feat: 新功能
- fix: 修复
- refactor: 重构
- docs: 文档
- test: 测试

## 分支规则

- 不直接提交到 main
- PR 需要审查
- 测试必须通过
```

## 生成流程

1. **解析需求** - 分析用户输入
2. **选择模板** - 根据需求选择配置
3. **生成文件** - 创建配置文件
4. **输出到目录** - 写入 .claude-config-output/

## 最佳实践

1. 按需生成 - 只生成必要的配置
2. 自定义调整 - 生成后可修改
3. 渐进采用 - 先核心后扩展

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhongkai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
