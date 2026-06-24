---
name: hot-topic-vocabulary
description: Automatically analyze AI/tech trends and generate updated hot topic vocabulary lists every 4-8 hours. Uses NLP to extract emerging keywords from multiple sources. Use when this capability is needed.
metadata:
  author: icetomoyo
---

# Hot Topic Vocabulary 🔥

动态热点词汇生成器，每 4-8 小时自动分析趋势并更新热点词汇表。

## 功能

- 📊 **多源分析**：从 arXiv、HackerNews、Reddit、Twitter 抓取数据
- 🧠 **NLP 提取**：使用自然语言处理提取新兴关键词
- 📈 **趋势评估**：计算词汇热度趋势（上升/下降/新兴）
- 🔄 **自动更新**：定时更新 ai-trend-monitor 的热点词汇表
- 💾 **历史追踪**：保存词汇热度历史，识别长期趋势

## 使用方法

### 手动运行
```bash
node skills/hot-topic-vocabulary/scripts/analyze.js
```

### 定时任务（推荐）
```bash
# 每 6 小时运行一次
0 */6 * * * cd /path/to/clawd && node skills/hot-topic-vocabulary/scripts/analyze.js
```

## 输出

- `output/vocabulary-YYYY-MM-DD-HH.json` - 完整词汇分析
- `output/hot-topics-latest.json` - 最新热点词汇（供其他技能使用）
- `output/trend-report.md` - 趋势报告

## 词汇分类

1. **AI Models** - 大模型、多模态模型
2. **Robotics** - 机器人、具身智能
3. **Agents** - AI代理、自动化工具
4. **Infrastructure** - 训练、推理、部署
5. **Applications** - 应用场景、行业应用
6. **Safety** - 安全、对齐、伦理

## 更新日志

### v1.0.0 (2026-02-02)
- 初始版本
- 支持 5 大分类动态词汇提取
- 自动更新 ai-trend-monitor 配置

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icetomoyo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
