---
name: flashduty-incident-analytics
description: This skill should be used when the user asks to "统计故障", "MTTR", "MTTA", "故障趋势", "故障报告", "incident trend", "incident metrics", "SLO report", "运营指标", "可靠性分析", or discusses incident statistical analysis and operational metrics (事件统计分析和运营指标). Use when this capability is needed.
metadata:
  author: futuretea
---

# FlashDuty 事件分析

本技能帮助你分析事件数据并生成统计信息。对于复杂分析，它委托给具有独立上下文的专用 Sub-Agent。

## 边界说明

- **本技能**：基于时间范围的聚合统计（MTTR/MTTA 趋势、故障率、多维度对比）
- **incident-diagnosis**：针对单个或少量事件的深度根因分析
- **判断标准**：用户关注"多少/趋势/对比"→ 本技能；用户关注"为什么/根因/时间线"→ diagnosis

## 前置条件

- **FlashDuty API 授权**：用户已配置 FlashDuty MCP 工具，API 连接可用
- **频道访问权限**：用户有权访问目标协作空间（Channel）
- **数据保留期限**：查询受 FlashDuty 数据保留策略限制

## 内部逻辑模式

本技能采用 **Tool Wrapper** 模式：包装 stats-collector 和 incident-analyzer 两个 Sub-Agent，根据查询类型路由到对应 Agent。

## 可用 Sub-Agent

### SRE 聚焦分析
当用户查询与可靠性工程相关时，可与以下 SRE Agent 并行启动：

- `flashduty-error-budget-tracker`：错误预算追踪、SLO 合规性分析
- `flashduty-toil-analyzer`：琐事识别、自动化机会

**与统计分析并行启动**：
```
用户: "分析本周故障并生成报告"
→ 并行：
   Sub-Agent 1: flashduty-stats-collector (基础指标)
   Sub-Agent 2: flashduty-error-budget-tracker (可靠性)
   Sub-Agent 3: flashduty-toil-analyzer (效率)
→ 生成综合 SRE 报告
```

### 1. `flashduty-stats-collector`
**用于**: 聚合统计、MTTR/MTTA 计算、趋势分析

**何时调用**：
- 用户要求 "本周故障统计"
- 用户要求 "MTTR趋势"
- 用户要求 "故障率"
- 任何带有时间范围和指标的请求

**传递参数**：
```json
{
  "time_range": "7d",
  "channel_ids": [可选],
  "team_ids": [可选],
  "severities": [可选],
  "aggregate_unit": "day" | "week" | "month",
  "dimensions": ["severity", "channel", "trend"],
  "query_type": "quick_stats" | "trend_analysis" | "detailed_list"
}
```

> **注意**：优先使用 `time_range`（如 `'7d'`、`'30d'`、`'last_week'`）而非手动计算 `start_time`/`end_time`。趋势分析时使用 `aggregate_unit` 参数。

### 2. `flashduty-incident-analyzer`
**用于**: 复杂过滤、基于标签的分组、自定义查询

**何时调用**：
- 用户要求 "按namespace统计"
- 用户要求 "production环境的故障"
- 需要多条件过滤

## 并行执行模式

### 模式 1: 多 Channel 对比
```
用户: "对比各协作空间的故障情况"

→ 并行启动：
  Sub-Agent 1: Channel A 的统计
  Sub-Agent 2: Channel B 的统计
  Sub-Agent 3: Channel C 的统计
  ...
→ 聚合结果并展示对比
```

### 模式 2: 多时间段趋势
```
用户: "过去3个月的趋势"

→ 并行启动：
  Sub-Agent 1: 月份 1 统计
  Sub-Agent 2: 月份 2 统计
  Sub-Agent 3: 月份 3 统计
→ 合并进行趋势分析
```

### 模式 3: 多维度分析
```
用户: "全面分析本周故障"

→ 并行启动：
  Sub-Agent 1: 严重级别分解
  Sub-Agent 2: Channel 分解
  Sub-Agent 3: MTTR/MTTA 指标
→ 聚合成综合报告
```

