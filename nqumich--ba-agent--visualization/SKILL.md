---
name: visualization
description: 使用 AI (Gemini 3 Pro) 自动生成 ECharts 可视化代码，经校验后返回前端渲染。 Use when this capability is needed.
metadata:
  author: nqumich
---

# 数据可视化 Skill

## 描述

使用 AI (Gemini 3 Pro) 自动生成 ECharts 可视化代码，经校验后返回前端渲染。

## 技术方案

**可视化架构：**
```
用户请求 → Agent → Gemini 3 Pro → ECharts 代码 → 校验 → 前端渲染
```

**流程说明：**
1. 用户提出可视化需求（自然语言）
2. Agent 分析数据并调用 Gemini 3 Pro
3. Gemini 3 Pro 生成 ECharts 配置代码
4. 系统校验代码语法和安全性
5. 返回 JSON 配置给前端渲染

## 使用场景

- "把最近30天的GMV趋势用图表展示"
- "生成各地区GMV占比的饼图"
- "展示用户增长的折线图"
- "对比本周和上周的销售额"

## 入口函数

`create_chart(data, chart_hint=None, theme='default')`

## 参数

- `data`: 要可视化的数据 (DataFrame 或 dict)
- `chart_hint`: 图表类型提示 (可选: line/bar/pie/scatter/heatmap/map)
- `theme`: 主题配置 (default/dark/macarons)

## 返回值

返回 ECharts 配置对象，包含：
- `title`: 图表标题
- `tooltip`: 提示框配置
- `legend`: 图例配置
- `xAxis`: X轴配置
- `yAxis`: Y轴配置
- `series`: 数据系列
- `theme`: 主题名称

## Gemini 3 Pro Prompt 模板

```
你是一个专业的数据可视化专家，擅长使用 ECharts 生成图表代码。

【数据】
{data}

【用户需求】
{user_query}

【要求】
1. 分析数据特征，选择最合适的图表类型
2. 生成完整的 ECharts option 配置（JSON格式）
3. 确保代码语法正确，可直接在前端执行
4. 添加合适的标题、图例、提示框
5. 使用清晰的颜色方案
6. 处理数据格式化（数值、日期等）

请返回纯 JSON 格式的 ECharts option，不要包含任何解释文字。
```

## ECharts 代码校验规则

```python
def validate_echarts_code(code: str) -> bool:
    """校验 ECharts 代码"""
    # 1. JSON 语法检查
    # 2. 安全性检查（禁止 eval, Function 等）
    # 3. 必需字段检查（title, series 等）
    # 4. 数据类型检查
    pass
```

## 前端渲染示例

```javascript
// 前端接收到的 ECharts option
const option = {
  title: { text: 'GMV趋势' },
  tooltip: { trigger: 'axis' },
  xAxis: { type: 'category', data: ['1月', '2月', ...] },
  yAxis: { type: 'value' },
  series: [{
    type: 'line',
    data: [1200, 1500, ...]
  }]
};

// 渲染
const chart = echarts.init(document.getElementById('chart'));
chart.setOption(option);
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nqumich) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
