---
name: x-create
description: Create viral X (Twitter) posts including short tweets, threads, and replies. Use when user wants to write X content, create posts, or mentions "create tweet", "write thread", "x-create", "写推文", "创作推文". Supports 5 post styles with customizable templates, plus a mandatory humanize pass to reduce AI-sounding phrasing. First-time users go through onboarding to set up profile. Use when this capability is needed.
metadata:
  author: kangarooking
---

# X Create

Create viral X posts (short tweets, threads, replies) based on user's persona and post patterns.

## First-Time Setup

**Check user profile before creating content:**

1. Read `references/user-profile.md`
2. If `initialized: false` or file doesn't exist → Run onboarding
3. If `initialized: true` → Proceed to content creation

### Onboarding Questions

Ask user these questions using AskUserQuestion tool:

1. **账号定位（领域）**: 你的X账号主要分享什么内容？
   - Options: AI/科技, 创业/商业, 个人成长, 投资理财, Other

2. **目标受众**: 你的目标读者是谁？
   - Options: 中文用户, 英文用户, 双语用户

3. **人设风格**: 你希望塑造什么样的人设？
   - Options: 专业严肃, 轻松幽默, 犀利观点, 温暖亲和, Other

After collecting answers, update `references/user-profile.md` with `initialized: true`.

## Post Types

### 5 Categories

| Type | Style | Use When | Intent Signals (路由线索) |
|------|-------|----------|--------------------------|
| **高价值干货** | 信息密度高，可收藏 | 教程、工具推荐、方法论 | 目标是收藏/转发；强调可执行清单、工具、步骤 |
| **犀利观点** | 有态度有立场 | 行业评论、反常识观点 | 目标是讨论/对立；需要强立场、对比、反常识 |
| **热点评论** | 快速反应 | 新闻评论、事件点评 | 目标是蹭热度/抢时效；围绕刚发生事件快速解读 |
| **故事洞察** | 个人经历+洞察 | 案例分析、经验复盘 | 目标是共鸣/关注；用具体场景+转折+金句 |
| **技术解析** | 深度技术 | 原理讲解、源码分析 | 目标是建立专业度；解释原理、机制、影响与建议 |

### Output Formats

1. **短推文** (≤280 characters) - Single tweet
2. **Thread** (多条串联) - 3-10 tweets connected
3. **评论回复** - For replying to trending posts

## Creation Workflow

### Step 1: Load Context

```
1. Read references/user-profile.md → Get persona, style
2. (Optional) Read state from ~/.claude/skills/x-create/state/
   - liked_topics.json (positive samples)
   - rejected_topics.json (negative samples)
   - events.jsonl (optional)
3. Check assets/templates/{type}/ → Look for user reference posts
4. If no references → Use default patterns from references/post-patterns.md
```

### Step 2: Intent-based Routing

Determine intent first, then choose style and format:

1. **Intent → Style (5 categories)**
   - 收藏/转发导向 → 高价值干货
   - 讨论/对立导向 → 犀利观点
   - 时效/热点导向 → 热点评论
   - 共鸣/关注导向 → 故事洞察
   - 专业/技术导向 → 技术解析

2. **Style → Output format**
   - **Short tweet**: Single insight, quick take, one-liner
   - **Thread**: Multi-point analysis, step-by-step, detailed breakdown
   - **Reply**: Designed to respond to specific post/topic

If user explicitly provides `--type`, follow it. Otherwise route automatically.

### Step 3: Apply Pattern

Read `references/post-patterns.md` for the specific post type pattern.

### Step 4: Generate Content (A/B Variants)

Create **two variants** by default:

- **Variant A**: More direct, stronger hook, higher contrast
- **Variant B**: More structured, more evidence, slightly more neutral

Follow:
1. User's persona style
2. Selected post style pattern
3. Reference examples (if available)

### Step 4.5: Humanize Pass（去 AI 味，默认必做）

For **each** variant, rewrite the text to sound like a real person on X while keeping meaning and claims unchanged:

- Delete filler + chatbot politeness: avoid "当然/希望这对你有帮助/让我们来深入探讨"
- Remove grand/marketing tone: avoid "标志着/至关重要/不断演变的格局/彰显/赋能/令人叹为观止"
- No vague attribution: avoid "专家认为/行业报告显示" unless you provide a specific source; otherwise rewrite as "我观察到/我的判断是..."
- Reduce connective phrases: avoid overusing "此外/然而/因此"; prefer simple sentences and line breaks
- Break formula: do not force "三段式"; 2 points is fine; mix short + long sentences
- Avoid dash spam: do not stack "——"
- Prefer concrete details over empty conclusions; if you are unsure, say it plainly and briefly

Thread constraints:
- Each tweet must be <= 280 characters
- Do not make every tweet identical in structure; allow 1-2 short "pause" lines

### Step 5: Critic (Self-evaluation) + Rewrite Once

