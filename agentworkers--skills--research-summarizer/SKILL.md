---
name: research-summarizer
description: **面向非开发人员的结构化研究摘要工具** Use when this capability is needed.
metadata:
  author: AgentWorkers
---
# 研究摘要工具

> 阅读更少，理解更多。正确引用。

这是一个结构化的研究摘要工作流程，能够将冗长的原始材料转化为实用的简报。专为产品经理、分析师、创业者以及所有需要阅读大量资料的人设计。

这不仅仅是一个简单的“总结”功能——而是一个可重复使用的框架，能够提取关键信息、对比不同来源的内容，并正确格式化引用。

---

## 命令行工具

| 命令 | 功能 |
|---------|-------------|
| `/research:summarize` | 将单个来源的内容总结为结构化的简报 |
| `/research:compare` | 并排比较2-5个来源，并进行综合分析 |
| `/research:cite` | 从文档中提取并格式化所有引用 |

---

## 该技能的触发条件

当用户发出以下请求时，该技能会被激活：
- “总结这篇论文/文章/报告”
- “这份文档的主要发现是什么？”
- “比较这些来源”
- “从这份PDF中提取引用”
- “提供关于[主题]的研究简报”
- “分析这份白皮书”

如果用户有一份文档并希望获得结构化的理解，那么这个技能就非常适用。

---

## 工作流程

### `/research:summarize` — 单个来源的摘要

1. **识别来源类型**
   - 学术论文 → 使用IMRAD结构（引言、方法、结果、分析、讨论）
   - 网络文章 → 使用“主张-证据-影响”的结构
   - 技术报告 → 使用执行摘要结构
   - 文档 → 使用参考文献摘要结构

2. **提取结构化简报**
   ```
   Title: [exact title]
   Author(s): [names]
   Date: [publication date]
   Source Type: [paper | article | report | documentation]

   ## Key Thesis
   [1-2 sentences: the central argument or finding]

   ## Key Findings
   1. [Finding with supporting evidence]
   2. [Finding with supporting evidence]
   3. [Finding with supporting evidence]

   ## Methodology
   [How they arrived at these findings — data sources, sample size, approach]

   ## Limitations
   - [What the source doesn't cover or gets wrong]

   ## Actionable Takeaways
   - [What to do with this information]

   ## Notable Quotes
   > "[Direct quote]" (p. X)
   ```

3. **评估质量**
   - 来源的可信度（是否经过同行评审、是否来自知名机构、是一手资料还是二手资料）
   - 证据的质量（是否有数据支持、是基于事实还是仅凭主观描述）
   - 新鲜度（发布时间、是否仍然具有相关性）
   - 偏见指标（资金来源、作者的隶属关系、方法上的缺陷）

---

### `/research:compare` — 多个来源的比较

1. **收集来源**（2-5份文档）
2. **使用上述单个来源的流程对每个来源进行总结**
3. **构建比较矩阵**
   ```
   | Dimension        | Source A        | Source B        | Source C        |
   |------------------|-----------------|-----------------|-----------------|
   | Central Thesis   | ...             | ...             | ...             |
   | Methodology      | ...             | ...             | ...             |
   | Key Finding      | ...             | ...             | ...             |
   | Sample/Scope     | ...             | ...             | ...             |
   | Credibility      | High/Med/Low    | High/Med/Low    | High/Med/Low    |
   ```

4. **综合分析**
   - 各来源在哪些方面达成一致？（一致的观点表明信息更可靠）
   - 各来源在哪些方面存在分歧？（分歧需要进一步调查）
   - 所有来源之间存在哪些差距？
   - 每种观点的证据力度如何？

5. **生成综合简报**
   ```
   ## Consensus Findings
   [What most sources agree on]

   ## Contested Points
   [Where sources disagree, with strongest evidence for each side]

   ## Gaps
   [What none of the sources address]

   ## Recommendation
   [Based on weight of evidence, what should the reader believe/do?]
   ```

### `/research:cite` — 引用提取

1. **扫描文档中的所有参考文献、脚注和文本内引用**
2. **根据指定的格式（默认为APA 7）提取并格式化引用**
3. **按类型分类引用**：
   - 一手资料（原始研究、数据）
   - 二手资料（综述、元分析、评论）
   - 三手资料（教科书、百科全书）
4. **输出带有分类标签的参考文献列表**

支持的引用格式：
- **APA 7**（默认）——社会科学、商业领域
- **IEEE** — 工程学、计算机科学
- **Chicago** — 人文学科、历史学
- **Harvard** — 一般学术领域
- **MLA 9** — 艺术、人文学科

