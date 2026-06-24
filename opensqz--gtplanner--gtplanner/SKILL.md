---
name: gtplanner
description: | Use when this capability is needed.
metadata:
  author: OpenSQZ
---

# GTPlanner - AI PRD 生成工具

GTPlanner 是一个 AI 驱动的项目规划工具，可以将用户需求转换为专业的项目规划和技术架构文档。

## 快速开始

### 环境检查

首先确认 GTPlanner 环境已正确配置：

```bash
cd "${CLAUDE_PLUGIN_ROOT}"
uv run python -c "from agent.function_calling.agent_tools import get_agent_function_definitions; print('GTPlanner 环境正常')"
```

### 基本用法

GTPlanner 提供四个核心工具，通过 Python API 调用：

| 工具 | 功能 | 阶段 |
|------|------|------|
| `short_planning` | 需求分析与规划 | 范围确认 |
| `tool_recommend` | 技术栈推荐 | 技术实现 |
| `research` | 深度技术调研 | 技术实现(可选) |
| `design` | 生成设计文档 | 文档生成 |

## 工作流程

### 标准工作流（推荐）

```
1. short_planning (initial)   → 初始需求分析
2. tool_recommend             → 技术栈推荐
3. short_planning (technical) → 技术规划整合
4. research (可选)            → 深度技术调研
5. design (quick/deep)        → 生成设计文档
```

### 简化工作流（简单项目）

```
1. short_planning (initial)   → 需求分析
2. design (quick)             → 快速生成文档
```

## 工具详解

### 1. short_planning - 短期规划

定义和细化项目范围，支持两个阶段：

**参数：**
- `user_requirements` (string, 必需): 用户的原始需求描述
- `improvement_points` (array): 需要改进的点或新需求
- `planning_stage` (string): `initial` 或 `technical`

**调用示例：**

```python
# 初始规划阶段
result = await execute_agent_tool(
    'short_planning',
    {
        'user_requirements': '设计一个博客系统',
        'planning_stage': 'initial'
    },
    shared
)

# 技术规划阶段（在 tool_recommend 之后）
result = await execute_agent_tool(
    'short_planning',
    {
        'user_requirements': '设计一个博客系统',
        'planning_stage': 'technical'
    },
    shared
)
```

### 2. tool_recommend - 工具推荐

基于项目需求推荐平台支持的 API 或库。

**参数：**
- `query` (string, 必需): 查询文本，描述需要的工具功能
- `top_k` (integer): 返回数量，默认 5
- `tool_types` (array): 过滤类型 `["PYTHON_PACKAGE", "APIS"]`
- `use_llm_filter` (boolean): 是否使用 LLM 筛选，默认 true

**调用示例：**

```python
result = await execute_agent_tool(
    'tool_recommend',
    {
        'query': '博客系统需要的数据库和认证工具',
        'top_k': 5,
        'tool_types': ['PYTHON_PACKAGE', 'APIS']
    },
    shared
)
```

### 3. research - 技术调研（可选）

对推荐的技术栈进行深入可行性调研。

**前置条件：** 需要 `JINA_API_KEY` 环境变量

**参数：**
- `keywords` (array, 必需): 调研关键词列表
- `focus_areas` (array, 必需): 调研关注点
- `project_context` (string): 项目背景信息

**调用示例：**

```python
result = await execute_agent_tool(
    'research',
    {
        'keywords': ['FastAPI', 'PostgreSQL', 'JWT认证'],
        'focus_areas': ['技术选型', '性能优化', '最佳实践'],
        'project_context': '博客系统后端开发'
    },
    shared
)
```

### 4. design - 架构设计

生成最终的系统架构方案和设计文档。

**参数：**
- `user_requirements` (string, 必需): 最终确认的项目需求
- `design_mode` (string, 必需):
  - `quick`: 快速设计，适合简单项目
  - `deep`: 深度设计，适合复杂项目

**调用示例：**

