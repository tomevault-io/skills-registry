---
name: ddg-search
description: DuckDuckGo 搜索工具 - 无需 API key 的网页搜索替代方案 Use when this capability is needed.
metadata:
  author: 90le
---

# DuckDuckGo Search 🦆

使用 DuckDuckGo 进行网页搜索，无需 API key，是 web_search 的理想替代方案。

## 概述

`ddg-search` 让你可以：
- 进行免费的网页搜索
- 获取搜索结果和链接
- 支持多个查询
- 无需注册或 API key

## 设置

### 前置要求

- **Bash:** 脚本运行
- **curl:** HTTP 请求

### 安装

1. **复制脚本到你的 workspace:**
```bash
cd ~/clawd
mkdir -p scripts
cp [path/to]/ddg-search.sh scripts/
chmod +x scripts/ddg-search.sh
```

## 使用方法

### 基本搜索
```bash
./scripts/ddg-search.sh "搜索关键词"
```

### 搜索示例
```bash
# 搜索技术文档
./scripts/ddg-search.sh "OpenClaw AI assistant skills"

# 搜索新闻
./scripts/ddg-search.sh "latest AI news 2026"

# 搜索教程
./scripts/ddg-search.sh "how to install homebrew"
```

## 输出格式

搜索结果包含：
- 标题
- 描述/摘要
- 链接 URL

示例输出：
```
搜索: OpenClaw AI assistant skills

1. OpenClaw - AI Assistant Framework
   https://github.com/openclaw/openclaw
   OpenClaw is an AI assistant framework...

2. OpenClaw Skills Hub
   https://github.com/90le/openclaw-skills-hub
   A GitHub repository for OpenClaw AI assistants...

3. 如何使用 OpenClaw 技能
   https://docs.openclaw.ai/skills
   详细指南...
```

## 高级用法

### 搜索并保存结果
```bash
./scripts/ddg-search.sh "查询关键词" > search-results.txt
```

### 搜索特定网站
```bash
./scripts/ddg-search.sh "关键词 site:github.com"
```

### 搜索文件类型
```bash
./scripts/ddg-search.sh "关键词 filetype:pdf"
```

### 多关键词搜索
```bash
./scripts/ddg-search.sh "keyword1 AND keyword2"
```

## 使用场景

### 场景 1: 查找技术文档
```bash
./scripts/ddg-search.sh "OpenClaw documentation"
```

### 场景 2: 研究 API
```bash
./scripts/ddg-search.sh "GitHub API endpoints"
```

### 场景 3: 学习教程
```bash
./scripts/ddg-search.sh "bash scripting tutorial"
```

### 场景 4: 查找工具
```bash
./scripts/ddg-search.sh "macOS automation tools"
```

### 场景 5: 调试问题
```bash
./scripts/ddg-search.sh "error: command not found jq"
```

## 与 web_fetch 配合

搜索后获取详细内容：
```bash
# 1. 搜索
./scripts/ddg-search.sh "OpenClaw skills" | grep "github.com"

# 2. 提取 URL
URL="https://github.com/90le/openclaw-skills-hub"

# 3. 获取详细内容
curl -s "$URL" | grep -A 5 "description"
```

## 替代方案对比

| 工具 | API Key | 速度 | 准确性 | 适用场景 |
|------|---------|------|--------|----------|
| web_search | 需要 | 快 | 高 | 商业用途 |
| ddg-search | 不需要 | 中 | 中 | 日常搜索 |
| wiki-search | 不需要 | 快 | 高 | 百科知识 |

## 限制

### DuckDuckGo API 限制
- 每日请求限制（非付费账户）
- 搜索结果可能受地区影响
- 高级搜索功能有限

### 替代方案
- 使用 **wiki-search** 查询百科知识
- 使用 **web_fetch** 获取特定页面内容
- 直接访问搜索引擎网页界面

## 最佳实践

### 1. 搜索策略
- 使用精确的关键词
- 多尝试不同的查询方式
- 结合多种搜索方法

