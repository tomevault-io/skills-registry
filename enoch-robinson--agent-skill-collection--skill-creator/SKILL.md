---
name: skill-creator
description: Skill 创建指南。当用户需要创建新的 Skill、更新现有 Skill、或学习如何设计高质量 Skill 时使用此技能。 Use when this capability is needed.
metadata:
  author: enoch-robinson
---

# Skill Creator

指导创建高质量的 Skills，扩展 Claude 的专业能力。

## 什么是 Skill？

Skill 是模块化、自包含的能力包，通过提供专业知识、工作流程和工具来扩展 Claude 的能力。它们就像特定领域的"入职指南"——将Claude 从通用助手转变为特定任务的专家。

### Skill 能提供什么

1. **专业工作流** - 特定领域的多步骤流程
2. **工具集成** - 特定文件格式或API 的操作指南
3. **领域知识** - 公司特定的知识、模式、业务逻辑
4. **捆绑资源** - 脚本、参考文档、模板资源

## 核心原则

### 1. 简洁至上

上下文窗口是公共资源。Skill 与系统提示、对话历史、用户请求共享空间。

**默认假设：Claude 已经很聪明。** 只添加 Claude 不知道的内容。对每条信息提问：
- "Claude 真的需要这个解释吗？"
- "这段内容值得占用 token 吗？"

**优先使用简洁示例，而非冗长解释。**

### 2. 设置适当的自由度

根据任务的脆弱性和可变性匹配具体程度：

| 自由度 | 形式 | 适用场景 |
|--------|------|----------|
| 高 | 文本指令 | 多种方法有效、依赖上下文决策 |
| 中 |伪代码/带参数脚本 | 存在首选模式、允许一定变化 |
| 低 | 具体脚本、少量参数 | 操作脆弱易错、一致性关键 |

### 3. 渐进式披露

Skill 使用三级加载系统管理上下文：

1. **元数据** (name + description) - 始终在上下文中 (~100词)
2. **SKILL.md 正文** - 触发时加载 (<5k词)
3. **捆绑资源** - 按需加载 (无限制)

## Skill 结构

每个 Skill 由必需的 SKILL.md 文件和可选的捆绑资源组成：

```
skill-name/
├── SKILL.md (必需)
│   ├── YAML frontmatter (必需)
│   │   ├── name: (必需)
│   │   └── description: (必需)
│   └── Markdown 指令 (必需)
└── 捆绑资源 (可选)
    ├── scripts/      - 可执行脚本 (Python/Bash等)
    ├── references/   - 按需加载的参考文档
    └── assets/       - 输出中使用的文件 (模板、图标等)
```

### SKILL.md 组成

#### Frontmatter (YAML)

```yaml
---
name: my-skill-name
description: 清晰描述功能和使用场景。这是触发机制的关键。
---
```

- `name`: 唯一标识符（小写，用连字符分隔）
- `description`: 完整描述功能和触发条件

#### Body (Markdown)

Skill 的具体指令、示例和指南。仅在Skill 触发后加载。

##创建流程

### 步骤 1：理解需求

通过具体示例理解 Skill 的使用场景：
- "这个 Skill 应该支持什么功能？"
- "用户会如何使用它？"
- "什么情况下应该触发这个 Skill？"

### 步骤 2：规划资源

分析每个示例，确定需要的可复用资源：
- `scripts/` - 重复编写的代码 →脚本化
- `references/` - 需要参考的文档 → 文档化
- `assets/` - 输出中使用的文件 → 模板化

### 步骤 3：创建目录结构

```bash
mkdir -p my-skill/{scripts,references,assets}
touch my-skill/SKILL.md
```

### 步骤 4：编写 SKILL.md

1. 编写 frontmatter（name + description）
2. 编写核心指令和工作流
3. 添加示例和指南
4. 引用捆绑资源

### 步骤 5：测试验证

- 在实际场景中测试 Skill
- 验证触发条件是否准确
- 检查输出是否符合预期

### 步骤 6：迭代优化

根据使用反馈持续改进：
- 补充遗漏的场景
- 优化指令清晰度
- 精简冗余内容

## 最佳实践

### Description编写要点

Description 是 Skill 的主要触发机制，必须包含：
- **功能描述**：Skill 做什么
- **触发条件**：何时使用这个 Skill
- **使用场景**：具体的应用示例

```yaml
#❌ 不好的 description
description: 处理 PDF 文件

# ✅ 好的 description
description: PDF 处理工具包，用于提取文本和表格、创建新PDF、合并拆分文档、处理表单。当需要程序化处理、生成或分析 PDF 文档时使用。
```

### 内容组织原则

1. **SKILL.md 保持精简** - 控制在 500 行以内
2. **详细内容放references/** - 按需加载
3. **避免重复** - 信息只在一处存在
4. **清晰引用** - 明确说明何时读取哪个文件

## 检查清单

创建 Skill 前确认：

- [ ] `name` 使用小写和连字符
- [ ] `description` 包含功能和触发条件
- [ ] 指令简洁，无冗余解释
- [ ] 示例具体可操作
- [ ] 资源文件有清晰引用
- [ ] 在实际场景中测试过

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enoch-robinson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
