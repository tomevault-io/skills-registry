---
name: ab-testing-analyzer
description: 全面的AB测试分析工具，支持实验设计、统计检验、用户分群分析和可视化报告生成。用于分析产品改版、营销活动、功能优化等AB测试结果，提供统计显著性检验和深度洞察。 Use when this capability is needed.
metadata:
  author: liangdabiao
---

# AB测试分析技能 (AB Testing Analyzer)

一个功能完整的智能AB测试分析工具，基于"数据分析咖哥十话"的AB测试模块开发。

## 🎯 技能概述

本技能提供从实验设计到结果分析的完整AB测试解决方案，支持多种统计检验方法、用户分群分析和可视化报告生成。

### ✨ 核心特性

- **🧪 完整的AB测试流程**
  - 实验设计和样本量计算
  - 随机分组验证
  - 转化率和留存率分析
  - 统计显著性检验

- **📊 全面的统计方法**
  - t检验 (独立样本、配对样本)
  - 卡方检验 (拟合优度、独立性)
  - 置信区间估计
  - 效应量计算
  - 多重比较校正

- **👥 智能用户分群**
  - 价值分群 (高/低价值客户)
  - 人口统计学分群
  - 行为模式分群
  - 自定义分群策略
  - 交互效应分析

- **📈 丰富的可视化功能**
  - 转化率对比图
  - 留存率曲线图
  - 用户分群热力图
  - 交互效应可视化
  - 统计检验结果图

- **🔧 高级分析功能**
  - 多变量AB测试
  - 贝叶斯AB测试
  - 时间序列分析
  - 稳健性检查
  - 因果推断支持

## 🚀 快速开始

### 1. 环境要求

```bash
# 依赖包
pip install pandas numpy scipy matplotlib seaborn statsmodels
```

### 2. 基础使用

```python
from scripts.ab_test_analyzer import ABTestAnalyzer
from scripts.statistical_tests import StatisticalTests
from scripts.visualizer import ABTestVisualizer

# 初始化分析器
analyzer = ABTestAnalyzer()
stats_tests = StatisticalTests()
visualizer = ABTestVisualizer()

# 加载AB测试数据
data = analyzer.load_data('ab_test_data.csv')

# 基础转化率分析
conversion_results = analyzer.analyze_conversion(
    data,
    group_col='页面版本',
    conversion_col='是否购买'
)

# 统计显著性检验
t_test_result = stats_tests.t_test(
    data,
    group_col='页面版本',
    metric_col='是否购买'
)

# 生成可视化报告
fig = visualizer.plot_conversion_comparison(conversion_results)
```

### 3. 运行示例

```bash
# 快速测试
python quick_test.py

# 基础AB测试示例
python examples/basic_ab_test_example.py

# 高级分群分析示例
python examples/advanced_segmentation_example.py

# 综合分析示例
python examples/comprehensive_analysis_example.py
```

## 📁 项目结构

```
ab-testing-analyzer/
├── SKILL.md                     # 技能详细文档
├── README.md                    # 使用指南 (本文件)
├── quick_test.py               # 快速功能测试
├── test_skill.py               # 完整测试套件
│
├── scripts/                    # 核心功能模块
│   ├── __init__.py
│   ├── ab_test_analyzer.py         # AB测试核心分析
│   ├── statistical_tests.py        # 统计检验模块
│   ├── segment_analyzer.py         # 用户分群分析
│   └── visualizer.py              # 可视化生成器
│
└── examples/                   # 示例和数据
    ├── sample_data/            # 样本数据
    │   ├── sample_ab_test_data.csv
    │   └── sample_user_segments.csv
    ├── basic_ab_test_example.py     # 基础AB测试示例
    ├── advanced_segmentation_example.py # 高级分群分析示例
    └── comprehensive_analysis_example.py # 综合分析示例
```

## 💡 主要功能

### 1. AB测试基础分析

