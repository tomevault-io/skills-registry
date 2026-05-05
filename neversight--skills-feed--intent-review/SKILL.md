---
name: intent-review
description: Interactive Intent approval. Review sections and mark status (locked/reviewed/draft). Use /intent-review <path> to review a specific file, or /intent-review to review Intent in current directory. Use when this capability is needed.
metadata:
  author: neversight
---

# Intent Review

交互式 Section 级别的 Intent 审批工具。

## 核心概念

Intent 文档按 Section 粒度审批，三种状态：

| 状态 | 标记 | 含义 | Agent 行为 |
|------|------|------|-----------|
| **LOCKED** | `::: locked` | 核心架构，修改需 human 明确同意 | 暂停，请求确认 |
| **REVIEWED** | `::: reviewed` | 已审阅，可修改但需通知 | 允许，事后通知 |
| **DRAFT** | `::: draft` | 草稿，可自由迭代 | 自由修改 |

## 工作流程

```
/intent-review [path]
        ↓
┌───────────────────┐
│  解析 Intent 文件  │
│  识别所有 Section  │
└─────────┬─────────┘
          ↓
┌───────────────────┐
│  展示状态概览      │
│  N locked         │
│  M reviewed       │
│  K draft/unmarked │
└─────────┬─────────┘
          ↓
┌───────────────────────────────────────┐
│  逐个未标记 Section 询问              │
│                                       │
│  AskUserQuestion:                     │
│  "Section: [标题]"                    │
│  选项:                                │
│  - Lock (核心架构)                    │
│  - Review (确认接受)                  │
│  - Skip (保持 draft)                  │
└─────────┬─────────────────────────────┘
          ↓
┌───────────────────┐
│  更新文件          │
│  添加标记和属性    │
└───────────────────┘
```

## 使用方法

### 审批指定文件

```
/intent-review src/core/intent/INTENT.md
```

### 审批当前模块

```
cd src/chambers/terminal
/intent-review
```

自动查找 `intent/INTENT.md`

### 批量审批

```
/intent-review --all
```

扫描项目所有 intent 文件

## 执行步骤

### 1. 定位 Intent 文件

```javascript
// 优先级：
// 1. 用户指定路径
// 2. 当前目录下 intent/INTENT.md
// 3. 当前目录下 INTENT.md
```

### 2. 解析 Section

识别两种 Section：

**已标记的**：
```markdown
::: locked
## 模块边界
...
:::
```

**未标记的**（普通 markdown heading）：
```markdown
## API 设计
...
```

### 3. 展示概览

```
Intent Review: src/core/intent/INTENT.md

状态概览：
├── 🔒 LOCKED: 2 sections
│   ├── 模块边界规则
│   └── 数据结构
├── ✓ REVIEWED: 3 sections
│   ├── API 签名 (by robert, 2026-01-19)
│   ├── 配置格式
│   └── 错误处理
└── 📝 UNMARKED: 4 sections
    ├── 实现建议
    ├── 性能考虑
    ├── 示例代码
    └── 变更记录

是否开始审批未标记的 sections？
```

### 4. 逐个审批

对每个未标记 Section 使用 AskUserQuestion：

```
使用 AskUserQuestion:
- question: "Section: API 签名\n\n内容预览：定义了 create(), delete(), list() 三个函数..."
- header: "审批状态"
- options:
  - label: "Lock (核心架构)"
    description: "锁定此 section，修改需要 human 明确同意"
  - label: "Review (确认接受)"
    description: "标记为已审阅，Agent 可修改但需通知"
  - label: "Skip (保持 draft)"
    description: "暂不审批，保持草稿状态"
```

### 5. 更新文件

根据用户选择，在 markdown 中添加标记：

**Lock 选择**：
```markdown
::: locked {reason="核心架构"}
## API 签名
...
:::
```

**Review 选择**：
```markdown
::: reviewed {by=<username> date=<today>}
## API 签名
...
:::
```

**Skip 选择**：
不修改，或添加：
```markdown
::: draft
## 实现建议
...
:::
```

## 特殊场景

### 检测 LOCKED Section 被修改

当文件有未提交的修改时：

```
⚠️ 检测到 LOCKED section 被修改：
- Section: 模块边界规则
- 修改内容: [diff preview]

此修改需要 human 明确同意。是否接受？
```

### 查看审批历史

```
/intent-review --history src/core/intent/INTENT.md
```

输出：
```
Section: API 签名
├── 2026-01-19 robert: reviewed
├── 2026-01-15 robert: draft → reviewed
└── 2026-01-10 created

Section: 模块边界
├── 2026-01-18 robert: locked (reason: 核心架构)
└── 2026-01-12 created
```

## 与其他工具配合

```
intent-interview → 创建 Intent (默认全是 draft)
        ↓
/intent-review → 审批关键 sections
        ↓
aine-dev-flow → 开发实现 (遵守 locked/reviewed 规则)
        ↓
intent-sync → 检查一致性
```

## 配置

可在 `~/.claude/settings.json` 中配置默认 reviewer：

```json
{
  "idd": {
    "reviewer": "robert",
    "autoLockPatterns": ["模块边界", "数据结构", "安全约束"]
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
