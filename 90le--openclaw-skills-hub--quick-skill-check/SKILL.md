---
name: quick-skill-check
description: 快速技能检查 - 一键查看已学习的技能列表和简要描述 Use when this capability is needed.
metadata:
  author: 90le
---

# Quick Skill Check 📚

快速查看已学习的 OpenClaw 技能列表，帮助 AI 助手了解自己的能力。

## 概述

`quick-skill-check` 让你可以：
- 快速列出所有已学习的技能
- 查看每个技能的简要描述
- 统计技能总数
- 易于自定义和扩展

## 设置

### 前置要求

- **Bash:** 脚本运行

### 安装

1. **复制脚本到你的 workspace:**
```bash
cd ~/clawd
mkdir -p scripts
cp [path/to]/quick-skill-check.sh scripts/
chmod +x scripts/quick-skill-check.sh
```

2. **（可选）自定义技能列表:**
编辑脚本中的 `learned` 数组以匹配你的技能：
```bash
declare -a learned=(
  "skill-name:技能描述"
  "another-skill:另一个描述"
)
```

## 使用方法

### 快速查看技能列表
```bash
./scripts/quick-skill-check.sh
```

### 输出示例
```
=== 已学习技能简要 ===

coding-agent         编程代理
mcporter            MCP工具
oracle              长思考
peekaboo            macOS自动化
things-mac          任务管理
...

总计: 53/52
```

## 自定义技能列表

### 格式说明
每条技能的格式为：
```bash
"技能名:技能描述"
```

### 示例技能列表
```bash
declare -a learned=(
  # 编程与自动化
  "coding-agent:编程代理"
  "mcporter:MCP工具"
  "oracle:长思考"
  
  # macOS集成
  "peekaboo:macOS自动化"
  "things-mac:任务管理"
  "apple-reminders:提醒"
  
  # 内容处理
  "summarize:内容总结"
  "openai-whisper:语音转文字"
  
  # 信息获取
  "weather:天气"
  "1password:密码管理"
  
  # 通讯
  "discord:Discord控制"
  "slack:Slack控制"
  
  # 等等...
)
```

## 使用场景

### 场景 1: 开始新任务前
```bash
# 查看有哪些技能可以使用
./scripts/quick-skill-check.sh | grep "自动化"
```

### 场景 2: 学习新技能后
```bash
# 添加新技能到列表
vim scripts/quick-skill-check.sh
# 在 learned 数组中添加新技能

# 验证
./scripts/quick-skill-check.sh
```

### 场景 3: 与其他 AI 交流时
```bash
# 快速分享能力
./scripts/quick-skill-check.sh
```

### 场景 4: 自我评估
```bash
# 统计技能数量
./scripts/quick-skill-check.sh | tail -1
```

## 扩展功能

### 按类别分组显示
```bash
#!/bin/bash
# 增强版 quick-skill-check.sh

echo "=== 编程与自动化 ==="
for skill in "${learned[@]}"; do
  IFS=':' read -r name desc <<< "$skill"
  [[ "$name" =~ (coding|mcporter|oracle|github|tmux) ]] && \
    printf "%-20s %s\n" "$name" "$desc"
done

echo ""
echo "=== macOS 集成 ==="
for skill in "${learned[@]}"; do
  IFS=':' read -r name desc <<< "$skill"
  [[ "$name" =~ (peekaboo|things|reminders|bear) ]] && \
    printf "%-20s %s\n" "$name" "$desc"
done
```

### 添加技能详细信息
```bash
# 显示技能目录位置
SKILL_PATH="/opt/homebrew/lib/node_modules/@qingchencloud/openclaw-zh/skills"

for skill in "${learned[@]}"; do
  IFS=':' read -r name desc <<< "$skill"
  path="$SKILL_PATH/$name"
  if [ -d "$path" ]; then
    size=$(du -sh "$path" 2>/dev/null | cut -f1)
    printf "%-20s %s [%s]\n" "$name" "$desc" "$size"
  fi
done
```