#### 转化率分析
```python
# 计算各组转化率
conversion_rates = analyzer.calculate_conversion_rates(
    data,
    group_col='实验组别',
    conversion_col='转化状态'
)

# 计算提升率和置信区间
lift_analysis = analyzer.calculate_lift(
    conversion_rates,
    control_group='对照组',
    test_group='测试组'
)
```

#### 留存率分析
```python
# 计算留存率
retention_rates = analyzer.calculate_retention_rates(
    data,
    group_col='实验组别',
    retention_col='retention_7'
)

# 留存率曲线可视化
fig = visualizer.plot_retention_curves(retention_rates)
```

### 2. 统计显著性检验

#### t检验
```python
# 独立样本t检验
t_result = stats_tests.t_test(
    data,
    group_col='页面版本',
    metric_col='购买金额',
    test_type='independent'
)

# 配对样本t检验
paired_t_result = stats_tests.t_test(
    before_after_data,
    group_col='用户ID',
    metric_col='行为指标',
    test_type='paired'
)
```

#### 卡方检验
```python
# 拟合优度检验
chi2_goodness = stats_tests.chi_square_test(
    observed_data,
    expected_data,
    test_type='goodness_of_fit'
)

# 独立性检验
chi2_independence = stats_tests.chi_square_test(
    data,
    group_col='实验组别',
    outcome_col='转化状态',
    test_type='independence'
)
```

#### 效应量计算
```python
# Cohen's d计算
cohens_d = stats_tests.cohens_d(
    data,
    group_col='实验组别',
    metric_col='转化状态'
)

# Cramer's V计算
cramers_v = stats_tests.cramers_v(data, group_col, outcome_col)
```

### 3. 用户分群分析

#### 价值分群
```python
from scripts.segment_analyzer import SegmentAnalyzer

segment_analyzer = SegmentAnalyzer()

# 基于价值的用户分群
value_segments = segment_analyzer.value_based_segmentation(
    data,
    value_col='累计消费金额',
    n_tiers=3
)

# 分群转化率分析
segment_conversion = segment_analyzer.segment_conversion_analysis(
    data,
    segment_col='价值组别',
    group_col='实验组别',
    conversion_col='转化状态'
)
```

#### 交互效应分析
```python
# 页面版本与用户特征的交互效应
interaction_analysis = segment_analyzer.interaction_analysis(
    data,
    group_col='页面版本',
    segment_col='价值组别',
    outcome_col='转化状态'
)

# 交互效应可视化
fig = visualizer.plot_interaction_effects(interaction_analysis)
```

### 4. 高级统计分析

#### 贝叶斯AB测试
```python
# 贝叶斯AB测试分析
bayesian_result = analyzer.bayesian_ab_test(
    data,
    group_col='实验组别',
    conversion_col='转化状态',
    prior='jeffreys'
)

# 计算获胜概率
win_probability = analyzer.calculate_win_probability(bayesian_result)
```

#### 多变量检验
```python
# 多变量AB测试 (MVT)
mvt_result = analyzer.multivariate_test(
    data,
    group_cols=['页面版本', '按钮颜色', '标题文案'],
    conversion_col='转化状态'
)
```

### 5. 可视化报告

#### 基础图表
```python
# 转化率对比图
fig = visualizer.plot_conversion_comparison(
    conversion_data,
    title='AB测试转化率对比'
)

# 置信区间图
fig = visualizer.plot_confidence_intervals(
    statistical_results,
    metric='转化率'
)

# 用户分群热力图
fig = visualizer.plot_segment_heatmap(
    segment_data,
    title='用户分群转化率热力图'
)
```

#### 交互式仪表板
```python
# 生成交互式仪表板
dashboard = visualizer.create_interactive_dashboard(
    analysis_results,
    output_file='ab_test_dashboard.html'
)
```

## 📊 数据格式

### AB测试数据格式 (ab_test_data.csv)

