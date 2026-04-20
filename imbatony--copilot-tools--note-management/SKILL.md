---
name: note-management
description: >- Use when this capability is needed.
metadata:
  author: imbatony
---

# 笔记管理技能

使用 Python 脚本直接调用 [Blinko](https://github.com/blinko-space/blinko) API 管理个人笔记和闪念。

## 可用命令

| 命令 | 功能 | 使用场景 |
|------|------|----------|
| `flash <内容>` | 创建闪念 (type 0) | 快速记录想法、灵感 |
| `note <内容>` | 创建笔记 (type 1) | 详细记录内容、备忘 |
| `todo <内容>` | 创建待办 (type 2) | 记录待办事项、任务 |
| `search <关键词>` | 搜索笔记 | 查找之前的记录 |
| `share <笔记ID>` | 分享笔记 | 分享笔记并获取链接 |
| `unshare <笔记ID>` | 取消分享 | 取消笔记分享 |
| `review` | 每日回顾 | 获取今日待回顾笔记 |
| `clear-recycle` | 清空回收站 | 永久删除已删除的笔记 |

## 使用方法

### 基础命令

```bash
# 切换到 skill 目录
cd .github/skills/note-management

# 创建闪念
uv run scripts/blinko.py flash "快速记一个想法"

# 创建笔记
uv run scripts/blinko.py note "详细的会议纪要..."

# 创建待办
uv run scripts/blinko.py todo "明天要完成的任务"

# 搜索笔记
uv run scripts/blinko.py search "项目" --size 10

# 分享笔记
uv run scripts/blinko.py share 123 --password 666666

# 每日回顾
uv run scripts/blinko.py review

# 清空回收站 (需确认)
uv run scripts/blinko.py clear-recycle --force
```

### 创建闪念

当用户说 "记录一下..."、"快速记一个想法..."、"闪念..." 时：

```bash
uv run scripts/blinko.py flash "用户的想法内容"
```

### 创建笔记

当用户说 "帮我记住..."、"创建一条笔记..."、"详细记录..." 时：

```bash
uv run scripts/blinko.py note "用户的笔记内容"
```

### 创建待办

当用户说 "添加待办..."、"记一个任务..."、"todo..." 时：

```bash
uv run scripts/blinko.py todo "用户的待办内容"
```

### 搜索笔记

当用户说 "找一下我之前记的..."、"搜索笔记..."、"我之前写过..." 时：

```bash
# 基础搜索
uv run scripts/blinko.py search "关键词"

# 指定返回数量
uv run scripts/blinko.py search "关键词" --size 10

# 按类型过滤 (flash/note/todo/all)
uv run scripts/blinko.py search "关键词" --type flash

# 精确匹配 (禁用 AI 搜索)
uv run scripts/blinko.py search "关键词" --exact

# 显示详细信息
uv run scripts/blinko.py search "关键词" --verbose
```

### 分享笔记

当用户说 "分享这条笔记..."、"取消分享..." 时：

```bash
# 分享笔记
uv run scripts/blinko.py share 笔记ID

# 带密码分享
uv run scripts/blinko.py share 笔记ID --password 666666

# 取消分享
uv run scripts/blinko.py unshare 笔记ID
```

### 每日回顾

当用户说 "今天要回顾什么..."、"每日回顾..." 时：

```bash
uv run scripts/blinko.py review
```

### JSON 输出

所有命令都支持 `--json` 参数输出 JSON 格式，便于程序处理：

```bash
uv run scripts/blinko.py flash "内容" --json
uv run scripts/blinko.py search "关键词" --json
```

## 配置要求

### 环境变量

设置以下环境变量：

```powershell
# Windows PowerShell (永久)
[Environment]::SetEnvironmentVariable("BLINKO_DOMAIN", "http://your-blinko-server:1111", "User")
[Environment]::SetEnvironmentVariable("BLINKO_TOKEN", "your-api-key", "User")
```

```bash
# Linux/macOS
export BLINKO_DOMAIN="http://your-blinko-server:1111"
export BLINKO_TOKEN="your-api-key"
```

### BLINKO_DOMAIN 格式

支持多种格式：
- 纯域名: `myblinko.com`, `localhost:3000`
- 完整 URL: `https://myblinko.com`, `http://localhost:3000`

## 重要：Markdown 文件保存规则

**当需要将 Markdown 文件内容保存为笔记时，必须直接读取文件原始内容并保存，不要对内容进行总结、摘要或任何修改。** 使用以下方式：

```powershell
# 读取文件内容并直接保存为笔记（不要总结或修改内容）
$content = Get-Content -Path "文件路径.md" -Raw
cd .github/skills/note-management
uv run scripts/blinko.py note $content
```

此规则确保保存到笔记中的内容与原始文档完全一致。

## 常见场景

| 用户问题 | 执行命令 |
|---------|---------|
| "快速记录一个想法" | `uv run scripts/blinko.py flash "想法内容"` |
| "记录一下今天的会议内容" | `uv run scripts/blinko.py note "会议内容"` |
| "添加一个待办：明天开会" | `uv run scripts/blinko.py todo "明天开会"` |
| "我之前记过关于XX的内容" | `uv run scripts/blinko.py search "XX"` |
| "分享笔记 123 给朋友" | `uv run scripts/blinko.py share 123` |
| "今天有什么要回顾的" | `uv run scripts/blinko.py review` |
| "清空回收站" | `uv run scripts/blinko.py clear-recycle --force` |
| "把这个文档保存到笔记" | 使用 `Get-Content -Raw` 读取文件，直接传给 `blinko.py note` |

## 文件结构

```
note-management/
├── SKILL.md              # 技能定义 (本文件)
├── scripts/
│   ├── blinko.py         # 主命令行工具
│   └── blinko_client.py  # Blinko API 客户端
└── references/
    └── REFERENCE.md      # API 参考文档
```

## 详细文档

- 查看 [references/REFERENCE.md](references/REFERENCE.md) 获取 API 详细说明
- 查看 [Blinko](https://github.com/blinko-space/blinko) 获取 Blinko 服务信息

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imbatony) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
