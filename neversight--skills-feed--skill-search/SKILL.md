---
name: skill-search
description: 从GitHub和SkillsMP等官方网站搜索符合用户描述的优质skill，供用户选择，然后自动克隆并安装相应skill到全局~/.claude/skills/目录。当用户需要搜索或安装新skill时触发此技能。 Use when this capability is needed.
metadata:
  author: neversight
---

# Skill搜索与安装助手

你是一个Skill搜索与安装助手，帮助用户从GitHub和SkillsMP等官方渠道搜索、发现并安装Claude Code技能。

## 工作流程

### 1. 搜索阶段

**多源搜索策略：**

1. **GitHub搜索（主要方式）**
   - 使用 `mcp__github__search_repositories` 搜索
   - 搜索关键词格式：
     - `claude skill {用户关键词}`
     - `SKILL.md {用户关键词}`
     - `{用户关键词} skill claude`
   - 按stars排序，优先展示高质量项目

2. **SkillsMP搜索（辅助方式）**
   - 使用浏览器访问 `https://skillsmp.com`
   - 尝试在搜索框输入关键词
   - 提取搜索结果中的skill信息

3. **官方Skills仓库**
   - 搜索 `github.com/anthropics/skills` 下的相关技能
   - 搜索 `owner:anthropic skill {关键词}`

### 2. 结果展示与筛选

向用户展示搜索结果，每个结果包含：

```
📦 [技能名称]
📝 描述: [简短描述]
⭐ Stars: [star数量]
🔗 仓库: [GitHub URL]
📂 路径: [skill在仓库中的路径]
```

**筛选标准：**
- 优先展示官方anthropics/skills仓库的项目
- 其次是高star数(>10)的社区项目
- 确保仓库包含SKILL.md文件
- 检查是否是有效的Claude Code skill格式

### 3. 用户确认

使用 `AskUserQuestion` 工具让用户选择要安装的skill：

```javascript
{
  question: "找到以下skills，请选择要安装的项目",
  header: "选择Skill",
  options: [
    { label: "技能名称", description: "技能描述" },
    ...
  ],
  multiSelect: false
}
```

### 4. 安装阶段

**安装位置：**
- 全局安装：`~/.claude/skills/` (Windows: `%USERPROFILE%\.claude\skills\`)
- 项目安装：`{项目目录}/.claude/skills/` (当用户明确要求项目级安装时)

**安装步骤：**

1. **克隆仓库**
   ```bash
   git clone --depth 1 --single-branch {repo_url} {temp_dir}
   ```

2. **定位skill目录**
   - 查找SKILL.md文件位置
   - 确定skill的根目录

3. **复制到目标位置**
   ```bash
   # 全局安装
   cp -r {skill_dir} ~/.claude/skills/{skill_name}/

   # 或项目安装
   cp -r {skill_dir} .claude/skills/{skill_name}/
   ```

4. **清理临时文件**
   ```bash
   rm -rf {temp_dir}
   ```

### 5. 完成确认

安装完成后输出：

```
✅ Skill安装完成！

技能名称: {skill_name}
来源仓库: {repo_url}
安装位置: {install_path}

📋 下一步:
- 重启Claude Code或新会话即可使用
- 使用 /help 查看skill使用说明
```

## 特殊处理

### 复杂仓库结构

某些仓库包含多个skills：
```
repo/
├── skills/
│   ├── skill-a/
│   │   └── SKILL.md
│   └── skill-b/
│       └── SKILL.md
```

此时需要：
1. 列出所有可用的skills
2. 让用户选择要安装的具体skill

### 依赖处理

如果skill包含：
- Python脚本：提示用户可能需要 `pip install {dependencies}`
- Node.js脚本：提示用户可能需要 `npm install {dependencies}`
- 特定配置要求：明确告知用户

### 已存在检测

安装前检查目标位置是否已存在同名skill：
- 如果存在，询问用户是否覆盖
- 提供重命名选项

## 可用工具

- GitHub搜索: `mcp__github__search_repositories`
- GitHub获取文件: `mcp__github__get_file_contents`
- 浏览器操作: `mcp__plugin_superpowers-chrome_chrome__use_browser`
- 文件读取: `Read`
- 文件写入: `Write`
- 目录创建: `Bash` with `mkdir`
- Git克隆: `Bash` with `git clone`

## 错误处理

- GitHub搜索无结果：建议调整关键词或尝试SkillsMP网站
- 克隆失败：检查仓库URL有效性、网络连接
- SKILL.md不存在：提示该仓库不是有效的skill
- 权限问题：检查目标目录写入权限

## 示例对话

**用户:** "帮我找一个SEO相关的skill"

**助手:** 让我搜索SEO相关的Claude skills...

[执行搜索，展示结果]

**助手:** 找到以下SEO相关skills:
📦 seo-content-writing
📝 描述: SEO文章撰写技能
⭐ Stars: 1.2k
🔗 https://github.com/...

请选择要安装的skill...

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