```python
result = await execute_agent_tool(
    'design',
    {
        'user_requirements': '博客系统需求...',
        'design_mode': 'quick'
    },
    shared
)
```

## 完整示例

以下是一个完整的 PRD 生成流程：

```python
import asyncio
from agent.function_calling.agent_tools import execute_agent_tool

async def generate_prd(user_requirements: str, use_deep_design: bool = False):
    """生成完整的 PRD 文档"""
    shared = {}  # 状态字典，在工具间传递数据

    # 步骤 1: 初始规划
    print("📋 步骤 1: 初始需求分析...")
    result = await execute_agent_tool(
        'short_planning',
        {
            'user_requirements': user_requirements,
            'planning_stage': 'initial'
        },
        shared
    )
    if not result['success']:
        return f"初始规划失败: {result['error']}"

    # 步骤 2: 工具推荐
    print("🔧 步骤 2: 技术栈推荐...")
    result = await execute_agent_tool(
        'tool_recommend',
        {
            'query': user_requirements,
            'top_k': 5
        },
        shared
    )
    # tool_recommend 可能没有匹配结果，继续执行

    # 步骤 3: 技术规划
    print("📐 步骤 3: 技术规划整合...")
    result = await execute_agent_tool(
        'short_planning',
        {
            'user_requirements': user_requirements,
            'planning_stage': 'technical'
        },
        shared
    )
    if not result['success']:
        return f"技术规划失败: {result['error']}"

    # 步骤 4: 生成设计文档
    design_mode = 'deep' if use_deep_design else 'quick'
    print(f"📝 步骤 4: 生成设计文档 ({design_mode} 模式)...")
    result = await execute_agent_tool(
        'design',
        {
            'user_requirements': user_requirements,
            'design_mode': design_mode
        },
        shared
    )
    if not result['success']:
        return f"设计生成失败: {result['error']}"

    # 返回设计文档
    return shared.get('agent_design_document', '')

# 使用示例
if __name__ == "__main__":
    requirements = "设计一个支持 Markdown 的博客系统，包含用户认证和评论功能"
    document = asyncio.run(generate_prd(requirements))
    print(document)
```

## 状态管理

GTPlanner 工具通过 `shared` 字典传递状态，关键状态字段包括：

| 字段 | 说明 | 来源工具 |
|------|------|----------|
| `short_planning` | 规划结果 | short_planning |
| `recommended_tools` | 推荐的工具列表 | tool_recommend |
| `research_findings` | 调研结果 | research |
| `agent_design_document` | 最终设计文档 | design |

## 环境配置

### 必需配置

```bash
# LLM 配置
LLM_API_KEY=your_api_key
LLM_BASE_URL=your_base_url
LLM_MODEL=your_model_name
```

### 可选配置

```bash
# 工具推荐（向量搜索服务）
VECTOR_SERVICE_BASE_URL=your_vector_service_url
VECTOR_SERVICE_INDEX_NAME=document_gtplanner_tools

# 技术调研（Jina Search）
JINA_API_KEY=your_jina_api_key
```

## 多语言支持

GTPlanner 支持以下语言，会自动检测输入语言：

- 中文 (zh)
- 英文 (en)
- 日文 (ja)
- 西班牙文 (es)
- 法文 (fr)

## 常见问题

### Q: design 工具执行失败，提示缺少 short_planning 结果

确保在调用 `design` 之前先调用 `short_planning`，且使用同一个 `shared` 字典。

### Q: tool_recommend 返回空结果

这可能是因为：
1. 向量服务未配置或不可用
2. 没有匹配的工具

空结果是正常情况，可以继续执行后续工具。

### Q: research 工具不可用

需要配置 `JINA_API_KEY` 环境变量。如果没有配置，该工具不会出现在可用工具列表中。

## 参考文档

- [工具参数详解](references/tools.md)
- [完整工作流指南](references/workflow.md)
- [使用示例](references/examples.md)

---
> Source: [OpenSQZ/GTPlanner](https://github.com/OpenSQZ/GTPlanner) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