## aggregate_incidents 工具

`aggregate_incidents` 是一个高效的原生聚合工具，**统计分析类查询应优先使用此工具**，而非通过 Sub-Agent 逐条拉取后在内存中聚合。

### 功能说明

- 分页拉取符合条件的 incident，自动处理游标分页
- 按多个维度进行分组统计，支持内置维度和自定义标签维度
- 可选附带每组明细字段，方便后续分析
- 返回结构化的分组计数结果，适合直接展示或生成报告

### 参数说明

| 参数 | 类型 | 说明 |
|------|------|------|
| `channel_name` | string | 协作空间名称（模糊匹配，与 `channel_id` 二选一） |
| `channel_id` | int | 协作空间 ID |
| `time_range` | string | 相对时间范围，如 `"24h"`, `"7d"`, `"30d"`, `"1w"`, `"6M"`, `"last_day"`, `"last_week"` 等；与 `start_time`+`end_time` 二选一，优先推荐使用 |
| `start_time` | int64 | 查询开始时间戳（Unix 秒），未设置 `time_range` 时才需要 |
| `end_time` | int64 | 查询结束时间戳（Unix 秒），未设置 `time_range` 时才需要 |
| `severities` | []string | 严重级别过滤，如 `["Critical", "Warning"]` |
| `query` | string | 标题关键词过滤 |
| `group_by` | []string | **必填**，分组维度列表（见下方维度类型说明） |
| `include_details` | bool | 是否在每组结果中附带明细列表，默认 false |
| `detail_fields` | []string | 明细中包含的字段，默认包含 `incident_id`, `title`, `incident_severity`, `progress`, `created_at`, `channel_name`, `channel_id` |
| `max_incidents` | int | 最大拉取条数，默认 **500**，大规模查询时可调高，但需注意内存 |

### group_by 支持的维度类型

**内置维度**：
- `severity` / `incident_severity`：故障严重级别（Critical / Warning / Info）
- `channel`：协作空间名称
- `progress`：处理进度（triggered / acknowledged / resolved）

**自定义标签维度**：
- 格式为 `labels.xxx`，例如：
  - `labels.datacenter`：数据中心
  - `labels.cluster`：集群
  - `labels.namespace`：命名空间
  - `labels.workload`：工作负载
  - `labels.node`：节点

> 支持多维度组合，如 `["labels.datacenter", "labels.cluster", "incident_severity"]`，会生成笛卡尔积形式的分组结果。

### 输出格式

```json
{
  "total_incidents": 128,
  "total_groups": 12,
  "group_by": ["labels.datacenter", "incident_severity"],
  "groups": [
    {
      "key": { "labels.datacenter": "cn-north", "incident_severity": "Critical" },
      "count": 23,
      "details": [
        { "title": "...", "incident_severity": "Critical", "created_at": "..." }
      ]
    }
  ]
}
```

### 调用示例

按数据中心、集群、严重级别三维度聚合统计上周容器集群异常告警：

```
aggregate_incidents(
  channel_name="容器集群异常告警",
  time_range="last_week",
  group_by=["labels.datacenter", "labels.cluster", "incident_severity"],
  include_details=true,
  detail_fields=["title", "incident_severity", "created_at", "labels.workload", "labels.node"]
)
```

### max_incidents 注意事项

- 默认值 **500**，适合大多数场景
- 若业务告警量较大（如按天统计超过 500 条），可设置 `max_incidents=2000` 甚至更高
- 调高后会增加 API 调用次数和内存占用，建议搭配 `severities` 或 `query` 过滤缩小范围

## 快速决策树