```csv
用户ID,实验组别,转化状态,留存状态,累计消费金额,性别,年龄,价值组别,设备类型
U001,测试组,是,TRUE,299.99,男,25,高价值,移动端
U002,对照组,否,FALSE,59.99,女,32,低价值,PC端
U003,测试组,是,TRUE,599.99,男,28,高价值,移动端
U004,对照组,否,FALSE,199.99,女,35,中价值,PC端
```

### 用户分群数据格式 (user_segments.csv)

```csv
用户ID,RFM分群,行为分群,人口统计分群,综合分群
U001,高价值,活跃用户,年轻男性,高价值年轻用户
U002,低价值,流失用户,成熟女性,需要唤醒用户
```

## 🎯 应用场景

### 产品优化
- 网页改版效果评估
- 功能上线影响分析
- 用户界面优化测试
- 性能改进验证

### 营销活动
- 广告创意测试
- 促销策略评估
- 邮件营销优化
- 社交媒体活动分析

### 运营策略
- 定价策略测试
- 推荐算法优化
- 用户注册流程改进
- 客户服务策略评估

## ⚙️ 高级配置

### 统计参数设置

```python
# 设置显著性水平
analyzer.set_significance_level(alpha=0.05)

# 设置统计功效
analyzer.set_statistical_power(power=0.8)

# 设置多重比较校正方法
analyzer.set_multiple_comparison_correction(method='bonferroni')
```

### 自定义分群策略

```python
# 定义自定义分群规则
custom_segments = {
    'high_value': {'累计消费金额': (500, float('inf'))},
    'medium_value': {'累计消费金额': (100, 500)},
    'low_value': {'累计消费金额': (0, 100)}
}

# 应用自定义分群
segmented_data = segment_analyzer.apply_custom_segments(
    data,
    segment_rules=custom_segments
)
```

### 高级可视化配置

```python
# 设置图表风格
visualizer.set_style(style='seaborn', palette='viridis')

# 自定义图表配置
chart_config = {
    'figsize': (12, 8),
    'dpi': 300,
    'format': 'png',
    'style': 'professional'
}

fig = visualizer.plot_with_config(data, config=chart_config)
```

## 🐛 常见问题

### Q: 如何确定合适的样本量？
A: 使用样本量计算功能，考虑效应量、显著性水平和统计功效来计算最小样本量。

### Q: p值小于0.05是否意味着结果显著？
A: p值小于0.05表示在原假设为真的情况下，观察到当前结果或更极端结果的概率小于5%。需要结合效应量和实际意义来解释。

### Q: 如何处理多重比较问题？
A: 使用Bonferroni校正、FDR校正等方法来调整p值，避免假阳性。

### Q: 何时使用贝叶斯AB测试？
A: 当需要先验信息、样本量较小或想要获得概率性结论时，考虑使用贝叶斯方法。

## 📈 性能优化

- 使用向量化操作加速计算
- 实现增量统计更新
- 采用并行计算处理大数据
- 缓存计算结果避免重复计算
- 优化内存使用模式

## 📚 技术原理

### 统计检验基础
基于假设检验理论，通过计算检验统计量和p值来判断实验结果的统计显著性。

### 中心极限定理
在大样本情况下，样本均值的分布趋近于正态分布，为许多统计方法提供理论基础。

### 贝叶斯推断
结合先验信息和观测数据，通过后验分布进行参数估计和假设检验。

### 多重比较校正
当同时进行多个假设检验时，控制总体错误率，避免假阳性结果的增加。

## 🤝 贡献指南

欢迎提交Issue和Pull Request来改进这个技能。

## 📄 许可证

MIT License

---

## 🎉 开始使用

现在你已经了解了AB测试分析技能的所有功能，可以开始使用了：

```bash
# 快速验证功能
python quick_test.py

# 运行示例
python examples/basic_ab_test_example.py
```

享受你的AB测试分析之旅！🚀

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liangdabiao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
