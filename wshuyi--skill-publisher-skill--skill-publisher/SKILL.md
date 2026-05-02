---
name: skill-publisher
description: Publish Claude Code Skills to GitHub with proper structure, privacy checks, and bilingual documentation. Use when user wants to "publish a skill", "share a skill", "release a skill to GitHub", or asks about skill distribution. Use when this capability is needed.
metadata:
  author: wshuyi
---

# Skill Publisher

帮助用户将 Claude Code Skill 发布到 GitHub，支持 Git Clone 和插件市场两种安装方式。

## 发布流程概览

```
[1] 定位 Skill 源文件
     ↓
[2] 隐私安全检查（关键！）
     ↓
[3] 收集用户信息（GitHub 用户名、许可证等）
     ↓
[4] 创建项目结构
     ↓
[5] 生成配置文件和文档
     ↓
[6] 初始化 Git 并推送
     ↓
[7] 输出安装说明
```

---

## Step 1: 定位 Skill 源文件

用户可能提供：
- Skill 名称：在 `~/.claude/skills/` 下查找
- 具体路径：直接使用

```bash
# 列出用户的所有 Skills
ls -la ~/.claude/skills/
```

确认 Skill 包含必要文件：
- `SKILL.md`（必须）
- `scripts/`（可选）
- 其他支持文件

---

## Step 2: 隐私安全检查（关键！）

**发布前必须检查所有文件，查找以下敏感信息：**

### 2.1 使用脚本检查

```bash
python ~/.claude/skills/skill-publisher/scripts/check_privacy.py /path/to/skill
```

### 2.2 手动检查要点

| 类型 | 示例 | 处理方式 |
|------|------|----------|
| API 密钥 | `sk-xxx`, `api_key=xxx` | 删除或用占位符替换 |
| OAuth Token | `oauth_token: xxx` | 删除，绝对不能提交 |
| 个人路径 | `/Users/username/` | 替换为 `~/` 或 `/path/to/` |
| 邮箱地址 | `user@example.com` | 确认是否需要公开 |
| 用户名 | 硬编码的用户名 | 使用通用占位符 |
| 密码 | 任何明文密码 | 删除 |

### 2.3 检查结果处理

- **发现敏感信息**：提示用户并帮助修复
- **无敏感信息**：继续下一步

**教训**：曾经在读取 `~/.config/gh/hosts.yml` 时暴露了 GitHub token。任何配置文件都要先检查内容再决定是否读取！

---

## Step 3: 收集用户信息

通过 AskUserQuestion 收集：

1. **项目目录**：`~/Projects`、`~/Developer` 或其他
2. **GitHub 用户名**：用于生成仓库链接
3. **许可证类型**：MIT（推荐）、Apache 2.0、GPL 3.0
4. **Skill 简短描述**：用于 plugin.json 和 README

---

## Step 4: 创建项目结构

```
{skill-name}/
├── .claude-plugin/
│   └── plugin.json           # 插件配置（支持市场安装）
├── skills/
│   └── {skill-name}/
│       ├── SKILL.md          # 核心指令（从源复制）
│       └── scripts/          # 脚本文件（如有）
├── docs/
│   └── GUIDE.md              # 详细使用指南（可选）
├── .gitignore
├── LICENSE
├── README.md                 # 英文说明
└── README_CN.md              # 中文说明
```

### 关键命令

```bash
# 创建目录结构
mkdir -p /path/to/project/.claude-plugin
mkdir -p /path/to/project/skills/{skill-name}/scripts
mkdir -p /path/to/project/docs

# 复制 Skill 文件
cp -r ~/.claude/skills/{skill-name}/* /path/to/project/skills/{skill-name}/
```

---

## Step 5: 生成配置文件和文档

### 5.1 plugin.json

使用模板 `~/.claude/skills/skill-publisher/templates/plugin.json.template`：

```json
{
  "name": "{skill-name}",
  "version": "1.0.0",
  "description": "{description}",
  "author": {
    "name": "{github-username}"
  },
  "repository": "https://github.com/{github-username}/{repo-name}",
  "license": "{license}",
  "keywords": ["{keyword1}", "{keyword2}"],
  "platforms": ["macos"]
}
```

### 5.2 README.md（英文）

必须包含以下章节：

