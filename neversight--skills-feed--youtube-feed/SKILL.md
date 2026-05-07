---
name: youtube-feed
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# YouTube 播客更新监控

获取关注的 YouTube 博主/播客的最新更新。

## 关注的频道列表

数据来源：[Zara's AI Learning Library](https://zara.faces.site/ai)

### AI 教育 & 技术深度
| 播客名称 | 频道 |
|---------|------|
| Andrej Karpathy | @AndrejKarpathy |
| Anthropic | @anthropic-ai |
| Lex Fridman | @lexfridman |

### AI 产品 & 创业
| 播客名称 | 频道 |
|---------|------|
| Lenny's Podcast | @LennysPodcast |
| Peter Yang | @PeterYangYT |
| The MAD Podcast (Matt Turck) | @DataDrivenNYC |
| Every | @EveryInc |

### VC & 投资人
| 播客名称 | 频道 |
|---------|------|
| Y Combinator | @ycombinator |
| Latent Space | @LatentSpacePod |
| South Park Commons | @southparkcommons |
| No Priors | @NoPriorsPodcast |
| a16z | @a16z |

### 大厂 & 研究
| 播客名称 | 频道 |
|---------|------|
| Google DeepMind | @googledeepmind |
| Google for Developers | @GoogleDevelopers |
| Stanford GSB | @stanfordgsb |

### Vibe Coding & 工具
| 播客名称 | 频道 |
|---------|------|
| Mckay Wrigley | @realmckaywrigley |
| Tiago Forte | @TiagoForte |
| The Pragmatic Engineer | @ThePragmaticEngineer |

### AI 新闻 & 趋势
| 播客名称 | 频道 |
|---------|------|
| The AI Daily Brief | @TheAIDailyBrief |
| TBPN | @TBPNLive |
| Brett Malinowski | @TheBrettWay |

## 快速使用

```bash
# 默认获取最近 2 天更新
python3 scripts/get_updates.py

# 获取最近 7 天（一周）的更新
python3 scripts/get_updates.py --days 7

# Markdown 格式输出（带可点击链接）
python3 scripts/get_updates.py --markdown

# 获取播放量（会增加请求时间）
python3 scripts/get_updates.py --views

# 组合使用
python3 scripts/get_updates.py --days 7 --markdown --views
```

## 自然语言映射

当用户说：
- "获取播客更新" → `--days 2`
- "获取近一周的播客更新" / "这周有什么新播客" → `--days 7`
- "获取近三天的播客更新" → `--days 3`
- "获取本月的播客更新" → `--days 30`

## 输出内容

每个播客更新包含：
- **标题**：可点击的 YouTube 链接
- **频道**：来源频道名称
- **日期**：发布日期
- **播放量**：视频观看次数（使用 --views 参数）
- **摘要**：约 300-400 字的内容摘要（从视频描述自动提取）

## 工作流程

当用户说"获取播客更新"时：

### Step 1: 获取更新列表

运行脚本获取最近 2 天的更新：

```bash
python3 /Users/ugreen/.claude/skills/youtube-feed/scripts/get_updates.py --days 2
```

### Step 2: 展示更新列表

向用户展示格式化的更新列表：

```
📺 最近 2 天共有 N 个新播客更新：

1. 【Lenny's Podcast】
   📌 视频标题
   🕐 2026-01-30 15:00
   🔗 https://www.youtube.com/watch?v=xxx
   📝 简要描述...

2. 【No Priors】
   ...
```

### Step 3: 用户选择

询问用户：
```
请选择要处理的播客（输入序号，如 1 或 1,3）：
```

### Step 4: 连接到 podcast-workflow

用户选择后，调用 `podcast-workflow` Skill 进行完整处理：
1. 提取字幕
2. Content-digest 处理
3. 保存到飞书

## 添加新频道

编辑 `scripts/get_updates.py` 中的 `CHANNELS` 列表：

```python
CHANNELS = [
    ("频道名称", "channel_id", "@handle"),
    # ...
]
```

获取 channel_id 的方法：
1. 打开频道页面
2. 查看页面源码，搜索 "channelId"
3. 或使用在线工具如 https://commentpicker.com/youtube-channel-id.php

## 与其他 Skill 配合

本 Skill 是播客处理流程的第一步：

```
youtube-feed → 用户选择 → podcast-workflow → 飞书
     ↓              ↓              ↓
  获取更新      挑选播客      字幕+处理+发布
```

## 完整工作流示例

**用户：** 获取播客更新

**Agent：**
1. 运行 get_updates.py
2. 展示更新列表
3. 询问用户要处理哪个

**用户：** 处理第 2 个

**Agent：**
1. 调用 podcast-workflow
2. 提取字幕
3. 询问是否 content-digest
4. 处理并保存飞书

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
