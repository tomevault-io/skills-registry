---
name: skill-management
description: 技能全生命周期管理系统，使用 LLM 智能分析技能类型、评估质量、审计安全。支持 GitHub、skills.sh marketplace 和 Skills CLI (npx skills) 三数据源搜索。当用户需要：（1）发现和评估新技能，（2）审计现有技能安全性，（3）改造技能适配本地环境，（4）测试技能融合效果，（5）收集技能使用反馈时使用。 Use when this capability is needed.
metadata:
  author: dwsy
---

# 技能全生命周期管理系统

完整的技能管理流程，从发现到部署的端到端管理，**充分利用 LLM 智能分析能力**。

## 🎯 核心特性

### 1. 三数据源搜索
- **GitHub** - 搜索 GitHub 上的技能仓库
- **skills.sh marketplace** - 搜索 skills.sh 技能市场
- **Skills CLI** - 使用 `npx skills` CLI 搜索和安装技能（基于 Vercel 开源方案）

### 2. 智能技能分类
- 自动识别技能类型：knowledge、tool、hybrid、process
- 差异化评估标准

### 3. LLM 深度分析
- 智能分类
- 深度评估
- 安全审计
- 定制化报告

### 4. 差异化评估
- 知识型：重点评估内容质量（40%）
- 工具型：重点评估功能和安全（40%）

### 5. Skills CLI 集成
基于 [Vercel skills](https://github.com/vercel-labs/skills) 开源方案，提供：
- **搜索技能** - `npx skills find <keyword>`
- **安装技能** - `npx skills add <owner/repo@skill>`
- **检查更新** - `npx skills check`
- **更新技能** - `npx skills update`

## 🔄 工作流程

```
┌─────────────┐
│  搜索技能   │ ← 脚本：GitHub CLI 或 skills.sh marketplace
└──────┬──────┘
       │
┌──────▼──────┐
│  选择技能   │ ← LLM：使用 interview 展示列表 + 智能介绍
└──────┬──────┘
       │
┌──────▼──────┐
│  发现技能   │ ← 脚本：克隆仓库、查找 SKILL.md
└──────┬──────┘
       │
┌──────▼──────┐
│  智能分类   │ ← LLM：判断技能类型
└──────┬──────┘
       │
┌──────▼──────┐
│  评估技能   │ ← LLM：差异化分析
└──────┬──────┘
       │
┌──────▼──────┐
│  安全审计   │ ← LLM：差异化审计
└──────┬──────┘
       │
┌──────▼──────┐
│  适应性改造 │ ← 脚本：分析需求
└──────┬──────┘
       │
┌──────▼──────┐
│  融合测试   │ ← 脚本：兼容性检查
└──────┬──────┘
       │
┌──────▼──────┐
│  智能报告   │ ← LLM：定制化报告
└──────┬──────┘
       │
┌──────▼──────┐
│  用户通知   │ ← 脚本：显示摘要
└─────────────┘
```

## 🚀 快速开始

### 搜索技能

#### GitHub 搜索
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts "claude office"
```

#### skills.sh marketplace 搜索
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts "react" --source marketplace
```

#### Skills CLI 搜索
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts "react" --source cli
```

#### 使用 Skills CLI 直接安装
```bash
# 搜索技能
npx skills find react

# 安装特定技能
npx skills add vercel-labs/agent-skills@vercel-react-best-practices

# 检查更新
npx skills check

# 更新所有技能
npx skills update
```

#### 查看热门技能
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts trending
```

#### 查看统计信息
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts stats
```

### 完整流程

```bash
# 使用 GitHub 搜索
bun ~/.pi/agent/skills/skill-management/scripts/pipeline.ts "claude office" --interactive

# 使用 marketplace 搜索
bun ~/.pi/agent/skills/skill-management/scripts/pipeline.ts "react" --source marketplace --interactive
```

## 🤖 LLM 智能分析

### 智能分类

自动识别 4 种技能类型：
- **knowledge** - 纯文档、指南、最佳实践
- **tool** - 包含可执行脚本和工具
- **hybrid** - 既有文档又有脚本
- **process** - 流程指南和工作流

### 差异化评估

| 维度 | 知识型 | 工具型 |
|------|--------|--------|
| 内容质量 | 40% | 20% |
| 功能完整性 | 20% | 40% |
| 实用性 | 20% | 20% |
| 文档质量 | 10% | 10% |
| 代码质量 | 5% | 10% |
| 安全性 | 5% | 10% |

## 📁 文件结构

```
~/.pi/agent/skills/skill-management/
├── SKILL.md                    # 本文档
├── README.md                   # 使用说明
├── config.json                 # 配置文件
├── scripts/
│   ├── assess.ts              # 智能评估（自动分类 + 差异化）
│   ├── audit.ts               # 智能审计（自动分类 + 差异化）
│   ├── pipeline.ts            # 完整流程脚本
│   ├── select.ts              # 技能选择脚本
│   ├── search.ts              # 三数据源搜索（GitHub + marketplace + CLI）
│   ├── skills-cli.ts          # Skills CLI (npx skills) 集成模块
│   ├── interactive.ts         # 交互式决策脚本
│   ├── report.ts              # 智能报告生成脚本
│   └── notify.ts              # 通知脚本
├── reports/                    # 智能评估报告目录
└── templates/                  # 模板文件
```

## 🎨 数据源对比

| 数据源 | 类型 | 优势 | 用途 |
|--------|------|------|------|
| **GitHub** | 代码仓库 | 搜索开源技能项目 | 搜索 GitHub 上的技能仓库 |
| **skills.sh** | 技能市场 | 技能聚合平台 | 发现热门和社区技能 |
| **Skills CLI** | 包管理器 | 统一的技能生态 | 搜索、安装、更新技能 |

## 📝 使用示例

### 示例 1: GitHub 搜索
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts "office"
```

### 示例 2: Marketplace 搜索
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts "react" --source marketplace
```

### 示例 3: 查看热门技能
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts trending
```

### 示例 4: 查看统计
```bash
bun ~/.pi/agent/skills/skill-management/scripts/search.ts stats
```

## 🎯 最佳实践

1. **先搜索后选择** - 使用三数据源搜索更多技能
2. **充分利用 LLM** - LLM 参与全流程分析
3. **区分技能类型** - 不同类型使用不同评估标准
4. **定制化报告** - 根据类型生成定制化报告
5. **优先使用 Skills CLI** - 对于 skills.sh 生态的技能，使用 `npx skills` 进行安装和管理
6. **技能命名格式** - 使用 `owner/repo@skill-name` 格式引用特定技能

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dwsy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
