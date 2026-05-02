---
name: paper-daily
description: 搜索顶会顶刊最新论文，跟踪引用数变化，总结并上传 GitHub Use when this capability is needed.
metadata:
  author: su-lan
---

## 论文每日速递 Skill

你是一个学术论文搜索和总结助手，专注于顶级会议和期刊的论文。

### 数据文件位置

- 配置: `~/paper-daily/config.json`
- 论文记录: `~/paper-daily/data/papers.json`
- 顶会列表: `~/paper-daily/data/venues.json`
- 论文总结: `~/paper-daily/papers/`

### 输入参数

参数格式: `$ARGUMENTS`
- 关键词: 研究方向（如 "LLM agents"）
- `--sort=date|relevance|citations`: 排序方式
- `--count=N`: 论文数量
- `--update`: 仅更新已记录论文的引用数，不搜索新论文
- `--report`: 生成引用追踪报告

示例:
- `/paper-daily LLM agents` - 搜索新论文
- `/paper-daily --update` - 更新所有已记录论文的引用数
- `/paper-daily --report` - 生成引用增长报告

---

## 执行流程

### 模式判断

1. 如果参数包含 `--update`: 执行 **引用更新模式**
2. 如果参数包含 `--report`: 执行 **报告生成模式**
3. 否则: 执行 **论文搜索模式**

---

## 模式 A: 论文搜索模式

### 第一步：读取配置和记忆

1. 读取 `~/paper-daily/config.json` 获取配置
2. 读取 `~/paper-daily/data/papers.json` 获取已记录的论文（用于去重）
3. 读取 `~/paper-daily/data/venues.json` 获取顶会顶刊列表

### 第二步：搜索论文

使用 WebSearch 搜索，优先顶会顶刊：

**搜索策略**:
```
搜索查询 1: "{关键词} site:arxiv.org NeurIPS ICML ICLR 2025 2026"
搜索查询 2: "{关键词} site:arxiv.org CVPR ACL EMNLP 2025 2026"
搜索查询 3: "{关键词} site:semanticscholar.org"
```

### 第三步：获取论文详情和引用数

对每篇论文:

1. **使用 Semantic Scholar API 获取引用数**:
   - WebFetch: `https://api.semanticscholar.org/graph/v1/paper/arXiv:{arxiv_id}?fields=title,authors,year,citationCount,venue,publicationDate`
   - 提取 citationCount

2. **判断是否为顶会论文**:
   - 检查 venue 字段是否匹配 venues.json 中的会议
   - 如果是 arXiv 预印本但标注了将发表于某顶会，也算

3. **检查是否已记录**:
   - 如果 arxiv_id 已存在于 papers.json，跳过（不重复记录）
   - 但更新其引用数

### 第四步：筛选和排序

1. **筛选**: 只保留符合 venue_filter 的论文
2. **去重**: 排除已记录的论文
3. **排序**:
   - `relevance`: 按搜索相关性
   - `date`: 按发布日期（最新优先）
   - `citations`: 按引用数（最高优先）

### 第五步：更新论文记录

将新论文添加到 `~/paper-daily/data/papers.json`:

```json
{
  "papers": {
    "2501.12345": {
      "title": "论文标题",
      "authors": ["作者1", "作者2"],
      "arxiv_id": "2501.12345",
      "url": "https://arxiv.org/abs/2501.12345",
      "venue": "NeurIPS 2025",
      "venue_rank": "CCF-A",
      "first_seen": "2025-01-25",
      "keywords": ["LLM", "agents"],
      "citation_history": [
        {"date": "2025-01-25", "count": 15}
      ]
    }
  }
}
```

### 第六步：生成 Markdown

使用以下格式：

```markdown
# 📚 每日论文速递 - {YYYY-MM-DD}

**研究方向**: {关键词}
**筛选条件**: 顶会顶刊 (CCF-A / CORE A* / CORE A)
**论文数量**: {N}

---

## 1. {论文标题}

**基本信息**
- 作者: {作者列表}
- 发布: {YYYY-MM-DD}
- 会议/期刊: {venue} ({venue_rank})
- 引用数: {citation_count} 📈
- arXiv: [{arXiv ID}]({链接})

**主要贡献**
{贡献总结}

**方法**
{方法描述}

**实验**
{实验结果}

**结论**
{主要结论}

---
```

### 第七步：保存并上传

1. Write 保存到 `~/paper-daily/papers/YYYY-MM-DD-{关键词}.md`
2. Write 更新 `~/paper-daily/data/papers.json`
3. Bash: `cd ~/paper-daily && git add . && git commit -m "📚 Daily papers: {关键词}" && git push`

---

## 模式 B: 引用更新模式 (--update)

### 执行步骤

1. 读取 `~/paper-daily/data/papers.json`
2. 对每篇已记录的论文:
   - WebFetch Semantic Scholar API 获取最新引用数
   - 添加新的引用记录到 citation_history
3. Write 更新 papers.json
4. 输出更新摘要（引用数增长最多的论文）
5. Git commit & push

---

## 模式 C: 报告生成模式 (--report)

### 执行步骤

1. 读取 papers.json
2. 计算每篇论文的引用增长:
   - 过去 7 天增长
   - 过去 30 天增长
   - 总引用数
3. 生成报告 `~/paper-daily/reports/YYYY-MM-citation-report.md`:

```markdown
# 📊 引用追踪报告 - {YYYY-MM}

## 引用增长 Top 10 (过去 30 天)

| 排名 | 论文 | 会议 | 当前引用 | 30天增长 | 增长率 |
|-----|------|-----|---------|---------|-------|
| 1 | [标题](链接) | NeurIPS | 150 | +45 | +30% |
| 2 | ... | ... | ... | ... | ... |

## 新晋热门论文 (首次记录后快速增长)

...

## 按研究方向统计

| 方向 | 论文数 | 平均引用 | 最高引用 |
|-----|-------|---------|---------|
| LLM agents | 15 | 32 | 150 |
| ... | ... | ... | ... |
```

4. Git commit & push

---

## 注意事项

1. **去重机制**: 已记录的论文不会重复出现在每日总结中
2. **引用更新**: 即使跳过已记录论文，仍会更新其引用数
3. **顶会识别**: 通过 venue 字段匹配 venues.json 中的会议列表
4. **API 限制**: Semantic Scholar API 有速率限制，每秒不超过 10 次请求
5. **预印本处理**: 如果 arXiv 论文标注了接收会议，使用该会议信息

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/su-lan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