### 自动发现技能
```bash
# 自动扫描技能目录
SKILLS_DIR="/opt/homebrew/lib/node_modules/@qingchencloud/openclaw-zh/skills"

for dir in "$SKILLS_DIR"/*/; do
  name=$(basename "$dir")
  # 尝试读取 SKILL.md 获取描述
  desc=$(grep -A1 "^description:" "$dir/SKILL.md" 2>/dev/null | tail -1 | tr -d ' ')
  [ -z "$desc" ] && desc="待补充"
  printf "%-20s %s\n" "$name" "$desc"
done
```

## 与其他工具配合

### 结合 evolution-report
```bash
#!/bin/bash
# 完整的能力检查
echo "=== 📊 能力统计 ==="
./scripts/evolution-report.sh | grep -E "技能总数|自定义工具"

echo ""
echo "=== 📚 技能列表 ==="
./scripts/quick-skill-check.sh
```

### 结合搜索功能
```bash
# 搜索特定技能
./scripts/quick-skill-check.sh | grep -i "搜索"
# 或
./scripts/quick-skill-check.sh | grep -i "自动化"
```

## 维护建议

### 定期更新
- 学习新技能后，立即添加到列表
- 删除已废弃的技能
- 更新技能描述

### 版本控制
```bash
# 提交到 Git
git add scripts/quick-skill-check.sh
git commit -m "update: 添加新技能 xxx"
```

### 与实际技能同步
```bash
# 检查是否有遗漏
for dir in /opt/homebrew/lib/node_modules/@qingchencloud/openclaw-zh/skills/*/; do
  name=$(basename "$dir")
  grep -q "$name" scripts/quick-skill-check.sh || echo "遗漏: $name"
done
```

## 最佳实践

### 1. 保持简洁
- 技能列表应该简洁明了
- 描述应该简短准确
- 避免冗余信息

### 2. 分类组织
- 按功能领域分组
- 使用注释标记类别
- 便于快速查找

### 3. 定期同步
- 与实际安装的技能保持同步
- 删除不再使用的技能
- 更新技能描述

### 4. 版本记录
- 使用 Git 跟踪变化
- 记录技能学习历史
- 分析成长趋势

## 故障排除

### 技能数量不准确
- 检查数组语法是否正确
- 确认技能名称没有重复
- 验证计数的准确性

### 描述显示不正确
- 检查冒号分隔符位置
- 确保描述中没有特殊字符
- 验证 IFS 分割逻辑

### 输出格式混乱
- 调整 `printf` 格式字符串
- 检查技能名称长度
- 考虑使用表格格式

## 技能类别参考

常见技能分类：
- **编程与自动化:** coding-agent, mcporter, oracle
- **macOS 集成:** peekaboo, things-mac, apple-reminders
- **内容处理:** summarize, openai-whisper, video-frames
- **信息获取:** weather, 1password, goplaces
- **通讯:** discord, slack, bird, wacli
- **邮件管理:** gog, himalaya
- **娱乐与音频:** blucli, songsee, spotify-player
- **AI 模型:** gemini, openai-image-gen
- **工具创建:** skill-creator, clawhub
- **监控与分析:** model-usage, session-logs

## 示例配置

### 完整的技能列表模板
```bash
declare -a learned=(
  # 编程与自动化
  "coding-agent:编程代理"
  "mcporter:MCP工具"
  "oracle:长思考"
  "github:GitHub CLI"
  "tmux:终端会话"
  
  # macOS 集成
  "peekaboo:macOS自动化"
  "things-mac:任务管理"
  "apple-reminders:提醒"
  "bear-notes:Bear笔记"
  
  # 内容处理
  "summarize:内容总结"
  "openai-whisper:语音转文字"
  "video-frames:视频帧"
  
  # 信息获取
  "weather:天气"
  "1password:密码管理"
  
  # 通讯
  "discord:Discord控制"
  "slack:Slack控制"
  "bird:X/Twitter"
  
  # 等等...
)
```

## 贡献

欢迎改进！请在 GitHub 上提交 issue 或 PR。

## 仓库

https://github.com/90le/openclaw-skills-hub

## 作者

Created by Xiaoqiu (小丘) - OpenClaw AI assistant

---

**快速了解自己的能力！** 📚

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/90le) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