```
用户查询提到：
├─ "按xx统计" / "按xx分组" / "各xx的故障数" / "交叉统计" / "标签维度"
│  └─ 优先使用: aggregate_incidents(group_by=[...], include_details=true)
│
├─ "MTTR" / "MTTA" / "趋势" / "报告"（仅需聚合指标，无需明细）
│  └─ 使用: flashduty-stats-collector
│
├─ "时间线" / "评论" / "处理过程" / "根因"（需要完整事件详情）
│  └─ 回退到: list_incidents + get_incident
│
├─ "按namespace统计" / "production环境过滤" / 多条件过滤
│  └─ 使用: flashduty-incident-analyzer
│
└─ 两者都有
   └─ 并行启动两个 Agent，然后合并结果
```

## 工作流

### 步骤 1: 解析用户请求与时间校验
- 提取时间范围（大多数查询必需）
- 识别分析维度
- 检测是否需要对比

**时间范围校验**：

**重要**: FlashDuty API 有数据保留期限限制，查询时必须注意：

1. **优先使用 `time_range` 参数**：简单直观，无需手动计算时间戳
2. **数据保留期限**: 只能查询一定天数内的数据
3. **时间顺序**: 如果使用 `start_time`/`end_time`，必须确保 `start_time` < `end_time`

**推荐的 `time_range` 参数值**：
```
time_range: "24h"        # 最近 24 小时
time_range: "7d"         # 最近 7 天
time_range: "30d"        # 最近 30 天
time_range: "1w"         # 最近 1 周
time_range: "6M"         # 最近 6 个月
time_range: "last_day"   # 昨天 00:00:00 到 23:59:59
time_range: "last_week"  # 上周一 00:00:00 到周日 23:59:59
```

**错误处理**:
- 如果遇到 "end_time is out of storage days" 错误，自动缩短查询范围为最近可查询的时间段
- 如果遇到 "start_time should be less than end_time"，检查并交换时间顺序

### 步骤 2: 确定 Agent 策略
- 单一分析 → 一个 Agent
- 多 channel 对比 → 多个 Agent 并行
- 复杂查询 → incident-analyzer

### 步骤 3: 启动 Sub-Agent
使用 Task 工具启动 Agent：
```
Task({
  subagent_type: "general-purpose",
  description: "收集 channel X 的统计",
  prompt: `你是 flashduty-stats-collector。
    参数：{
      "time_range": "7d",
      "channel_ids": ["channel_x_id"],
      "dimensions": ["severity", "channel", "trend"],
      "aggregate_unit": "day",
      "query_type": "quick_stats"
    }
    分析以上范围的事件数据并返回结构化统计。`
})
```

### 步骤 4: 聚合结果
- 等待所有并行 Agent 完成
- 合并结构化结果
- 展示统一报告

## 调用示例

**示例 1: 快速统计**
```
用户: "本周故障统计"
→ 启动 flashduty-stats-collector
   参数: { time_range: "7d", query_type: "quick_stats" }
→ 展示摘要报告
```

**示例 2: 趋势分析**
```
用户: "本月每天的故障趋势"
→ 启动 flashduty-stats-collector
   参数: { time_range: "30d", query_type: "trend_analysis", aggregate_unit: "day" }
→ 展示按天分解的趋势图表
```

**示例 3: 多 Channel 对比**
```
用户: "对比基础架构和数据库的故障"
→ 识别两个 channel 的 ID
→ 并行启动 2 个 Agent
→ 展示对比表格
```

**示例 4: 复杂过滤**
```
用户: "统计production namespace的Critical故障"
→ 启动 flashduty-incident-analyzer
   参数: {
     filters: { severity: "Critical", labels: { namespace: "production" } },
     time_range: "7d"
   }
→ 展示过滤结果
```

## 响应模式

### 单 Agent 结果
直接展示 Agent 的报告，稍作格式化。

### 多 Agent 结果
```
## 分析报告

### 总体统计 (来自 Agent 1)
...

### Channel 分解 (来自 Agent 2)
...

### 趋势 (来自 Agent 3)
...

### 摘要
合并各 Agent 结果，给出整体结论...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/futuretea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
