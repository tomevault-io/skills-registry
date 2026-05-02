---
name: anthropic-skills-reference
description: 参考 Anthropic skills 仓库来理解和创建 Cursor skills。当用户需要了解 skills 系统、查看 Anthropic 的 skills 示例、学习如何创建高质量的 skills，或需要参考最佳实践时使用。 Use when this capability is needed.
metadata:
  author: lihua1234567890
---

# Anthropic Skills 参考指南

本 skill 帮助你在 Cursor 中理解和使用 Anthropic 的 skills 系统，参考 [anthropics/skills](https://github.com/anthropics/skills) 仓库的最佳实践。

## 什么是 Skills？

Skills 是包含指令、脚本和资源的文件夹，用于动态加载以提升 AI 在特定任务上的表现。Skills 教会 AI 如何以可重复的方式完成特定任务。

### Skills 的核心概念

- **自包含**：每个 skill 在自己的文件夹中，包含 `SKILL.md` 文件
- **元数据驱动**：通过 YAML frontmatter 定义名称和描述
- **可发现性**：描述字段帮助 AI 决定何时应用该 skill
- **可扩展**：可以包含参考文档、示例和工具脚本

## Skills 文件结构

参考 Anthropic 仓库的标准结构：

```
skill-name/
├── SKILL.md              # 必需 - 主指令文件
├── reference.md          # 可选 - 详细文档
├── examples.md           # 可选 - 使用示例
└── scripts/              # 可选 - 工具脚本
    ├── validate.py
    └── helper.sh
```

## SKILL.md 基本格式

参考 Anthropic 模板：

```markdown
---
name: my-skill-name
description: 清晰描述这个 skill 做什么以及何时使用它
---

# My Skill Name

[在这里添加 AI 在激活此 skill 时将遵循的指令]

## Examples
- 使用示例 1
- 使用示例 2

## Guidelines
- 指导原则 1
- 指导原则 2
```

### 必需字段

- `name`: 唯一标识符（小写字母、数字、连字符，最多 64 字符）
- `description`: 完整描述（最多 1024 字符，非空）

## 编写有效的描述

描述对于 skill 的发现至关重要。遵循以下最佳实践：

### 1. 使用第三人称

- ✅ 正确："处理 Excel 文件并生成报告"
- ❌ 错误："我可以帮你处理 Excel 文件"

### 2. 具体且包含触发词

- ✅ 正确："从 PDF 文件提取文本和表格，填充表单，合并文档。在处理 PDF 文件或用户提到 PDF、表单或文档提取时使用。"
- ❌ 错误："帮助处理文档"

### 3. 包含 WHAT 和 WHEN

- **WHAT**：skill 做什么（具体能力）
- **WHEN**：何时使用（触发场景）

## 核心编写原则

### 1. 简洁是关键

上下文窗口是共享资源。只添加 AI 真正需要的信息。

**好的（简洁）**：
```markdown
## 提取 PDF 文本

使用 pdfplumber 进行文本提取：

```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
```

**不好的（冗长）**：
```markdown
## 提取 PDF 文本

PDF（便携式文档格式）文件是一种常见的文件格式，包含文本、图像和其他内容。要从 PDF 提取文本，你需要使用库。有很多库可用于 PDF 处理，但我们推荐 pdfplumber，因为它易于使用且能很好地处理大多数情况...
```

### 2. 保持 SKILL.md 在 500 行以内

主文件应简洁。使用渐进式披露处理详细内容。

### 3. 渐进式披露

将核心信息放在 SKILL.md 中；详细参考材料放在单独文件中，仅在需要时读取。

```markdown
# PDF 处理

## 快速开始
[核心指令]

## 其他资源
- 完整 API 详情，参见 [reference.md](reference.md)
- 使用示例，参见 [examples.md](examples.md)
```

### 4. 设置适当的自由度

根据任务的脆弱性匹配具体程度：

| 自由度级别 | 何时使用 | 示例 |
|-----------|---------|------|
| **高**（文本指令） | 多种有效方法，依赖上下文 | 代码审查指南 |
| **中**（伪代码/模板） | 首选模式，允许变化 | 报告生成 |
| **低**（特定脚本） | 脆弱操作，一致性关键 | 数据库迁移 |

## 常见模式

### 模板模式

提供输出格式模板：

```markdown
## 报告结构

使用此模板：

```markdown
# [分析标题]

## 执行摘要
[关键发现的一段概述]

## 关键发现
- 发现 1 及支持数据
- 发现 2 及支持数据

## 建议
1. 具体的可操作建议
2. 具体的可操作建议
```
```

### 示例模式

对于输出质量依赖示例的 skills：

```markdown
## 提交消息格式

**示例 1：**
输入：添加了带 JWT 令牌的用户认证
输出：
```
feat(auth): 实现基于 JWT 的认证

添加登录端点和令牌验证中间件
```

**示例 2：**
输入：修复了日期显示不正确的 bug
输出：
```
fix(reports): 修正时区转换中的日期格式化

在报告生成中一致使用 UTC 时间戳
```
```

### 工作流模式

将复杂操作分解为清晰的步骤：

```markdown
## 表单填充工作流

复制此清单并跟踪进度：

```
任务进度：
- [ ] 步骤 1：分析表单
- [ ] 步骤 2：创建字段映射
- [ ] 步骤 3：验证映射
- [ ] 步骤 4：填充表单
- [ ] 步骤 5：验证输出
```

**步骤 1：分析表单**
运行：`python scripts/analyze_form.py input.pdf`
...
```

## 工具脚本

预制的脚本比生成的代码更有优势：
- 比生成的代码更可靠
- 节省 token（代码不在上下文中）
- 节省时间（无需生成代码）
- 确保使用的一致性

```markdown
## 工具脚本

**analyze_form.py**：从 PDF 提取所有表单字段
```bash
python scripts/analyze_form.py input.pdf > fields.json
```

**validate.py**：检查错误
```bash
python scripts/validate.py fields.json
# 返回："OK" 或列出冲突
```
```

明确说明 AI 应该**执行**脚本（最常见）还是**读取**它作为参考。

## 要避免的反模式

### 1. Windows 风格路径
- ✅ 使用：`scripts/helper.py`
- ❌ 避免：`scripts\helper.py`

### 2. 太多选项
```markdown
# 不好 - 令人困惑
"你可以使用 pypdf，或 pdfplumber，或 PyMuPDF，或..."

# 好 - 提供默认值并允许例外
"使用 pdfplumber 进行文本提取。
对于需要 OCR 的扫描 PDF，改用 pdf2image 和 pytesseract。"
```

### 3. 时间敏感信息
```markdown
# 不好 - 会过时
"如果你在 2025 年 8 月之前这样做，使用旧 API。"

# 好 - 使用"旧模式"部分
## 当前方法
使用 v2 API 端点。

## 旧模式（已弃用）
<details>
<summary>遗留 v1 API</summary>
...
</details>
```

### 4. 不一致的术语
选择一个术语并始终使用：
- ✅ 始终使用 "API 端点"（不混用 "URL"、"路由"、"路径"）
- ✅ 始终使用 "字段"（不混用 "框"、"元素"、"控件"）

### 5. 模糊的 Skill 名称
- ✅ 好：`processing-pdfs`、`analyzing-spreadsheets`
- ❌ 避免：`helper`、`utils`、`tools`

## Anthropic Skills 仓库资源

### 主要目录

- **skills/**：Creative & Design、Development & Technical、Enterprise & Communication 和 Document Skills 的示例
- **spec/**：Agent Skills 规范
- **template/**：Skill 模板

### 推荐的示例 Skills

浏览以下示例以了解不同模式：

1. **文档处理**：`skills/docx`、`skills/pdf`、`skills/pptx`、`skills/xlsx`
2. **开发任务**：测试 Web 应用、MCP 服务器生成
3. **创意应用**：艺术、音乐、设计
4. **企业工作流**：通信、品牌等

### 访问仓库

- GitHub: https://github.com/anthropics/skills
- 规范: https://agentskills.io
- 文档: https://support.claude.com/en/articles/12512198-creating-custom-skills

## 在 Cursor 中应用

### 创建新 Skill 的步骤

1. **确定目的**：明确 skill 要解决的具体任务
2. **选择位置**：
   - 个人 skill：`~/.cursor/skills/skill-name/`
   - 项目 skill：`.cursor/skills/skill-name/`
3. **编写描述**：包含 WHAT 和 WHEN，使用第三人称
4. **编写指令**：简洁、具体、包含示例
5. **测试**：验证 skill 能被正确发现和应用

### 验证清单

创建 skill 前，验证：

- [ ] 描述具体且包含关键触发词
- [ ] 描述包含 WHAT 和 WHEN
- [ ] 使用第三人称
- [ ] SKILL.md 主体在 500 行以内
- [ ] 术语一致
- [ ] 示例具体而非抽象
- [ ] 文件引用仅一层深度
- [ ] 适当使用渐进式披露
- [ ] 工作流有清晰步骤
- [ ] 无时间敏感信息

## 示例：参考 Anthropic 模式创建 Skill

当用户需要创建新 skill 时：

1. 参考 Anthropic 仓库中的类似 skill
2. 使用模板作为起点
3. 遵循最佳实践（简洁、具体、包含触发词）
4. 包含实际示例
5. 使用渐进式披露处理复杂内容

## 总结

Anthropic skills 系统提供了一个强大的框架来扩展 AI 能力。关键原则：

- **简洁**：只添加必要信息
- **具体**：清晰的描述和触发词
- **结构化**：使用模板、示例和工作流
- **可维护**：避免时间敏感信息和反模式

参考 Anthropic 仓库获取更多示例和灵感。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lihua1234567890) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