---

## 工具

### `scripts/extract_citations.py`

这是一个用于从文本中提取和格式化引用的命令行工具。

**特点：**
- 基于正则表达式的引用检测（DOI、URL、作者-年份、编号引用）
- 支持多种输出格式（APA、IEEE、Chicago、Harvard、MLA）
- 可导出JSON格式，以便与参考文献管理工具集成
- 可去除重复的引用

**使用方法：**
```bash
# Extract citations from a file (APA format, default)
python3 scripts/extract_citations.py document.txt

# Specify format
python3 scripts/extract_citations.py document.txt --format ieee

# JSON output
python3 scripts/extract_citations.py document.txt --format apa --output json

# From stdin
cat paper.txt | python3 scripts/extract_citations.py --stdin
```

### `scripts/format_summary.py`

这是一个用于生成结构化研究摘要的命令行工具。

**特点：**
- 提供多种摘要模板（学术论文、文章、报告、执行摘要）
- 可配置摘要的长度（简短、标准、详细）
- 支持Markdown和纯文本输出
- 可提取关键发现并标注证据来源

**使用方法：**
```bash
# Generate structured summary template
python3 scripts/format_summary.py --template academic

# Brief executive summary format
python3 scripts/format_summary.py --template executive --length brief

# All templates listed
python3 scripts/format_summary.py --list-templates

# JSON output
python3 scripts/format_summary.py --template article --output json
```

---

## 质量评估框架

从四个维度对每个来源进行评估：

| 维度 | 高 | 中 | 低 |
|-----------|------|--------|-----|
| **可信度** | 经过同行评审、作者知名 | 来自知名机构、作者可靠 | 博客文章、作者未知、无评审 |
| **证据** | 数据样本量大、方法严谨 | 数据量适中、方法合理 | 仅凭主观描述、无数据支持 |
| **新鲜度** | 发布时间在2年内 | 发布时间在2-5年内 | 发布时间超过5年，可能过时 |
| **客观性** | 无利益冲突、观点平衡 | 作者有部分关联关系 | 由利益相关方资助、观点片面 |

**总体评分：**
- 4个高评价 = 来源可靠——可放心引用
- 2个或以上中等评价 = 来源尚可——引用时需谨慎
- 2个或以上低评价 = 来源不可靠——引用前需独立验证

---

## 摘要模板

请参阅`references/summary-templates.md`：
- 学术论文摘要模板（IMRAD格式）
- 网络文章摘要模板（“主张-证据-影响”结构）
- 技术报告摘要模板（执行摘要格式）
- 对比分析模板（包含对比矩阵和综合分析）
- 文献综述模板（按主题组织）

请参阅`references/citation-formats.md`：
- APA 7格式规则及示例
- IEEE格式规则及示例
- Chicago、Harvard、MLA格式的快速参考

---

## 主动提醒机制

在以下情况下会自动发出提醒：
- **来源没有日期** → 请注意：无日期的来源可信度较低。
- **来源与其他来源相互矛盾** → 明确指出矛盾之处，不要掩盖分歧。
- **来源需要付费才能访问** → 请注意访问限制；如果知道其他替代来源，请提供建议。
- **用户仅提供了一个比较用的来源** → 请至少再提供另一个来源。比较至少需要两个来源。
- **引用信息不完整** → 请标记缺失的字段（年份、作者、标题）。不要随意补充元数据。
- **在变化迅速的领域中，来源发布时间超过5年** → 警告该来源可能已经过时。

---

## 安装方法

### 单个工具的安装命令
```bash
git clone https://github.com/alirezarezvani/claude-skills.git
cp -r claude-skills/product-team/research-summarizer ~/.claude/skills/
```

### 多个工具的安装方法
```bash
./scripts/convert.sh --skill research-summarizer --tool codex|gemini|cursor|windsurf|openclaw
```

### OpenClaw
```bash
clawhub install cs-research-summarizer
```

---

## 相关技能

- **产品分析** — 定量分析。两者互补：使用`研究摘要工具`处理定性资料，使用`产品分析工具`处理定量数据。
- **竞争分析** — 竞争研究。两者互补：使用`研究摘要工具`分析单个来源，使用`竞争分析工具`分析市场格局。
- **内容创作** — 内容撰写。`研究摘要工具`可为内容创作提供素材——先总结资料，再开始写作。
- **产品发现** — 信息发现工具。两者互补：`研究摘要工具`用于基础研究，`产品发现工具`用于用户研究。

---
> Source: [AgentWorkers/skills](https://github.com/AgentWorkers/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
