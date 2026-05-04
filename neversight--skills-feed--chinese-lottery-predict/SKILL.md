---
name: chinese-lottery-predict
description: Predicts the next lottery numbers for Chinese lotteries like Double Color Ball (双色球) and Super Lotto (大乐透). Use this skill when user asks to predict lottery results in Chinese (e.g., "预测双色球", "大乐透推荐"). Use when this capability is needed.
metadata:
  author: neversight
---

# Chinese Lottery Predict

Analyzes historical data from major Chinese lottery websites to provide statistical predictions for the next draw.

## Prerequisites

- **WebSearch**: To fetch the latest lottery results.
- **Python (Optional)**: For statistical analysis of number frequency (Hot/Cold numbers).

## Workflow

### 1. Input Parsing
The user will provide:
- **Lottery Type**: e.g., "双色球" (Double Color Ball) or "大乐透" (Super Lotto).
- **Funds** (Optional): Budget for the purchase (default: "10元").

### 2. Data Retrieval
Use `WebSearch` to find the latest 30-50 draw results.

#### Search Strategy (Anti-Scraping & Reliability)
1. **Primary Search (Official Sources)**: Attempt to fetch data from official government or authoritative industry sites first.
   - **Keywords**: `site:cwl.gov.cn {Lottery Type} 往期`, `site:lottery.gov.cn {Lottery Type} 开奖公告`, `site:500.com {Lottery Type} 走势图`
   - **Target Domains**:
     - `cwl.gov.cn` (China Welfare Lottery - Official for Double Color Ball)
     - `lottery.gov.cn` (China Sports Lottery - Official for Super Lotto)
     - `zhcw.com` (China Lottery Online)
     - `500.com` (500.com)

2. **Fallback Search (Static/Portal Sites)**:
   - **Trigger**: If official sites fail to load content (due to dynamic JS rendering or anti-scraping blocks) or return incomplete data.
   - **Action**: Search for static news portals or text-based lists which are easier to parse.
   - **Keywords**: `"{Lottery Type}" 近50期开奖结果 汇总 新浪`, `"{Lottery Type}" 历史号码 文本版`
   - **Target Domains**: `sina.com.cn`, `163.com`, `sohu.com`.

#### Data Verification
- Cross-reference the latest draw date from at least two sources to ensure data is up-to-date.
- Ensure the "Issue Number" (期号) is continuous.

### 3. Data Analysis
Analyze the retrieved data to identify:
- **Hot Numbers**: Numbers that appeared most frequently in the last 30 draws.
- **Cold Numbers**: Numbers that haven't appeared in a long time.
- **Omitted Numbers**: Current omission count for each number.

### 4. Prediction Generation
Generate 1-5 sets of numbers based on a mix of Hot and Cold numbers.
*Disclaimer: Lottery draws are independent random events. Predictions are for entertainment only.*

### 5. Output Generation
Generate a report in Chinese using the following format.

#### Output Template

```markdown
# {LotteryType} 预测分析报告

## 📅 基本信息
- **分析期数**: 近 {count} 期
- **数据来源**: {source_domain}
- **下期开奖**: {next_draw_date}

## 📊 历史数据分析
- **热号 (Hot)**: {hot_numbers}
- **冷号 (Cold)**: {cold_numbers}

## 🔮 推荐号码
根据历史走势分析，为您生成以下推荐：

| 方案 | 红球 | 蓝球/后区 | 说明 |
| :--- | :--- | :--- | :--- |
| 1 | {reds} | {blues} | {reason} |
| 2 | {reds} | {blues} | {reason} |

## 💡 购彩建议 (预算: {funds})
{suggestion_text}

> **⚠️ 风险提示**: 彩票无绝对规律，预测结果仅供参考，请理性投注。
```

## Examples

**User**: "预测下期双色球"
**Action**: Search "双色球近30期开奖", analyze frequencies, generate report.

**User**: "大乐透，买50块钱的"
**Action**: Search "大乐透近30期开奖", generate ~2-3 combinations fitting the 50 RMB budget.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
