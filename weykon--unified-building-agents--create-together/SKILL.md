---
name: create-together
description: 顶层调度 Skill，协调 trend2work、tech-dev、iterate-check 三个子 Skill Use when this capability is needed.
metadata:
  author: weykon
---

# Create-Together Skill (顶层调度)

触发条件: 用户启动新项目、开发或验证

## v1 edit scope (hard limit)
- `.create-together/state.json` - 项目状态文件
- `.create-together/context.json` - 上下文数据文件

### 不可修改文件
- `.claude/skills/*/references/*` - 子 Skill 参考文档
- `templates/built-in/*` - 内置模版

## Dev workflow (required)

- Before routing: If `.create-together/state.json` or `.create-together/context.json` is missing, initialize from templates:
  - `python3 dev/scripts/init_create_together_state.py`
- Before routing: Read `state.json` to understand current phase
- Before routing: Parse user intent to determine target skill
- After execution:
  - Verify sub-skill updated state/context correctly
  - Return clear status to user
  - If state transition occurred, inform user of next steps

## Execution order

### Step 1: 读取项目状态
检查 `.create-together/state.json` 是否存在

- 不存在 → 新项目，进入 trend2work
- 存在 → 读取当前阶段，路由到对应 Skill

### Step 2: 理解用户意图

| 用户请求类型               | 目标 Skill       |
|---------------------------|------------------|
| "新项目"/"启动项目"/"创建" | trend2work       |
| "开发"/"集成"/"配置"       | tech-dev         |
| "check"/"验证"/"测试"     | iterate-check    |

### Step 3: 路由并执行

#### 路由到 trend2work
读取 `.claude/skills/trend2work/SKILL.md`
执行 trend2work 的完整流程
完成后更新 `state.json` 为 `"phase": "tech-dev"`

#### 路由到 tech-dev
读取 `.claude/skills/tech-dev/SKILL.md`
从 `context.json` 读取 trend2work 输出
执行 tech-dev 集成流程
完成后更新 `state.json` 为 `"phase": "iterate-check"`
提示：如涉及 GitHub 仓库创建，优先使用 GitHub MCP（gh-mcp）并采用默认配置（`command: gh` + `args: ["mcp"]`）

#### 路由到 iterate-check
读取 `.claude/skills/iterate-check/SKILL.md`
从 `context.json` 读取项目配置
执行验证检查流程
完成后保持 `"phase": "iterate-check"` (可重复)

### Step 4: 更新状态
执行完成后更新 `.create-together/state.json`

## 项目状态管理

### 状态文件格式

`.create-together/state.json`:
```json
{
  "phase": "trend2work|tech-dev|iterate-check",
  "created_at": "2025-01-04T...",
  "last_updated": "2025-01-04T...",
  "current_skill": "当前执行中的 Skill",
  "outputs": {
    "trend2work": {
      "status": "completed|pending",
      "files": ["project-brief.json", "keywords.json", ...]
    },
    "tech-dev": {
      "status": "pending|completed",
      "files": []
    },
    "iterate-check": {
      "status": "pending",
      "last_check": null
    }
  }
}
```

### 上下文文件格式

`.create-together/context.json`:
```json
{
  "project_brief": "trend2work 输出",
  "keywords": "trend2work 输出",
  "template_selection": "trend2work 输出",
  "mcp_config": "trend2work 输出",
  "dev_config": "tech-dev 输出",
  "validation_results": "iterate-check 输出"
}
```

## 完整工作流

```
用户请求
    ↓
Create-Together 解析意图
    ↓
检查 state.json
    ↓
    ├─ 不存在 → trend2work → 更新状态
    │
    ├─ phase="trend2work" → trend2work → 更新状态 → phase="tech-dev"
    │
    ├─ phase="tech-dev" → tech-dev → 更新状态 → phase="iterate-check"
    │
    └─ phase="iterate-check" → iterate-check → 保持状态
    ↓
返回结果给用户
```

## 常用命令

### 新项目启动
```
创建新项目 / 启动新项目
```

### 开发集成
```
集成 stripe
配置 supabase
添加支付功能
```

### 验证检查
```
check build
验证支付
run all checks
```

## GitHub -> Vercel 标准链路（tech-dev）
1. GitHub MCP（gh-mcp）创建仓库（默认 private）
2. 本地 push 到 GitHub
3. Vercel import/link 关联仓库
4. 触发 build/deploy 并检查日志

## Bundled script

TODO: 状态管理脚本 `scripts/manage_state.py`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/weykon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