1. **项目标题和简介**
2. **痛点分析**（The Problem）- 用户面临什么问题
3. **解决方案**（The Solution）- 这个 Skill 如何解决
4. **环境要求**（Requirements）- 列出所有依赖
5. **安装方式**（Installation）- Git Clone + 插件市场
6. **使用方法**（Usage）- 自然语言 + 命令
7. **工作流程**（Workflow）- 步骤说明
8. **示例**（Example）- 完整的使用示例
9. **FAQ** - 常见问题
10. **许可证和作者**

使用模板：`~/.claude/skills/skill-publisher/templates/README.md.template`

### 5.3 README_CN.md（中文）

与英文版结构相同，内容翻译为中文。

顶部添加语言切换链接：
```markdown
[English](README.md) | [中文](README_CN.md)
```

### 5.4 LICENSE

根据用户选择生成：
- MIT: `~/.claude/skills/skill-publisher/templates/LICENSE-MIT.template`
- Apache 2.0: `~/.claude/skills/skill-publisher/templates/LICENSE-APACHE.template`
- GPL 3.0: `~/.claude/skills/skill-publisher/templates/LICENSE-GPL.template`

### 5.5 .gitignore

```
# Python
__pycache__/
*.py[cod]
.venv/
venv/

# macOS
.DS_Store

# Temp
*.tmp
/tmp/
```

---

## Step 6: 初始化 Git 并推送

### 6.1 检查 gh CLI

```bash
which gh || echo "gh not installed"
```

如未安装，提示用户：
```bash
brew install gh
```

### 6.2 检查 GitHub 登录状态

```bash
gh auth status
```

如未登录，提示用户：
```bash
gh auth login
```

### 6.3 初始化并推送

```bash
cd /path/to/project

# 初始化 Git
git init
git branch -m main  # 使用 main 而非 master

# 添加所有文件
git add .

# 提交（使用 HEREDOC 保证格式）
git commit -m "$(cat <<'EOF'
Initial release: {Skill Name}

Features:
- Feature 1
- Feature 2
- Feature 3

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
EOF
)"

# 创建 GitHub 仓库并推送
gh repo create {repo-name} --public --source=. --remote=origin --push \
  --description "{short description}"
```

---

## Step 7: 输出安装说明

发布完成后，输出以下信息：

```
✅ Skill 发布成功！

📦 仓库地址：https://github.com/{username}/{repo-name}

📥 安装方式：

方式 1：Git Clone
git clone https://github.com/{username}/{repo-name}.git
cp -r {repo-name}/skills/{skill-name} ~/.claude/skills/

方式 2：插件市场
/plugin marketplace add {username}/{repo-name}
/plugin install {skill-name}@{username}/{repo-name}
```

---

## 经验教训总结

### 隐私安全

1. **发布前必须检查所有文件**，不仅是 SKILL.md
2. **配置文件特别危险**：hosts.yml、.env、credentials.json
3. **路径中可能包含用户名**：`/Users/wsy/` 应替换为 `~/`
4. **读取文件前先确认**：不要随意读取可能包含敏感信息的文件

### 项目结构

1. **支持两种安装方式**：Git Clone（简单）+ 插件市场（标准）
2. **双语文档**：README.md（英文）+ README_CN.md（中文）
3. **详细文档单独放**：docs/GUIDE.md，README 保持简洁

### Git 操作

1. **分支命名**：使用 `main` 而非 `master`
2. **提交信息**：使用 HEREDOC 保证多行格式
3. **gh CLI**：比手动操作更高效，但需要先安装和登录

### 文档质量

1. **痛点驱动**：先说明问题，再介绍解决方案
2. **时间对比**：用数据展示效率提升（如 10 倍）
3. **完整示例**：包含输入、命令、预期输出
4. **FAQ**：预先回答常见问题

---

## 快速命令

```bash
# 检查隐私
python ~/.claude/skills/skill-publisher/scripts/check_privacy.py /path/to/skill

# 一键发布（需要先收集信息）
# 使用本 Skill 的交互式流程
```

---

## 相关文件

- 模板目录：`~/.claude/skills/skill-publisher/templates/`
- 检查脚本：`~/.claude/skills/skill-publisher/scripts/check_privacy.py`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wshuyi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