Score the **humanized** Variant A/B as the target reader (0-10):
- Hook strength
- Information density / value
- Clarity and readability
- Credibility (no exaggeration / no made-up facts)
- Persona fit
- Action likelihood: like / repost / bookmark / reply
- "AI 味" control: no empty grand statements, no templated endings, no vague authority

Rules:
- If **both** Variant A and B score < 7, rewrite **once** (produce A2/B2), then run the **same humanize pass again**, and re-score.
- Select the best variant as final output, but still show both drafts.

## Output Format

```markdown
# 推文创作

## 选题
{topic}

## 推文类型
{short_tweet/thread/reply}

## 风格
{post_style}

---

## Drafts

### Variant A

{For short tweet: single tweet content}

{For thread:}
### 1/N
{first tweet}

### 2/N
{second tweet}

...

### N/N
{final tweet with call to action}

**Critic score (0-10)**: {critic_score_a}

### Variant B

{For short tweet: single tweet content}

{For thread:}
### 1/N
{first tweet}

### 2/N
{second tweet}

...

### N/N
{final tweet with call to action}

**Critic score (0-10)**: {critic_score_b}

---

## Selected

Selected variant: {A|B|A2|B2}
Reason: {one-sentence reason}

---

## 发布建议
- 最佳发布时间: {suggestion}
- 配图建议: {image suggestion if applicable}
- 预期互动: {engagement prediction}

下一步：运行 /x-publish 发布到草稿箱
```

Append machine-readable blocks for hooks/state ingestion:

```json
CREATE_JSON
{
  "schema_version": "x_skills.create.v1",
  "topic": "{topic}",
  "post_type": "short|thread|reply",
  "post_style": "high-value|sharp-opinion|trending-comment|story-insight|tech-analysis",
  "variants": [
    {"id":"A","critic_score_0_10":0,"text":"..."},
    {"id":"B","critic_score_0_10":0,"text":"..."}
  ],
  "selected": "A|B|A2|B2",
  "rewrite_once": true
}
```

```json
HOOKS_JSON
{
  "schema_version": "x_skills.hooks.v1",
  "topic": "{topic}",
  "hooks": [
    {"text":"...","source":"variant.A","tags":["数字|反常识|痛点|悬念|类比"],"score_0_10":0}
  ]
}
```

## Template Priority

1. **User templates first**: Check `assets/templates/{type}/`
2. **Default patterns**: Use `references/post-patterns.md`

Example:
```
Creating 高价值干货 post:
1. Check assets/templates/high-value/
2. If files exist → Learn style from examples
3. If empty → Use default pattern from post-patterns.md
```

## Resources

### references/user-profile.md
User customization info (shared across all x-skills)

### references/post-patterns.md
Default viral post patterns for 5 categories

### assets/templates/
User-provided reference posts organized by type:
- `high-value/` - 高价值干货类参考
- `sharp-opinion/` - 犀利观点类参考
- `trending-comment/` - 热点评论类参考
- `story-insight/` - 故事洞察类参考
- `tech-analysis/` - 技术解析类参考

## Example

User: `/x-create Claude 4.5 Opus发布 --type thread`

1. Read user-profile.md → persona: 专业严肃、犀利观点
2. Check assets/templates/tech-analysis/ → empty
3. Read post-patterns.md → Get tech-analysis pattern
4. Generate thread:

```
### 1/5
Claude 4.5 Opus 上线了。我先说结论：它更像“慢一点，但更稳”的那类模型。

我用 3 个小任务试了下，写个线程记录👇

### 2/5
我最直观的感受不是“更聪明”，而是更会停下来检查自己。

同一个问题，它更少给“听起来对”的答案。

### 3/5
三个场景（都不算大项目）：
1) 重构一个旧模块：更愿意先问清边界，再动手改
2) 复杂推理题：会把关键假设写出来（这点很救命）
3) 长文档梳理：更少漏掉前后矛盾的地方

### 4/5
代价也很现实：
- 反应慢一点
- 成本可能更高（看你用的套餐/调用方式）
- 你得给它更明确的上下文

### 5/5
如果你做的是“错一次就很麻烦”的任务（代码、决策、长文整理），值得试。

只是日常闲聊，感知没那么强。你们试过了吗？
```

## Integration

After creation, suggest:
```
推文创作完成！

- 类型: {thread/short/reply}
- 字数: {word_count}
- 预计阅读: {read_time}

下一步：运行 /x-publish 发布到X草稿箱

（反馈闭环，可选）
- 采纳并进入正样本：
  python ~/.claude/skills/x-create/scripts/x_state.py like --topic-json '{"title":"{topic}","selected":"{A|B}","critic_score":8}'
- 否决并进入负样本：
  python ~/.claude/skills/x-create/scripts/x_state.py reject --topic-json '{"title":"{topic}","reason":"low_value"}'
- 写入事件（hooks 自动收集也可用）：
  python ~/.claude/skills/x-create/scripts/x_state.py event --event create.generated --payload-json '{"topic":"{topic}","variants":["A","B"],"selected":"{A|B}"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kangarooking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
