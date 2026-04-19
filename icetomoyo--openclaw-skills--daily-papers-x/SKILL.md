---
name: daily-papers-x
description: Automatically fetch and summarize trending AI papers from arXiv and Hugging Face with x.com-style hot topic detection. Focuses on 4 research directions - AI/LLM, Embodied AI, AI+Finance, AI+Biomedical. Use when this capability is needed.
metadata:
  author: icetomoyo
---

# Daily Papers X - Trending Edition

Fetch trending AI papers with x.com-style hot topic detection across 4 research directions.

## 四大研究方向 (4 Research Directions)

### 1. 人工智能 (AI & LLM)
- **arXiv**: cs.AI, cs.LG, cs.CL
- **涵盖**: 大模型、深度学习、NLP、多模态
- **热门关键词**: GPT, Claude, DeepSeek, Llama, Mistral, LoRA, RAG, Agent, RLHF, reasoning

### 2. 具身智能 (Embodied AI)
- **arXiv**: cs.RO, cs.AI, cs.CV
- **涵盖**: VLA, World Model, 机器人学习、人形机器人
- **热门关键词**: VLA, RT-2, World Model, JEPA, humanoid, sim-to-real, teleoperation

### 3. AI与金融结合 (AI + Finance)
- **arXiv**: cs.AI, q-fin.*
- **涵盖**: 量化交易、风险管理、加密货币、DeFi
- **热门关键词**: algorithmic trading, portfolio, crypto, DeFi, risk management, FinGPT

### 4. AI与生物医学结合 (AI + Biomedical)
- **arXiv**: cs.AI, cs.CV, q-bio.*
- **涵盖**: 药物发现、医学影像、临床诊断、蛋白质折叠
- **热门关键词**: AlphaFold, drug discovery, medical imaging, clinical LLM, genomics

## Quick Start

```bash
# Run once
node skills/daily-papers-x/scripts/fetch-papers.js

# Output
# - Full report: memory/papers-YYYY-MM-DD.md
# - WhatsApp summary: memory/papers-YYYY-MM-DD-summary.txt
```

## Trending Detection Algorithm

### Hot Topic Scoring (0-10 scale)

| Signal Type | Weight | Examples |
|------------|--------|----------|
| 方向-specific 热门话题 | +2.5 | VLA, RT-2, AlphaFold 3, GPT-4o |
| 病毒传播指标 | +4.0 | SOTA, beats GPT-4, open source |
| 通用热门话题 | +1.5 | efficiency, multimodal, synthetic data |
| 时效性 (6h内) | +1.5 | 新发布论文 |
| HF 点赞 | +0.5×log | 社区互动 |

### Featured Papers

- 🏆 **Most Recommended**: 综合热度+质量 TOP 1
- 🔥 **Most Trending**: x.com 风格最热论文
- 🎨 **Most Interesting**: 创新概念
- 👍 **Most Popular**: 最高互动
- 🧠 **Most Deep**: 理论深度
- 💎 **Most Valuable**: 实用价值

## Data Sources

- **arXiv API**: cs.AI, cs.RO, cs.LG, cs.CL, cs.CV, q-fin.*, q-bio.*
- **Hugging Face Daily Papers**: Community engagement data
- **Trending Signals**: 按4大方向分类的热门话题

## Automation

Set up cron for daily 8 AM:
```bash
0 8 * * * cd /path/to/clawd && node skills/daily-papers-x/scripts/fetch-papers.js
```

## WhatsApp Output Format

```
📚 Daily AI Papers - 2026-02-01
🔥 x.com Trending Edition

📊 四大方向共 25 篇 | 均热: 6.8/10

📈 方向分布:
   人工智能: 10篇
   具身智能: 5篇
   AI与金融: 4篇
   AI与生物医学: 6篇

🏆 TOP PICK - 最推荐
━━━━━━━━━━━━━━━━━━━━━━
📌 [Paper Title]

📝 [Abstract preview...]

✨ 推荐理由:
   🔥 高热度话题
   📦 开源可用
   🏆 SOTA性能

🔥 热度: 8.5/10
🔗 [PDF URL]
━━━━━━━━━━━━━━━━━━━━━━

📋 Top Papers:
🔥 [trending paper 1]
🔥 [trending paper 2]
• [other paper 3]
...
```

## Configuration

Edit `scripts/fetch-papers.js`:
- `CONFIG.hoursBack`: 搜索时间范围 (默认 24h)
- `CONFIG.totalMaxResults`: 最大论文数 (默认 30)
- `TRENDING_TRACKERS`: 自定义热门话题关键词
- `CATEGORIES`: 调整4大方向的分类和关键词

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icetomoyo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
