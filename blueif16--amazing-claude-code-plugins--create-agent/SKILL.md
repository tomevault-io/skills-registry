---
name: create-agent
description: 创建新的subagent，如果需要会自动初始化插件结构 Use when this capability is needed.
metadata:
  author: blueif16
---

# 创建Agent

## 目的
创建新的subagent文件，如果插件结构不存在则自动初始化。

## 执行逻辑

### 1. 检查插件结构

```
检查当前目录是否存在 .claude/ 目录
├─ 找到 → 继续到步骤3
└─ 未找到 → 进入步骤2（同create-skill的自动初始化）
```

### 2. 自动初始化插件（如需要）

与create-skill相同的初始化流程。

### 3. 收集agent信息

询问用户：
1. "Agent名称？"
2. "描述（何时应该使用这个agent）？"
3. "模型？(sonnet/opus/haiku/inherit)"
4. "需要哪些工具？（逗号分隔）"

### 4. 创建agent

1. 创建文件: `.claude/agents/{agent-name}.md`
2. 从模板生成内容，插入用户输入
3. 显示成功消息

## 验证规则

- Agent名称格式: `^[a-z0-9-]+$`
- 不允许重复名称
- 描述必需

## 成功输出

```
✅ Agent已创建: {agent-name}
📁 位置: ./.claude/agents/{agent-name}.md
🚀 该agent立即可在/agents菜单中使用
```

## 模型选项说明

- **sonnet**: 使用Claude Sonnet模型（平衡性能和成本）
- **opus**: 使用Claude Opus模型（最强性能）
- **haiku**: 使用Claude Haiku模型（快速响应）
- **inherit**: 继承父级模型设置

## 示例

用户: "创建一个git-helper agent"

执行流程:
1. 检查 .claude/ → 找到
2. 询问agent信息:
   - 名称: git-helper
   - 描述: 帮助执行git操作和解决冲突
   - 模型: sonnet
   - 工具: Bash, Read
3. 创建 .claude/agents/git-helper.md
4. 输出成功消息

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blueif16) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