### 2. 结果验证
- 检查链接有效性
- 验证内容来源
- 交叉验证重要信息

### 3. 搜索效率
- 避免过于频繁的请求
- 缓存搜索结果
- 使用高级搜索语法

### 4. 信息提取
- 使用 grep 过滤结果
- 提取关键信息
- 保存有用的链接

## 搜索技巧

### DuckDuckGo Bang 语法
```bash
# 搜索特定网站
./scripts/ddg-search.sh "!g keyword"        # Google
./scripts/ddg-search.sh "!github keyword"    # GitHub
./scripts/ddg-search.sh "!docs keyword"     # 文档搜索

# 搜索特定平台
./scripts/ddg-search.sh "!stackoverflow problem"
```

### 高级操作符
```bash
# 精确匹配
./scripts/ddg-search.sh "\"exact phrase\""

# 排除关键词
./scripts/ddg-search.sh "keyword -exclude"

# 通配符
./scripts/ddg-search.sh "key*word"

# 范围搜索
./scripts/ddg-search.sh "price $50..$100"
```

## 与其他工具集成

### 结合 skill-creator
```bash
# 查找技能模板
./scripts/ddg-search.sh "OpenClaw skill template"
# 创建新技能
```

### 结合 github CLI
```bash
# 搜索 GitHub 仓库
./scripts/ddg-search.sh "site:github.com openclaw skills"
# 克隆感兴趣的仓库
```

### 结合 summarize
```bash
# 搜索长文章
./scripts/ddg-search.sh "AI agents long article"
# 使用 summarize 总结
```

## 故障排除

### 无搜索结果
- 尝试不同的关键词
- 检查网络连接
- 验证 DuckDuckGo 服务状态

### 搜索结果不相关
- 调整关键词精确度
- 使用引号精确匹配
- 尝试同义词

### 请求失败
- 检查 curl 是否安装
- 验证网络配置
- 尝试其他搜索方法

## 自动化脚本

### 批量搜索
```bash
#!/bin/bash
# batch-search.sh

keywords=("OpenClaw" "AI assistant" "skills hub")

for keyword in "${keywords[@]}"; do
  echo "搜索: $keyword"
  ./scripts/ddg-search.sh "$keyword" > "results/${keyword}.txt"
  sleep 2  # 避免请求过快
done
```

### 定期搜索
```bash
# 添加到 cron
openclaw cron add \
  --name "daily-news-search" \
  --schedule "0 9 * * *" \
  --command "./scripts/ddg-search.sh 'AI news' > memory/daily-news.txt"
```

### 搜索通知
```bash
#!/bin/bash
# search-and-notify.sh

keyword=$1
results=$(./scripts/ddg-search.sh "$keyword")

# 如果有结果，发送通知
if [ -n "$results" ]; then
  echo "找到 $keyword 的搜索结果！"
  echo "$results"
fi
```

## 扩展功能

### 搜索历史记录
```bash
#!/bin/bash
# search-history.sh

HISTORY_FILE="memory/search-history.txt"

echo "$(date '+%Y-%m-%d %H:%M') - $*" >> "$HISTORY_FILE"
./scripts/ddg-search.sh "$*"
```

### 搜索结果分析
```bash
#!/bin/bash
# analyze-search.sh

results=$(./scripts/ddg-search.sh "$1")

# 统计结果数量
count=$(echo "$results" | grep -c "^[0-9]\+\.")

# 提取所有链接
links=$(echo "$results" | grep -o "https://[^ ]*")

echo "搜索结果数: $count"
echo "链接数: $(echo "$links" | wc -l)"
```

## 贡献

欢迎改进！请在 GitHub 上提交 issue 或 PR。

## 仓库

https://github.com/90le/openclaw-skills-hub

## 作者

Created by Xiaoqiu (小丘) - OpenClaw AI assistant

---

**自由搜索，无需限制！** 🦆

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/90le) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
